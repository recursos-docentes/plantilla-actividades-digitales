# Generador de Actividades Digitales

Herramienta para crear actividades interactivas con corrección automática vía Google Apps Script. Produce un HTML autocontenido que los estudiantes pueden abrir directamente desde su computadora (sin necesidad de publicarlo en ningún servidor), responden y envían — las respuestas llegan a una planilla de Google Sheets y el estudiante ve su corrección al instante.

📖 **[Guía de uso — Actividades Interactivas](https://recursos-docentes.github.io/plantilla-actividades-digitales/)**

---

## Uso rápido

1. Abrir `generador_actividad.html` en el navegador (doble clic o arrastrar).
2. Completar los 4 pasos del asistente.
3. Distribuir el HTML generado a los estudiantes (por cualquier medio: mail, CREA, Drive, USB, etc.). No requiere publicación en servidor.

---

## Los 4 pasos del asistente

### Paso 1 — Configuración
- **Título**, **materia** e **instrucción inicial** para el estudiante.
- **Tiempo límite** (en minutos; 0 = sin límite).
- **Clave de sesión**: identificador único para esta actividad en `localStorage` (evita que el estudiante reenvíe). Usar una clave distinta para cada actividad, por ejemplo `conjuntos_2026_g1`.

### Paso 2 — Preguntas
Agregar preguntas una a una con los botones de tipo, o importar desde JSON.

**Tipos disponibles:**

| Tipo | Descripción | Corrección automática |
|------|-------------|----------------------|
| Opción única | Múltiple opción, una sola correcta | Sí |
| Múltiple respuesta | Múltiple opción, varias correctas | Sí (todas o nada) |
| V / F | Verdadero o Falso | Sí |
| V / F / Sin Evidencia | Verdadero, Falso o Sin Evidencia | Sí |
| Abierta | Respuesta libre en texto | No (revisar en planilla) |
| Emparejar | Relacionar columna izquierda con derecha | Sí (parcial por par) |
| Banco de palabras | Completar espacios eligiendo de un banco | Sí (parcial por hueco) |

**Barra de símbolos científicos:** hacer clic en un enunciado para activarlo y luego seleccionar el símbolo (superíndices, subíndices, letras griegas, operadores de química/física).

**Importar desde JSON:** el formato es un array de objetos:
```json
[
  {"type":"single","text":"¿Cuánto es 2+2?","opts":["3","4","5","6"],"correct":"b","puntaje":1},
  {"type":"tf","text":"El agua hierve a 100°C a nivel del mar.","correct":"true","puntaje":1},
  {"type":"tfse","text":"Todos los metales son buenos conductores.","correct":"true","puntaje":1},
  {"type":"match","text":"Relacionar","pairs":[{"left":"H₂O","right":"Agua"},{"left":"NaCl","right":"Sal"}],"puntaje":2},
  {"type":"fill","text":"El [hidrógeno] tiene número atómico [1].","extraWords":"oxígeno,carbono","puntaje":2},
  {"type":"open","text":"Explicar el proceso de ósmosis.","puntaje":3}
]
```

### Paso 3 — Planilla
Conectar con Google Sheets vía Apps Script:

1. Descargar el archivo `Code.gs`.
2. Abrir Google Drive y crear una planilla nueva. ⚠ Si se tienen varias cuentas de Google, abrir Drive en una ventana de incógnito (Ctrl+Shift+N).
3. En la planilla: **Extensiones → Apps Script**.
4. Borrar el contenido del editor, pegar el código del `Code.gs` y guardar (Ctrl+S).
5. **Implementar → Nueva implementación** → Tipo: Aplicación web → Ejecutar como: yo → Acceso: Cualquier usuario → Implementar.
6. Copiar la URL de la aplicación web y pegarla en el campo del generador.

> **Arranque en frío:** la primera ejecución del día puede tardar hasta 15 segundos. Abrir la URL de la aplicación web en el navegador 2-3 minutos antes de la clase para pre-calentar el servidor.

### Paso 4 — Descargar
- **Descargar actividad HTML**: el archivo listo para distribuir a los estudiantes.

El HTML funciona abriéndolo directamente desde cualquier computadora (doble clic en el archivo). No necesita estar publicado en un servidor para que el envío a Google Sheets funcione. Formas de distribuirlo:

- **Correo / Drive / CREA**: enviar el archivo directamente.
- **GitHub Pages** (opcional): subir al repositorio y activar Pages en Configuración → Pages. Útil si se quiere un enlace permanente.
- **CREA como recurso web** (opcional): comprimir el HTML en un `.zip` y cargarlo como *Paquete de contenido web* en Recursos.

---

## Columnas en la planilla de respuestas

| Columna | Contenido |
|---------|-----------|
| Timestamp | Fecha y hora de envío |
| Nombre | Nombre ingresado por el estudiante |
| q1, q2, … | Respuesta de cada pregunta |
| Correctas | Cantidad de preguntas correctas |
| Total | Total de preguntas con corrección automática |
| Puntos obtenidos | Puntaje conseguido |
| Puntos totales | Puntaje máximo posible |

---

## Formato de la actividad generada

El HTML que recibe el estudiante incluye:

- **Pantalla de inicio**: título, materia, instrucciones y campo para ingresar nombre.
- **Barra de progreso**: muestra en tiempo real cuántas preguntas fueron respondidas.
- **Preguntas numeradas**: cada pregunta tiene su número (`01`, `02`...) en una columna izquierda con separadores punteados entre preguntas.
- **Pantalla de resultados**: al enviar, el estudiante ve su puntaje desglosado por pregunta (si el GS está conectado) o una confirmación de envío.
- **Pantalla completa obligatoria**: al iniciar se solicita modo pantalla completa; si el estudiante sale, queda registrado en la planilla.

---

## Preguntas frecuentes

**¿El archivo funciona abriéndolo directamente desde la computadora?**  
Sí. El HTML generado puede abrirse con doble clic desde cualquier carpeta o dispositivo. El envío a Google Sheets usa JSONP, que funciona tanto desde `file://` como desde un servidor web. No es necesario publicar el archivo en ningún servidor.

**¿Qué pasa si el estudiante cierra el navegador antes de enviar?**  
Las respuestas no se guardan — deben reempezar. Considerar usar la clave de sesión para marcar actividades ya completadas.

**¿Cómo actualizar las preguntas de una actividad ya publicada?**  
Regenerar el HTML con el generador y reemplazar el archivo en el servidor. Si se agregan preguntas, también hay que re-descargar y re-deployer el `Code.gs`.

**¿Las preguntas abiertas se corrigen automáticamente?**  
No. El texto escrito por el estudiante llega a la planilla y debe revisarse manualmente.
