# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Proyecto

Clon de Asteroids en HTML5 Canvas y JavaScript puro. Sin dependencias, sin bundler, sin paso de build, sin tests y sin linter. El README y los textos del juego están en español; mantén en español las cadenas de la interfaz y los comentarios para ser consistente.

## Cómo correr

Abre `index.html` en el navegador, o sirve el proyecto localmente con `npx serve .` (http://localhost:3000). Recarga la página para ver los cambios.

## Arquitectura

Todo vive en `game.js` (cargado desde `index.html` con un `<script>` simple; el canvas es fijo de 800x600 y las constantes `W`/`H` deben coincidir con los atributos de `<canvas>`).

- **Entidades** (`Ship`, `Asteroid`, `Bullet`, `Particle`): cada clase tiene `update(dt)` y `draw()`, y dibuja directamente en el `ctx` global. Se eliminan mediante la bandera `dead`; los arreglos se filtran en cada frame (no se usa `splice` en el lugar).
- **Estado global del juego**: variables `let` a nivel de módulo (`ship`, `bullets`, `asteroids`, `particles`, `score`, `lives`, `level`, `state`, `deadTimer`). `state` es `'playing' | 'dead' | 'gameover'`; `update()` ramifica primero según este valor. `initGame()` reinicia todo; `nextLevel()` se dispara cuando `asteroids` queda vacío.
- **Loop**: `requestAnimationFrame` -> `update(dt)` + `draw()`; `dt` está en segundos y limitado a 0.05.
- **Input**: `keys[code]` guarda las teclas mantenidas; `pressed(code)` devuelve la pulsación única (consume la bandera). El disparo usa `pressed('Space')` (sin autodisparo); el movimiento usa `keys`.
- **Mundo toroidal**: las posiciones se envuelven con `wrap(v, max)`. Las colisiones usan la distancia simple entre centros (`dist`), sin considerar el envolvimiento de bordes.
- **Tamaños de asteroide**: del 1 al 3, con tablas por tamaño `RADII`, `SPEEDS` y `POINTS` indexadas por tamaño (el índice 0 no se usa). `split()` produce dos asteroides de tamaño `size - 1`.
- La colisión de la nave usa `a.radius * 0.82`; la nave es invulnerable mientras `ship.invincible > 0` (esto también controla el parpadeo en `Ship.draw`).

Nota: el README menciona power-ups y un tipo de asteroide "estrella fugaz", pero no están implementados en `game.js`.

## Git

Remoto: `origin` = https://github.com/stephenlucic/asteorides-js.git

- **Ramas**: `main` siempre estable, sin commits directos. Cambios en `feat/<tema>`, `fix/<tema>`, `docs/<tema>` o `chore/<tema>`.
- **Commits**: Conventional Commits en español, imperativo y corto. Ej: `fix: declara keys y justPressed`.
- **Integración**: sin PR. Con el cambio probado en el navegador, `git switch main && git merge --squash <rama>`, commit, `git push` y borrar la rama (local y remota). Sin preguntar cada vez.
- **Commits**: nunca agregar `Co-Authored-By` ni atribución de Claude.
- **Versiones**: tags semver anotados (`v0.1.0`, `v0.2.0`) al cerrar hitos; `git push --tags`.
