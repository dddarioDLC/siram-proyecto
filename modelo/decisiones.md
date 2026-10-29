# Decisiones del modelo de datos

Registro de cada cambio al modelo canónico (`siram.mmd`) y su porqué.
Lo mantiene el agente de diseño.

---

## 2026-10-08 · Versión 0 (línea base)

- Se toma como base el modelo de «De las pantallas al modelo» (`recursos/diseno/`), propuesto el
  05/10/2026. El boceto ER original del equipo queda superado.
- Se agregan los atributos de ORGANISMO (`nombre`, `tipo`) y USUARIO (`organismo_id`, `nombre`,
  `rol`) que figuraban solo en `recursos/modelo-er/siram-modelo-er.svg`.
- Brechas conocidas listadas en `CONTEXTO.md` §7. Próximo paso: incorporar los 19 campos de la
  matriz sin lugar propio y definir la representación de «No sabe».

## 2026-10-08 · Decisiones del equipo

- **Gatos dentro del alcance** (perros y gatos). Cierra HZ06. Sin cambio estructural: `ANIMAL.especie` ya los admite.
- **CASO ≠ DENUNCIA.** El caso lo registra SIRAM; la denuncia la hace una entidad externa y SIRAM solo
  la relaciona con un caso. Un caso tiene 0..N denuncias. La relación `DENUNCIA }o--o| CASO` ya lo
  expresa (un caso sin denuncias es válido). El «ABM de denuncias» del PDF se lee como ABM de casos.
- **DNI obligatorio.** `PERSONA.dni` deja de ser opcional. Una denuncia ciudadana puede llegar sin DNI
  (queda en `datos_crudos`), pero hay que obtenerlo antes de incorporarla a un caso.

## 2026-10-08 · Flujo caso → denuncia, borrado y DNI

- **Primero el caso, después la denuncia.** El agente registra el caso y luego le carga la denuncia.
  Se elimina `incorporada` de `DENUNCIA.estado`: queda `recibida|en_revision|vinculada|desestimada`.
- **No existe el borrado.** La baja de un caso es `CASO.estado = desestimado`.
- **DNI obligatorio en todo registro nuevo**, de cualquier origen. Solo los registros históricos pueden
  no tenerlo. `PERSONA.dni` lo anota; falta cómo distinguir un registro histórico (CONTEXTO §7.9).
- Pendiente: campo y lista de motivos de desestimación del caso (CONTEXTO §7.8).

## 2026-10-08 · Tres repos y quién conoce el modelo

- El proyecto se divide en tres repos hermanos dentro de `~/analisis/siram/`: `proyecto/` (este:
  diseño y dominio), `front/` y `back/`. Ninguno contiene a otro; la carpeta contenedora no es repo.
- **El modelo solo lo consume el backend**, en una versión fija (tags `modelo-vN` en este repo).
- **El front no conoce el modelo**: trabaja contra la API (OpenAPI generada por el back) y recibe el
  lenguaje visual y el prototipo en `front/docs/`.
- Las preguntas de `preguntas.md` pasan a ser del agente de backend.

## 2026-10-08 · Versión 1: SIRAM trata casos

Narrativa acordada por el equipo (CONTEXTO §3, «Narrativa de uso»): el caso lo registra siempre un
agente interno, que carga los datos por la persona; la denuncia es un acto oficial del mundo real
ante un ente (Zoonosis, policía…) y SIRAM solo la anota en un caso que ya existe. El formulario
público queda en suspenso.

- **DENUNCIA pasa a ser un dato del caso.** `caso_id` obligatorio (`CASO ||--o{ DENUNCIA`). Se agregan
  `ente`, `numero`, `fecha` (H0, cierra la brecha §7.3), `denunciante_persona_id`, `anotada_en`.
  Se eliminan `origen`, `organismo_id`, `recibida_en`, `estado`, `motivo_desestimacion`,
  `observacion_revision` y `datos_crudos`: describían denuncias que llegaban solas y se revisaban en
  una cola, y eso ya no existe. Se elimina la relación ORGANISMO–DENUNCIA (quién la anotó sale de
  USUARIO). No tiene identificador visible propio (`D-…`).
- **CASO.hizo_denuncia** (`si|no`, nulo = sin dato, RN-23) y **CASO.motivo_no_denuncia** (D3).
  D1 «denunció en Zoonosis» y D2 «denuncia policial» no son campos: son denuncias anotadas con su ente.
- **CASO.estado:** se eliminan `sin_confirmar` y `confirmado`; el caso nace `registrado`. Confirmar
  tenía sentido cuando cualquiera podía cargar un caso desde el formulario público.
- **RN-28:** las estadísticas cuentan los casos vigentes (sin `desestimado` ni `unificado`).
- La entrada «DNI obligatorio» anterior decía que una denuncia ciudadana podía llegar sin DNI:
  queda sin efecto junto con el canal ciudadano.
- **`TIPO_DENUNCIA` → `TIPO_EVENTO`.** «Denuncia» ya nombra otra cosa (el trámite oficial); el
  catálogo clasifica el evento. La «T» de la matriz ya significaba «Tipo de evento». Valores
  propuestos y su deslinde con el tipo de lesión: CONTEXTO §9.

