# Actividades digitales — Generador y plantilla

Herramienta para armar actividades y evaluaciones digitales con autocorrección: un **Generador visual** (HTML que corre en el navegador, sin instalación) produce dos archivos — el HTML de la actividad y el script de Google Apps Script — que juntos funcionan como un examen digital autocorregido con medidas razonables contra copia.

📘 **[Guía paso a paso para docentes](https://recursos-docentes.github.io/plantilla-actividades-digitales/)**

🛠️ **[Abrir el Generador](https://recursos-docentes.github.io/plantilla-actividades-digitales/generador_escritos.html)**

---

## Qué resuelve

Armar una actividad digital "a mano" con Google Forms u otras herramientas similares tiene límites: no se puede forzar pantalla completa, no hay forma simple de registrar si el estudiante intentó salirse, y las respuestas correctas suelen quedar visibles en el código si se arma algo custom. Esta plantilla resuelve los tres problemas:

- **Pantalla completa forzada**, con aviso visible si el estudiante sale de ella antes de terminar (queda registrado en la planilla).
- **Anti-doble-envío**: una vez enviado, el navegador queda marcado y no se puede reabrir la misma actividad ahí. Para habilitar un reintento, la docente ejecuta `localStorage.clear()` en la consola del navegador (F12 → Consola).
- **Corrección 100% del lado del servidor**: la clave de respuestas vive solo en Apps Script. El HTML que recibe el estudiante no la contiene, así que abrir el código fuente o las herramientas de desarrollador (F12) no sirve para verla.

---

## Archivos del repositorio

```
generador_escritos.html        # Generador visual — produce los dos archivos de abajo
index.html # Guía completa para docentes
```

El Generador produce al descargar:

```
[nombre-elegido].html          # La actividad (HTML autocontenido, sin dependencias locales)
Code.gs                        # Script de Apps Script (corrección + guardado en Sheets)
```

El HTML de la actividad **no contiene ninguna respuesta correcta** — todo el criterio de corrección vive en `Code.gs`, que se pega en el proyecto de Apps Script vinculado a la planilla de la docente.

---

## Tipos de pregunta soportados

| Tipo | Qué es | Corrección |
|------|--------|------------|
| `single` | Opción única (radio buttons, una sola correcta) | Automática |
| `multi` | Múltiple respuesta (checkboxes, pueden ser correctas varias opciones) | Automática — todo o nada: el alumno debe marcar exactamente las correctas |
| `tf` | Verdadero / Falso | Automática |
| `open` | Pregunta abierta de desarrollo — el alumno escribe con texto enriquecido y símbolos matemáticos | Manual — queda guardada en la planilla |
| `match` | Emparejar — dos columnas, el estudiante une cada elemento de la izquierda con el correspondiente de la derecha | Automática con crédito parcial |
| `fill` | Banco de palabras — el estudiante completa espacios en blanco eligiendo palabras de un banco | Automática con crédito parcial |

Cada pregunta puede tener una **imagen** opcional (se sube en el Generador y queda embebida en el HTML). Cada pregunta puede marcarse como **opcional** y tiene un puntaje configurable.

---

## Cómo armar una actividad nueva

El flujo completo está documentado paso a paso en la [guía para docentes](https://recursos-docentes.github.io/plantilla-actividades-digitales/). En resumen:

1. Abrir el [Generador](https://recursos-docentes.github.io/plantilla-actividades-digitales/generador_escritos.html).
2. Completar título, subtítulo, tiempo y clave localStorage (única por actividad).
3. Agregar las preguntas desde la interfaz visual, **o pedirle las preguntas a una IA** con el prompt incluido en la guía e importar el JSON directamente en el Generador.
4. Descargar el `Code.gs` → pegarlo en Apps Script → publicarlo como Web App → copiar la URL.
5. Pegar la URL en el Generador → descargar el HTML de la actividad.
6. Publicar el HTML (GitHub Pages u otro hosting estático).

---

## Configurar la planilla de notas

### 1. Crear la planilla
Crear una planilla nueva en Google Sheets (el encabezado lo genera `Code.gs` automáticamente al recibir la primera respuesta).

### 2. Pegar el script
Extensiones → Apps Script → borrar el contenido de `Code.gs` y pegar el generado → guardar.

### 3. Publicar como aplicación web
Implementar → Nueva implementación → Tipo: **Aplicación web**. Configurar:
- **Ejecutar como:** Yo
- **Quién tiene acceso:** Cualquiera

Autorizar cuando lo pida (el aviso de "no verificado" es normal para scripts propios). Copiar la URL que termina en `/exec` — la de la sección *App web*, no la de "Biblioteca".

### 4. Conectar al Generador
Pegar esa URL en el campo **URL del Web App** del Generador antes de descargar el HTML de la actividad.

### Actualizar el script sin romper la URL
Para cambiar `Code.gs` más adelante sin que cambie la URL: Implementar → Administrar implementaciones → lápiz → Versión: **Nueva versión** → Implementar. Crear una implementación nueva desde cero genera una URL distinta y obliga a volver a tocar el HTML.

---

## Decisiones técnicas

Vale la pena documentarlas para no volver a pisar los mismos problemas al adaptar la plantilla:

**Todo el intercambio con Apps Script es por GET con formato JSONP** (un `<script src="...">`, nunca `fetch` con POST). Google Apps Script redirige cada pedido internamente, y en ese salto algunos navegadores convierten un POST en GET y descartan el cuerpo — con GET no hay cuerpo que perder. Además, `fetch` normal contra Apps Script choca con CORS al intentar leer la respuesta; JSONP los evita del todo porque cargar un `<script>` no está sujeto a esa política.

**Un solo intento, con timeout de 15 segundos.** Si no llega respuesta a tiempo, se asume que el envío se disparó correctamente (porque ya se disparó) y se avisa sin mostrar el detalle de corrección, en vez de reintentar en bucle y arriesgarse a mandar la misma respuesta varias veces.

**La pantalla completa no se puede forzar en iOS** (iPhone ni iPad, en ningún navegador, porque Apple no lo permite). En esos dispositivos la actividad funciona con normalidad, pero sin ese modo de protección.

**El anti-doble-envío usa `localStorage`**, que es por navegador y por origen (dominio). Dos actividades distintas publicadas en el mismo sitio no deben compartir la misma clave de `localStorage` — el Generador lo aclara y permite elegir la clave libremente.

**No hay forma de garantizar al 100%** que un estudiante no reabra la actividad (otro navegador, modo incógnito, otro dispositivo evaden la marca). Es una traba razonable, no una prueba con cámara — para eso hace falta software de bloqueo real como Safe Exam Browser, que es una capa aparte y no depende de esta plantilla.

---

## Personalización visual

El HTML generado usa variables CSS (`:root`) para todos los colores. El Generador ofrece varios temas visuales; para agregar uno nuevo, alcanza con definir el juego de colores y pasarlo como opción en el selector de temas.

Temas incluidos actualmente: papel de examen (claro, tipografía serif), editor de código (oscuro, acento ámbar), y otros.

---

Herramienta creada por **Prof. Elizabeth Izquierdo** con asistencia de IA · 2026 · profe.eliza17@gmail.com
