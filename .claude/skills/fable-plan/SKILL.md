---
name: fable-plan
description: Usar SIEMPRE antes de tocar código en nocta-studio cuando Diego pida una función, sección, mejora o cambio ("añade X", "quiero que la web haga Y", "cambia Z", "nueva sección", "mejora el FAQ/hero/portfolio"). Obliga a explorar el archivo, hacer las preguntas correctas y entregar un plan en pasos verificables ANTES de editar. No aplica a typos de una línea ni a cambios de texto puro.
---

# fable-plan — cómo planear una función en nocta-studio

Este proyecto es **dos archivos HTML monolíticos sin build, sin tests y sin
linter**. La única red de seguridad es entender el archivo antes de tocarlo y
mirar el resultado con ojos (los tuyos vía screenshot, los de Diego en su
móvil). Por eso aquí el plan pesa más que en un proyecto con test suite.

## 0. Anatomía (léela, no la asumas — las líneas derivan)

- **`index.html` (~805 líneas)** — la web entera:
  - Líneas 1-15: `<head>` con SEO (title, description, canonical, favicon SVG
    inline, fuentes Google, JSON-LD LocalBusiness). Si tu cambio añade/quita
    contenido relevante, este bloque también se toca.
  - Líneas ~16-320: CSS. Cada zona tiene un marcador `/* ── NOMBRE ── */`
    (TOKENS, NAV, HERO, MARQUEE, SERVICES, PORTFOLIO, WHY NOCTA, PROCESS, FAQ,
    CTA FINAL, FOOTER, WHATSAPP FLOAT, COOKIES). **Busca por marcador, no por
    número de línea.** Los tokens viven en `:root` (acento `--accent` lima
    #C6F432, fondos #080808/#111, Bebas Neue display + Outfit body +
    JetBrains Mono). Colores nuevos = token nuevo, nunca hex suelto.
  - Líneas ~321-739: HTML. Anclas estables: `#servicios`, `#portfolio`,
    `#por-que`, `#proceso`, `#contacto`, más nav/`#mobMenu`/hero/FAQ/`#ck`
    (cookies). El nav y el menú móvil enlazan a estas anclas: si añades o
    renombras una sección, hay TRES sitios que actualizar (nav desktop, menú
    móvil, la sección).
  - Líneas ~740-802: todo el JS: cursor custom (guardado con
    `matchMedia('(hover:hover)')`), barra de progreso, nav `.scrolled`,
    hamburguesa, `IntersectionObserver` de revelados, banner de cookies
    (`localStorage 'nocta-ck'`).
- **`cuestionario.html`** — formulario de captación. `<form>` POST a
  `formsubmit.co/contactonocta.studio@gmail.com`: los atributos `name=` de los
  campos SON el formato del email que recibe Diego. No renombrar a la ligera.
- **No hay más archivos.** Ni package.json, ni CSS/JS externos, ni /img (las
  fotos del portfolio hoy son de Unsplash — herencia; toda imagen NUEVA va
  local en webp).

### Qué leer primero según la zona que toques

| Pedido toca... | Lee antes de proponer |
|---|---|
| Estilo/color/tipografía | `:root` (TOKENS) + el bloque CSS de la zona por su marcador |
| Una sección existente | Su bloque CSS + su HTML + si sus elementos llevan `.rv`/`.rv-s` (revelado) |
| Sección nueva | Una sección hermana entera como plantilla + nav + `#mobMenu` |
| Cualquier cosa interactiva | El `<script>` completo (son ~60 líneas, léelo entero) |
| Formulario/captación | `cuestionario.html` completo + el CTA de `#contacto` |
| SEO/textos | `<head>` completo incluido el JSON-LD |

## 1. Preguntas obligatorias (responderlas TODAS en el plan)

