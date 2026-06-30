# PropValor Pro — Sistema de Valoración Inmobiliaria España

**Plataforma profesional de valoración de propiedades inmobiliarias en España con integración de APIs en tiempo real y análisis inteligente con IA.**

## 🎯 Características principales

### Valoración Multi-Fuente
- **7 fuentes AVM calibradas**: RealAdvisor, Idealista, Tinsa, BBVA Valora, Fotocasa, Housfy, Properfy
- **Mediana consolidada**: cálculo robusto de la valoración más precisa
- **Horquilla de precios**: rango mínimo-máximo con análisis de dispersión

### Integración de APIs en Tiempo Real
- **Catastro OVC REST/SOAP**: datos catastrales oficiales (superficie, año construcción, uso)
- **INE API**: Índice de Precios de Vivienda oficial
- **INSPIRE WFS**: geometría catastral bajo licencia libre

### Análisis Inteligente con IA
- **Claude AI (Sonnet 4)**: análisis profesional multiparramétrico
- Informe de valoración con horquilla realista
- Análisis de dispersión entre fuentes
- Evaluación de factores clave del inmueble
- Rentabilidad bruta de alquiler y potencial inversor

### Interfaz Premium
- Diseño moderno con tema oscuro profesional
- Tipografía serif/mono para máxima legibilidad
- Animaciones fluidas y retroalimentación visual
- Responsive para desktop y tablet

---

## 📋 Entrada de datos

```
Referencia Catastral (RC)
├─ Validación: 20 caracteres alfanuméricos
├─ Formato: RRRRRRGGSSPPPPPPPPAA (según estándar catastral)
└─ Búsqueda: REST JSON o SOAP desde Catastro OVC

Municipio (REQUERIDO)
├─ Datalist con 40+ municipios españoles
├─ Autocomplete con normalización
└─ Calibración de precios de mercado por municipio

Provincia
└─ Se completa automáticamente desde Catastro OVC

Parámetros del inmueble
├─ Superficie construida (m²) — auto desde Catastro si disponible
├─ Dormitorios (1-12)
├─ Baños (1-8)
├─ Planta (0-30, opcional)
├─ Antigüedad (6 categorías: nuevo, reciente, moderno, antiguo, muy antiguo)
├─ Estado (3 categorías: a reformar, buen estado, reformado)
├─ Garaje (sí/no)
└─ Ascensor (sí/no)
```

---

## 🔧 Correctores de Valoración

| Factor | Ajuste | Impacto |
|--------|--------|--------|
| **Estado: A reformar** | –13% | Reducción significativa |
| **Estado: Buen estado** | –4% | Ajuste moderado |
| **Estado: Reformado** | ±0% | Base (sin ajuste) |
| **Con garaje** | –5% | Suma valor (+5% al descuento) |
| **Sin ascensor** | +6% | Penalización en edificios |

**Fórmula**: `€/m² final = €/m² base × (1 - descuento_combinado)`

---

## 📊 Resultados y Visualización

### Tiles de Catastro
- Superficie catastral oficial
- Año de construcción
- Uso catastral
- Código postal y municipio
- IPV (Índice de Precios de Vivienda) INE

### Valoración Consolidada
- **Precio mediana** (grande, destacado)
- **Horquilla** mín–máx con todos los valores
- **Celdas de resumen**:
  - Mínimo
  - Mediana
  - Máximo
  - €/m² mediana

### Tarjetas por Fuente (API Grid)
Cada fuente muestra:
- Nombre y icono de color
- Precio total €
- €/m² aplicado (con descuentos)
- €/m² base sin descuentos
- Variación anual estimada
- Descripción metodológica
- Barra de progreso de comparación

### Gráfico Comparativo
- Barras horizontales ordenadas de mayor a menor
- Código de color por fuente
- Valores €/m² en tiempo real
- Animación suave de carga

### Análisis con Claude AI
Informe estructurado en 4 párrafos:
1. **Valoración**: precio cierre realista con horquilla
2. **Dispersión de fuentes**: análisis de diferencias y fiabilidad
3. **Factores clave**: características que afectan el valor
4. **Potencial inversor**: rentabilidad bruta y post-reforma

---

## 🌐 Fuentes AVM

