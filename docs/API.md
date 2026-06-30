# Documentación de APIs — PropValor Pro

## 1. Catastro OVC

### REST JSON (Recomendado)

**Endpoint**:
```
GET https://ovc.catastro.meh.es/OVCServWeb/OVCWcfLibres/OVCFotoFachada.svc/RecursoWeb/CONS/RESTJSON/OVCCallejero/datos_rc
```

**Parámetros**:
```
RC: Referencia Catastral (20 caracteres, alfanuméricos)
```

**Request**:
```javascript
const url = `https://ovc.catastro.meh.es/OVCServWeb/OVCWcfLibres/OVCFotoFachada.svc/RecursoWeb/CONS/RESTJSON/OVCCallejero/datos_rc?RC=5882502VK4858S0015LE`;

fetch(url, {
  mode: 'cors',
  signal: AbortSignal.timeout(5000)
})
.then(r => r.json())
.then(d => {
  // Procesar respuesta
});
```

**Response (200 OK)**:
```json
{
  "consulta_dnprcResult": {
    "bico": {
      "bi": {
        "debi": {
          "sfc": "88",
          "ant": "2001",
          "luso": "Residencial"
        },
        "dt": {
          "locs": {
            "lous": {
              "lourb": {
                "nm": "Alcobendas",
                "dir": {
                  "dp": "28100",
                  "tv": "Calle",
                  "nv": "Principal",
                  "pnp": "123"
                }
              }
            }
          }
        }
      }
    }
  }
}
```

**Campos clave**:
| Campo | Tipo | Descripción |
|-------|------|-------------|
| `debi.sfc` | string | Superficie construida (m²) |
| `debi.ant` | string | Año de construcción |
| `debi.luso` | string | Uso catastral |
| `lourb.nm` | string | Nombre municipio |
| `dir.dp` | string | Código postal |

**Errores**:
```javascript
// RC inválido
{"consulta_dnprcResult": null}

// RC no encontrado en catastro
{"consulta_dnprcResult": {"bico": null}}

// CORS bloqueado (navegador)
// → Fallback: usar SOAP endpoint
```

---

### SOAP (Fallback)

**Endpoint**:
```
POST https://ovc.catastro.meh.es/OVCServWeb/OVCWcfLibres/OVCFotoFachada.svc
```

**Headers**:
```
Content-Type: text/xml;charset=utf-8
SOAPAction: http://www.catastro.meh.es/ws/Consulta_DNPRC/Consulta_DNPRC
```

**Body**:
```xml
<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" 
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
               xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <soap:Body>
    <Consulta_DNPRC xmlns="http://www.catastro.meh.es/ws/Consulta_DNPRC">
      <entrada>
        <cons>DNPRC</cons>
        <RC>5882502VK4858S0015LE</RC>
      </entrada>
    </Consulta_DNPRC>
  </soap:Body>
</soap:Envelope>
```

**Parsing de respuesta**:
```javascript
const parseSOAP = (xmlText) => {
  const getSup = xmlText.match(/<sfc>([^<]+)<\/sfc>/);
  const getAnio = xmlText.match(/<ant>([^<]+)<\/ant>/);
  const getUso = xmlText.match(/<luso>([^<]+)<\/luso>/);
  const getMuni = xmlText.match(/<nm>([^<]+)<\/nm>/);
  const getCP = xmlText.match(/<dp>([^<]+)<\/dp>/);
  
  return {
    sup: getSup?.[1] || null,
    anio: getAnio?.[1] || null,
    uso: getUso?.[1] || 'Residencial',
    muni: getMuni?.[1] || null,
    cp: getCP?.[1] || null,
    source: 'soap'
  };
};
```

---

## 2. INE — Índice de Precios de Vivienda

### Endpoint

```
GET https://servicios.ine.es/wstempus/js/ES/DATOS_SERIE/IPC259800
```

**Parámetros**:
```
nult: número de últimos registros a retornar (ej: 4 = últimos 4 meses)
```

### Request

```javascript
const url = 'https://servicios.ine.es/wstempus/js/ES/DATOS_SERIE/IPC259800?nult=4';

fetch(url, {
  signal: AbortSignal.timeout(5000)
})
.then(r => r.json())
.then(d => {
  const lastValue = d.Data[d.Data.length - 1];
  console.log('IPV:', lastValue.Valor); // ej: 185.3
  console.log('Período:', lastValue.Periodo); // ej: "2026Q2"
});
```

### Response (200 OK)

```json
{
  "Data": [
    {
      "Valor": "182.1",
      "Periodo": "2025Q4"
    },
    {
      "Valor": "183.5",
      "Periodo": "2026Q1"
    },
    {
      "Valor": "184.9",
      "Periodo": "2026Q2"
    },
    {
      "Valor": "185.3",
      "Periodo": "2026Q3"
    }
  ]
}
```

**Campos**:
| Campo | Tipo | Descripción |
|-------|------|-------------|
| `Valor` | string | IPV con base 2015 = 100 |
| `Periodo` | string | Período (AñoQx, siendo x=trimestre) |

### Cálculo de Variación Anual

```javascript
const calcVariationYoY = (currentIPV, lastYearIPV) => {
  return ((currentIPV - lastYearIPV) / lastYearIPV) * 100;
};

