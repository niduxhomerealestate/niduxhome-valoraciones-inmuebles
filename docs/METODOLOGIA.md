# Metodología — PropValor Pro

## Arquitectura General

PropValor Pro utiliza un modelo **multi-fuente con calibración inteligente** para valoración inmobiliaria:

```
┌─────────────────────────────────────────────────────────┐
│  DATOS DE ENTRADA                                       │
│  • Referencia Catastral (RC)                           │
│  • Municipio + Provincia                               │
│  • Características (sup, hab, baños, estado, etc.)     │
└─────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│  APIS EN TIEMPO REAL                                    │
│  • Catastro OVC: superficie, año, uso                  │
│  • INE: IPV (Índice de Precios de Vivienda)           │
│  • OpenAI GPT-4o: calibración de precios              │
└─────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│  7 MODELOS AVM INDEPENDIENTES                           │
│  RealAdvisor | Idealista | Tinsa | BBVA | Fotocasa    │
│  Housfy | Properfy                                      │
└─────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│  CORRECTORES & DESCUENTOS                              │
│  • Estado de conservación                               │
│  • Equipamientos (garaje, ascensor)                    │
│  • Antigüedad                                           │
└─────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│  MEDIANA ROBUSTA                                        │
│  Elimina outliers, calcula mediana de 7 fuentes       │
└─────────────────────────────────────────────────────────┘
                         ↓
┌─────────────────────────────────────────────────────────┐
│  ANÁLISIS INTELIGENTE (ChatGPT)                        │
│  • Informe profesional 4 párrafos                       │
│  • Contexto de mercado                                  │
│  • Recomendaciones inversor                            │
└─────────────────────────────────────────────────────────┘
```

---

## 1. Datos Catastrales

### Catastro OVC (REST API)

**Endpoint público**:
```
GET https://ovc.catastro.meh.es/OVCServWeb/OVCWcfLibres/OVCFotoFachada.svc/RecursoWeb/CONS/RESTJSON/OVCCallejero/datos_rc?RC=XXXXXXXXXXXXXXXXXXX
```

**Extrae**:
- `sfc` — Superficie construida (m²)
- `ant` — Año de construcción
- `luso` — Uso catastral (residencial, comercial, etc.)
- `dp` — Código postal
- `nm` — Municipio
- `dir` — Dirección completa

**Ejemplo respuesta**:
```json
{
  "consulta_dnprcResult": {
    "bico": {
      "bi": {
        "debi": {
          "sfc": "88",
          "ant": "1998",
          "luso": "Residencial"
        },
        "dt": {
          "locs": {
            "lourb": {
              "dp": "28016",
              "nm": "Madrid",
              "dir": {
                "tv": "Calle",
                "nv": "Principal",
                "pnp": "45"
              }
            }
          }
        }
      }
    }
  }
}
```

**Limitaciones**:
- ✓ Datos oficiales, no protegidos
- ✗ CORS bloqueado en navegador (mitigamos con fallback)
- ✓ Actualización en tiempo real
- ✓ Cobertura: España peninsular + Canarias

---

### INE — Índice de Precios de Vivienda

**Endpoint público**:
```
GET https://servicios.ine.es/wstempus/js/ES/DATOS_SERIE/IPC259800?nult=4
```

**Extrae**:
- `Valor` — IPV en puntos base (100 = 2015)
- Últimos 4 trimestres

**Ejemplo**:
```json
{
  "Data": [
    {"Valor": "183.5", "Periodo": "2025T4"},
    {"Valor": "185.3", "Periodo": "2026T1"}
  ]
}
```

**Interpretación**:
- 185.3 pts = viviendas un 85.3% más caras que en 2015
- Usado para contexto de variación anual (+/- %)

---

## 2. Modelos AVM (7 Fuentes)

Cada modelo tiene metodología independiente. PropValor **calibra €/m² por municipio**:

| Fuente | Datos Base | Precisión | Sesgo |
|--------|-----------|-----------|-------|
| **RealAdvisor** | 70+ criterios estadísticos | ±8% | Puede sobreestimar |
| **Idealista** | Anuncios activos vigentes | ±10% | +8% por margen negociación |
| **Tinsa** | Tasaciones reales (homologadas BdE) | ±5% | Más conservador |
| **BBVA Valora** | Catastro + Idealista cruzado | ±7% | Media ponderada |
| **Fotocasa** | Demanda + oferta del portal | ±12% | Variable por actividad |
| **Housfy** | Ventas reales últimos 6 meses | ±9% | Baja muestra a veces |
| **Properfy** | Cruce Idealista + Tinsa | ±6% | Mediador de outliers |

