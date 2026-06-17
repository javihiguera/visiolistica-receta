# CHANGELOG — Generador de Receta Visiolística

## [2.0.0] — 2025-06-17

### Refactorización completa del parser de Entrada libre

#### Añadido

- **`normalizeInput()`** — nueva fase de normalización previa al parseo:
  - Convierte coma decimal a punto (`1,50` → `1.50`)
  - Normaliza símbolo de grado (`°` → `º`)
  - Convierte `NEUTRO` y `N` a `0`
  - Convierte `COMPENSADOR` a `COMP`
  - Normaliza nombres de base prismática: `INFERIOR` → `INF`, `SUPERIOR` → `SUP`, `DERECHA` → `DCHA`, `IZQUIERDA` → `IZQ`

- **`parseEyeLine()`** — nuevo parser clínico por línea de ojo con orden explícito:
  `ESFERA → CILINDRO → EJE → PRISMA → BASE`
  - Reconoce cilindro con sufijo `cil`, `CIL` o `C` (pegado o separado)
  - Reconoce prisma con sufijo `V` (ej: `5V`, `2.5V`)
  - Reconoce base prismática por nombre (`INF`, `SUP`, `DCHA`, `IZQ`) y por grados directos (`270º`, `90º`)
  - Mapeo automático nombre → grados: `INF=270`, `SUP=90`, `DCHA=180`, `IZQ=0`
  - Soporta esfera `COMP` como valor textual especial

- **`parseReceta()`** — nuevo parser principal de receta completa:
  - Detecta la palabra clave `CERCA` y activa modo cerca (`isCercaMode`)
  - Detecta `ADD` con adición única (`ADD 2,50`) o diferenciada por ojo (`ADD 2,50/2,75`)
  - Asigna OD a la primera línea de datos y OI a la segunda

- **`validateEye()`** — nueva función de validación clínica independiente por ojo:
  - Cilíndrico requiere EJE y viceversa
  - Prisma requiere BASE y viceversa
  - EJE debe estar en rango 0–180°
  - Prisma debe ser mayor que 0
  - Add debe ser mayor que 0

- **Modo CERCA**:
  - Detección automática de la palabra `CERCA` en la entrada
  - La tabla del PDF muestra `CERCA` en lugar de `LEJOS`
  - El selector "Para utilizar" se marca automáticamente en `CERCA`

- **Herencia de prisma en adición**:
  - Cuando existe ADD, el prisma y la base de lejos se trasladan automáticamente a las filas de adición en el PDF

- **Ayuda visual en Entrada libre**:
  - Bloque de ejemplos reales visible bajo el textarea con los formatos admitidos

#### Modificado

- `generatePDF()` — actualizada para:
  - Usar la etiqueta `CERCA` o `LEJOS` en la tabla según `isCercaMode`
  - Incluir herencia de prisma/base en filas de adición
  - Invocar las nuevas funciones de validación `validateEye()` y `validateAdd()`
  - Abrir el documento en nueva pestaña con `window.open()` y lanzar `window.print()` automáticamente al cargar

- `newReceta()` — resetea ahora también el estado `isCercaMode`

#### Eliminado

- Función `parseRaw()` — sustituida íntegramente por `normalizeInput()` + `parseEyeLine()` + `parseReceta()`
- Lógica antigua de detección de base prismática mediante expresiones regulares genéricas
- Lógica antigua de detección de cilindro sin orden clínico explícito

---

## [1.2.0] — 2025-06-17

### Seguridad y acceso

#### Añadido

- **Pantalla de PIN** con teclado numérico antes de acceder a la aplicación
  - PIN de 4 dígitos configurable en el código (`PIN_CORRECT`)
  - Animación de sacudida y mensaje de error en PIN incorrecto
  - Botón "Cerrar sesión" que restablece el estado y vuelve a la pantalla de PIN

---

## [1.1.0] — 2025-06-17

### Generación de PDF

#### Modificado

- Botón `/pdf — Generar e imprimir` ahora abre el documento en una nueva pestaña del navegador
- El diálogo de impresión del navegador se lanza automáticamente al abrir el documento (`window.onload = print`)
- Eliminados intentos previos con `iframe` oculto y descarga de archivo `.html` (incompatibles con el entorno de despliegue)

---

## [1.0.0] — 2025-06-17

### Lanzamiento inicial — GitHub Pages

#### Añadido

- Archivo `index.html` único con toda la aplicación (HTML + CSS + JS sin dependencias externas)
- **Modo Manual**: entrada campo a campo (Esférico, Cilíndrico, Eje, Prisma, Base, AV) para OD y OI
- **Modo Entrada libre**: textarea con parser automático
- Selectores de **Especificaciones** (Monofocal, Monofocal Avanzado, Bifocal, Progresivo)
- Selectores de **Para utilizar** (Constante, Lejos, Cerca)
- Campos de **Nombre** y **Fecha** del paciente
- **Validación clínica** básica antes de generar el PDF
- **Logo Visiolística** en cabecera de la app y del documento imprimible
- Degradado de marca (marrón → magenta) como separador visual en el PDF
- Espacio de **Firma / Sello** en el documento generado
- Botón **Nueva receta** para limpiar todos los campos
- Diseño responsive adaptado a escritorio y móvil
- Despliegue configurado en `https://javihiguera.github.io/visiolistica-receta/`
