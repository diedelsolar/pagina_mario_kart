# Mario Kart World Cup — sitio del torneo

Sitio estático (HTML + CSS + JS puro, sin build). Son 3 archivos:

- `index.html` — la página, no hace falta tocarla casi nunca.
- `data.js` — **acá se edita todo**: resultados, calendario, estado de fase.
- `styles.css` — estilos visuales.

## 1. Publicarlo en Vercel (una sola vez)

**Repo ya creado:** https://github.com/diedelsolar/pagina_mario_kart

**Paso 1 — Subir el código**
1. Abrí la Terminal (Spotlight → "Terminal") y pará en esta carpeta:
   ```
   cd "/Users/diego/Library/CloudStorage/OneDrive-Entel/mario kart cup/pagina_web"
   ```
2. Inicializá el repo local y conectalo al de GitHub:
   ```
   git init
   git add .
   git commit -m "sitio inicial"
   git branch -M main
   ```
3. Conectar el remote. Si ya existe (error "remote origin already exists"), usá `set-url` en vez de `add`:
   ```
   git remote add origin https://github.com/diedelsolar/pagina_mario_kart.git
   ```
   o si ya existe:
   ```
   git remote set-url origin https://github.com/diedelsolar/pagina_mario_kart.git
   ```
4. Subir:
   ```
   git push -u origin main
   ```
   Te va a pedir usuario y contraseña — **GitHub ya no acepta contraseña**, hay que usar un token:
   - Andá a [github.com/settings/tokens](https://github.com/settings/tokens) → "Generate new token" → "Generate new token (classic)"
   - Nombre: `mario-kart-push`, marcá el casillero **repo**, "Generate token"
   - Copiá el token (`ghp_...`, se muestra una sola vez)
   - `Username`: `diedelsolar` · `Password`: pegá el token (no tu contraseña de GitHub)

   El Keychain de tu Mac guarda el token después del primer push, así que esto solo se hace una vez.

**Paso 2 — Conectar Vercel**
1. Andá a [vercel.com](https://vercel.com) y entrá con tu cuenta de GitHub.
2. Click en "Add New… → Project".
3. Elegí el repo que acabás de crear.
4. Como es un sitio estático, Vercel no necesita configuración (Framework: "Other", sin build command). Click en "Deploy".
5. En 1 minuto te da una URL tipo `mario-kart-world-cup.vercel.app`. Esa es la página pública.

Desde acá en adelante, **cada vez que hagas `git push`, Vercel republica solo** en segundos. No hay que volver a tocar nada en Vercel.

## 2. Actualizar resultados cada semana

Solo se edita `data.js`. Los pasos:

1. Abrí `data.js` con cualquier editor de texto.
2. Actualizá `"actualizado"` con la fecha del día.
3. Si cambia la fase del torneo, actualizá `"faseActual"`.
4. En `"calendario"`, marcá `"estado": "hecho"` en la semana que terminó y `"estado": "actual"` en la que empieza.
5. Para cargar los resultados de una copa/ronda, agregá un objeto dentro de `"rondas"`. Hay un ejemplo comentado al final del archivo — copialo y completalo con los gamertags reales.
6. Guardá el archivo y subilo:
   ```
   git add data.js
   git commit -m "resultados semana 3"
   git push
   ```
7. Esperá ~30 segundos y refrescá el sitio: ya va a estar actualizado para todos.

No hace falta tocar `index.html` ni `styles.css` para las actualizaciones semanales — el sitio lee todo desde `data.js` automáticamente.

## 3. Ver el sitio en tu computador antes de subirlo (opcional)

Como no usa ningún framework, alcanza con abrir `index.html` directo en el navegador (doble clic), o si preferís un servidor local:

```
cd "/Users/diego/Library/CloudStorage/OneDrive-Entel/mario kart cup/pagina_web"
python3 -m http.server 8000
```

y abrís `http://localhost:8000` en el navegador.

## Nota sobre los datos de clasificación

Se armó la tabla de "Fase 0" a partir de `Inscripcion_Mario_Kart_World_Cup.xlsx`, ordenando por tiempo (menor = mejor):

- **Chile**: 25 inscritos → se eliminaron los 5 peores tiempos (MatiF, zapa, bastiking, fer, alondra) → quedan 20 clasificados (5 copas de 4 en R1).
- **Perú**: 12 inscritos → se eliminaron los 3 peores tiempos (rex, Chrispy, valita) + 1 sin tiempo registrado (KioShiMa) → quedan 8 clasificados (2 copas de 4 en R1, sin pase directo).

Nota: **"Hurtado De Mendoza Diaz, Valeria Fernanda" aparece dos veces en el Excel** con gamertags distintos (`valita` y `YuGo`). Con el ajuste de arriba, `valita` quedó eliminada por tiempo y `YuGo` sigue clasificada, así que solo una de las dos entradas sigue en juego — ya no es un problema para el bracket.