## 2026-10-23 · Redefinición del dominio (hacia la versión 2)

`siram.mmd` **no cambia todavía**: faltan definir DENUNCIA, COINCIDENCIA y los datos de cada ente, y
no conviene pasarle al back un contrato a medio cerrar. Lo acordado, para volcar en la v2
(detalle y porqué en CONTEXTO §3 y RN-20, RN-22, RN-27, RN-30 a RN-34):

- **SOLICITUD** (nueva): lo que declaró un ciudadano, tal cual, en una tabla de staging. Estados
  `pendiente|descartada|registrada`; referencia al caso (CASO 1 — 0..N SOLICITUD; registrada ⇔ tiene
  caso); fechas de envío y de resolución; token del formulario detallado con vencimiento. Nunca
  escribe en PERSONA ni en CASO sin un agente. Sin historial. Borrado lógico.
- **CASO:** `canal_origen` (`ciudadano|salud|zoonosis|cadic`), que define la obligatoriedad al nacer
  (RN-30). Ubicación: punto, dirección y referencias (cierra §7.6). Vuelve un campo de origen, que la
  v1 había eliminado de DENUNCIA: ahora es del caso, no de la denuncia.
- **PERSONA:** única por DNI (clave sustituta + `dni` único, nulo solo en históricos).
- **DUEÑO:** relación opcional ANIMAL → PERSONA. No «denunciado».
- **AUDITORIA → historial por entidad:** quién, organismo, cuándo, valor anterior y nuevo; la fila
  viva es la última versión.
- **Atención de Salud** (nueva): fecha de consulta, antirrábica, antitetánica, zona de mordedura,
  antibiótico, sutura, observaciones; ligada a la víctima del caso (PARTICIPACION), no a PERSONA.
  Absorbe L5–L7.
- **Todo registro:** marca de tiempo de creación automática.
- **DENUNCIA:** sin cambios hasta saber cómo la guardan los entes. **COINCIDENCIA:** en revisión
  (motor nuevo).

## 2026-10-28 · Pizarrón del equipo (hacia la versión 2)

Fuente: `recursos/pizarron-2026-10-28/` (dos iteraciones). `siram.mmd` sigue sin cambiar. Detalle
en CONTEXTO RN-20, RN-22 y RN-34.

- **Muere PARTICIPACION.** Como hay una sola víctima por caso, CASO tiene `victima_persona_id`
  (obligatoria) y `contacto_persona_id` (opcional, «contacto responsable»: quien registró en nombre
  de la víctima). Se elimina `vinculo`: la relación entre ambos no importa. El denunciante oficial
  no es un rol: es el DNI anotado en la denuncia. Reemplaza lo dicho el 23/10 sobre ligar el
  tratamiento a «la víctima del caso (PARTICIPACION)».
- **TRATAMIENTO** (Salud) se liga a CASO.
- **ANIMAL pasa a tener identidad** (especie, chip). Se invierte RN-22. Relación N:M con CASO
  mediante **CASO_ANIMAL**, que guarda lo que varía por caso: dueño durante el caso, tenencia (A5),
  vacunado, observación del día 10. El DUEÑO del 23/10 (relación ANIMAL → PERSONA) pasa a ser
  `CASO_ANIMAL.dueno_persona_id`, porque el dueño puede cambiar entre casos.
- **DESCRIPCIÓN** (nueva, 1:1 con CASO_ANIMAL): rasgos físicos. Separada por opcionalidad en bloque,
  ciclo de vida y cohesión; no por tamaño de tabla ni por permisos (se resuelven por campo en la API).
- **Reconciliación de animales:** dos ANIMAL que resultan ser el mismo se unifican (mismo patrón que
  `CASO.unificado_en_caso_id`); los casos apuntan al que sobrevive. Detalle del histórico a definir.
- **Abierto:** certificado médico (¿entidad o solo archivo?, §7.4), A6 y A10 de la matriz, y si un
  caso con varios animales finaliza cuando todos pasan el día 10.

## 2026-10-29 · Historial y reconciliación de animales (hacia la versión 2)

`siram.mmd` sigue sin cambiar. Detalle en CONTEXTO RN-22, RN-27 y RN-35.

- **Historial = una tabla espejo por entidad** (`<ENTIDAD>_HISTORICO`), en lugar de AUDITORIA. Cada
  fila es una foto completa del registro más `operacion` (alta, modificación, baja lógica),
  `fecha_hora`, `usuario_id` (nulo = Sistema), `organismo_id` (el del usuario **en ese momento**,
  copiado porque el usuario puede cambiar de organismo) y `caso_contexto_id` (opcional: el cambio
  puede hacerse fuera de un caso, p. ej. desde la vista de la persona). El delta anterior/nuevo se
  deriva comparando fotos. En Django, `django-simple-history` implementa el patrón.
- **ANIMAL:** `chip` único y opcional; nuevos `vigente` y `absorbido_por_animal_id` (FK a ANIMAL),
  mismo patrón que `CASO.unificado_en_caso_id`. Sobrevive el que tiene chip; si ninguno, el más
  viejo. Absorber repunta `CASO_ANIMAL.animal_id`; nada se borra ni se vacía.
- **Permisos:** por fila y por campo, en el servidor (RN-35). No cambia el modelo.
