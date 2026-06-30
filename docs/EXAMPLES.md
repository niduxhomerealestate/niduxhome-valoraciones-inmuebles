# Ejemplos de Uso — PropValor Pro

## Ejemplo 1: Valoración Básica en Alcobendas

### Input
```javascript
{
  rc: '5882502VK4858S0015LE',
  municipio: 'Alcobendas',
  provincia: 'Madrid',
  superficie: 88,
  dormitorios: 3,
  banos: 2,
  planta: 3,
  antigüedad: 'moderno',
  estado: 'buen_estado',
  garaje: 'si',
  ascensor: 'si'
}
```

### Proceso

**Paso 1: Consulta Catastro OVC**
```json
{
  "sup": 88,
  "anio": 2001,
  "uso": "Residencial",
  "muni": "Alcobendas",
  "cp": "28100",
  "source": "live"
}
```

**Paso 2: Consulta INE**
```json
{
  "ipv": 185.3,
  "periodo": "2026Q3",
  "variacion_yoy": 11.5,
  "source": "live"
}
```

**Paso 3: Claude AI - Calibración de Precios**
```json
{
  "municipio": "Alcobendas",
  "provincia": "Madrid",
  "zona": "zona urbana consolidada",
  "precios_m2": {
    "RealAdvisor": 3200,
    "Idealista": 3939,
    "Tinsa": 4054,
    "BBVA_Valora": 3500,
    "Fotocasa": 3575,
    "Housfy": 3300,
    "Properfy": 3550
  },
  "variacion_anual_pct": 11.5,
  "alquiler_m2_mes": 16.1,
  "nota": "Mercado dinámico, demanda alta en zona residencial premium"
}
```

**Paso 4: Cálculo de Descuentos**
```
Estado: buen_estado = -4%
Garaje: sí = -5% (suma valor)
Ascensor: sí = ±0%
Descuento neto: -4% - 5% = -9% (suma 9% de valor)
```

**Paso 5: Valoraciones Finales**
```javascript
// RealAdvisor
€/m² base: 3200
€/m² final: 3200 × (1 - (-0.09)) = 3200 × 1.09 = 3488 €/m²
Precio total: 3488 × 88 = 306,944 €

// Idealista
€/m² base: 3939
€/m² final: 3939 × 1.09 = 4,293.51 €/m²
Precio total: 4293.51 × 88 = 377,829 €

// Tinsa
€/m² base: 4054
€/m² final: 4054 × 1.09 = 4,418.86 €/m²
Precio total: 4418.86 × 88 = 388,859 €

// BBVA_Valora
€/m² base: 3500
€/m² final: 3500 × 1.09 = 3815 €/m²
Precio total: 3815 × 88 = 335,720 €

// Fotocasa
€/m² base: 3575
€/m² final: 3575 × 1.09 = 3,896.75 €/m²
Precio total: 3896.75 × 88 = 342,914 €

// Housfy
€/m² base: 3300
€/m² final: 3300 × 1.09 = 3597 €/m²
Precio total: 3597 × 88 = 316,536 €

// Properfy
€/m² base: 3550
€/m² final: 3550 × 1.09 = 3,869.5 €/m²
Precio total: 3869.5 × 88 = 340,516 €
```

**Paso 6: Mediana**
```
Valores ordenados:
[306,944, 316,536, 335,720, 340,516, 342,914, 377,829, 388,859]
                                      ↑
                                   mediana

Mediana: 340,516 €
€/m² mediana: 340,516 / 88 = 3,869.73 €/m²
Horquilla: 306,944 € — 388,859 €
Dispersión: 388,859 / 306,944 = 1.27 (27%)
```

**Paso 7: Claude AI - Análisis**
```
Párrafo 1 - Valoración:
"La mediana de las 7 fuentes sitúa el valor en 340.500€ (3.870€/m²) 
para 88m² en Alcobendas — moderno, buen estado y con garaje. 
Horquilla realista: 306.000€ – 389.000€ tras aplicar correctores 
por estado y equipamientos."

Párrafo 2 - Dispersión:
"La dispersión es moderada (27% máximo). Idealista y Tinsa tienden 
a sobrevalorizar (+10–15%) por incluir márgenes de negociación en 
anuncios. RealAdvisor y Housfy son más conservadores pero altamente 
fiables según datos de transacciones reales."

Párrafo 3 - Factores:
"Estado constructivo: buen estado suma valor (+4%). Garaje y plaza 
suman +5%. Ascensor presente no penaliza. Antigüedad de 25 años 
(moderno, construida 2001) es favorable. Ubicación en Alcobendas 
(municipio premium Madrid norte) con demanda sostenida."

Párrafo 4 - Inversor:
"Rentabilidad bruta alquiler: 5.7% anual (16.1€/m²/mes × 12 / 340.500€). 
Post-reforma estimada: +15% → 391.600€. ROI atractivo para inversión 
residencial estándar. Margen de reforma: ~51.000€ potenciales."
```

