# Tablero de Negociaciones — Houghton Propiedades

Tablero online de seguimiento y negociación de propiedades, con el diseño de la marca (mismo estilo que la app de Tasaciones).

## Qué hace

- **Listado de propiedades** por dirección y localidad, con comprador, vendedor, oferta de compra, contraoferta, valor acordado, valor de escrituración, forma de pago, fecha de entrega, requisitos de cada parte y condiciones especiales (aires acondicionados, cortinas, etc.).
- **Seguimiento de la negociación**: cada conversación con comprador o vendedor queda registrada con fecha en la línea de tiempo de la propiedad — nada se olvida en el ida y vuelta.
- **Incongruencias automáticas**: escrituración menor al acordado con pago bancarizado por el total, entrega antes de la escritura, contraoferta menor a la oferta, refuerzos que superan el acordado, datos clave faltantes, etc. Aparecen como alertas en la tarjeta y en la ficha.
- **Vencimientos**: panel superior con reservas, refuerzos, boletos, escrituras y entregas que vencen en los próximos 14 días (rojo ≤3 días o vencido, ámbar ≤7). Calculado por el propio navegador: costo cero.
- **Estadísticas de negociación**: activas, escrituradas, % promedio entre oferta inicial y cierre, alertas abiertas.

## Cómo actualizar desde el celular (audio)

1. Después de hablar con un cliente, mandale a Claude un audio o mensaje: *"Riobamba 1234: el vendedor contraofertó 188, acepta esperar el crédito, quiere quedarse hasta fin de julio"*.
2. Claude actualiza `data.json`, registra la conversación en el seguimiento, detecta incongruencias y te avisa de vencimientos próximos.
3. Al recargar el tablero online, ves todo actualizado (cuando `data.json` se subió al host).

También podés editar todo a mano desde el tablero (botón **+ Propiedad** y tocando cualquier tarjeta). Lo editado a mano queda en el navegador; usá **Exportar** o **Copiar JSON** para pasárselo a Claude o subirlo al repo.

## Cómo publicarlo (GitHub Pages)

1. Crear un repo (ej. `TABLERO-HOUGHTON`) y subir `index.html` y `data.json`.
2. Settings → Pages → Deploy from branch → `main` / root.
3. Listo: `https://<usuario>.github.io/TABLERO-HOUGHTON/`. En el celular: "Agregar a pantalla de inicio" para tenerlo como app.

Cada vez que Claude actualiza `data.json`, hay que subirlo al repo (o inicializar git en esta carpeta y Claude commitea/pushea solo).

## Verificación con Dropbox

Cada ficha muestra la ruta `Dropbox / 10 Propiedades Generales / {Localidad} / {Dirección}`. Pedile a Claude "revisá la reserva de {dirección}" y compara el documento real (montos, plazos) contra el tablero.

## Archivos

| Archivo | Rol |
|---|---|
| `index.html` | La app completa (autocontenida: fuentes y logo embebidos) |
| `data.json` | Los datos — la fuente de verdad que mantiene Claude |
| `CLAUDE.md` | Protocolo que siguen las sesiones de Claude para actualizar el tablero |
