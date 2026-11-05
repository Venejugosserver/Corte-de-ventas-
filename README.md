# CORTE DE VENTAS · Venejugos 2015

Tablero del corte de facturación diario por coordinación, con histórico de cada corte registrado.
Misma familia visual que GLOBAL KPI (Manrope, azul y rojo Venejugos), con modo oscuro e impresión A4.

| Archivo | Para qué sirve |
|---|---|
| `index.html` | **Tablero** (cuadro de seguimiento, indicadores, líneas y marcas, gráficas, con filtros) e **Histórico** de cortes registrados. |
| `admin.html` | **Panel administrativo**: carga de archivos, registro del corte y publicación en GitHub. Llave: **DIGIMARKET2026**. |
| `data/corte.json` | Último corte procesado (todos los días del archivo de ventas). |
| `data/historial.json` | Registros congelados de cada corte. Lo publica el panel administrativo. |
| `icono.svg` | Ícono de CORTE DE VENTAS. |

SheetJS y Chart.js van incrustados en las páginas: no necesitan instalación.

## Uso diario

1. Abre `admin.html` y escribe la llave.
2. Suelta los Excel: **ventas de MobilVendor** (ReporteGeneral, hoja Ventas), **ventas de Módulo B**, maestro de clientes y,
   si lo tienes, `SEGUIMIENTO_DIARIO.xlsx` (de ahí se toman los comentarios de MobilVendor y las órdenes por facturar).
   La venta se calcula cruzando los dos archivos de ventas; ya no hace falta armar el consolidado en Excel.
   Si un archivo de MobilVendor cae en la casilla equivocada (ventas u órdenes), usa **Es de órdenes / Son ventas** para moverlo.
3. **Procesar corte** y revisa la vista previa.
4. Elige el día, marca **Parcial** (corte durante el día) o **Cierre del día**, agrega una nota si quieres y pulsa **Registrar corte**.
5. Si la conexión con GitHub está configurada, se publica solo. Si no, usa **Publicar ahora** o descarga los JSON y súbelos a `data/`.

## Tablero y filtros

- **Fechas**: calendario por intervalo. Toca el primer día y luego el último, o usa un atajo (último día, últimos 7 días,
  esta semana, este mes, mes anterior, todo el período). Los días con punto rojo tienen datos.
- El período une el **corte publicado** (manda en sus días) con los **cierres del histórico** de los demás días.
- **Coordinación** y **Vendedor**: selección múltiple; los vendedores se agrupan por coordinación y la lista se reduce a las
  coordinaciones elegidas. El cuadro muestra solo esas filas y los totales se recalculan.
- **Línea** (unidad de negocio) y **Marca**: selección múltiple con buscador. La lista muestra solo lo vendido en el período.
  Con estos filtros, venta, devolución, clientes atendidos y fuera de ruta son solo de esa selección; visita programada,
  órdenes y visitas comentadas no se separan por línea, así que el cumplimiento es referencial (el tablero lo avisa).
- **Líneas y marcas**: tabla con venta, devolución, neto, participación, clientes y número de marcas. Toca una línea para
  ver sus marcas; **Solo esta** aplica el filtro directo.
- **Descargar Excel** baja el cuadro filtrado y una hoja con líneas y marcas.
- Coordinación, vendedor, línea y marca se recuerdan en el navegador; las fechas vuelven siempre al último día con datos.
- El detalle por línea y marca se guarda en `corte.json` y en cada registro del histórico desde esta versión. Los cortes
  procesados antes no lo tienen: se ven en el cuadro y se filtran por vendedor, pero no por línea ni marca
  (vuelve a procesarlos para tenerlo). La marca sale de `DescrFamilia` en MobilVendor y de `marca` en Módulo B.

## Histórico

- Cada registro guarda cómo quedó el cuadro del día en ese momento: asesores, coordinaciones, venta, devoluciones,
  clientes atendidos, cumplimiento, marcas y clientes con mayor compra. Cambios posteriores en los archivos no lo alteran.
- Se pueden hacer varios registros del mismo día (por ejemplo, mediodía y cierre). En la página Histórico, las variaciones
  de los indicadores comparan con el registro anterior del mismo día.
