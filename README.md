# Generador de Actividades Digitales

Herramienta para crear actividades interactivas con corrección automática vía Google Apps Script. Produce un HTML autocontenido que los estudiantes abren en el navegador, responden y envían — las respuestas llegan a una planilla de Google Sheets y el estudiante ve su corrección al instante.

📖 **[Guía de uso — Actividades Interactivas](https://recursos-docentes.github.io/plantilla-actividades-digitales/)**

---

## Uso rápido

1. Abrir `generador_actividad.html` en el navegador (doble clic o arrastrar).
2. Completar los 4 pasos del asistente.
3. Publicar el HTML generado en GitHub Pages o CREA.

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

> **Arranque en frío:** la primera ejecución del día puede tardar hasta 30 segundos. Abrir la URL de la aplicación web en el navegador 2-3 minutos antes de la clase para pre-calentar el servidor.

### Paso 4 — Descargar
- **Descargar actividad HTML**: el archivo listo para publicar.
- **Opción A — GitHub Pages**: subir al repositorio y activar Pages en Configuración → Pages.
- **Opción B — CREA**: comprimir el HTML en un `.zip` y cargarlo como *Paquete de contenido web* en Recursos.

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

## Preguntas frecuentes

**¿El archivo funciona abriéndolo directamente desde la computadora?**  
No. Necesita estar publicado en un servidor web (GitHub Pages, CREA, etc.) para que el envío a Google Sheets funcione.

**¿Qué pasa si el estudiante cierra el navegador antes de enviar?**  
Las respuestas no se guardan — deben reempezar. Considerar usar la clave de sesión para marcar actividades ya completadas.

**¿Cómo actualizar las preguntas de una actividad ya publicada?**  
Regenerar el HTML con el generador y reemplazar el archivo en el servidor. Si se agregan preguntas, también hay que re-descargar y re-deployer el `Code.gs`.

**¿Las preguntas abiertas se corrigen automáticamente?**  
No. El texto escrito por el estudiante llega a la planilla y debe revisarse manualmente.