1. **¿Cuál es el problema real detrás del pedido?** Diego pide soluciones
   ("pon un carrusel"); busca el problema ("el portfolio no se ve en móvil
   sin scrollear mucho"). El objetivo de esta web es UNO: que un dueño de
   negocio local escriba por WhatsApp. Todo cambio se juzga contra eso.
2. **¿Qué es lo más pequeño que lo resuelve?** En un monolito de 805 líneas
   (tope autoimpuesto: ~800), "pequeño" es literal: si el cambio mete >100
   líneas, el paso 0 del plan es separar `styles.css`/`main.js`.
3. **¿Qué se rompe con este cambio AQUÍ?** Checklist propia del proyecto:
   - ¿El elemento nuevo debe animarse al entrar? → clase `.rv` (o `.rv-s`)
     o aparecerá sin revelado, desentonando.
   - ¿Es interactivo? → el cursor custom solo reacciona a la lista
     `a,button,.faq-item,.port-card,.svc-card` del JS; añade tu selector o
     el hover se sentirá muerto en desktop.
   - ¿Es sección nueva? → nav desktop + menú móvil + ancla (3 sitios).
   - ¿CSS nuevo? → base móvil primero; lo de escritorio dentro de
     `@media(min-width:768px)`. Aquí no hay max-width queries.
   - ¿Toca el `<head>`? → title 50-60 chars, description 150-160, JSON-LD
     coherente.
   - ¿Menciona precios? → **decisión congelada: la web NO muestra precios de
     paquetes** (se pusieron y quitaron 3 veces en 2026). Cualquier pedido
     que los reintroduzca se confirma con Diego antes de planear.
   - ¿Toca el form del cuestionario? → los `name=` cambian el email recibido.
4. **¿Qué casos límite aplican?** Los de esta web son siempre los mismos:
   móvil 390px (mayoría del tráfico), táctil sin hover (el cursor custom no
   existe ahí), menú móvil abierto (bloquea scroll con `overflow:hidden`),
   JS de revelado: un elemento observado que ya está en viewport al cargar.
5. **¿Cómo verificamos que quedó?** Con los comandos de la sección 3 — cada
   paso del plan nombra el suyo.
6. **¿Qué NO vamos a hacer y por qué?** Explícito en el plan. Lo habitual que
   se descarta aquí: frameworks, build steps, librerías de animación para lo
   que resuelve IntersectionObserver, efectos que den tirones en móvil, y
   cualquier cosa "por si escala" — esta web se entrega, no escala.

## 2. Formato del plan

```
## Plan: <título corto>
Problema real: <1 frase>
Lo mínimo que lo resuelve: <1 frase>

Pasos (cada uno commiteable y reversible por sí solo):
1. <cambio concreto, zona por marcador> → verificar: <comando/mirada concreta>
2. ...
N. push + verificación en producción (comandos abajo)

No haremos: <lista con porqués>
Decisiones tomadas sin preguntar: <las que no cambiaban el plan, anotadas>
```

**La regla de las preguntas:** si una respuesta de Diego puede cambiar el plan
(¿precios sí/no? ¿esta sección reemplaza o se suma? ¿el texto lo escribe él?),
se pregunta ANTES de escribir el plan. Si no lo cambia (nombre de una clase,
orden de dos bloques CSS), se decide y se deja anotada en "Decisiones tomadas".
Y el plan se presenta a Diego antes de editar — él prefiere aprobar lo grande.

## 3. Verificación — los comandos reales

No hay build ni tests. La verificación es visual y en este orden:

1. **Local, antes de commitear** (permiso `start:*` ya concedido):
   ```powershell
   start C:\Users\bethe\desarrollo-web\nocta-studio\index.html
   ```
   Abre en el navegador de Diego. Para mirarlo tú mismo: screenshot con las
   herramientas de browser/Playwright disponibles, viewports 390px y 1440px.
2. **Commit por paso del plan**, en español, `feat:`/`fix:`/`chore:`:
   ```powershell
   git add -A; git commit -m "feat: <qué y dónde>"
   ```
3. **Push = deploy** (Vercel auto, repo `-nocta-studio` — sí, con guion, no es
   errata tuya):
   ```powershell
   git push
   ```
4. **Verificar PRODUCCIÓN el mismo día, no "ya lo miraré"** — el script de
   Diego saca screenshots reales de la URL viva (desktop 1440 y móvil 390,
   requiere Edge; `puppeteer-core` ya está en `~/node_modules`):
   ```powershell
   node C:\Users\bethe\desarrollo-web\check-web.js
   ```
   Luego **lee tú los PNG** (`nocta-check-desktop.png` y
   `nocta-check-mobile.png` en desarrollo-web) con la herramienta Read y
   describe a Diego lo que se ve. "Vercel dice deployed" no es verificación;
   este proyecto ya tiene un commit "forzar deploy v3" que lo recuerda.
5. **Ojo humano final:** Diego lo mira en su móvil. Avísale de qué debe mirar
   exactamente ("abre el FAQ, toca la 2ª pregunta").