### Output Final
```json
{
  "inmueble": {
    "rc": "5882502VK4858S0015LE",
    "municipio": "Alcobendas",
    "superficie": 88,
    "caracteristicas": "3dorm, 2baños, moderno, buen estado, con garaje"
  },
  "valoracion_consolidada": {
    "precio_mediana": 340500,
    "precio_m2": 3870,
    "minimo": 306944,
    "maximo": 388859,
    "dispersion": "27%"
  },
  "por_fuente": {
    "RealAdvisor": 306944,
    "Idealista": 377829,
    "Tinsa": 388859,
    "BBVA_Valora": 335720,
    "Fotocasa": 342914,
    "Housfy": 316536,
    "Properfy": 340516
  },
  "mercado": {
    "variacion_anual": "+11.5%",
    "alquiler_m2_mes": 16.1,
    "rentabilidad_bruta": "5.7%",
    "post_reforma": 391600
  },
  "analisis_ia": "..."
}
```

---

## Ejemplo 2: Inmueble a Reformar en Madrid Centro

### Input
```javascript
{
  rc: '2450106VK3825A0001RK',
  municipio: 'Madrid',
  provincia: 'Madrid',
  superficie: 75,
  dormitorios: 2,
  banos: 1,
  planta: 4,
  antigüedad: 'antiguo',
  estado: 'reformar',
  garaje: 'no',
  ascensor: 'no'
}
```

### Descuentos Aplicados
```
Estado: reformar = -13%
Garaje: no = ±0%
Ascensor: no = +6%
Descuento neto: -13% + 6% = -7% (suma 7% de valor)

Nota: -13% por reformar, pero +6% sin ascensor. 
Resultado neto: -7% (sigue siendo penalización pero menor).
```

### Precios Base (Madrid Centro - Barrio Premium)
```
RealAdvisor: 4,500 €/m²
Idealista: 5,200 €/m²
Tinsa: 5,100 €/m²
BBVA_Valora: 4,800 €/m²
Fotocasa: 4,900 €/m²
Housfy: 4,400 €/m²
Properfy: 4,950 €/m²
```

### Valuaciones Finales
```
Descuento combinado: -7% → multiplicador: 0.93

RealAdvisor: 4,500 × 0.93 × 75 = 313,500 €
Idealista: 5,200 × 0.93 × 75 = 362,700 €
Tinsa: 5,100 × 0.93 × 75 = 355,950 €
BBVA_Valora: 4,800 × 0.93 × 75 = 335,400 €
Fotocasa: 4,900 × 0.93 × 75 = 342,075 €
Housfy: 4,400 × 0.93 × 75 = 307,800 €
Properfy: 4,950 × 0.93 × 75 = 345,375 €

Mediana: 342,075 €
Horquilla: 307,800 € – 362,700 €
```

### Análisis Claude AI
```
"Piso 75m² en Madrid Centro (Barrio de las Letras) para reformar. 
Mediana: 342.075€ (4.561€/m²). La penalización por necesitar reforma 
(-13%) es parcialmente compensada por la ausencia de ascensor (+6%). 
Sin garaje es típico en centro histórico (no penaliza adicional).

Dispersión alta (18%): Idealista y Tinsa incluyen márgenes de 
especulación post-reforma. RealAdvisor más realista sobre coste 
actual de obra (200-300€/m² típicos en Madrid).

Factores clave: Ubicación premium (centro), conectividad excelente, 
potencial reformista alto. Antigüedad 70+ años requiere inspección 
estructural (posibles sorpresas).

INVERSOR: Pre-reforma: 342.075€. Post-reforma (+20% estimado): 410.490€. 
Margen reforma: ~68.000€ potenciales (menos costes obra ~15.000€). 
ROI reforma: ~68%. Rentabilidad alquiler pre-reforma: ~4.2% 
(bajo por estado). Post-reforma: ~7.8% (muy atractivo)."
```

---

## Ejemplo 3: Vivienda Nueva en Barcelona

### Input
```javascript
{
  rc: '7432101DF2873S0001OA',
  municipio: 'Barcelona',
  provincia: 'Barcelona',
  superficie: 90,
  dormitorios: 3,
  banos: 2,
  planta: 15,
  antigüedad: 'nuevo',
  estado: 'reformado',
  garaje: 'si',
  ascensor: 'si'
}
```

### Descuentos
```
Estado: reformado = ±0% (base sin ajuste)
Garaje: sí = -5% (suma valor)
Ascensor: sí = ±0%
Descuento neto: -5% (suma 5% de valor)
Multiplicador: 1.05
```

