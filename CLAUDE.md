# Tablero de Negociaciones Houghton — Protocolo para Claude

App estática (index.html + data.json) que Santiago hostea en GitHub Pages. **La fuente de verdad es `data.json` de esta carpeta.** El navegador lo lee al abrir la página y lo mezcla con lo editado a mano (gana el `updatedAt` más nuevo por propiedad).

## Cuando Santiago manda un audio/mensaje sobre una negociación

1. Leer SOLO `data.json` (no hace falta leer index.html — gastar mínimos tokens).
2. Identificar la propiedad por dirección/localidad. Si no existe, crearla con el schema de abajo (`id` nuevo, ej. `p` + timestamp base36).
3. Volcar lo conversado:
   - Actualizar los campos que cambien (oferta, contraoferta, valorAcordado, fechas, requisitos, condiciones…). Montos en USD como string numérico sin puntos. Fechas `YYYY-MM-DD`.
   - SIEMPRE agregar una entrada a `seguimiento`: `{fecha: hoy, parte: "comprador"|"vendedor"|"sistema", nota: resumen fiel de lo conversado}`.
   - Actualizar `updatedAt` de la propiedad y del root (ISO completo).
4. Chequear incongruencias y avisarle en la respuesta (la app también las muestra, pero conviene decírselo):
   - valorEscrituracion < valorAcordado, sobre todo si formaPago menciona transferencia/depósito "por el total" (choque impositivo/bancario).
   - fechaEntrega anterior a escrituraFecha sin posesión anticipada pactada.
   - contraoferta menor que la oferta; valorAcordado fuera del rango; reserva+refuerzos > acordado; refuerzos posteriores a la escritura.
   - reservas/refuerzos vencidos o que vencen en ≤7 días → avisar SIEMPRE al final de la respuesta ("Ojo: la reserva de X vence el …").
5. Confirmarle en 2-3 líneas qué se registró. Si el repo git está inicializado en esta carpeta, commitear y pushear para que el tablero online se actualice; si no, recordarle subir `data.json`.

## Verificación de documentos en Dropbox

Los documentos de cada propiedad están en:
`Dropbox / 10 Propiedades Generales / {Localidad} / {Dirección} /` (Reservas, Refuerzos, Boletos, etc.)

Cuando pida "revisá la reserva/refuerzo de X": buscar el documento en esa ruta, comparar montos, plazos y vencimientos contra `data.json`, reportar diferencias y actualizar el tablero si corresponde.

## Chequeo de vencimientos (mínimo costo de tokens)

- La app calcula y muestra los vencimientos sola (panel "Próximos vencimientos", ventana 14 días) → costo cero.
- Claude NO necesita monitoreo programado: en CADA interacción sobre el tablero, tras cargar `data.json`, revisar los vencimientos ≤7 días y mencionarlos. Eso mantiene el seguimiento sin tareas agendadas.
- Si Santiago pide un recordatorio proactivo, ofrecerle una tarea agendada semanal que solo lea `data.json` y le mande el resumen de vencimientos (archivo chico ⇒ barato).

## Estrategias y patrones

Cuando pida análisis: usar el histórico de `seguimiento` + valores de todas las propiedades (gap oferta→cierre, tiempos entre etapas, requisitos que suelen trabar, condiciones especiales frecuentes) y darle 2-3 recomendaciones accionables, no un informe largo.

## Schema de propiedad

```json
{
 "id": "p...", "direccion": "", "localidad": "", "estado": "En negociación|Reservada|Refuerzo|Boleto|Escriturada|Caída",
 "comprador": "", "vendedor": "", "telComprador": "", "telVendedor": "",
 "ofertaCompra": "", "contraoferta": "", "valorAcordado": "", "valorEscrituracion": "",
 "formaPago": "", "fechaEntrega": "YYYY-MM-DD",
 "requisitosComprador": "", "requisitosVendedor": "", "condicionesEspeciales": "",
 "reservaFecha": "", "reservaVence": "", "reservaMonto": "",
 "refuerzos": [{ "fecha": "", "monto": "", "pagado": false }],
 "boletoFecha": "", "escrituraFecha": "",
 "seguimiento": [{ "fecha": "", "parte": "comprador|vendedor|sistema", "nota": "" }],
 "updatedAt": "ISO"
}
```
