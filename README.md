# 📖 DOCUMENTACIÓN TÉCNICA - ANALIZADOR DE BPM

## 📋 ÍNDICE

1. [Información General](#información-general)
2. [Arquitectura del Proyecto](#arquitectura-del-proyecto)
3. [Estructura HTML](#estructura-html)
4. [Estilos CSS](#estilos-css)
5. [Lógica JavaScript](#lógica-javascript)
6. [Algoritmo de Cálculo BPM](#algoritmo-de-cálculo-bpm)
7. [Progressive Web App (PWA)](#progressive-web-app-pwa)
8. [Guía de Mantenimiento](#guía-de-mantenimiento)
9. [Ejercicios de Ampliación](#ejercicios-de-ampliación)
10. [Glosario de Términos](#glosario-de-términos)

---

## 📌 INFORMACIÓN GENERAL

### Descripción del Proyecto
Aplicación web interactiva que permite medir los BPM (Beats Per Minute) de cualquier pieza musical mediante clics del usuario. La aplicación calcula en tiempo real el tempo basándose en los intervalos entre clics.

### Características Principales
- ✅ Cálculo de BPM en tiempo real
- ✅ Interfaz responsive (adaptable a móviles y tablets)
- ✅ Diseño moderno con efectos glassmorphism
- ✅ Estadísticas: clics registrados y promedio de BPM
- ✅ Ventana de tiempo inteligente (5 segundos)
- ✅ Instalable como PWA en Android
- ✅ Animaciones visuales para feedback del usuario
- ✅ Sin dependencias externas (vanilla JavaScript)

### Tecnologías Utilizadas
- **HTML5**: Estructura semántica
- **CSS3**: Estilos modernos (Flexbox, Grid, animaciones)
- **JavaScript ES6+**: Lógica de la aplicación (arrow functions, template literals, etc.)
- **Web APIs**: Service Worker, Local Storage (preparado)

### Compatibilidad
- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+
- ✅ Navegadores móviles (iOS Safari, Chrome Android)

---

## 🏗️ ARQUITECTURA DEL PROYECTO

### Patrón de Diseño
La aplicación sigue el patrón **MVC simplificado**:

- **Modelo (Datos)**: Variables globales (`clicks`, `bpmHistory`)
- **Vista (UI)**: Elementos DOM manipulados dinámicamente
- **Controlador (Lógica)**: Funciones de cálculo y event listeners

### Estructura de Archivos
```
proyecto/
│
├── index.html          # Archivo único que contiene todo
│   ├── <head>         # Metadatos y configuración PWA
│   ├── <style>        # Estilos CSS inline
│   └── <script>       # Lógica JavaScript
│
└── (Opcional para producción)
    ├── manifest.json  # Configuración PWA separada
    ├── sw.js          # Service Worker separado
    ├── styles.css     # Estilos externos
    └── script.js      # JavaScript externo
```

### Flujo de Datos
```
Usuario hace clic
    ↓
Se registra timestamp
    ↓
Se filtra ventana temporal
    ↓
Se calculan intervalos
    ↓
Se calcula promedio
    ↓
Se convierte a BPM
    ↓
Se actualiza interfaz
```

---

## 📄 ESTRUCTURA HTML

### Metadatos Esenciales

#### Charset UTF-8
```html
<meta charset="UTF-8">
```
**Propósito**: Permite usar cualquier carácter Unicode (acentos, emojis, etc.)  
**Importancia**: DEBE ser la primera meta tag  
**Sin esto**: Caracteres especiales se mostrarían incorrectamente (Ã± en lugar de ñ)

#### Viewport
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```
**Propósito**: Hace la web responsive  
**width=device-width**: El ancho de la página = ancho del dispositivo  
**initial-scale=1.0**: Zoom inicial al 100%  
**Sin esto**: En móviles se vería la versión de escritorio diminuta

#### Theme Color
```html
<meta name="theme-color" content="#667eea">
```
**Propósito**: Color de la barra de estado en Android/Chrome  
**Mejora UX**: La aplicación parece más nativa  
**Solo funciona en**: Chrome, Edge, Safari (móviles)

### Elementos Principales

#### Container Principal
```html
<div class="container">
```
**Propósito**: Contenedor centrado con efecto cristal  
**Estilos aplicados**: backdrop-filter, padding, border-radius  
**Alternativa semántica**: `<main>` para el contenido principal

#### Área de Clic
```html
<div class="click-area" id="clickArea">
```
**Propósito**: Zona interactiva donde el usuario hace clic  
**Por qué DIV y no BUTTON**: Necesitamos un área grande y personalizable  
**ID único**: Para selección desde JavaScript

#### Display de BPM
```html
<div class="bpm-display" id="bpmDisplay">-- BPM</div>
```
**Propósito**: Muestra el BPM calculado en grande  
**Contenido inicial**: "-- BPM" como placeholder  
**JavaScript**: Cambia el contenido dinámicamente

#### Estadísticas
```html
<div class="stats">
  <div class="stat-box">
    <div class="stat-label">Clics registrados</div>
    <div class="stat-value" id="clickCount">0</div>
  </div>
  <div class="stat-box">
    <div class="stat-label">Promedio BPM</div>
    <div class="stat-value" id="avgBpm">--</div>
  </div>
</div>
```
**Estructura**: Grid de 2 columnas (responsive a 1 en móvil)  
**IDs importantes**: `clickCount`, `avgBpm` (actualizados por JS)

#### Botón de Reinicio
```html
<button class="reset-btn" id="resetBtn">Reiniciar</button>
```
**Por qué BUTTON**: Semántica correcta, accesibilidad, eventos nativos  
**Nunca usar**: DIV o A para botones (mala práctica)

---

## 🎨 ESTILOS CSS

### Reset CSS
```css
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}
```
**Propósito**: Eliminar estilos predeterminados del navegador  
**box-sizing**: Hace que width/height incluyan padding y border  
**Por qué**: Facilita cálculos de tamaño

### Diseño del Body
```css
body {
    font-family: 'Arial', sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}
```

#### Gradiente de Fondo
- **135deg**: Diagonal de esquina superior izquierda a inferior derecha
- **#667eea**: Azul violeta (inicio)
- **#764ba2**: Morado oscuro (fin)
- **Herramienta recomendada**: https://cssgradient.io/

#### Centrado con Flexbox
- **display: flex**: Activa Flexbox
- **justify-content: center**: Centra horizontalmente
- **align-items: center**: Centra verticalmente
- **Resultado**: Contenido perfectamente centrado en pantalla

#### Unidad vh (Viewport Height)
- **100vh**: 100% de la altura de la ventana
- **Ventaja**: Se adapta automáticamente al tamaño de pantalla
- **Alternativas**: %, px (menos flexibles)

### Efecto Glassmorphism
```css
.container {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    border-radius: 20px;
    box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
}
```

#### RGBA y Transparencia
- **rgba(R, G, B, Alpha)**: Color con transparencia
- **Alpha 0.1**: 10% de opacidad (muy transparente)
- **Efecto**: Fondo blanco semi-transparente

#### Backdrop Filter
- **blur(10px)**: Desenfoca lo que está detrás del elemento
- **Efecto moderno**: Vidrio esmerilado (macOS, Windows 11)
- **Compatibilidad**: No funciona en Firefox < 103

#### Box Shadow
- **Sintaxis**: offset-x offset-y blur spread color
- **0 8px 32px**: Sin desplazamiento horizontal, 8px abajo, 32px de difuminado
- **rgba(0,0,0,0.3)**: Negro con 30% de opacidad
- **Efecto**: Elevación y profundidad

### Responsive Design

#### Breakpoints Usados
```css
@media (max-width: 768px)  /* Tablets y móviles grandes */
@media (max-width: 480px)  /* Móviles pequeños */
```

#### Estrategia Mobile-First vs Desktop-First
**Este proyecto usa Desktop-First**:
- Estilos base para desktop
- Media queries para reducir en móviles

**Alternativa Mobile-First**:
- Estilos base para móvil
- Media queries para aumentar en desktop

#### Cambios en Móvil
```css
@media (max-width: 768px) {
    .container { padding: 30px 20px; }  /* Menos padding */
    .stats { grid-template-columns: 1fr; }  /* 1 columna en lugar de 2 */
}
```

### Sistema de Grid para Estadísticas
```css
.stats {
    display: grid;
    grid-template-columns: 1fr 1fr;  /* 2 columnas iguales */
    gap: 20px;
}
```

#### Unidad FR (Fraction)
- **1fr**: Una fracción del espacio disponible
- **1fr 1fr**: Divide el espacio en 2 partes iguales (50% cada una)
- **2fr 1fr**: Primera columna es el doble que la segunda (66% y 33%)

#### Gap vs Margin
- **gap**: Espacio entre elementos del grid
- **Ventaja**: No necesitas margin negativo para compensar
- **Antes de gap**: Se usaba margin en cada elemento

### Transiciones y Animaciones

#### Transiciones CSS
```css
.click-area {
    transition: all 0.3s ease;
}
.click-area:hover {
    transform: scale(1.02);
}
```
- **transition**: Anima cambios de propiedades
- **all**: Anima todas las propiedades que cambien
- **0.3s**: Duración de 300 milisegundos
- **ease**: Curva de animación (lento-rápido-lento)

#### Curvas de Animación
- **linear**: Velocidad constante
- **ease**: Lento al inicio y final (predeterminado)
- **ease-in**: Empieza lento
- **ease-out**: Termina lento
- **ease-in-out**: Lento al inicio y final
- **cubic-bezier()**: Personalizada

#### Transformaciones 2D
```css
transform: scale(1.02);      /* Escala al 102% */
transform: rotate(45deg);    /* Rota 45 grados */
transform: translateX(10px); /* Mueve 10px a la derecha */
transform: skew(10deg);      /* Inclina */
```

#### Keyframes
```css
@keyframes pulse {
    0%, 100% { transform: scale(1); }
    50% { transform: scale(1.05); }
}
```
- **0%**: Estado inicial
- **50%**: Estado intermedio (máximo)
- **100%**: Estado final
- **Efecto**: Crecimiento y reducción (latido)

### Optimizaciones Táctiles (Mobile)
```css
.click-area {
    -webkit-tap-highlight-color: transparent;
    touch-action: manipulation;
    user-select: none;
}
```

#### Webkit Tap Highlight
- **Problema**: Flash azul molesto al tocar en iOS
- **Solución**: transparent (sin resaltado)

#### Touch Action
- **manipulation**: Permite tocar y zoom, pero deshabilita doble-tap
- **Beneficio**: Respuesta más rápida (no espera segundo tap)

#### User Select
- **none**: Evita selección de texto
- **Útil**: En elementos interactivos donde no queremos selección

---

## 💻 LÓGICA JAVASCRIPT

### Variables Globales

#### Estado de la Aplicación
```javascript
let clicks = [];          // Array de timestamps de clics
let bpmHistory = [];      // Historial de BPM calculados
const maxClicks = 10;     // Máximo de clics en memoria
const timeWindow = 5000;  // Ventana temporal (5 segundos)
```

#### Tipos de Declaración
- **const**: No se puede reasignar (usar por defecto)
- **let**: Se puede reasignar (variables que cambian)
- **var**: Antiguo, evitar (problemas de scope)

**Regla de oro**: Usa `const` siempre que puedas, `let` solo cuando necesites cambiar el valor.

### Selección de Elementos DOM

```javascript
const clickArea = document.getElementById('clickArea');
const bpmDisplay = document.getElementById('bpmDisplay');
const clickCount = document.getElementById('clickCount');
const avgBpm = document.getElementById('avgBpm');
const status = document.getElementById('status');
const resetBtn = document.getElementById('resetBtn');
```

#### Document Object Model (DOM)
- **document**: Representa toda la página HTML
- **getElementById()**: Busca elemento por ID
- **Retorna**: El elemento o null si no existe

#### Métodos de Selección Alternativos
```javascript
document.querySelector('#id')           // Por ID
document.querySelector('.clase')        // Primera clase
document.querySelectorAll('.clase')     // Todas las clases
document.getElementsByClassName('clase') // Por clase (antiguo)
document.getElementsByTagName('div')    // Por etiqueta (antiguo)
```

**Recomendación**: `querySelector` y `querySelectorAll` son más versátiles.

### Función calculateBPM()

#### Propósito
Calcula los BPM basándose en los intervalos de tiempo entre clics.

#### Algoritmo Paso a Paso

**1. Obtener timestamp actual**
```javascript
const now = Date.now();
```
- **Date.now()**: Milisegundos desde 1 enero 1970 (Unix Epoch)
- **Ejemplo**: 1709664000000
- **Ventaja**: Perfecto para medir intervalos

**2. Filtrar clics antiguos**
```javascript
clicks = clicks.filter(time => now - time < timeWindow);
```
- **filter()**: Crea nuevo array con elementos que cumplen condición
- **Condición**: (now - time) < 5000
- **Resultado**: Solo clics de últimos 5 segundos
- **Razón**: Medir tempo actual, no el de hace minutos

**3. Validar datos suficientes**
```javascript
if (clicks.length < 2) {
    return null;
}
```
- **Necesitamos**: Al menos 2 clics para calcular intervalo
- **null**: Indica que no se puede calcular aún

**4. Calcular intervalos**
```javascript
const intervals = [];
for (let i = 1; i < clicks.length; i++) {
    intervals.push(clicks[i] - clicks[i - 1]);
}
```
- **Loop desde i=1**: Para comparar con i-1
- **clicks[i] - clicks[i-1]**: Tiempo entre clics consecutivos
- **Ejemplo**: clicks = [1000, 1500, 2000] → intervals = [500, 500]

**5. Calcular promedio de intervalos**
```javascript
const avgInterval = intervals.reduce((a, b) => a + b, 0) / intervals.length;
```
- **reduce()**: Reduce array a un valor único
- **(a, b) => a + b**: Suma todos los elementos
- **0**: Valor inicial del acumulador
- **División**: Obtener el promedio

**6. Convertir a BPM**
```javascript
const bpm = Math.round(60000 / avgInterval);
return bpm;
```
- **60000 ms**: Un minuto en milisegundos
- **Fórmula**: BPM = 60000 / intervalo_promedio
- **Ejemplo**: 60000 / 500 = 120 BPM
- **Math.round()**: Redondear al entero más cercano

#### Ejemplo Completo de Cálculo
```
Clics: [1000, 1500, 2000, 2500]
Intervalos: [500, 500, 500]
Promedio: 500 ms
BPM: 60000 / 500 = 120 BPM
```

### Función updateDisplay()

#### Propósito
Actualiza todos los elementos visuales con los datos calculados.

#### Separación de Responsabilidades
- **calculateBPM()**: Solo calcula (lógica pura)
- **updateDisplay()**: Solo muestra (presentación)
- **Ventaja**: Código organizado, fácil de mantener y testear

#### Proceso de Actualización

**1. Calcular BPM**
```javascript
const bpm = calculateBPM();
```

**2. Actualizar contador de clics**
```javascript
clickCount.textContent = clicks.length;
```
- **textContent**: Establece/lee contenido de texto
- **Más seguro que innerHTML**: No interpreta HTML

**3. Validar y mostrar BPM**
```javascript
if (bpm !== null && bpm > 0 && bpm < 300) {
    bpmDisplay.textContent = `${bpm} BPM`;
    // ... más lógica
}
```
- **Validaciones**:
  - `bpm !== null`: Hay valor calculado
  - `bpm > 0`: Valor positivo
  - `bpm < 300`: Valor realista (música normal: 60-180 BPM)

**4. Gestionar historial**
```javascript
bpmHistory.push(bpm);
if (bpmHistory.length > 20) {
    bpmHistory.shift();
}
```
- **push()**: Añade al final
- **shift()**: Elimina del inicio
- **Resultado**: Mantiene máximo 20 valores

**5. Calcular y mostrar promedio**
```javascript
const avg = Math.round(bpmHistory.reduce((a, b) => a + b, 0) / bpmHistory.length);
avgBpm.textContent = avg;
```
- **Promedio**: Suaviza variaciones
- **Ventaja**: Medición más estable

### Event Listeners

#### Clic en Área
```javascript
clickArea.addEventListener('click', () => {
    const now = Date.now();
    clicks.push(now);
    
    if (clicks.length > maxClicks) {
        clicks.shift();
    }
    
    updateDisplay();
    
    // Reiniciar animación
    bpmDisplay.style.animation = 'none';
    setTimeout(() => {
        bpmDisplay.style.animation = 'pulse 0.5s ease-in-out';
    }, 10);
});
```

#### Proceso del Evento
1. Usuario hace clic
2. Se registra timestamp
3. Se limita array a maxClicks
4. Se actualiza display
5. Se reinicia animación visual

#### Reinicio de Animación
**Problema**: La animación solo se ejecuta una vez.  
**Solución**:
1. Desactivar: `animation = 'none'`
2. Esperar 10ms: `setTimeout(..., 10)`
3. Reactivar: `animation = 'pulse ...'`

**Por qué 10ms**: El navegador necesita tiempo para "registrar" el cambio.

#### Botón de Reinicio
```javascript
resetBtn.addEventListener('click', () => {
    clicks = [];
    bpmHistory = [];
    bpmDisplay.textContent = '-- BPM';
    clickCount.textContent = '0';
    avgBpm.textContent = '--';
    status.textContent = 'Esperando clics...';
});
```

**Operaciones**:
- Vaciar arrays de datos
- Restaurar valores iniciales en UI
- Devolver aplicación a estado limpio

---

## 🧮 ALGORITMO DE CÁLCULO BPM

### Fórmula Matemática

```
BPM = 60000 / intervalo_promedio_en_milisegundos
```

### Ejemplos Prácticos

#### Ejemplo 1: Tempo Moderado
```
Clics cada 500 ms
BPM = 60000 / 500 = 120 BPM
(Tempo típico de pop/rock)
```

#### Ejemplo 2: Tempo Rápido
```
Clics cada 300 ms
BPM = 60000 / 300 = 200 BPM
(Drum & Bass, música electrónica rápida)
```

#### Ejemplo 3: Tempo Lento
```
Clics cada 1000 ms (1 segundo)
BPM = 60000 / 1000 = 60 BPM
(Balada lenta)
```

### Precisión del Algoritmo

#### Factores que Afectan la Precisión

**1. Número de Clics**
- **< 4 clics**: Baja precisión
- **4-8 clics**: Precisión media
- **> 8 clics**: Alta precisión

**2. Consistencia del Usuario**
- **Humano perfecto**: Imposible
- **Variación ±5%**: Normal y aceptable
- **Promedio suaviza**: Compensa irregularidades

**3. Ventana Temporal**
- **5 segundos**: Balance entre precisión y respuesta
- **Más corta**: Responde rápido pero menos preciso
- **Más larga**: Más preciso pero lento para cambios

### Manejo de Casos Extremos

#### BPM Muy Rápidos (> 300)
```javascript
if (bpm > 300) {
    // No se muestra, probablemente error
}
```
**Razón**: Speedcore extremo llega a ~300 BPM. Más allá suelen ser clics erróneos.

#### BPM Muy Lentos (< 40)
```javascript
if (bpm < 40) {
    // No se muestra, probablemente inactividad
}
```
**Razón**: Música más lenta suele estar en 60-80 BPM.

#### Sin Clics Recientes
```javascript
if (clicks.length < 2) {
    return null;  // No hay suficientes datos
}
```

### Comparación con Métodos Alternativos

#### Método Actual: Promedio de Intervalos
✅ Simple de implementar  
✅ Robusto ante variaciones  
✅ Respuesta rápida  
❌ Puede tener pequeñas oscilaciones

#### Alternativa 1: Primero y Último Clic
```javascript
// NO IMPLEMENTADO
const totalTime = clicks[clicks.length-1] - clicks[0];
const bpm = Math.round(60000 * (clicks.length-1) / totalTime);
```
✅ Más suave  
❌ Menos responsive a cambios

#### Alternativa 2: Regresión Lineal
```javascript
// NO IMPLEMENTADO - Más complejo
```
✅ Muy preciso  
❌ Computacionalmente costoso  
❌ Overkill para esta aplicación

---

## 📱 PROGRESSIVE WEB APP (PWA)

### ¿Qué es una PWA?

Una PWA es una aplicación web que:
- Se puede instalar en el dispositivo
- Funciona offline
- Se parece a una app nativa
- Recibe notificaciones push (opcional)

### Componentes de la PWA

#### 1. Manifest.json

**Contenido (decodificado)**:
```json
{
  "name": "Analizador de BPM",
  "short_name": "BPM",
  "description": "Mide los BPM de cualquier canción con solo hacer clic al ritmo",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#667eea",
  "theme_color": "#667eea",
  "orientation": "portrait",
  "icons": [...]
}
```

**Propiedades Explicadas**:
- **name**: Nombre completo (se muestra al instalar)
- **short_name**: Nombre corto (debajo del icono)
- **display: standalone**: Sin barra del navegador
- **background_color**: Color al abrir la app
- **theme_color**: Color de la barra de estado
- **orientation**: portrait (vertical), landscape (horizontal)

#### 2. Service Worker

**Código Actual**:
```javascript
const swCode = `
    self.addEventListener('install', (e) => {
        console.log('SW instalado');
        self.skipWaiting();
    });
    
    self.addEventListener('activate', (e) => {
        console.log('SW activado');
        e.waitUntil(clients.claim());
    });
    
    self.addEventListener('fetch', (e) => {
        e.respondWith(fetch(e.request).catch(() => e.request));
    });
`;
```

**Eventos del Service Worker**:

1. **install**: Primera instalación
   - `skipWaiting()`: Activa inmediatamente sin esperar

2. **activate**: Activación del service worker
   - `clients.claim()`: Toma control de todas las páginas

3. **fetch**: Intercepta peticiones de red
   - Permite funcionar offline (con caché)
   - Actualmente: Pasa peticiones directamente

### Limitaciones Actuales

#### Requiere HTTPS
```javascript
if ('serviceWorker' in navigator && window.location.protocol === 'https:')
```
**Por qué**: Seguridad. Service Workers pueden interceptar tráfico.  
**Excepción**: localhost (para desarrollo)

#### Manifest Embebido
```html
<link rel="manifest" href="data:application/json;base64,...">
```
**Actual**: Manifest en Base64 inline  
**Mejor práctica**: Archivo manifest.json separado

### Instalación en Android

#### Pasos para el Usuario
1. Abrir en Chrome Android
2. Aparece banner "Instalar app"
3. O menú (⋮) → "Instalar aplicación"
4. Confirmar instalación
5. Icono aparece en launcher

#### Experiencia Post-Instalación
- ✅ Pantalla completa (sin barra del navegador)
- ✅ Icono independiente
- ✅ En lista de apps
- ✅ Transiciones entre páginas más suaves
- ❌ No funciona offline (service worker básico)

### Mejoras Recomendadas para PWA

#### 1. Caché de Recursos
```javascript
// En install event
const CACHE_NAME = 'bpm-v1';
const urlsToCache = ['/'];

self.addEventListener('install', (e) => {
    e.waitUntil(
        caches.open(CACHE_NAME)
            .then(cache => cache.addAll(urlsToCache))
    );
});
```

#### 2. Estrategia Cache-First
```javascript
self.addEventListener('fetch', (e) => {
    e.respondWith(
        caches.match(e.request)
            .then(response => response || fetch(e.request))
    );
});
```

#### 3. Manifest Separado
Crear `manifest.json` en lugar de Base64:
```html
<link rel="manifest" href="/manifest.json">
```

---

## 🔧 GUÍA DE MANTENIMIENTO

### Estructura del Código

#### Organización Actual
```
1. Metadatos HTML (líneas 1-50)
2. Estilos CSS (líneas 51-350)
3. Estructura HTML (líneas 351-400)
4. JavaScript (líneas 401-fin)
```

#### Ventajas del Archivo Único
- ✅ Fácil de compartir (un solo archivo)
- ✅ Sin dependencias externas
- ✅ Carga rápida (una sola petición HTTP)

#### Desventajas del Archivo Único
- ❌ Difícil de mantener en proyectos grandes
- ❌ Sin caché selectivo (todo o nada)
- ❌ Dificulta trabajo en equipo

### Migración a Archivos Separados

#### Paso 1: Extraer CSS
```html
<!-- En index.html -->
<link rel="stylesheet" href="styles.css">
```

```css
/* En styles.css */
* { margin: 0; padding: 0; }
/* ... resto del CSS */
```

#### Paso 2: Extraer JavaScript
```html
<!-- Al final del body -->
<script src="script.js"></script>
```

```javascript
// En script.js
let clicks = [];
// ... resto del código
```

#### Paso 3: Manifest Externo
```html
<link rel="manifest" href="manifest.json">
```

```json
// En manifest.json
{
  "name": "Analizador de BPM",
  ...
}
```

### Modificaciones Comunes

#### Cambiar Colores del Tema
**Ubicación**: CSS, líneas ~80-90
```css
background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
```
**Modificar**: Los códigos de color hexadecimales
**Herramienta**: https://coolors.co/

#### Ajustar Ventana Temporal
**Ubicación**: JavaScript, línea ~420
```javascript
const timeWindow = 5000;  // Cambiar valor en milisegundos
```
**Valores sugeridos**:
- 3000 (3s): Más responsive
- 10000 (10s): Más estable

#### Cambiar Límite de BPM
**Ubicación**: JavaScript, función updateDisplay()
```javascript
if (bpm !== null && bpm > 0 && bpm < 300) {
```
**Modificar**: El valor 300 según necesidades

#### Modificar Tamaño de Fuentes
**Ubicación**: CSS
```css
h1 { font-size: 2.5em; }  /* Cambiar valor */
.bpm-display { font-size: 4em; }  /* Cambiar valor */
```

### Debugging y Testing

#### Consola del Navegador
**Abrir**: F12 o Ctrl+Shift+I
**Pestaña Console**: Ver mensajes de log
**Pestaña Elements**: Inspeccionar HTML/CSS
**Pestaña Network**: Ver peticiones HTTP

#### Puntos de Verificación Comunes

**1. BPM no se calcula**
- ¿Hay al menos 2 clics?
- ¿Los clics están dentro de la ventana temporal?
- ¿Hay errores en consola?

**2. Animación no funciona**
- ¿El CSS está correctamente aplicado?
- ¿setTimeout se ejecuta?
- ¿Hay errores de JavaScript?

**3. No se puede instalar como PWA**
- ¿La página está en HTTPS?
- ¿El manifest está correctamente formado?
- ¿El service worker se registró?

#### Añadir Logs de Debug
```javascript
function calculateBPM() {
    console.log('Calculando BPM...');
    console.log('Clics actuales:', clicks);
    const bpm = // ... cálculo
    console.log('BPM calculado:', bpm);
    return bpm;
}
```

### Performance

#### Optimizaciones Actuales
- ✅ Selección de elementos una sola vez (no en cada evento)
- ✅ Uso de `const` para valores inmutables
- ✅ Animaciones CSS (más eficientes que JavaScript)
- ✅ Event listeners específicos (no globales)

#### Medición de Performance
```javascript
console.time('calculateBPM');
const bpm = calculateBPM();
console.timeEnd('calculateBPM');  // Muestra tiempo en ms
```

#### Posibles Cuellos de Botella
- ❌ Ninguno crítico en esta aplicación
- ⚠️ Si bpmHistory crece mucho (limitado a 20)
- ⚠️ Múltiples animaciones simultáneas (no es el caso)

---

## 🎓 EJERCICIOS DE AMPLIACIÓN

### Nivel Básico (1-10)

#### Ejercicio 1: Cambiar el Gradiente
**Objetivo**: Modificar los colores del fondo  
**Ubicación**: CSS, body background  
**Dificultad**: ⭐☆☆☆☆  
**Pista**: Usa https://cssgradient.io/

#### Ejercicio 2: Añadir un Logo
**Objetivo**: Insertar imagen de logo en el header  
**Elementos**: `<img src="logo.png">`, CSS para tamaño  
**Dificultad**: ⭐☆☆☆☆

#### Ejercicio 3: Cambiar la Fuente
**Objetivo**: Usar fuente de Google Fonts  
**Pasos**:
1. Ir a fonts.google.com
2. Elegir fuente
3. Copiar link en `<head>`
4. Aplicar en CSS
**Dificultad**: ⭐☆☆☆☆

#### Ejercicio 4: Mostrar Timestamp del Último Clic
**Objetivo**: Añadir display que muestre cuándo fue el último clic  
**Nuevo elemento HTML**: `<div id="lastClick">--</div>`  
**JavaScript**: `lastClick.textContent = new Date(now).toLocaleTimeString();`  
**Dificultad**: ⭐⭐☆☆☆

#### Ejercicio 5: Botón para Cambiar Ventana Temporal
**Objetivo**: Permitir elegir entre 3s, 5s, 10s  
**HTML**: Botones o select  
**JavaScript**: Modificar `timeWindow`  
**Dificultad**: ⭐⭐☆☆☆

#### Ejercicio 6: Indicador Visual de Clics Recientes
**Objetivo**: Mostrar círculos que representen cada clic  
**HTML**: Contenedor con divs dinámicos  
**JavaScript**: Crear/eliminar elementos  
**Dificultad**: ⭐⭐☆☆☆

#### Ejercicio 7: Sonido al Hacer Clic
**Objetivo**: Reproducir un "beep" en cada clic  
**JavaScript**: Web Audio API o `<audio>`  
**Código**:
```javascript
const audio = new Audio('beep.mp3');
audio.play();
```
**Dificultad**: ⭐⭐☆☆☆

#### Ejercicio 8: Cambiar Animación de Pulso
**Objetivo**: Probar diferentes efectos (rotación, desvanecimiento)  
**CSS**: Modificar @keyframes  
**Dificultad**: ⭐⭐☆☆☆

#### Ejercicio 9: Modo Oscuro/Claro
**Objetivo**: Botón para alternar entre temas  
**JavaScript**: Añadir/quitar clase CSS  
**CSS**: Variables CSS para colores  
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 10: Validación de BPM
**Objetivo**: Mostrar rango (Lento/Normal/Rápido) según BPM  
**JavaScript**:
```javascript
if (bpm < 80) return 'Lento';
else if (bpm < 120) return 'Normal';
else return 'Rápido';
```
**Dificultad**: ⭐⭐☆☆☆

### Nivel Intermedio (11-20)

#### Ejercicio 11: LocalStorage
**Objetivo**: Guardar datos al cerrar y restaurar al abrir  
**JavaScript**:
```javascript
localStorage.setItem('bpmData', JSON.stringify(clicks));
const saved = JSON.parse(localStorage.getItem('bpmData'));
```
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 12: Historial de Sesiones
**Objetivo**: Guardar todas las mediciones con fecha/hora  
**Estructura**:
```javascript
const history = [
    { date: '2024-03-18', bpm: 120, clicks: 8 },
    ...
];
```
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 13: Gráfico de BPM
**Objetivo**: Mostrar gráfico de línea con historial  
**Librería**: Chart.js o Canvas API  
**Dificultad**: ⭐⭐⭐⭐☆

#### Ejercicio 14: Soporte de Teclado
**Objetivo**: Permitir usar barra espaciadora  
**JavaScript**:
```javascript
document.addEventListener('keydown', (e) => {
    if (e.key === ' ') {
        // código del clic
    }
});
```
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 15: Exportar Datos
**Objetivo**: Botón para descargar datos como JSON  
**JavaScript**:
```javascript
const data = JSON.stringify({ clicks, bpmHistory });
const blob = new Blob([data], { type: 'application/json' });
const url = URL.createObjectURL(blob);
// Crear link de descarga
```
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 16: Importar Datos
**Objetivo**: Subir archivo JSON para cargar sesión  
**HTML**: `<input type="file">`  
**JavaScript**: FileReader API  
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 17: Detección de Subdivisión
**Objetivo**: Detectar si usuario marca negras, corcheas o semicorcheas  
**Algoritmo**: Analizar si BPM es múltiplo de 2 o 4  
**Dificultad**: ⭐⭐⭐⭐☆

#### Ejercicio 18: Metrónomo Integrado
**Objetivo**: Reproducir clicks al ritmo calculado  
**JavaScript**: Web Audio API + setInterval  
**Dificultad**: ⭐⭐⭐⭐☆

#### Ejercicio 19: Estadísticas Avanzadas
**Objetivo**: Calcular desviación estándar, varianza, etc.  
**JavaScript**: Fórmulas estadísticas  
**Dificultad**: ⭐⭐⭐⭐☆

#### Ejercicio 20: Drag & Drop de Archivos
**Objetivo**: Arrastrar archivo de audio para analizar  
**APIs**: Drag & Drop API + Web Audio API  
**Dificultad**: ⭐⭐⭐⭐☆

### Nivel Avanzado (21-30)

#### Ejercicio 21: Análisis de Audio Real
**Objetivo**: Detectar BPM de archivo MP3 automáticamente  
**Librería**: Web Audio API  
**Algoritmo**: FFT, detección de picos  
**Dificultad**: ⭐⭐⭐⭐⭐

#### Ejercicio 22: Integración con Spotify API
**Objetivo**: Buscar canciones con BPM similar  
**API**: Spotify Web API  
**Requisitos**: OAuth, API key  
**Dificultad**: ⭐⭐⭐⭐⭐

#### Ejercicio 23: Modo Multijugador
**Objetivo**: Competir con amigos en precisión  
**Backend**: WebSockets o Firebase  
**Dificultad**: ⭐⭐⭐⭐⭐

#### Ejercicio 24: Machine Learning
**Objetivo**: Entrenar modelo para mejorar detección  
**Librería**: TensorFlow.js  
**Dificultad**: ⭐⭐⭐⭐⭐

#### Ejercicio 25: Modo de Juego
**Objetivo**: Juego donde debes mantener BPM target  
**Elementos**: Sistema de puntuación, niveles, vidas  
**Dificultad**: ⭐⭐⭐⭐☆

#### Ejercicio 26: Share API
**Objetivo**: Compartir resultado en redes sociales  
**JavaScript**:
```javascript
navigator.share({
    title: 'Mi BPM',
    text: `Medí ${bpm} BPM`,
    url: window.location.href
});
```
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 27: Base de Datos de Canciones
**Objetivo**: Comparar con canciones famosas  
**Estructura**:
```javascript
const songs = {
    "Stayin' Alive": 104,
    "Billie Jean": 117,
    "Uptown Funk": 115
};
```
**Dificultad**: ⭐⭐⭐☆☆

#### Ejercicio 28: PWA Completa Offline
**Objetivo**: Funcionar 100% sin internet  
**Service Worker**: Cache completo  
**Dificultad**: ⭐⭐⭐⭐☆

#### Ejercicio 29: Notificaciones Push
**Objetivo**: Recordatorios para practicar ritmo  
**API**: Push API + Service Worker  
**Dificultad**: ⭐⭐⭐⭐⭐

#### Ejercicio 30: Sincronización de Fondo
**Objetivo**: Sincronizar datos cuando vuelva conexión  
**API**: Background Sync API  
**Dificultad**: ⭐⭐⭐⭐⭐

---

## 📚 GLOSARIO DE TÉRMINOS

### HTML

**DOCTYPE**: Declaración del tipo de documento  
**Element**: Etiqueta HTML completa (apertura, contenido, cierre)  
**Attribute**: Propiedad de una etiqueta (id, class, src, etc.)  
**Semantic HTML**: HTML que describe el significado del contenido  
**Meta Tag**: Etiqueta de metadatos en el `<head>`  
**Viewport**: Área visible de la página web

### CSS

**Selector**: Patrón que identifica qué elementos estilizar  
**Property**: Característica a modificar (color, font-size, etc.)  
**Value**: Valor asignado a una propiedad  
**Pseudo-class**: Estado especial de un elemento (:hover, :active)  
**Media Query**: Regla condicional según características del dispositivo  
**Flexbox**: Sistema de diseño flexible uni-dimensional  
**Grid**: Sistema de diseño bi-dimensional  
**Box Model**: Modelo de caja (content, padding, border, margin)  
**z-index**: Orden de apilamiento de elementos  
**Specificity**: Nivel de prioridad de un selector CSS  
**Cascade**: Orden en que se aplican los estilos  
**Inheritance**: Propiedades que se heredan a elementos hijos  
**Responsive**: Diseño que se adapta a diferentes tamaños

### JavaScript

**Variable**: Contenedor para almacenar datos  
**Constant**: Variable cuyo valor no cambia  
**Function**: Bloque de código reutilizable  
**Array**: Lista ordenada de elementos  
**Object**: Colección de pares clave-valor  
**Event**: Acción que ocurre en la página (clic, tecla, etc.)  
**Event Listener**: Función que escucha eventos  
**Callback**: Función pasada como argumento a otra función  
**Promise**: Objeto que representa una operación asíncrona  
**Async/Await**: Sintaxis para trabajar con promesas  
**DOM**: Document Object Model (representación del HTML)  
**API**: Application Programming Interface  
**JSON**: JavaScript Object Notation (formato de datos)  
**Scope**: Alcance/visibilidad de una variable  
**Closure**: Función que accede a variables de su scope exterior  
**Arrow Function**: Sintaxis corta de función (=>)  
**Template Literal**: String con interpolación (`${var}`)  
**Destructuring**: Extracción de valores de arrays/objetos  
**Spread Operator**: Expansión de arrays/objetos (...)  
**Rest Parameters**: Parámetros variables en funciones  
**Higher-Order Function**: Función que recibe/devuelve funciones

### Desarrollo Web

**PWA**: Progressive Web App (app web instalable)  
**Service Worker**: Script que corre en segundo plano  
**Manifest**: Archivo de configuración de PWA  
**Cache**: Almacenamiento temporal de recursos  
**LocalStorage**: Almacenamiento persistente en navegador  
**SessionStorage**: Almacenamiento temporal de sesión  
**HTTP/HTTPS**: Protocolos de transferencia web  
**API Key**: Clave de autenticación para APIs  
**OAuth**: Protocolo de autorización  
**CORS**: Cross-Origin Resource Sharing  
**SPA**: Single Page Application  
**SSR**: Server-Side Rendering  
**CDN**: Content Delivery Network  
**Minification**: Reducción de tamaño de código  
**Bundle**: Agrupación de archivos en uno  
**Transpiling**: Conversión de código moderno a compatible

### Música

**BPM**: Beats Per Minute (pulsaciones por minuto)  
**Tempo**: Velocidad de una pieza musical  
**Beat**: Pulso básico de la música  
**Downbeat**: Primer tiempo del compás  
**Subdivision**: División del beat (corcheas, semicorcheas)  
**Time Signature**: Métrica musical (4/4, 3/4, etc.)  
**Rubato**: Tempo flexible expresivo  
**Metronome**: Dispositivo que marca el tempo

### Algoritmos

**Filter**: Filtrar elementos de un array  
**Map**: Transformar cada elemento de un array  
**Reduce**: Reducir array a un valor único  
**Average**: Promedio aritmético  
**Timestamp**: Marca de tiempo (ms desde 1970)  
**Interval**: Tiempo entre dos eventos  
**Window**: Ventana temporal de análisis  
**Buffer**: Almacenamiento temporal de datos  
**Throttle**: Limitar frecuencia de ejecución  
**Debounce**: Retrasar ejecución hasta que cesen eventos

---

## 📞 SOPORTE Y RECURSOS

### Documentación Oficial

**MDN Web Docs**: https://developer.mozilla.org/  
- Referencia completa de HTML, CSS, JavaScript
- Tutoriales y guías
- Compatibilidad de navegadores

**W3C**: https://www.w3.org/  
- Estándares web oficiales

**Can I Use**: https://caniuse.com/  
- Verificar compatibilidad de features

### Herramientas Útiles

**CodePen**: https://codepen.io/  
- Probar código HTML/CSS/JS online

**JSFiddle**: https://jsfiddle.net/  
- Similar a CodePen

**CSS Gradient Generator**: https://cssgradient.io/  
- Crear gradientes CSS visualmente

**Google Fonts**: https://fonts.google.com/  
- Fuentes gratuitas

**Coolors**: https://coolors.co/  
- Generador de paletas de colores

### Comunidades

**Stack Overflow**: https://stackoverflow.com/  
- Preguntas y respuestas técnicas

**GitHub**: https://github.com/  
- Código open source

**Dev.to**: https://dev.to/  
- Artículos y tutoriales

---

## 📝 REGISTRO DE CAMBIOS

### Versión 1.0 (Actual)
- ✅ Cálculo de BPM por clics
- ✅ Interfaz responsive
- ✅ Estadísticas básicas
- ✅ PWA básica
- ✅ Animaciones visuales

### Futuras Mejoras Sugeridas
- ⏳ Gráfico de historial
- ⏳ LocalStorage persistente
- ⏳ Soporte de teclado
- ⏳ Export/Import de datos
- ⏳ Metrónomo integrado
- ⏳ Base de datos de canciones
- ⏳ Modo oscuro
- ⏳ Análisis estadístico avanzado

---

## ✅ CHECKLIST DE MANTENIMIENTO

### Antes de Modificar
- [ ] Hacer backup del código
- [ ] Leer esta documentación
- [ ] Identificar qué sección modificar
- [ ] Probar en navegador de desarrollo

### Al Modificar HTML
- [ ] Mantener estructura semántica
- [ ] Usar IDs únicos
- [ ] Clases reutilizables
- [ ] Accesibilidad (alt, aria-labels)

### Al Modificar CSS
- [ ] Comentar cambios importantes
- [ ] Probar en diferentes resoluciones
- [ ] Verificar responsive
- [ ] Comprobar colores (contraste)

### Al Modificar JavaScript
- [ ] Comentar lógica compleja
- [ ] Manejar errores (try-catch)
- [ ] Validar inputs
- [ ] Probar casos extremos
- [ ] Verificar en consola (sin errores)

### Antes de Publicar
- [ ] Probar en múltiples navegadores
- [ ] Probar en móvil
- [ ] Verificar que PWA funciona
- [ ] Comprimir/minificar código (producción)
- [ ] Actualizar documentación si es necesario

---

**Fin de la Documentación**

Última actualización: 18 de marzo de 2026  
Versión: 1.0  
Autor: [Tu nombre/equipo]  
Licencia: [Tu licencia preferida]
