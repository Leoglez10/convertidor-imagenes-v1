<div align="center">

# Convertidor de Imágenes Pro

### Convierte imágenes a WebP, JPEG o PNG y quita fondos con IA, sin que tus archivos salgan de tu computadora

![Local](https://img.shields.io/badge/procesamiento-100%25%20local-3ECF8E?style=flat-square)
![JavaScript](https://img.shields.io/badge/JavaScript-vanilla-F7DF1E?style=flat-square)
![Build](https://img.shields.io/badge/build-sin%20dependencias-555555?style=flat-square)
![Canvas](https://img.shields.io/badge/API-Canvas-4c51bf?style=flat-square)
![Lotes](https://img.shields.io/badge/lotes-ZIP-orange?style=flat-square)

</div>

> Este README sirve tanto para quien quiere **usar la herramienta** como para quien quiere **modificar el código**.
> Para usarla, ve a [Cómo se usa](#-cómo-se-usa). Para tocar el código, ve a [Desarrollo](#-desarrollo).

---

## 📑 Índice

- [¿Qué es?](#-qué-es)
- [Qué hace exactamente](#-qué-hace-exactamente)
- [Privacidad: qué sale y qué no de tu equipo](#-privacidad-qué-sale-y-qué-no-de-tu-equipo)
- [Cómo se usa](#-cómo-se-usa)
- [Formatos y nombres de archivo](#-formatos-y-nombres-de-archivo)
- [Desarrollo](#-desarrollo)
- [Arquitectura](#-arquitectura)
- [Estructura del proyecto](#-estructura-del-proyecto)
- [Limitaciones conocidas](#-limitaciones-conocidas)
- [Problemas frecuentes](#-problemas-frecuentes)
- [Preguntas frecuentes](#-preguntas-frecuentes)
- [Licencia](#-licencia)

---

## 🎯 ¿Qué es?

**Convertidor de Imágenes Pro** es una herramienta web que hace dos cosas con tus imágenes:

1. **Convertirlas** entre WebP, JPEG y PNG, de una en una o por lotes.
2. **Quitarles el fondo** con un modelo de inteligencia artificial que se ejecuta dentro del navegador.

Lo que la distingue no es la conversión, que hacen muchas herramientas, sino **dónde ocurre**: todo el procesamiento pasa en tu navegador. Tus imágenes nunca se suben a ningún servidor.

### ¿Para quién es?

| Perfil | Caso de uso |
|---|---|
| Diseñadores y gente de marketing | Pasar carpetas enteras de JPEG a WebP antes de subirlas a una web |
| Cualquier persona con fotos privadas | Quitar un fondo sin entregar la foto a un servicio en la nube |
| Desarrolladores web | Comprimir recursos de un proyecto sin instalar nada |

---

## ⚙️ Qué hace exactamente

### Modo 1 — Convertir Formato

Convierte cada imagen cargada al formato elegido:

| Formato de salida | Nota |
|---|---|
| **WebP** | Opción por defecto. El más comprimido de los tres. |
| **JPEG** | No admite transparencia: las zonas transparentes se rellenan de blanco. |
| **PNG** | Conserva la transparencia. |

La conversión se hace redibujando la imagen en un elemento `<canvas>` a su tamaño original y exportándola con **calidad 0,9**. No hay control de calidad ni de redimensionado en la interfaz: la calidad está fija en el código y **las dimensiones no se modifican**.

### Modo 2 — Quitar Fondo (IA)

Recorta el sujeto y elimina el fondo usando la librería `@imgly/background-removal`, que ejecuta el modelo en el navegador. El resultado **siempre se entrega en PNG**, para conservar la transparencia. La barra de progreso muestra el avance del cálculo de la silueta imagen por imagen.

### Carga de archivos

- Botón **Archivos** para seleccionar imágenes sueltas.
- Botón **Carpeta** para cargar una carpeta completa.
- **Arrastrar y soltar** archivos o carpetas sobre la zona de carga. Las carpetas se recorren de forma recursiva, incluidas las subcarpetas.
- La carga es **acumulativa**: puedes añadir varias tandas antes de procesar.

### Cola de procesamiento

Cada imagen cargada aparece en una lista con miniatura, nombre y tamaño. Desde ahí puedes:

- Eliminar una imagen concreta.
- Vaciar la cola completa.

La cola **detecta duplicados** (mismo nombre y mismo tamaño) y los descarta avisando. Las imágenes de **más de 15 MB** se marcan con la etiqueta *"Pesada - IA lenta"*.

### Salida

- **Una sola imagen** → se descarga directamente el archivo convertido.
- **Varias imágenes** → se procesan en secuencia y se descargan empaquetadas en un único **archivo ZIP**.

---

## 🔒 Privacidad: qué sale y qué no de tu equipo

Esta es la razón de ser del proyecto, así que conviene ser preciso.

**Tus imágenes nunca se envían a ningún servidor.** En todo el código no existe ninguna llamada de subida: los archivos se leen con las APIs del navegador (`File`, `URL.createObjectURL`), se procesan con `<canvas>` y se descargan desde la propia página. No hay backend, ni base de datos, ni analítica, ni almacenamiento de ningún tipo (la página tampoco guarda nada en el navegador entre sesiones).

**Lo que sí se descarga desde internet** son los recursos de la propia página:

| Recurso | Origen | Cuándo |
|---|---|---|
| Tipografías Outfit y Plus Jakarta Sans | Google Fonts | Al abrir la página |
| JSZip 3.10.1 (empaquetado de lotes) | cdnjs | Al abrir la página |
| `@imgly/background-removal` 1.5.5 y su modelo de IA | jsDelivr | Solo la primera vez que usas *Quitar Fondo* |

> 💡 En resumen: la página **necesita internet para cargarse**, pero tus archivos **no viajan**. La descarga es en un solo sentido: del CDN hacia tu navegador.

> ⚠️ Si necesitas una garantía total sin conexión externa, tendrías que servir localmente las tipografías, JSZip y el paquete de eliminación de fondo. Actualmente el proyecto los toma de un CDN.

---

## 🚶 Cómo se usa

### Requisitos

- Un navegador moderno con soporte de `<canvas>` y módulos ES: Chrome, Edge, Firefox o Safari actualizados.
- Conexión a internet para la carga inicial de la página y, la primera vez, para el motor de IA.
- No hay que instalar nada ni crear ninguna cuenta.

### Pasos

1. Abre la página.
2. Elige la pestaña: **Convertir Formato** o **✨ Quitar Fondo (IA)**.
3. Carga imágenes arrastrándolas, o con los botones **Archivos** / **Carpeta**.
4. Revisa la cola y quita lo que no quieras procesar.
5. En modo conversión, elige el formato de salida en el desplegable.
6. Pulsa el botón principal.
7. Al terminar, el navegador descarga el archivo (o el ZIP, si eran varias).

> 💡 Con la primera imagen a la que quites el fondo, verás *"Descargando motor de IA local…"*. Es la descarga del modelo; a partir de ahí el proceso es más rápido.

### Accesibilidad

La interfaz es navegable con teclado: las pestañas se recorren con las flechas izquierda/derecha y se activan con `Enter` o `Espacio`, y la zona de carga se abre igualmente desde el teclado. Los controles llevan etiquetas ARIA y la cola anuncia sus cambios a los lectores de pantalla.

---

## 🗂 Formatos y nombres de archivo

### Entrada

El selector de archivos filtra por **PNG, JPEG y WebP**. Al arrastrar archivos o cargar una carpeta se acepta cualquier tipo `image/*` que el navegador sepa decodificar; lo que no sea una imagen se descarta.

### Salida

| Situación | Nombre del archivo |
|---|---|
| Una imagen, modo conversión | `nombre-convertida.webp` (o `.jpeg` / `.png`) |
| Una imagen, modo quitar fondo | `nombre-sinfondo.png` |
| Lote, modo conversión | `lote-convertido-<marca de tiempo>.zip` |
| Lote, modo quitar fondo | `lote-sinfondo-<marca de tiempo>.zip` |

Dentro del ZIP cada archivo conserva su nombre original con la extensión nueva, y con el sufijo `-sinfondo` cuando corresponde.

---

## 👨‍💻 Desarrollo

### Stack

- **HTML, CSS y JavaScript en un único archivo, sin compilación.** `index.html` contiene la estructura, los estilos y toda la lógica.
- **JSZip 3.10.1** desde CDN, para el empaquetado de lotes.
- **`@imgly/background-removal` 1.5.5**, importado dinámicamente como módulo ES desde CDN, solo cuando se usa el modo de eliminación de fondo.

No hay `package.json`, ni `npm install`, ni bundler, ni paso de build. Es una decisión de diseño: cero dependencias que instalar, cero superficie de mantenimiento y carga inmediata. El precio está anotado en [Limitaciones](#-limitaciones-conocidas).

### Ejecutar en local

Como el modo de IA usa `import()` dinámico, conviene servir el archivo por HTTP en lugar de abrirlo con doble clic:

```bash
python3 -m http.server 8080
```

Y abre `http://localhost:8080`.

### Despliegue

Sube `index.html` a cualquier hosting estático. No hay build, ni variables de entorno, ni configuración de servidor.

> ⚠️ Este repositorio **no incluye CI, tests automatizados ni workflows de despliegue.**

### Dónde tocar el código

Todo está en `index.html`, dentro del bloque `<script>` final:

| Qué quieres cambiar | Dónde |
|---|---|
| Calidad de exportación | `canvas.toDataURL(targetFormat, 0.9)` en `convertSingleFile()` |
| Formatos disponibles | El `<select id="formatSelect">` y la función `convertSingleFile()` |
| Color de relleno para JPEG | `ctx.fillStyle = '#FFFFFF'` en `convertSingleFile()` |
| Umbral de "imagen pesada" | `file.size > 15 * 1024 * 1024` en `processCollectedFiles()` |
| Versión de la librería de IA | La URL del `import()` dentro del manejador del botón principal |
| Nombres de los archivos de salida | Los sufijos en el manejador del botón principal |
| Paleta y estilos | El bloque `<style>` de la cabecera |

---

## 🏗 Arquitectura

Todo el trabajo ocurre en el navegador, en este orden:

```text
Archivos del usuario
  │  (File API / arrastrar y soltar / lectura recursiva de carpetas)
  ▼
Cola en memoria  ──► miniaturas con URLs de objeto
  │
  ├─ modo "quitar fondo": @imgly/background-removal → recorte con transparencia
  │
  ▼
<canvas>  ──► redibujado y exportación al formato elegido (calidad 0,9)
  │
  ├─ 1 archivo  → descarga directa
  └─ N archivos → JSZip → descarga de un ZIP
```

Detalles de implementación relevantes:

- Las miniaturas usan `URL.createObjectURL` y se liberan con `revokeObjectURL` al eliminar el elemento o vaciar la cola, para no acumular memoria.
- El lote se procesa **en secuencia**, no en paralelo, para no saturar la memoria del navegador con imágenes grandes.
- La librería de IA se carga con `import()` **bajo demanda**: quien solo convierta formatos nunca la descarga.
- Durante el procesamiento se deshabilitan los botones de la cola para evitar que cambie mientras se trabaja.

---

## 🗺 Estructura del proyecto

```text
convertidor-imagenes-v1/
├── index.html         # La aplicación completa: HTML, CSS y JavaScript
└── skills-lock.json   # Registro de skills de asistente usadas en el desarrollo (no afecta a la app)
```

---

## ⚠️ Limitaciones conocidas

- **Sin control de calidad ni de tamaño.** La exportación está fija en calidad 0,9 y conserva las dimensiones originales. No hay opción de redimensionar.
- **Sin conversión a otros formatos** como AVIF, GIF o SVG. La salida solo puede ser WebP, JPEG o PNG.
- **Depende de tres CDN.** Sin internet, la página no carga; sin acceso a jsDelivr, el modo de eliminación de fondo no funciona.
- **Todo pasa en memoria.** Lotes muy grandes o imágenes de mucho peso pueden agotar la memoria de la pestaña, especialmente en móviles. Por eso las imágenes de más de 15 MB se marcan.
- **El modo IA es lento** en equipos modestos: el modelo se ejecuta en el propio dispositivo.
- **No hay caché entre sesiones.** El modelo de IA se vuelve a descargar cuando el navegador ya no lo tiene en su propia caché.
- **La transparencia se pierde al convertir a JPEG**, sustituida por blanco. Es una limitación del formato, no del programa.
- **No hay tests automatizados ni CI.**

---

## 🔧 Problemas frecuentes

| Problema | Causa probable | Solución |
|---|---|---|
| "No se detectaron imágenes válidas" | Los archivos no son de tipo `image/*` | Comprueba el contenido de la carpeta que cargaste |
| El botón principal está desactivado | La cola está vacía o hay un proceso en curso | Carga imágenes o espera a que termine el proceso actual |
| "El motor de IA local falló al remover el fondo" | No se pudo descargar el paquete de IA, o la imagen agotó la memoria disponible | Revisa la conexión y que jsDelivr no esté bloqueado; reintenta con menos imágenes o más ligeras |
| El proceso se queda en "Descargando motor de IA local…" | Primera descarga del modelo en una conexión lenta | Espera; solo ocurre en el primer uso de esa pestaña |
| La imagen convertida a JPEG salió con fondo blanco | El JPEG no admite transparencia | Usa PNG o WebP para conservar la transparencia |
| Un archivo cargado no aparece en la cola | Ya estaba en la cola (mismo nombre y tamaño) | Se avisa con un mensaje; es un descarte intencionado |
| El navegador bloquea la descarga del ZIP | Bloqueo de descargas múltiples o automáticas | Permite las descargas de este sitio en el navegador |

---

## ❓ Preguntas frecuentes

**¿Mis imágenes se suben a algún servidor?**
No. Se procesan en tu navegador y se descargan desde la propia página. No existe ningún código de subida en el proyecto.

**Entonces, ¿por qué necesito internet?**
Para cargar la página y, la primera vez que quitas un fondo, para descargar la librería y el modelo de IA. Esa descarga va del CDN hacia tu equipo; tus archivos no viajan en ningún momento.

**¿Hay límite de imágenes por lote?**
No hay un límite programado. El límite real es la memoria del dispositivo: se procesan una tras otra para reducir el riesgo.

**¿Puedo elegir la calidad de compresión?**
No desde la interfaz. Está fija en 0,9 en el código, en `convertSingleFile()`.

**¿Puedo cambiar el tamaño de las imágenes?**
No. La conversión conserva las dimensiones originales.

**¿Funciona en el celular?**
Sí, pero el modo de eliminación de fondo es exigente: con imágenes grandes puede tardar bastante o agotar la memoria.

**¿Guarda algo de lo que proceso?**
No. Al cerrar o recargar la pestaña no queda nada: la cola vive solo en memoria.

---

## 📄 Licencia

Este repositorio **no incluye un archivo de licencia**. El pie de la aplicación indica *"Todos los derechos reservados © 2026"*.

---

<div align="center">

### Diseñado y desarrollado por **Leonardo González**

[![GitHub](https://img.shields.io/badge/GitHub-Leoglez10-181717?style=flat-square&logo=github)](https://github.com/Leoglez10)

</div>