| Fuente | Metodología | Acceso | €/m² base |
|--------|-------------|--------|-----------|
| **RealAdvisor** | AVM estadístico · 70 criterios | Web gratis | 3,000–6,800 |
| **Idealista** | Anuncios activos · mayor portal ES | Login requerido | 3,400–6,800 |
| **Tinsa** | Tasaciones reales · homologada BdE | Simulador | 3,100–6,500 |
| **BBVA Valora** | Catastro + Idealista cruzado | Web gratis | 3,200–6,000 |
| **Fotocasa** | Demanda + oferta portal | Web gratis | 3,050–6,200 |
| **Housfy** | Ventas reales últimos 6 meses | Web gratis | 2,850–5,800 |
| **Properfy** | Cruce Idealista + Tinsa | Web gratis | 3,300–6,300 |

---

## 🔐 Autenticación y API Keys

### Claude AI (Anthropic)
```javascript
// Requerido para análisis inteligente
fetch('https://api.anthropic.com/v1/messages', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-api-key': 'sk-ant-...' // Usar variable de entorno
  },
  body: JSON.stringify({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 900,
    messages: [{ role: 'user', content: prompt }]
  })
});
```

**Vars de entorno recomendadas**:
- `ANTHROPIC_API_KEY` — clave de API de Anthropic
- `CATASTRO_ENDPOINT` — URL del servicio Catastro OVC
- `INE_API_ENDPOINT` — URL de servicios INE

---

## 🚀 Uso Rápido

1. **Abrir la aplicación**:
   ```
   https://github.com/niduxhomerealestate/niduxhome-valoraciones-inmuebles
   ```

2. **Introducir datos**:
   - RC válida (20 caracteres)
   - Municipio (obligatorio)
   - Parámetros opcionales del inmueble

3. **Pulsar "Valorar"**:
   - Consulta Catastro OVC (1–2 seg)
   - Calibra precios con Claude AI (2–3 seg)
   - Calcula 7 fuentes AVM
   - Genera informe inteligente (1–2 seg)

4. **Leer resultados**:
   - Valoración consolidada (mediana)
   - Tarjetas de cada fuente
   - Gráfico comparativo
   - Análisis profesional

---

## 📁 Estructura del Proyecto

```
niduxhome-valoraciones-inmuebles/
├── index.html               # Aplicación completa (HTML+CSS+JS)
├── README.md                # Este archivo
├── docs/
│   ├── API.md               # Documentación de APIs
│   ├── METODOLOGIA.md       # Metodología de cálculo
│   └── EXAMPLES.md          # Ejemplos de uso
├── assets/
│   ├── logo.svg             # Logo PropValor Pro
│   └── favicon.ico          # Favicon
└── tests/
    ├── test-catastro.js     # Tests Catastro OVC
    ├── test-ine.js          # Tests INE API
    └── test-claude.js       # Tests Claude AI
```

---

## 🛠️ Desarrollo y Contribución

### Requisitos
- Node.js 18+ (para desarrollo local)
- Clave API de Anthropic
- Navegador moderno (Chrome, Firefox, Safari, Edge)

### Instalación Local
```bash
git clone https://github.com/niduxhomerealestate/niduxhome-valoraciones-inmuebles.git
cd niduxhome-valoraciones-inmuebles
# Servir con: python -m http.server 8000
# O: npx http-server
```

### Roadmap v2
- [ ] Integración con base de datos PostgreSQL
- [ ] Historial de valoraciones
- [ ] Exportación a PDF profesional
- [ ] API REST backend (Node.js/Express)
- [ ] Autenticación de usuarios
- [ ] Comparativas multi-inmueble
- [ ] Mapas interactivos con Leaflet
- [ ] Análisis de tendencias temporales

---

## 📞 Contacto y Soporte

**Nidux Home Real Estate**  
📧 niduxhome@gmail.com  
🌐 [Web corporativa](https://niduxhome.es)  
📱 Teléfono: +34 XXX XXX XXX  

---

## 📄 Licencia

Proyecto propietario. Todos los derechos reservados.
© 2026 Nidux Home Real Estate

---

## 🙏 Créditos y Referencias

- **Catastro OVC**: Ministerio de Transformación Digital y de la Función Pública
- **INE**: Instituto Nacional de Estadística
- **INSPIRE**: Directiva Europea de Datos Espaciales
- **Claude AI**: Anthropic
- **Tipografía**: DM Serif Display, DM Mono, Syne (Google Fonts)
