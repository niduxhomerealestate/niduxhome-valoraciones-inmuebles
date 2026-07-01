# Contribución al Proyecto — PropValor Pro

## 🤝 ¿Cómo Contribuir?

Nos encanta recibir contribuciones. Aquí te explicamos cómo:

### 1. Reportar Bugs

**Crear Issue**:
1. Ir a [GitHub Issues](https://github.com/niduxhomerealestate/niduxhome-valoraciones-inmuebles/issues)
2. Clic en "New Issue"
3. Seleccionar plantilla "Bug Report"
4. Completar:
   - Descripción del problema
   - Pasos para reproducir
   - Comportamiento esperado vs actual
   - Screenshots (si aplica)

**Ejemplo**:
```
Título: Catastro no responde para RC válida en Bilbao

Descripción:
Al introducir RC válida 1234567VK3825A0001RK en Bilbao,
el sistema muestra error CORS indefinidamente.

Pasos:
1. Abrir aplicación
2. Introducir RC: 1234567VK3825A0001RK
3. Municipio: Bilbao
4. Pulsar Valorar
5. Error no se resuelve después de 10 segundos

Esperado: Usar fallback SOAP automáticamente
Actual: Queda bloqueado
```

### 2. Proponer Mejoras

**Feature Request**:
1. Issues → "Feature Request"
2. Describir:
   - Qué problema resuelve
   - Por qué es importante
   - Casos de uso

**Ejemplo**:
```
Título: Agregar exportación a PDF

Descripción:
Usuarios quieren descargar reportes de valoración
como PDF profesional para compartir con clientes.

Por qué: Valor agregado para agencias inmobiliarias
```

### 3. Contribuir Código

**Workflow Git**:
```bash
# 1. Fork el repositorio
git clone https://github.com/TU_USERNAME/niduxhome-valoraciones-inmuebles.git
cd niduxhome-valoraciones-inmuebles

# 2. Crear rama feature
git checkout -b feature/nombre-descriptivo
# o
git checkout -b fix/descripcion-del-bug

# 3. Hacer cambios
# ... editar archivos ...

# 4. Commit con mensaje descriptivo
git add .
git commit -m "feat: agregar exportación PDF"
# o
git commit -m "fix: resolver issue #42 - CORS bloqueado"

# 5. Push a tu fork
git push origin feature/nombre-descriptivo

# 6. Crear Pull Request
# - Ir a GitHub
# - Clic en "New Pull Request"
# - Base: niduxhomerealestate/main
# - Compare: tu rama
# - Completar descripción
```

**Formato de Commits** (Conventional Commits):
```
feat:     Nueva característica
fix:      Corrección de bug
docs:     Cambios en documentación
style:    Formato, espacios, etc.
refactor: Refactorización sin cambios funcionales
test:     Tests
chore:    Tareas de mantenimiento

Ejemplo:
- feat: agregar soporte para exportar a Excel
- fix: resolver timeout en Catastro SOAP
- docs: actualizar guía de deployment
- refactor: modularizar funciones de cálculo
```

---

## 📋 Guía de Estilo

### JavaScript

```javascript
// ✓ Bien: funciones nombradas, comentarios claros
function calculateMedianPrice(prices) {
  // Ordenar y calcular mediana
  const sorted = [...prices].sort((a, b) => a - b);
  const mid = Math.floor(sorted.length / 2);
  return sorted.length % 2 
    ? sorted[mid] 
    : (sorted[mid - 1] + sorted[mid]) / 2;
}

// ✓ Bien: variables descriptivas
const discountByState = {
  'reformar': 0.13,
  'buen_estado': 0.04,
  'reformado': 0.0
};

// ✗ Mal: nombres ambiguos, sin comentarios
function calc(a) {
  return a.sort((x, y) => x - y)[Math.floor(a.length / 2)];
}

// ✗ Mal: magia sin explicación
const disc = {r: 0.13, b: 0.04, f: 0.0};
```

### HTML/CSS

```html
<!-- ✓ Bien: IDs/clases descriptivas -->
<div class="consolidated" id="consolidated">
  <div class="cons-price" id="cons-price">345.600€</div>
</div>

<!-- ✓ Bien: estructura semántica -->
<section class="form-panel">
  <label for="inp-rc">Referencia Catastral</label>
  <input id="inp-rc" type="text">
</section>

<!-- ✗ Mal: nombres genéricos -->
<div class="box" id="box1">
  <div class="text" id="t1">345.600€</div>
</div>
```

---

## 🧪 Testing

### Tests Manuales (v1.0)

```javascript
// docs/EXAMPLES.md tiene 4 casos de prueba

// Test 1: Alcobendas (normal)
RC: 5882502VK4858S0015LE
Municipio: Alcobendas
Resultado esperado: ~340.000€

// Test 2: Madrid Centro (a reformar)
RC: 2450106VK3825A0001RK
Municipio: Madrid
Resultado esperado: ~342.000€

// Test 3: Barcelona (obra nueva)
RC: 7432101DF2873S0001OA
Municipio: Barcelona
Resultado esperado: ~481.000€

// Test 4: Municipio desconocido (fallback)
RC: [válida]
Municipio: Brihuega
Resultado esperado: precios genéricos
```

### Casos Edge

```
1. RC inválida (19 caracteres)
   → Error: "Formato inválido"

2. RC válida pero no en catastro
   → Fallback SOAP
   → Si ambos fallan: usar input del usuario

3. Municipio vacío
   → Error: "El municipio es obligatorio"

4. Claude AI no responde
   → Usar fallbacks hardcoded por municipio

5. INE no responde
   → Usar IPV estimado (185.3 pts)
```

---

## 📝 Documentación

### Actualizar Documentación
Si cambias funcionalidad, actualiza:
1. README.md (si cambios principales)
2. docs/METODOLOGIA.md (si cambios en cálculos)
3. docs/API.md (si cambios en APIs)
4. docs/EXAMPLES.md (agregar ejemplos nuevos)

### Plantilla Documentación
```markdown
# Título Descriptivo

## ¿Qué es?
Explicación corta (1-2 párrafos)

## Características
- Feature 1
- Feature 2

## Ejemplo
\`\`\`javascript
// Código ejemplo
\`\`\`

## Referencia
- [Link 1](url)
- [Link 2](url)
```

---

## ✅ Checklist Pre-PR

Antes de hacer Pull Request, verifica:

- [ ] Código sigue guía de estilo
- [ ] Cambios testeados manualmente
- [ ] Documentación actualizada
- [ ] No hay archivos `.env` o secretos
- [ ] Commits con mensajes descriptivos
- [ ] Rama actualizada desde `main`
- [ ] Sin conflictos de merge

---

## 🎓 Recursos

- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [JavaScript Style Guide](https://google.github.io/styleguide/javascriptguide.html)
- [Markdown Guide](https://www.markdownguide.org/)

---

## 📞 Contacto

¿Preguntas?
- 📧 niduxhome@gmail.com
- 💬 GitHub Discussions
- 🐛 GitHub Issues

---

**¡Gracias por contribuir! 🙏**