### Precios Base (Barcelona Zona Residencial)
```
RealAdvisor: 4,800 €/m²
Idealista: 5,500 €/m²
Tinsa: 5,300 €/m²
BBVA_Valora: 5,000 €/m²
Fotocasa: 5,100 €/m²
Housfy: 4,700 €/m²
Properfy: 5,200 €/m²
```

### Valuaciones Finales
```
Con multiplicador 1.05 (suma 5% por garaje):

RealAdvisor: 4,800 × 1.05 × 90 = 453,600 €
Idealista: 5,500 × 1.05 × 90 = 519,750 €
Tinsa: 5,300 × 1.05 × 90 = 499,350 €
BBVA_Valora: 5,000 × 1.05 × 90 = 472,500 €
Fotocasa: 5,100 × 1.05 × 90 = 481,950 €
Housfy: 4,700 × 1.05 × 90 = 444,150 €
Properfy: 5,200 × 1.05 × 90 = 490,800 €

Mediana: 481,950 €
€/m² mediana: 5,355 €/m²
```

### Análisis
```
"Obra nueva de 90m², 3 dormitorios, en Barcelona. Estado impecable, 
todas las características favorables: planta alta (15ª), ascensor, 
garaje incluido. Mediana: 481.950€ (5.355€/m²).

Mercado Barcelona 2026: demanda sostenida, crecimiento del 9% anual, 
alquiler: 22€/m²/mes (muy rentable).

Rentabilidad bruta: (22 × 90 × 12 / 481.950) = 4.9% (aceptable 
para obra nueva en zona premium).

Potencial: bajo margen reforma (ya está reforma), pero excelente 
como inversión pasiva de mediano/largo plazo. Ubicación consolidada, 
liquidez alta."
```

---

## Ejemplo 4: Fallo de APIs y Fallbacks

### Scenario: Catastro OVC No Responde

```javascript
// Catastro falla
try {
  const catData = await fetchCatastroREST(rc);
} catch (err) {
  console.warn('REST falló, intentando SOAP...');
  try {
    const catData = await fetchCatastroSOAP(rc);
  } catch (err2) {
    console.error('SOAP también falló, usando fallback');
    
    // Fallback: usar input del usuario
    const catData = {
      sup: inputSup || 88,
      anio: null,
      uso: 'Residencial',
      muni: inputMuni,
      cp: null,
      source: 'fallback'
    };
  }
}
```

### Scenario: Claude AI No Responde

```javascript
// Claude falla, usar precios hardcoded por municipio
const fallbackPrices = {
  'madrid': {
    precios_m2: {
      RealAdvisor: 4500,
      Idealista: 5200,
      Tinsa: 5100,
      BBVA_Valora: 4800,
      Fotocasa: 4900,
      Housfy: 4400,
      Properfy: 4950
    },
    variacion_anual_pct: 12,
    alquiler_m2_mes: 19.5,
    nota: 'Datos de referencia (Claude AI no disponible)'
  },
  'barcelona': {
    precios_m2: {
      RealAdvisor: 4800,
      Idealista: 5500,
      Tinsa: 5300,
      BBVA_Valora: 5000,
      Fotocasa: 5100,
      Housfy: 4700,
      Properfy: 5200
    },
    variacion_anual_pct: 9,
    alquiler_m2_mes: 22,
    nota: 'Datos de referencia (Claude AI no disponible)'
  }
};

const marketData = fallbackPrices[muni.toLowerCase()] || 
                   getFallbackGeneric();
```

---

## Validación de Inputs

### RC Válida ✓
```
5882502VK4858S0015LE
2450106VK3825A0001RK
7432101DF2873S0001OA
1234502YJ2720S0012AB
3308901TF3430N0001QF
```

### RC Inválida ✗
```
588250-VK4858S0015LE    (guión no permitido)
5882502VK4858S0015     (solo 19 caracteres)
5882502VK4858S0015LEX  (21 caracteres)
5882502-k4858s0015le   (caracteres especiales)
```

---

## Pruebas Manuales

### Test 1: Alcobendas
```
RC: 5882502VK4858S0015LE
Municipio: Alcobendas
Esperado: ✓ Catastro live, ~340.000€
```

### Test 2: Madrid Centro
```
RC: 2450106VK3825A0001RK
Municipio: Madrid
Esperado: ✓ Catastro live, ~342.000€
```

### Test 3: Barcelona
```
RC: 7432101DF2873S0001OA
Municipio: Barcelona
Esperado: ✓ Catastro live, ~481.000€
```

### Test 4: Municipio Desconocido (Fallback)
```
RC: [válida]
Municipio: Brihuega
Esperado: ⚠ Precios genéricos, nota "municipio desconocido"
```