- La gráfica y la matriz de asesores usan, por día, el cierre más reciente; si el día no tiene cierre, el último parcial.
- Un registro con error se **anula** (✕) con motivo opcional: queda tachado y fuera de la gráfica y la matriz. Se puede **restaurar**.
- Al publicar, el histórico se une con el que ya está en el repositorio, así que nunca se pierden registros.
- Cada registro tiene enlace propio: `index.html#registro-<id>`.

## Publicar en GitHub Pages

1. Crea un repositorio propio (por ejemplo `Venejugosserver/CORTE-DE-VENTAS`) y sube todo el contenido de esta carpeta.
2. **Settings → Pages → Source: Deploy from a branch → main / (root)**.
3. Crea un token de alcance fino solo para ese repositorio con **Contents: Read and write**.
4. En el panel administrativo, tarjeta **Publicar en GitHub**, escribe `usuario/repositorio`, rama, carpeta `data` y el token.

Las llaves de almacenamiento del navegador usan el prefijo `vjc_`, así que conviven con GLOBAL KPI (`vjp_`, `vjm_`) en el mismo dominio.

## Reglas de cálculo

- **Visita PRG**: clientes del maestro con `DIA DE VISITA` igual al día, sin estatus INACTIVO, CERRADO, CAMBIO DE RAZON SOCIAL ni VACACIONES.
  Anthony Chacón, Liliana Romero y Directo Oficina en 0; Venejugos Empleados en guion.
- **Clientes atendidos**: RIF con venta neta positiva en el día.
- **Fuera de ruta**: atendidos cuyo día de visita en el maestro no es el día del corte.
- **Órdenes**: clientes con orden por facturar; su monto se suma a la venta del día.
- **Visitas comentadas**: clientes distintos con comentario del asesor en MobilVendor ese día.
- **CUMPL%** = (visitas comentadas + órdenes + atendidos en ruta) ÷ visita PRG.
- **Venta y devolución**: suma por tipo, sin DISTRIBUIDORA BIGOTT (cigarros, aparte), CHOCOLATES ST. MORITZ ni #N/A.

### Cruce de ventas (MobilVendor + Módulo B)

| | MobilVendor (ReporteGeneral) | Módulo B |
|---|---|---|
| Se reconoce por | `RucCliente`, `NombreVend`, `PrecioTotal` y `CodTrans` VT/NC | `total_venta_dolar` y `rif`, sin columnas calculadas |
| Fecha | `FechaFactura` | `fecha` |
| Monto $ | `PrecioTotal` (sin IVA) | `total_venta_dolar` |
| Devolución | `CodTrans` = NC (documentos NCTE…) | monto negativo |
| Cliente | `RucCliente` | `rif` |
| Unidad de negocio | `CodMarca` (NIM → NESTLE, BIG → DISTRIBUIDORA BIGOTT…) | `unidad_negocio` (INDUSTRIAS JAU → FRUGY…) |

- Los números de documento de los dos módulos no se repiten, así que se suman directo. Si alguno aparece en los dos, el panel avisa.
- **Asesor**, en este orden: venta propia del vendedor (Liliana Romero; Directo, Anthony Urreta → Directo Oficina;
  Yeimy Martínez, Carlos Patriarca y la ruta D11 → Venejugos Empleados), luego el maestro por RIF y, si el cliente no está
  en el maestro, el grupo del cliente o el vendedor de la factura (Televentas → Mary Suárez).
  Si un RIF está en varias carteras del maestro, se usa la del asesor que aparece en la factura.
- **Cigarros**: unidad DISTRIBUIDORA BIGOTT solo si la línea es tabaco o cigarrillos; los yesqueros BIC cuentan en la venta.
- La lista de venta propia (`VENTA_PROPIA`) y las equivalencias de marcas (`ALIAS_UN`) están al inicio de la sección
  *Ventas: MobilVendor + Módulo B* de `admin.html` e `index.html` (el bloque de lógica es el mismo en las dos páginas).
- El consolidado anterior (`ventas_corte.xlsx`) todavía se acepta, pero solo se usa si no se cargó ninguno de los dos archivos.

---

Venejugos 2015 · CORTE DE VENTAS