// Ejemplo:
// IPV hace 1 año: 167.2
// IPV actual: 185.3
// Variación: (185.3 - 167.2) / 167.2 × 100 = 10.8%
```

---

## 3. Claude AI — Calibración y Análisis

### Autenticación

```
Header: x-api-key: sk-ant-xxxxxxxxxxxxx
```

**Obtener clave**:
1. Registrarse en [Anthropic Console](https://console.anthropic.com)
2. Crear API key en "API Keys"
3. Guardar en variable de entorno: `ANTHROPIC_API_KEY`

### Endpoint

```
POST https://api.anthropic.com/v1/messages
```

### Calibración de Precios

**Propósito**: Obtener €/m² por fuente AVM para municipio específico

**Request**:
```javascript
const prompt = `Eres un experto en mercado inmobiliario español con datos actualizados a 2025/2026.

MUNICIPIO: Alcobendas (Madrid)
ZONA/BARRIO: zona urbana consolidada
Características: moderno (10-30 años) · garaje sí · ascensor sí

Da los precios €/m² de vivienda usada para ESTE municipio concreto según cada fuente, 
con datos reales de mercado 2025/2026. Sé preciso y realista.

Responde SOLO JSON, sin markdown:
{"municipio":"Alcobendas","provincia":"Madrid","zona":"zona urbana consolidada","precios_m2":{"RealAdvisor":0,"Idealista":0,"Tinsa":0,"BBVA_Valora":0,"Fotocasa":0,"Housfy":0,"Properfy":0},"variacion_anual_pct":0.0,"alquiler_m2_mes":0.0,"nota":""}`;

fetch('https://api.anthropic.com/v1/messages', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-api-key': 'sk-ant-...'
  },
  body: JSON.stringify({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 500,
    messages: [
      {
        role: 'user',
        content: prompt
      }
    ]
  })
})
.then(r => r.json())
.then(d => {
  const responseText = d.content[0].text;
  const jsonData = JSON.parse(responseText.replace(/```json|```/g, ''));
  console.log(jsonData);
  // Usar jsonData para rellenar tarjetas
});
```

**Response**:
```json
{
  "id": "msg-xxxxx",
  "type": "message",
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "{\"municipio\":\"Alcobendas\",\"provincia\":\"Madrid\",\"zona\":\"zona urbana consolidada\",\"precios_m2\":{\"RealAdvisor\":3200,\"Idealista\":3939,\"Tinsa\":4054,\"BBVA_Valora\":3500,\"Fotocasa\":3575,\"Housfy\":3300,\"Properfy\":3550},\"variacion_anual_pct\":11.5,\"alquiler_m2_mes\":16.1,\"nota\":\"Mercado dinámico, demanda alta en zona residencial premium\"}"
    }
  ],
  "model": "claude-sonnet-4-20250514",
  "stop_reason": "end_turn",
  "stop_sequence": null,
  "usage": {
    "input_tokens": 312,
    "output_tokens": 152
  }
}
```

### Análisis de Valoración

**Propósito**: Generar informe profesional de 4 párrafos

**Request**:
```javascript
const prompt = `Eres tasador inmobiliario experto. Informe de valoración profesional en 4 párrafos concisos.

INMUEBLE:
- RC: 5882502VK4858S0015LE · Alcobendas
- 88m² · 3dorm. · 2baños · planta 3
- Estado: buen estado · antigüedad: moderno
- Garaje: sí · Ascensor: sí

VALORACIONES:
  RealAdvisor: 284.416€
  Idealista: 346.432€
  Tinsa: 356.752€
  BBVA_Valora: 308.000€
  Fotocasa: 314.600€
  Housfy: 290.400€
  Properfy: 312.400€
Mediana: 312.400€ (3.550€/m²)

MERCADO ALCOBENDAS:
- YoY: +11.5%
- Alquiler: 16.1€/m²/mes
- IPV INE: 185.3 pts
- Mercado dinámico, demanda alta

Párrafos: (1) valoración con horquilla y precio cierre, (2) dispersión entre fuentes, 
(3) factores clave, (4) potencial inversor. Números concretos. Español.`;

