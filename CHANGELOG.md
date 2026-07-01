# Changelog — PropValor Pro

Todas las novedades significativas del proyecto.

## [1.0.0] — 2026-07-01

### ✨ Lanzamiento Oficial

**Características Core**:
- ✅ Sistema profesional de valoración inmobiliaria
- ✅ Integración Catastro OVC (REST + SOAP)
- ✅ Integración INE API
- ✅ Calibración con Claude AI Sonnet 4
- ✅ 7 fuentes AVM consolidadas (mediana robusta)
- ✅ Descuentos y correctores automáticos
- ✅ Análisis inteligente de 4 párrafos
- ✅ Gráficos comparativos animados
- ✅ Interfaz responsiva (desktop/tablet)

**Documentación**:
- ✅ README.md completo
- ✅ Metodología detallada (METODOLOGIA.md)
- ✅ Documentación de APIs (API.md)
- ✅ Ejemplos prácticos (EXAMPLES.md)
- ✅ Guía de instalación (DEPLOYMENT.md)
- ✅ Quick start (QUICKSTART.md)
- ✅ Roadmap hasta v4.0

**Deployment**:
- ✅ GitHub Pages
- ✅ Vercel ready
- ✅ Netlify ready
- ✅ Variables de entorno (.env.example)

**Equipo**:
- Nidux Home Real Estate

### 🐛 Bugs Conocidos

- Catastro OVC CORS bloqueado en algunos navegadores (mitiga con SOAP fallback)
- INE API ocasionalmente lenta (timeout a 5 seg)
- Claude AI tasa limitada en plan free (max 100 req/día típico)

### 📋 Testing

- Testeado en: Chrome, Firefox, Safari, Edge
- Ejemplos cargados: Alcobendas, Madrid, Barcelona, Valencia, Marbella
- Casos edge: municipios desconocidos, RCs inválidas, APIs caídas

---

## [1.1.0] — Próximamente (Q3 2026)

### Planeado

- [ ] Exportar a PDF profesional
- [ ] Exportar a Excel
- [ ] Historial local (localStorage)
- [ ] Favoritos/Marcadores
- [ ] Toggle modo oscuro/claro
- [ ] Más municipios (auto-expand)
- [ ] Búsqueda RC por dirección
- [ ] Comparar 2-3 inmuebles
- [ ] PWA (progressive web app)
- [ ] i18n (ES/EN)

---

## [2.0.0] — Próximamente (Q4 2026)

### Planeado

- [ ] Backend Node.js/Express
- [ ] BD PostgreSQL
- [ ] Autenticación JWT
- [ ] Historial sincronizado
- [ ] API REST pública
- [ ] Dashboard admin
- [ ] Estadísticas históricas
- [ ] Alertas de precios
- [ ] Planes SaaS (Stripe)

---

## [3.0.0] — Próximamente (2027)

### Planeado

- [ ] BI / Data warehouse
- [ ] Predicción de precios (ML)
- [ ] Mapas interactivos
- [ ] Análisis rentabilidad por zona
- [ ] Reportes automatizados
- [ ] Integraciones de portales
- [ ] Notificaciones web push
- [ ] Comparativas de barrios

---

## [4.0.0] — Próximamente (2027-2028)

### Planeado

- [ ] SaaS completo para agencias
- [ ] White label
- [ ] Integraciones CRM
- [ ] Multi-usuario / Roles
- [ ] Auditoría de cambios
- [ ] Soporte 24/7
- [ ] Certificaciones profesionales

---

## Notas Técnicas

### Dependencias
- **Frontend**: JavaScript Vanilla (sin dependencias)
- **APIs**: Catastro OVC, INE, Anthropic Claude
- **CDN**: Google Fonts

### Soporte de Navegadores
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

### Performance Baseline
- Carga inicial: <1s
- Valuación completa: 5-7s
- Mediana de 7 fuentes: <100ms
- Análisis Claude: 2-3s

---

## Changelog Anterior

*(No aplica, es v1.0.0)*

---

**Próxima revisión**: 2026-09-30  
**Mantenedor**: Nidux Home Real Estate  
**Repositorio**: https://github.com/niduxhomerealestate/niduxhome-valoraciones-inmuebles
