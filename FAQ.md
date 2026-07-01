# FAQ — PropValor Pro

## Preguntas Frecuentes

### ¿Cuál es el rango de precisión?

**R**: La mediana de 7 fuentes tiene precisión **±10–15%** vs transacciones reales en el mismo municipio y fecha. Factores que afectan:
- Antigüedad: clave importante
- Estado: reformas recientes no siempre visibles
- Ubicación específica: microzonas pueden variar ±20%
- Momento del mercado: volatilidad temporal

**Ejemplo**: Si mediana es 350.000€, rango real probablemente 297.500€ – 402.500€

---

### ¿Puedo subir mi propia RC?

**R**: Sí, total libertad. Pasos:
1. Introduce RC válida (20 caracteres alfanuméricos)
2. El sistema valida formato automáticamente
3. Consulta Catastro OVC en tiempo real
4. Si falla, usa SOAP fallback

---

### ¿Qué datos almacenan de mí?

**R**: 
- **Cliente (you)**: Nada se almacena sin tu consentimiento
- **Catastro OVC**: Solo consulta, no almacena
- **INE**: Datos públicos, sin identificación
- **Claude AI**: Respuesta textual generada, no guardada

Si usas con backend futuro: localStorage local.

---

### ¿Cuánto cuesta?

**R**: 
- **v1.0**: Gratis (frontend público)
- **v2.0+**: Se estudia modelo SaaS para agencias (previsto 2026-2027)

Costos operativos actuales: ~$0.003/valuación (Claude AI)

---

### ¿Qué municipios cubre?

**R**: 
- **Directos**: 40+ municipios (ver datalist en formulario)
- **Fallback**: Todos los municipios españoles (precios genéricos por provincia)
- **Limitación**: Catastro OVC cubre España peninsular + Canarias (Ceuta/Melilla parcial)

---

### ¿Las valoraciones son legales/válidas?

**R**: 
❌ **NO son tasaciones profesionales** (requieren colegiado TASOC)  
✓ **SÍ son indicativas** (orientación de mercado)  
✓ **Para**: ofertas, análisis inversor, referencias internas  
✗ **No para**: hipotecas, litigios, transacciones oficiales  

Disclaimer obligatorio en interfaz.

---

### ¿Cómo actualizo los precios?

**R**: Automático:
- **Catastro**: en tiempo real
- **INE**: datos mensuales (ej: junio 2026)
- **Claude AI**: calibra con datos 2025/2026
- **Cada consulta**: recalcula todo

No hay caché manual, siempre actual.

---

### ¿Funciona sin API key?

**R**: 
✓ **Catastro + INE**: Sí (APIs públicas)  
✗ **Claude AI**: No, requiere API key de Anthropic  

Sin Claude: muestra precios genéricos pero sin análisis inteligente.

---

### ¿Por qué varían tanto las fuentes?

**R**: Razones normales:
1. **Idealista**: incluye márgenes de negociación (+8%)
2. **Tinsa**: datos de tasaciones reales (más conservador)
3. **RealAdvisor**: algoritmo propio (a veces outlier)
4. **Housfy**: solo ventas últimos 6 meses (poca muestra)
5. **BBVA**: cruce estadístico de dos fuentes

Dispersión ±15% es normal. >25% revisa datos.

---

### ¿Qué hacer si hay error?

**R**: 
1. Revisar console del navegador (F12)
2. Intentar otra RC conocida
3. Comprobar conexión internet
4. Reportar en GitHub Issues con:
   - RC intentada
   - Municipio
   - Error exacto
   - Screenshot

---

### ¿Exportar a PDF?

**R**: 
- v1.0: Solo pantalla (print nativo de navegador)
- v1.1: Botón exportar PDF (previsto Q3 2026)
- Mientras: print a PDF desde navegador (Ctrl+P)

---

### ¿API para terceros?

**R**: 
- v1.0: No (frontend solo)
- v2.0: Sí (REST API planeada 2026)
- Contactar: niduxhome@gmail.com para beta

---

### ¿Datos históricos?

**R**: 
- v1.0: Solo valoración actual
- v2.0: Historial personal (próximamente)
- v3.0: Gráficos históricos por municipio

---

### ¿Móvil/App?

**R**: 
- Actualmente: Web responsive (funciona en móvil)
- Próximo: PWA offline (v1.1)
- Futuro: Apps nativa iOS/Android (estudio 2027)

---

### ¿Integración con Idealista/Fotocasa?

**R**: 
- v1.0: No (scraping prohibido)
- v2.0: APIs públicas de partners (en negociación)
- Contactar: niduxhome@gmail.com

---

### ¿Soporte técnico?

**R**: 
- 📧 Email: niduxhome@gmail.com
- 💬 GitHub Issues
- 📱 Redes sociales (@niduxhome)
- ⏱️ Respuesta típica: 24-48h

---

### ¿Cómo proponer mejoras?

**R**: 
1. GitHub Issues → "Feature Request"
2. Describir qué, por qué, cómo
3. Comunidad vota con 👍
4. Priorización según votos

---

### ¿Licencia?

**R**: 
- **Código**: Propietario (Nidux Home Real Estate)
- **Uso**: Libre en niduxhomerealestate.github.io
- **Fork**: Contactar para licencia comercial

---

### ¿Beta/Early Access?

**R**: 
- Suscribirse a newsletter (próximamente)
- O contactar directamente: niduxhome@gmail.com
- Beta v2.0: Q4 2026

---

## 🆘 No encuentro respuesta

📧 Escribe a niduxhome@gmail.com con:
- Tu pregunta clara
- Contexto (qué intentas hacer)
- Navegador/dispositivo
- Cualquier error/screenshot

**Respondemos en 24-48h**

---

**Última actualización**: 2026-07-01
