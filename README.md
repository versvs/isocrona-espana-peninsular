# Isócrona España · Cartograma Polar de Tiempos de Viaje

> **España deformada por el reloj, no por la distancia.**  
> Un cartograma interactivo polar de tiempos de viaje en transporte público terrestre entre las 47 capitales de provincia de la España peninsular.

[![Licencia](https://img.shields.io/badge/Licencia-MIT-blue.svg)](LICENSE)
[![Zero Dependencies](https://img.shields.io/badge/Dependencias-Cero%20(Vanilla%20JS%2FSVG)-2ea44f.svg)](#arquitectura-técnica)
[![Ámbito](https://img.shields.io/badge/%C3%81mbito-47%20Capitales%20Peninsulares-597ea3.svg)](#metodología-y-modelo-de-datos)
[![Población](https://img.shields.io/badge/Poblaci%C3%B3n%20INE-44.5M%20habitantes-1d2d3d.svg)](#metodología-y-modelo-de-datos)

---

## 🧭 ¿De qué trata este proyecto?

La geografía tradicional nos enseña una España donde la cercanía entre dos ciudades se mide en kilómetros euclídeos. Sin embargo, en el mundo real nos movemos según el **reloj**: los corredores de Alta Velocidad (AVE), las líneas convencionales de ferrocarril y las redes regulares de autobús interurbano distorsionan radicalmente la accesibilidad entre regiones.

Este proyecto propone un **cartograma polar anamórfico e interactivo**:
- **El centro ($r = 0$)** es la capital de origen seleccionada (0 horas de viaje).
- **El ángulo polar ($\theta$)** conserva el **rumbo geográfico verdadero** (geodésico) entre las coordenadas reales: Barcelona permanece al noreste, Sevilla al suroeste y A Coruña al noroeste.
- **La distancia radial ($r$)** representa estrictamente el **tiempo comercial mínimo de viaje** en transporte público terrestre.

El resultado es un mapa que "respira" y muta cada vez que cambias de capital: las ciudades comunicadas por vías de alta velocidad se aproximan al centro en una contracción temporal; las capitales peor conectadas o sin ferrocarril directo son empujadas a la periferia exterior.

---

## ✨ Características Principales

- 🔄 **Reubicación Instantánea del Origen:** Haz clic sobre cualquier capital en el mapa o selecciónala en la lista lateral para convertirla en el centro del universo temporal ($0\text{ h}$).
- ⏱️ **Enrutamiento Multimodal Óptimo:** Resuelve en tiempo real la ruta más rápida mediante el **algoritmo de Dijkstra** sobre más de 80 corredores interurbanos de Alta Velocidad, Ferrocarril Convencional y Autobús regular.
- 🚊 **Penalización Realista por Transbordo:** Aplica una penalización fija de **12 minutos por cada cambio de tramo o modo**, modelando el tiempo real de cruce de andén, controles de billete/seguridad y desfase de enlace.
- 👥 **Métricas Territoriales y Demográficas (Población INE):**
  - **Media de tiempos** a todas las capitales.
  - **Mediana Territorial:** Tiempo al 50% de las capitales de provincia (hito de las 23 capitales más cercanas).
  - **Mediana Demográfica:** Tiempo necesario para alcanzar a más de la mitad de la población peninsular española (~22,2 millones de habitantes).
  - **Cobertura bajo 3 horas:** Porcentaje y cifra absoluta de población y capitales alcanzables en $\le 180\text{ min}$ (el umbral clave de la movilidad de ida y vuelta en la misma jornada).
- 🗺️ **Capas Cartográficas Conmutables:**
  - **Anillos Isócronos:** Círculos concéntricos de 1 hora con escala dinámica (px/h).
  - **Silueta Deformada (*Convex Hull*):** Envolvente convexa calculada sobre las coordenadas temporales de las ciudades limítrofes, ilustrando la asimetría temporal del país.
  - **Desvío Geográfico Fantasma (*Ghost offset*):** Dibuja dónde se situaría cada capital si la velocidad fuese homogénea e idéntica a la media peninsular, poniendo en evidencia la ventaja del AVE o el déficit de aislamiento.
  - **Radios por Modo:** Trazos diferenciados por Alta Velocidad, Tren convencional y Autobús.
  - **Etiquetas Inteligentes:** Algoritmo codicioso (*greedy*) de prevención de colisiones tipográficas flotantes en el espacio SVG.
- 📊 **Ranking Comparativo de Conectividad (47 Capitales):**
  - Vista modal flotante accesible (`Ver Ranking`) con cálculo reactivo de la centralidad de cada una de las 47 capitales peninsulares.
  - **5 criterios de ordenación intercambiables:**
    1. *Tiempo medio ponderado por población:* penaliza la desconexión con los grandes polos urbanos.
    2. *Mediana ponderada por población:* umbral de tiempo para alcanzar a la mitad de los ciudadanos peninsulares.
    3. *Tiempo medio a todas las capitales:* centralidad geométrica promedio en la red.
    4. *Mediana de tiempo a capitales:* tiempo al 50% de las capitales provinciales.
    5. *Accesibilidad en Alta Velocidad (% capitales en <3h):* capacidad de conexión en el mismo día sin pernocta.
  - **Diseño enriquecido:** Distintivos de podio para el Top 3 (#1 Oro, #2 Plata, #3 Bronce), etiquetas de tipología de nodo (*Alta Velocidad*, *Convencional*, *Autobús*), y barras de magnitud relativa proporcionales frente a la primera posición.
  - **Filtro reactivo con "Efecto Contexto" (Truncamiento elíptico):** Al buscar una capital concreta (ej. *Palencia*), la tabla mantiene el Top 3 inmutable como ancla de referencia, inserta divisores de puntos suspensivos con el conteo de capitales intermedias ocultas, atenúa los vecinos inmediato superior e inferior, resalta la capital buscada y fija la última posición como ancla inferior.
  - **Acción rápida "Centrar en mapa":** Permite convertir inmediatamente cualquier ciudad del ranking en el nuevo origen del cartograma radial.
- 🎨 **Industrial Design System (Blueprint):**
  - Dos modos de visualización calibrados: **Papel** (modo claro técnico de alta legibilidad) y **Acero** (modo oscuro de contraste analítico).
  - Estética arquitectónica y de ingeniería de precisión (*blueprint corners*, tipografías Barlow y Barlow Condensed).
- ⚡ **100% Client-Side:** Cero dependencias externas, sin frameworks pesados, sin peticiones lentas de red; cálculos memoizados a 60 FPS.

---

## 🔬 Metodología y Modelo de Datos

### 1. Universo de Estudio y Delimitación Territorial
El modelo abarca las **47 capitales de provincia de la España peninsular**.

> [!NOTE]
> **Criterio de exclusión insular y extrapeninsular:**  
> Se excluyen las provincias insulares (Illes Balears, Las Palmas y Santa Cruz de Tenerife) y las ciudades autónomas de Ceuta y Melilla. Sus conexiones requieren transporte marítimo o aéreo, cuyas rutinas de facturación obligatoria, embarque anticipado, frecuencias reducidas y discontinuidades de escala horaria impiden una comparación homogénea con la red terrestre interurbana continua.

### 2. Fuentes Demográficas
Cada capital de provincia tiene asignada la población consolidada de su respectiva provincia según las cifras oficiales del censo del **Instituto Nacional de Estadística (INE)**, totalizando una población peninsular de referencia de **44,5 millones de habitantes**.

Esto permite cruzar los tiempos de desplazamiento con el impacto humano:
$$\text{Mediana Demográfica} = \min \left\{ t \in \text{Dests} \;\middle|\; \text{Pob}(\text{Origen}) + \sum_{d \in \text{Dests},\, \text{dist}(d) \le t} \text{Pob}(d) \ge \frac{\text{Pob Total}}{2} \right\}$$

### 3. Red de Transporte Multimodal
La red está modelada como un **grafo bidireccional no dirigido** $G = (V, E)$ donde $V$ son las 47 capitales y $E$ agrupa más de 80 arcos directos:
1. **Alta Velocidad Ferroviaria (AVE / Avant):** Tramos a 250–310 km/h en líneas Adif AV (operadores Renfe, Iryo, Ouigo).
2. **Ferrocarril Convencional / Alvia / MD:** Líneas de ancho ibérico electrificadas y diésel, servicios Regionales y trenes mixtos de ancho variable.
3. **Autobús Regular Interurbano:** Rutas en régimen de concesión (ALSA, Avanza, Samar, etc.) que cubren tramos transversales huérfanos de ferrocarril competitivo (como la Cornisa Cantábrica Oviedo–Santander–Bilbao–San Sebastián o enlaces de Castilla y León y Extremadura).

Los pesos de las aristas corresponden a las mejores duraciones comerciales oficiales punto a punto publicadas para un día laborable estándar.

### 4. Algoritmo de Enrutamiento y Coste de Transbordo
Para cada origen seleccionado, se ejecuta el algoritmo de caminos mínimos de Dijkstra:
- **Coste de enlace intermedio:** Si una ruta óptima requiere transbordar entre dos tramos consecutivos, se añade una constante fija de **12 minutos de penalización**.
- Dicha penalización representa el tiempo físico de desembarque, cruce de andenes o cambio de terminal, control de acceso/escáner y margen prudencial de correspondencia.
- Los árboles de caminos mínimos generados se almacenan en una caché en memoria (*memoization*) para posibilitar transiciones fluidas.

---

## 💻 Arquitectura Técnica

El proyecto sigue una filosofía artesanal de máxima ligereza, velocidad y portabilidad:

- **HTML5 Semántico:** Estructura limpia y accesible con diálogos modales nativos.
- **SVG Dinámico Reactivo:** Lienzo vectorial escalable con cálculo geométrico en tiempo real (círculos, líneas cardinales, polígonos de casco convexo y etiquetas de colisión adaptativa).
- **CSS Vanilla (Industrial Design System):** Variables y tokens de diseño para tipografías, rejillas, paletas de papel y acero, y esquinas técnicas tipo *blueprint*.
- **JavaScript ES6+ Nativo:** Sin librerías externas (sin jQuery, sin D3, sin React, sin bundlers como Webpack o Vite). El archivo `index.html` es completamente autosuficiente y ejecutable en cualquier navegador moderno.

---

## 🚀 Cómo Ejecutar el Proyecto en Local

No requiere instalación previa de paquetes (`npm install`) ni procesos de compilación (`npm run build`).

### Opción 1: Servidor local simple con Python
```bash
# Clona el repositorio
git clone https://github.com/versvs/isocrona-espana-peninsular.git
cd isocrona-espana-peninsular

# Inicia un servidor web en el puerto 8080
python -m http.server 8080
```
Abre en tu navegador: [http://localhost:8080](http://localhost:8080)

### Opción 2: Con Node.js (`npx serve` o `http-server`)
```bash
npx serve .
```

### Opción 3: Directamente en el navegador
Puedes abrir directamente el archivo `index.html` haciendo doble clic sobre él o arrastrándolo a la ventana de tu navegador (Chrome, Firefox, Safari, Edge).

---

## 📁 Estructura del Repositorio

```text
isocrona-espana-peninsular/
├── index.html        # Aplicación completa (HTML, CSS industrial, SVG y JavaScript)
├── README.md         # Documentación exhaustiva y metodología del proyecto
└── design/           # Bocetos de diseño previos y referencias visuales
```

---

## 📖 Cómo Leer el Cartograma

1. **Selecciona un origen:** Haz clic en una ciudad en la lista izquierda o directamente sobre el mapa.
2. **Observa el tiempo radial:** Los anillos concéntricos marcan intervalos de 1 hora ($1\text{ h}$, $2\text{ h}$, $3\text{ h}$, etc.).
3. **Pasa el ratón sobre un destino:** En la columna derecha (*03 — Lectura*) se desglosa el itinerario óptimo tramo a tramo, los kilómetros reales y la comparación frente a la mediana.
4. **Analiza la accesibilidad:** En la sección *04 — Accesibilidad*, revisa el porcentaje de población conectada a menos de 3 horas y la mediana demográfica.
5. **Activa las capas de trazado:** En *05 — Trazado*, experimenta con *Desvío vs. geografía* para descubrir qué capitales gozan de alta velocidad frente a su distancia física real.

---

## 👤 Autor y Créditos

- **Desarrollo y concepto:** [José Alcántara](https://www.josealcantara.com/) ([@versvs](https://github.com/versvs))
- **Código y repositorio:** [github.com/versvs/isocrona-espana-peninsular](https://github.com/versvs/isocrona-espana-peninsular)

---

## 📄 Licencia

Este proyecto se distribuye bajo la licencia **MIT**. Eres libre de usarlo, adaptarlo y compartirlo citando la autoría.
