# Producción Matriz

Control de bodega, cuarto frío, congelador y producción de la matriz.
Registra los insumos que entran, lo que se consume al producir, lo que se
despacha a cafeterías y las mermas, y calcula la existencia sola.

## Aplicación

La app está publicada como Artifact y guarda los datos en una base
compartida: lo que captura una persona lo ven las demás al instante.

Archivo fuente: `app/index.html`

## Estructura de almacenes

Replica las secciones de `datos/INV_MATRIZ.xlsx`, más el cuarto frío de
cárnicos que hacía falta.

| Clave | Almacén | Temperatura | Artículos |
|---|---|---|---|
| `bodega` | Bodega de secos | Ambiente | 68 |
| `refrigerado` | Refrigerado, lácteos y varios | 2 a 6 °C | 11 |
| `verdura` | Cuarto frío, verdura | 2 a 6 °C | 35 |
| `carnico` | Cuarto frío, cárnicos | 0 a 4 °C | 0, por capturar |
| `cafeterias` | Congelador, empacado al vacío | −18 °C | 22 |
| `limpieza` | Limpieza | Ambiente | 7 |
| `desechables` | Desechables | Ambiente | 20 |

## Modelo de datos

Colecciones de la base del Artifact:

- `almacenes` — zonas de guarda con su rango de temperatura.
- `articulos` — insumos y productos terminados, con unidad de compra,
  almacén habitual y stock mínimo.
- `recetas` — fichas técnicas: qué insumos y cuánto rinde cada tanda.
- `movimientos` — la bitácora. Cada renglón lleva signo: entra positivo,
  sale negativo.
- `ordenes` — órdenes de producción, con lo que entró y lo que salió.

La existencia nunca se guarda: se calcula sumando los movimientos. Así no
hay dos cifras que se puedan desincronizar.

Tipos de movimiento: `entrada`, `consumo`, `produccion`, `despacho`,
`merma`, `ajuste`, `traspaso`.

## Conteo y pedido

La pantalla de conteo reproduce la hoja original. Se captura el conteo
físico por sección y el sistema calcula el pedido con la misma fórmula:

```
PEDIDO = MÍNIMO − INVENTARIO
```

Al aplicar el conteo se registra un movimiento de ajuste por cada
diferencia, así queda el rastro de por qué cambió la existencia.

## Volver a importar el inventario

```
python3 datos/importar_inventario.py
```

Lee `datos/INV_MATRIZ.xlsx` y regenera `datos/inventario-matriz.json`.