fetch('https://api.anthropic.com/v1/messages', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'x-api-key': 'sk-ant-...'
  },
  body: JSON.stringify({
    model: 'claude-sonnet-4-20250514',
    max_tokens: 900,
    messages: [{ role: 'user', content: prompt }]
  })
})
.then(r => r.json())
.then(d => {
  const analysisText = d.content[0].text;
  // Mostrar en UI
  document.getElementById('ai-body').innerHTML = analysisText
    .replace(/\n\n/g, '<br><br>')
    .replace(/\*\*(.*?)\*\*/g, '<strong>$1</strong>');
});
```

**Response**:
```json
{
  "content": [
    {
      "type": "text",
      "text": "La mediana de las 7 fuentes sitúa el valor en 312.400€ (3.550€/m²) para 88m² en Alcobendas — buen estado y moderno. Horquilla realista: 284.000€ – 356.000€...\n\nLa dispersión es moderada (25%). Idealista sobrevalora típicamente (+10%) por márgenes de negociación..."
    }
  ]
}
```

---

## 4. Límites y Cuotas

### Catastro OVC

| Límite | Valor |
|--------|-------|
| Timeout | 5 segundos |
| Rate limit | Sin límite público |
| Formato | JSON o SOAP |

### INE

| Límite | Valor |
|--------|-------|
| Timeout | 5 segundos |
| Rate limit | Sin límite público |
| Frecuencia datos | Mensual |

### Claude AI (Anthropic)

| Límite | Valor |
|--------|-------|
| Modelo | claude-sonnet-4-20250514 |
| Tokens entrada | 200k (contrato) |
| Tokens salida | 4k por mensaje |
| Timeout | 30 segundos |
| Rate limit | Según plan |

**Costos aproximados (May 2025)**:
- Entrada: $3 por 1M tokens
- Salida: $15 por 1M tokens

**Ej. valuación**:
- Calibración: ~300 tokens entrada → ~$0.0009
- Análisis: ~600 tokens entrada → ~$0.0018
- **Total aprox: $0.003 por valuación completa**

---

## 5. Manejo de Errores

### Códigos HTTP Comunes

| Código | Significado | Acción |
|--------|-------------|--------|
| 200 | OK | Procesar normalmente |
| 400 | Bad Request | Validar parámetros |
| 401 | Unauthorized | Revisar API key (Claude) |
| 403 | Forbidden | CORS bloqueado (Catastro) |
| 408 | Request Timeout | Reintentar con backoff |
| 429 | Too Many Requests | Esperar y reintentar |
| 500 | Server Error | Usar fallback |

### Estrategia de Fallback

```javascript
async function fetchWithFallback(primary, fallback) {
  try {
    return await primary();
  } catch (err) {
    console.warn('Primary failed:', err);
    try {
      return await fallback();
    } catch (err2) {
      console.error('Fallback also failed:', err2);
      return getHardcodedDefault();
    }
  }
}

// Uso:
const catastroData = await fetchWithFallback(
  () => fetchCatastroREST(rc),
  () => fetchCatastroSOAP(rc)
);
```

---

## 6. Variables de Entorno

**Recomendadas para producción**:

```bash
# .env (no commitear a git)
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxx
CATASTRO_ENDPOINT=https://ovc.catastro.meh.es/...
INE_ENDPOINT=https://servicios.ine.es/...
TIMEOUT_CATASTRO=5000
TIMEOUT_INE=5000
TIMEOUT_CLAUDE=10000
DEBUG=false
```

**Uso en JS**:
```javascript
const apiKey = process.env.ANTHROPIC_API_KEY || 
               localStorage.getItem('apiKey');
const timeout = parseInt(process.env.TIMEOUT_CLAUDE) || 10000;
```

---

## 7. Ejemplo Completo

```javascript
// main.js - Flujo completo de valoración

async function runValuation() {
  const rc = '5882502VK4858S0015LE';
  const muni = 'Alcobendas';
  const prov = 'Madrid';
  const sup = 88;
  const hab = 3, ban = 2, planta = 3;
  const estado = 'buen_estado', ant = 'moderno';
  const garaje = 'si', ascensor = 'si';

  try {
    // 1. Catastro
    const catData = await fetchCatastroSOAP(rc);
    console.log('Catastro:', catData);
    
    // 2. INE
    const ineData = await fetchINE();
    console.log('INE:', ineData);
    
    // 3. Claude - Calibración
    const market = await calibratePrices(muni, prov, '', garaje, ascensor, ant, sup);
    console.log('Precios calibrados:', market);
    
    // 4. Aplicar descuentos
    let disc = {reformar: 0.13, buen_estado: 0.04, reformado: 0}[estado];
    if (garaje === 'si') disc -= 0.05;
    if (ascensor === 'no') disc += 0.06;
    
    const prices = {};
    for (const [name, pm2] of Object.entries(market.precios_m2)) {
      prices[name] = Math.round(sup * pm2 * (1 - disc));
    }
    console.log('Precios finales:', prices);
    
    // 5. Mediana
    const median = calculateMedian(Object.values(prices));
    console.log('Mediana:', median);
    
    // 6. Claude - Análisis
    const analysis = await aiAnalysis(
      rc, muni, sup, hab, ban, estado, planta, 
      ant, garaje, ascensor, prices, ineData, market
    );
    console.log('Análisis:', analysis);
    
    // 7. Renderizar resultados
    renderResults({
      catData, ineData, market, prices, median, analysis
    });
    
  } catch (err) {
    console.error('Error:', err);
    showError('Error en la valoración. Intenta de nuevo.');
  }
}

// Ejecutar
runValuation();
```

---

## Referencias

- [Catastro OVC](https://www.catastro.meh.es)
- [INE API](https://www.ine.es)
- [Anthropic API Docs](https://docs.anthropic.com)
- [MDN Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
