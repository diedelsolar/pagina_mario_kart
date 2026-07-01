# Mario Kart World Cup — sitio del torneo (Chile vs Perú)

Este archivo es para vos, Claude Code. Cuando Diego te pida "sube los resultados",
"actualiza el sitio", "publica los cambios" o algo similar en esta carpeta, seguí
las instrucciones de abajo sin pedirle que copie/pegue comandos — ejecutalos vos
directamente con la herramienta de shell.

## Qué es esto

Sitio estático (HTML + CSS + JS puro, sin build, sin frameworks). 4 archivos:

- `index.html` — la página. Casi nunca se toca.
- `styles.css` — estilos. Casi nunca se toca.
- `data.js` — **el único archivo que se edita normalmente**: resultados de
  clasificación, calendario, rondas del bracket.
- `README.md` — guía original en español para el usuario (Diego), enfocada en
  explicarle los pasos a mano. Este CLAUDE.md es la versión "hacelo vos" de lo
  mismo.

Repo de GitHub: **https://github.com/diedelsolar/pagina_mario_kart**
Deploy: Vercel, conectado a ese repo (auto-deploy en cada push a `main`).

## Tarea 1 — Publicar / sincronizar cambios (git)

Cuando Diego pida subir cambios (nuevos resultados, ajustes al sitio, etc.):

1. Pararte en esta carpeta (ya deberías estarlo si Claude Code se abrió acá).
2. Verificar estado y remote:
   ```
   git status
   git remote -v
   ```
   Si `origin` no apunta a `https://github.com/diedelsolar/pagina_mario_kart.git`,
   corregilo con:
   ```
   git remote set-url origin https://github.com/diedelsolar/pagina_mario_kart.git
   ```
3. Agregar, commitear y subir:
   ```
   git add -A
   git commit -m "<mensaje descriptivo, ej: resultados semana 3 Chile>"
   git push
   ```
   Si es el primer push del repo y la rama `main` no tiene upstream, usar:
   ```
   git push -u origin main
   ```
4. Si `git push` pide usuario/contraseña, avisale a Diego — significa que el
   token de acceso (Personal Access Token) de GitHub no está guardado en el
   Keychain. No intentes generarlo vos (requiere que Diego lo cree a mano en
   github.com/settings/tokens); simplemente pausá y pedile que lo autentique
   una vez desde la Terminal.
5. Vercel redeploya solo al detectar el push. No hace falta ningún comando de
   Vercel ni build step — es un sitio 100% estático.

## Tarea 2 — Actualizar resultados en `data.js`

Cuando Diego te pase resultados nuevos (de una copa, ronda, o cambios en el
calendario), editá `data.js` — nunca `index.html` (el sitio lee todo desde
`data.js` dinámicamente vía `<script src="data.js">`).

Estructura relevante del objeto `DATA` en `data.js`:

```js
const DATA = {
  "actualizado": "YYYY-MM-DD",     // actualizar SIEMPRE a la fecha del cambio
  "faseActual": "texto libre",     // actualizar si cambia la fase del torneo
  "resumen": {                     // recalcular si cambian clasificados/eliminados
    "chile": { "inscritos": N, "clasificados": N, "eliminados": N },
    "peru":  { "inscritos": N, "clasificados": N, "eliminados": N }
  },
  "calendario": [                  // una fila por semana
    { "sem": 1, "fecha": "jue 18 jun", "chile": "texto", "peru": "texto",
      "estado": "hecho" | "actual" | "pendiente" }
  ],
  "chile": [ /* array de jugadores, ver abajo */ ],
  "peru":  [ /* array de jugadores, ver abajo */ ],
  "rondas": [ /* resultados de bracket por ronda, ver abajo */ ]
};
```

Objeto de jugador dentro de `"chile"` / `"peru"` (clasificación Fase 0):

```js
{
  "rank": 1,                       // posición por tiempo, null si no tiene tiempo
  "nombre": "Nombre Apellido",
  "gamertag": "tag",
  "tiempo": 2.07,                  // formato M.SS como decimal, menor = mejor
  "estado": "Clasificado" | "Eliminado",
  "motivo": "",                    // motivo si fue eliminado, texto libre
  "tiempoFmt": "2:07"              // string M:SS para mostrar, null si no hay tiempo
}
```

Objeto de ronda dentro de `"rondas"` (bracket, se va llenando semana a semana):

```js
{
  "pais": "chile" | "peru",
  "nombre": "Principal R1",        // nombre de la ronda/fase
  "copas": [
    {
      "nombre": "Copa A",
      "resultados": [
        { "gamertag": "tag", "posicion": 1, "avanza": "Principal R2" }
        // "avanza" puede ser el nombre de la próxima ronda, "Eliminado",
        // "Final Nacional" o "Campeón"
      ]
    }
  ]
}
```

Reglas al editar:

- No rompas la sintaxis JSON del objeto `DATA` (comas, comillas dobles). Al
  terminar, corré `node -e "require('./data.js')"` no funciona directo porque
  es `const DATA = {...}` sin export — en su lugar validá con:
  ```
  node -e "eval(require('fs').readFileSync('data.js','utf8')); console.log('OK', DATA.chile.length, DATA.peru.length)"
  ```
  Si tira error, hay un problema de sintaxis: revisalo antes de commitear.
- Si agregás jugadores nuevos o cambiás estados, recalculá a mano los números
  de `"resumen"` (clasificados/eliminados) para que el contador de arriba del
  sitio no quede desactualizado.
- Guardá el criterio ya usado en la Fase 0: **menor tiempo = mejor**. Los
  jugadores sin tiempo registrado van con `"tiempo": null`, `"tiempoFmt": null`,
  `"rank": null` y `"estado": "Eliminado"` con motivo "Sin tiempo registrado".

## Tarea 3 — Verificar antes de subir

Antes de hacer `git push`, abrí el sitio localmente y confirmá que renderiza
sin errores:

```
python3 -m http.server 8000
```

y mirá `http://localhost:8000` — o simplemente validá `data.js` con el
comando de node de arriba, que alcanza para pescar errores de sintaxis.

## Contexto del torneo (por si hace falta para dar contexto a resultados)

- Formato: doble eliminación por país, rama Principal + rama Repechaje, copas
  de 4 carreras (top 2 avanza, bottom 2 baja).
- Fase 0 ya jugada: Chile 25 inscritos → 20 clasificados (5 peores tiempos
  eliminados). Perú 12 inscritos → 9 clasificados (2 peores tiempos + 1 sin
  tiempo eliminados).
- Nota pendiente: "Hurtado De Mendoza Diaz, Valeria Fernanda" (Perú) aparece
  dos veces con gamertags distintos (`valita` y `YuGo`), ambas clasificadas.
  Confirmar con Diego si es la misma persona antes de armar el bracket de
  Perú.
- Cierra con Gran Final Internacional online entre el campeón de Chile y el
  campeón de Perú.
