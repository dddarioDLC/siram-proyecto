# SIRAM — proyecto (diseño y dominio)

Este repo es el del **agente de diseño**: dominio, reglas de negocio, modelo de datos y lenguaje
visual, desde el backend hasta el front. No contiene código.

@CONTEXTO.md

## Reglas
- Leé §0 de CONTEXTO.md antes de empezar: el proyecto **no arranca de cero**.
- `recursos/` es solo lectura.
- `modelo/siram.mmd` es el contrato con el backend. Cada cambio se registra en `modelo/decisiones.md`;
  las dudas del backend llegan por `modelo/preguntas.md`.
- `../front` y `../back` son repos aparte (ver CONTEXTO §1). No se editan desde acá salvo que el
  usuario lo pida; lo que el front necesita saber se le entrega en `../front/docs/`.
  Si cambia `diseno/prototipo-siram.html` o el lenguaje visual, actualizá su copia ahí.
- No hagas commits salvo que el usuario lo pida.
