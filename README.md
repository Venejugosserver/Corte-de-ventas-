# CORTE DE VENTAS · Venejugos 2015

Tablero del corte de facturación diario por coordinación, con histórico de cada corte registrado.
Misma familia visual que GLOBAL KPI (Manrope, azul y rojo Venejugos), con modo oscuro e impresión A4.

| Archivo | Para qué sirve |
|---|---|
| `index.html` | **Corte del día** (cuadro de seguimiento, indicadores y gráficas) e **Histórico** de cortes registrados. |
| `admin.html` | **Panel administrativo**: carga de archivos, registro del corte y publicación en GitHub. Llave: **DIGIMARKET2026**. |
| `corte.json` | Último corte procesado (todos los días del archivo de ventas). |
| `historial.json` | Registros congelados de cada corte. Lo publica el panel administrativo. |
| `icono.svg` | Ícono de CORTE DE VENTAS. |

SheetJS y Chart.js van incrustados en las páginas: no necesitan instalación.

## Uso diario

1. Abre `admin.html` y escribe la llave.
2. Suelta los Excel: ventas del corte, maestro de clientes y, si lo tienes, `SEGUIMIENTO_DIARIO.xlsx`
   (de ahí se toman los comentarios de MobilVendor y las órdenes por facturar).
3. **Procesar corte** y revisa la vista previa.
4. Elige el día, marca **Parcial** (corte durante el día) o **Cierre del día**, agrega una nota si quieres y pulsa **Registrar corte**.
5. Si la conexión con GitHub está configurada, se publica solo. Si no, usa **Publicar ahora** o descarga los JSON y súbelos a la raíz del repositorio.

## Día del corte

El día se elige en un **calendario**: los días con facturación en el archivo cargado tienen un punto rojo y son los únicos
que se pueden elegir. Las flechas ‹ › a los lados pasan al día anterior o siguiente, y **Ver acumulado** suma todos los días del corte.

## Líneas de servicio

En *Corte del día* y en *Histórico* hay tres botones de **Línea**:

- **Sin cigarros**: el cuadro original del Excel, sin DISTRIBUIDORA BIGOTT ni cigarrera.
- **Todas las líneas**: todas las unidades de negocio, cigarros incluidos.
- **Bigott y cigarrera**: solo cigarros (unidad de negocio Bigott o cigarrera, o línea de tabaco), con el desglose por marca.

La elección se recuerda en el navegador. Las visitas programadas y comentadas son las mismas en las tres vistas; cambian la venta,
las devoluciones, los clientes atendidos, las órdenes y el cumplimiento.

## Clientes excluidos

**INLUVENCA** queda fuera de todos los cálculos (ventas, órdenes y maestro). La lista está en `CLIENTES_EXCLUIDOS` dentro de
`index.html` y `admin.html`; el panel administrativo avisa cuántas líneas y cuánto monto se excluyeron.

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

1. Crea un repositorio propio (por ejemplo `Venejugosserver/CORTE-DE-VENTAS`) y sube los seis archivos sueltos en la raíz
   (*Add file → Upload files*, selecciónalos todos). No hace falta ninguna carpeta.
2. **Settings → Pages → Source: Deploy from a branch → main / (root)**.
3. Crea un token de alcance fino solo para ese repositorio con **Contents: Read and write**.
4. En el panel administrativo, tarjeta **Publicar en GitHub**, escribe `usuario/repositorio`, rama y token. Deja vacía la carpeta de datos:
   el panel actualiza `corte.json` e `historial.json` en la raíz.

Las llaves de almacenamiento del navegador usan el prefijo `vjc_`, así que conviven con GLOBAL KPI (`vjp_`, `vjm_`) en el mismo dominio.

## Reglas de cálculo

- **Visita PRG**: clientes del maestro con `DIA DE VISITA` igual al día, sin estatus INACTIVO, CERRADO, CAMBIO DE RAZON SOCIAL ni VACACIONES.
  Anthony Chacón, Liliana Romero y Directo Oficina en 0; Venejugos Empleados en guion.
- **Clientes atendidos**: RIF con venta neta positiva en el día.
- **Fuera de ruta**: por defecto igual que el Excel (fuera de ruta = clientes atendidos). Con la casilla *Fuera de ruta real* del panel, son los atendidos cuyo día de visita en el maestro no es el día del corte.
- **Órdenes**: clientes con orden por facturar. Igual que en el Excel, su monto se suma al **TOTAL VENTA DIARIA $** del día del corte (último día del archivo de ventas). El indicador de venta muestra el desglose facturado + órdenes.
- **Visitas comentadas**: clientes distintos con comentario del asesor en MobilVendor ese día.
- **CUMPL%** = (visitas comentadas + órdenes + atendidos en ruta) ÷ visita PRG. En el modo Excel, los atendidos en ruta quedan en 0.
- **Órdenes por vendedor**: se agrupan por `NombreVend`, como la tabla dinámica del Excel; las de Anthony Chacón no se cuentan (el Excel las oculta). En la vista *Sin cigarros* las órdenes entran completas, igual que en el Excel.
- **Venta y devolución**: suma de `total_venta_dolar` por tipo, sin DISTRIBUIDORA BIGOTT (cigarros, aparte), CHOCOLATES ST. MORITZ ni #N/A.

---

Venejugos 2015 · CORTE DE VENTAS