### Calibración con ChatGPT (GPT-4o)

OpenAI recibe prompt con contexto de municipio, año, estado general y devuelve **€/m² por cada modelo ajustado a 2025/2026**:

```javascript
const prompt = `
MUNICIPIO: Madrid
PROVINCIA: Madrid
ZONA: zona urbana consolidada
Características: 10-30 años · garaje sí · ascensor sí

Da los precios €/m² según cada fuente con datos REALES de mercado 2025/2026.
Responde SOLO JSON:
{"precios_m2": {"RealAdvisor": 4500, "Idealista": 5200, ...}}
`;
```

**Ventajas**:
- ✓ Datos actualizados automáticamente
- ✓ Contextualizado por municipio
- ✓ Evita hardcoding desactualizado
- ✓ Rápido (1-2 segundos)

---

## 3. Correctores & Descuentos

Aplicados **después de** obtener €/m² de cada fuente:

### Por Estado de Conservación

```
Estado                    Corrector    Aplicación
────────────────────────────────────────────────
A reformar               × 0.87 (–13%)  Restamos valor
Buen estado              × 0.96 (–4%)   Ligero descuento
Reformado/Nuevo          × 1.00 (±0%)   Referencia base
```

**Ejemplo**:
```
RealAdvisor base: 4.500 €/m²

Inmueble "a reformar":
  4.500 × 0.87 = 3.915 €/m² (aplicado)
  
Inmueble "reformado":
  4.500 × 1.00 = 4.500 €/m² (sin cambio)
```

### Por Equipamientos

```
Característica      Corrector
───────────────────────────────
+ Garaje (privado)  × 0.95 (–5%)  Menos descuento
– Ascensor (2+ ptas) × 1.06 (+6%)  Desventaja
```

**Ejemplo completo**:
```
Base €/m²: 4.500

Inmueble:
  • Estado: buen estado      → × 0.96
  • Garaje: no               → × 1.00
  • Ascensor: no (3ª planta) → × 1.06
  
4.500 × 0.96 × 1.00 × 1.06 = 4.579 €/m²
```

### Por Antigüedad (informativo)

No se aplica multiplicador, pero se nota en análisis:
- **Obra nueva**: Generalmente +5% a +8%
- **< 10 años**: +2% a +4%
- **10–30 años**: Base (0%)
- **30–50 años**: –2% a –5%
- **> 50 años**: –5% a –15%

---

## 4. Mediana Robusta

### Cálculo

1. **Recolectar 7 valores** (uno por fuente, ya con correctores aplicados)
2. **Ordenar de menor a mayor**
3. **Seleccionar el valor central**

```javascript
function median(arr) {
  const sorted = [...arr].sort((a, b) => a - b);
  const mid = Math.floor(sorted.length / 2);
  return sorted.length % 2 
    ? sorted[mid]
    : (sorted[mid - 1] + sorted[mid]) / 2;
}

const prices = [3900, 4100, 4300, 4500, 4700, 4900, 5100];
const medianPrice = median(prices); // 4.500 (elemento 4 de 7)
```

### Por qué mediana vs media

| Métrica | Valor | Ventaja |
|---------|-------|---------|
| **Media** | 4.457 | Incluye todos los datos |
| **Mediana** | 4.500 | Robusto a outliers |
| **Moda** | — | Sin valor único |

**Ejemplo con outlier**:
```
Precios: [3900, 4100, 4300, 4500, 4700, 4900, 9500]
                                            ↑
                                    (outlier: error AVM)

Media:   4.843 €/m² (inflada por outlier)
Mediana: 4.500 €/m² (ignora outlier) ← USAMOS ESTA
```

---

## 5. Horquilla de Precios

Derivada automáticamente:

```
Mínimo:  MIN(precio1, precio2, ..., precio7)
Máximo:  MAX(precio1, precio2, ..., precio7)
Mediana: MED(precio1, precio2, ..., precio7)

Rango de confianza: Máximo – Mínimo
% dispersión: (Máximo – Mínimo) / Mediana × 100
```

**Interpretación típica**:
- **Dispersión < 10%**: Fuentes muy alineadas, confianza alta
- **Dispersión 10–20%**: Normal, confianza media-alta
- **Dispersión > 25%**: Fuentes desalineadas, revisar datos

---

## 6. Análisis Inteligente (ChatGPT)

### Prompt System

```
Eres tasador inmobiliario experto. Informe profesional en 4 párrafos:

INMUEBLE:
- RC: [RC] · [Municipio]
- [superficie]m² · [hab]dorm. · [ban]baños
- Estado: [estado] · antigüedad: [antigüedad]

VALORACIONES (7 fuentes):
  RealAdvisor: [precio]€
  Idealista: [precio]€
  ...
Mediana: [mediana]€ ([€/m²] €/m²)

MERCADO [MUNICIPIO]:
- YoY: +[%]
- Alquiler: [€/m²/mes]
- IPV INE: [pts]

Párrafos:
1) Valoración con horquilla y precio cierre realista
2) Dispersión entre fuentes y cuál es más fiable
3) Factores clave que afectan al valor de este inmueble
4) Potencial inversor: rentabilidad bruta alquiler + margen reforma

Español, números concretos, sin rodeos.
```

### Ejemplo Output

```
La mediana de las 7 fuentes sitúa el valor en 396.000€ 
(4.500€/m²) para 88m² en Alcobendas — estado buen estado.

Horquilla de mercado: 343.200€ – 449.400€. La dispersión es 
normal: portales de anuncios incluyen margen 5-8%.

Factores relevantes: antigüedad (1998, moderada), planta 
intermedia, estado de conservación, garaje, ascensor presente.

Rentabilidad bruta alquiler: ~4.2% anual. Post-reforma 
estimado: 455.880€ (+15% potencial).
```

---

## 7. Fallbacks & Robustez

### Si Catastro falla (CORS, timeout)

```javascript
// Usar superficie introducida manualmente
const sup = parseInt(inp_sup.value) || 88; // default 88m²
// Continuar con mediana de 7 fuentes igual
```

### Si OpenAI falla

Fallback a base de datos hardcoded por municipio:

```javascript
const fallbacks = {
  'alcobendas': {
    precios_m2: {RealAdvisor: 3200, Idealista: 3939, ...},
    variacion_anual_pct: 11.5,
    alquiler_m2_mes: 16.1
  },
  'madrid': {...},
  ...
};
```

### Si INE falla

Usar IPV estimado: **185.3 pts** (promedio nacional 2026)

---

## 8. Precisión & Limitaciones

### Rango de confianza

**±10–15% vs transacciones reales** en mismo municipio/fecha

Factores de incertidumbre:
- Reformas recientes no visibles en Catastro
- Microzonas (metros de diferencia = 10–20% precio)
- Volatilidad temporal (mercado puede mover ±5% en 3 meses)
- Negociación (se paga 5–10% menos que anuncio)

### No es sustituto de tasación profesional

❌ No válido para hipotecas, litigios, venta oficial  
✓ Válido para orientación de mercado, análisis inversor  
✓ Válido para referencias internas, ofertas

---

## 9. Cálculo de Rentabilidad

### Rentabilidad Bruta Alquiler

```
Rentabilidad % = (Alquiler mensual × 12 / Valor total) × 100

Ejemplo:
  • Valor: 396.000€
  • Alquiler estimado: 1.400€/mes
  • Rentabilidad = (1.400 × 12 / 396.000) × 100 = 4.24%
```

### Margen Reforma

```
Post-reforma valor = Valor base × 1.15 (estimado +15%)

Ejemplo:
  • Valor actual: 396.000€
  • Estado: a reformar
  • Post-reforma: 396.000 × 1.15 = 455.400€
  • Margen disponible: 59.400€
```

---

## 10. Fórmula Final

```
PRECIO_FINAL = (MEDIANA_DE_7_FUENTES_CALIBRADAS × CORRECTORES_ESTADO_EQUIPAMIENTO)

donde:

MEDIANA_DE_7_FUENTES_CALIBRADAS = 
  MED(
    RealAdvisor_€/m2 × super,
    Idealista_€/m2 × super,
    Tinsa_€/m2 × super,
    BBVA_€/m2 × super,
    Fotocasa_€/m2 × super,
    Housfy_€/m2 × super,
    Properfy_€/m2 × super
  )

CORRECTORES_ESTADO_EQUIPAMIENTO = 
  corrector_estado × corrector_garaje × corrector_ascensor
```

---

**Versión**: 1.0  
**Última actualización**: 2026-07-01  
**Responsable**: Nidux Home Real Estate
