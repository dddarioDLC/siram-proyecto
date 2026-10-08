# SIRAM — Contexto del proyecto

> Fuente de verdad del dominio de SIRAM. La leen el agente de diseño y el de backend; el front recibe
> solo lo que necesita en `front/docs/` (ver §1).
> Última actualización: 2026-10-08. Lo mantiene el agente de diseño (ver §1).

---

## 0. Directiva: este proyecto NO arranca de cero

Antes de escribir una línea de código o proponer una pantalla, asumí esto:

1. **El dominio ya está relevado y destilado.** Hay requerimientos (R.01–R.31), una matriz
   de 50 campos que compara los formularios reales, 16 hallazgos (HZ01–HZ16), un modelo de
   datos y un prototipo navegable. Lo que sigue en este documento es el resultado de ese trabajo.
   **No vuelvas a relevar ni a reinventar lo que ya está decidido acá.**
2. **El lenguaje visual ya existe.** Se deriva del módulo `modulos/servicios-publicos` de
   **Delta**, el backoffice React de la Municipalidad de Ushuaia (ver §8). SIRAM hereda su
   *lenguaje visual*. Sus *paquetes funcionales* (componentes, queries, formularios, ventanas) son
   **referencia de solo lectura**: se estudian para escribir **componentes propios** de SIRAM, pero no
   se instalan ni se importan. SIRAM tiene repo propio y no vive en el monorepo de Delta.
3. **El prototipo es la referencia de comportamiento.** `diseno/prototipo-siram.html` (copia en revisión del original de `recursos/`)
   muestra cómo se ven y se comportan las pantallas principales, con datos de ejemplo realistas.
   Abrilo en un navegador antes de implementar cualquier pantalla.
4. **El modelo de datos es una propuesta avanzada, no terminada.** `modelo/siram.mmd` es la
   línea base; tiene brechas conocidas (§7) que se están cerrando. Puede cambiar.
5. **Si algo de acá contradice lo que ibas a hacer, gana este documento.** Si creés que este
   documento está mal, no lo ignores: anotalo en `modelo/preguntas.md`.

---

## 1. Organización del trabajo

Tres repos, uno al lado del otro, cada uno con su propio git. **Ninguno contiene a otro.**

```
~/analisis/siram/
├── proyecto/   ← este repo: diseño, dominio, reglas de negocio, modelo (sin código)
├── front/      ← React + Vite
└── back/       ← Django REST (el repo ya existe; todavía no está clonado acá)
```

La carpeta `~/analisis/siram/` es solo un contenedor: **no es repo ni tiene `CLAUDE.md`**. Así, una
sesión abierta en `front/` o `back/` no carga este documento.

| Rol | Sesión en | Es dueño de | Conoce |
|---|---|---|---|
| **Agente de diseño** (narrativa, dominio, modelo) | `proyecto/` | `CONTEXTO.md`, `modelo/`, `diseno/` | todo |
| **Agente de backend** | `back/` | el código Django, su esquema y la API | el modelo (`modelo/siram.mmd`, en una versión fija) |
| **Agente de front** | `front/` | el código React | **la API y el lenguaje visual. No conoce el modelo.** |

**Contratos:**
- **proyecto → back:** `modelo/siram.mmd`, versionado con tags (`modelo-v0`, `modelo-v1`, …). El back
  declara qué versión implementa y la traduce a Django sin cambiar su significado. Si algo no
  cierra, lo anota en `modelo/preguntas.md`; el agente de diseño responde actualizando el modelo
  y registrando el porqué en `modelo/decisiones.md`.
- **back → front:** la API REST, descripta en OpenAPI y generada por el back. Las dudas del front
  sobre datos van al back, no a este repo.
- **proyecto → front:** el lenguaje visual, la navegación y el prototipo, entregados en
  `front/docs/` (los mantiene el agente de diseño). Hasta que exista la API, el front trabaja con
  datos de ejemplo con la forma del prototipo.
- `recursos/` es **material fuente de solo lectura**. No se edita.

---

## 2. Qué es SIRAM

**Sistema Integrado de Registro de Ataques y Mordeduras.** Registro provincial único de
casos de ataques y mordeduras de **perros y gatos** a personas en Tierra del Fuego.

- **El problema.** Salud registra mordeduras cuando la persona va a la guardia; los municipios
  reciben denuncias y después las comparten con Salud. Hay **subregistro** (ataques sin lesión,
  gente que solo denuncia en el municipio), **duplicados** (el mismo caso en dos organismos) y
  **registros incompletos** que requieren seguimiento. Eso impide evaluar las políticas de
  control de perros sueltos.
- **La propuesta.** Un registro único que integra fuentes, detecta duplicados, sigue los casos
  incompletos y produce información epidemiológica (distribución espacial, franjas etarias,
  condiciones del ataque).
- **No reemplaza** los sistemas actuales de cada organismo: es el espacio común.
- **Clientes:** CADIC, Dirección de Zoonosis (Municipalidad de Ushuaia), UNTDF, Ministerio de
  Salud de TDF. Vinculado al Comité de Seguimiento del Plan de Manejo de Perros.
  **Referente:** Adrián Schiavini.
- **Contexto académico:** Laboratorio de Desarrollo de Software, UNTDF, curso 2026, grupo 5
  (Acosta, Cabrera, Iraola, Marchisoney, Valle). Metodología Scrum.
- **Stack (RT.01–RT.08):** React + Vite · Python/Django con API REST · PostgreSQL · Docker ·
  tecnología geográfica (a definir; PostGIS es el candidato natural) · HTTPS/TLS obligatorio.

---

## 3. Estado del proyecto

| Fecha | Hito |
|---|---|
| 14/08/2026 | 1ª entrega: propuesta |
| 28/08/2026 | 2ª entrega: requerimientos draft (10 subsistemas) |
| 02–03/09/2026 | Entrevista 1 con Adrián Schiavini (su resultado está destilado en este documento) |
| 11/09/2026 | 3ª entrega: requerimientos (`recursos/requerimientos/SIRAM.pdf`) |
| 18/09/2026 | 4ª entrega: análisis de requerimientos |
| 22/09/2026 | Matriz de comparación de formularios F1 vs F2 + hallazgos HZ01–HZ16 |
| 25/09/2026 | Lenguaje de diseño «De Delta a SIRAM», prototipo navegable, modelo ER rearmado desde las pantallas |
| 05/10/2026 | «De las pantallas al modelo» (modelo completo en Mermaid) |
| 08/10/2026 | Se habilita el código. Etapa actual: **cerrar el modelo de datos** antes de implementar |
| 08/10/2026 | Pizarrón del equipo: qué propone SIRAM, qué falta resolver, dudas técnicas y de dominio (abajo) |

**Qué propone SIRAM** (la respuesta a la necesidad, contada como narrativa de uso con ejemplos prácticos):
- Seguir un caso a través de sus estados.
- Sacar un caso del registro: desestimarlo si es falso, o consolidarlo si es un duplicado (RN-14).
- Relacionar casos: duplicado (el mismo incidente cargado dos veces) o evento relacionado (mismo animal).
- Ver un caso.
- Ver estadísticas (revisando el diseño propuesto).

SIRAM tiene dos caras: el **formulario público** (para todos) y el **backoffice** (solo personal interno).

**Qué falta resolver para dar esas respuestas:** cómo detectar duplicados · cómo resolverlos ·
la sección de casos por validar · la línea de tiempo de auditoría · el panel de gráficos de resumen
(qué datos le sirven más al usuario) · el formulario público · la carga de CSV.

> El pizarrón es una explicación semiformal, anterior a la terminología de este documento: donde dice
> «desestimar (falso o duplicado)» o «relacionar (repetido, mismo atacante)» se lee como arriba.

**Diseñado en el prototipo:** bandeja de casos, ficha del caso (50 campos + auditoría),
comparar registros (duplicado vs. relacionado), cola de pendientes de validación, inicio con
panel epidemiológico.
**En curso:** panel con gráficos (R.30). **Pendiente:** mapa (R.22), importación CSV (R.25).
**Fuera del primer sprint:** el formulario de denuncia del canal ciudadano (R.17). Sigue siendo parte
de SIRAM y está en «qué falta resolver»: queda pendiente para mostrar, diseñar y codear en sprints
posteriores. Su contenido se mantiene; **está en revisión si el formulario es público o privado** (§9).

---

## 4. Universo del discurso (glosario)

| Término | Significado en SIRAM |
|---|---|
| **Caso** | El **incidente** que SIRAM registra y gestiona (`C-2026-0418`). Lo registra un agente. **Puede tener o no denuncias asociadas.** Es lo que se sigue, se completa y se cuenta en las estadísticas. |
| **Denuncia** | Un acto **hecho por una entidad externa** a SIRAM (un ciudadano, la ventanilla de Zoonosis, la policía…), tal como llegó. Tiene identidad propia (`D-2026-0091`). SIRAM no la crea ni la gestiona: **la relaciona con un caso**. **Una denuncia no es un caso.** |
| **Registro unificado** | El caso que sobrevive a una consolidación de duplicados, conservando la trazabilidad de los originales. |
| **Persona** | Un ser humano con sus datos (nombre, **DNI obligatorio**, edad, contacto, domicilio). Independiente de su rol. |
| **Participación** | El rol de una persona en un caso: **víctima** (el lesionado) o **denunciante** (quien hace la denuncia, p. ej. la madre por la hija mordida), con su vínculo. |
| **Coincidencia** | La sospecha, generada por el motor, de que dos casos están relacionados. Guarda tipo, puntaje 0–100, señales y la decisión del agente. |
| **Posible duplicado** | Coincidencia donde **coincide la persona**: el mismo incidente cargado dos veces. |
| **Evento relacionado** | Coincidencia donde coinciden lugar, fecha y animal pero **las personas son distintas**: dos víctimas del mismo animal. |
| **Consolidar** | Fundir dos casos duplicados en uno. **Irreversible.** |
| **Vincular** | Enlazar dos casos como hermanos sin fundirlos. **Reversible.** |
| **Marca** | Señal paralela al estado (el «semáforo»): incompleto, posible duplicado, posible evento relacionado. |
| **Motivo de espera** | Por qué un caso en seguimiento está detenido: contacto, respuesta o certificado. |
| **Origen** | De dónde vino una denuncia: `organismo`, `ciudadano` o `csv`. |
| **Organismo** | Ente participante (Zoonosis Ushuaia, Hospital Regional, CADIC, etc.). En SIRAM es un **atributo del dato**, nunca una marca visual del sistema. |
| **ADC** | Animal Doméstico de Compañía (término del formulario de Zoonosis, O.M. 4800). |
| **F1 / F2 / F3** | F1 = formulario público online (Schiavini, «formulario 2025»). F2 = formulario en papel de ventanilla de Zoonosis Ushuaia. F3 = pendiente (probablemente guardias de Salud). |
| **Códigos P·H·T·X·A·L·D** | Identificadores de los 50 campos de la matriz: Persona, Hecho, Tipo de evento, Documentación, Animal, Lesión y atención, Denuncia y seguimiento. Ej.: `A12` = mordedor reincidente. |
| **HZ01–HZ16** | Hallazgos de la comparación de formularios (hoja «Hallazgos» de la planilla). |
| **Fecha del hecho / de registro** | Distintas. La del hecho es cuándo ocurrió el ataque; la de registro, cuándo entró al sistema. |

---

## 5. Actores

| Actor | Qué hace |
|---|---|
| **Administrador del sistema** | ABM de usuarios, permisos y asociación a organismo. Copias de seguridad. Solo administración de accesos. |
| **Agente interno** | Personal de un organismo. Registra, completa, valida y da seguimiento a casos; revisa coincidencias y denuncias ciudadanas. Sus permisos dependen de organismo + rol. |
| **Usuario externo** | Ciudadano sin cuenta. Hace una denuncia por formulario web/QR; entra como **propuesta** pendiente de verificación. |
| **Analista / Comité** | Consulta, filtra y exporta (R.20, R.21). Aparece en los requerimientos sin definición formal. |
| **Sistema** | Actor automático: importa lotes, marca coincidencias. Aparece en la auditoría como «Sistema». |

---

## 6. Reglas de negocio

Cada regla indica su fuente. **R.xx** = requerimiento del PDF · **E1** = Entrevista 1 ·
**HZxx** = hallazgo · **DS** = decisión de diseño («De Delta a SIRAM» / «De las pantallas al modelo») ·
**P** = comportamiento del prototipo.

### Identidad y alcance
- **RN-01 · Ningún organismo domina.** SIRAM es tierra compartida. El organismo aparece como
  atributo del dato (chip, columna, filtro), nunca como marca del contenedor. *(DS)*
- **RN-02 · Alcance del evento.** Ataques o intentos de ataque de **perros y gatos** (ambos dentro del
  alcance, decisión 2026-10-08) **hacia personas**. Se
  excluyen mordeduras entre animales, ataques a ganado y lesiones por fauna silvestre. *(Restricciones del PDF)*
  El importador descarta los tipos de denuncia fuera de alcance del F2 («Canes sueltos,
  agresivos», «Ataque de ADC a otro ADC»). *(HZ03)*
- **RN-03 · Ataque sin lesión y lesión derivada son registrables** (p. ej. caída al huir). *(E1, HZ04)*
- **RN-04 · No es canal de emergencias, ni red social, ni búsqueda de mascotas, ni trámite
  judicial.** Es registro e indicador epidemiológico. *(Restricciones del PDF)*

### Denuncia y caso
- **RN-05 · CASO ≠ DENUNCIA. No son lo mismo.** *(decisión del equipo, 2026-10-08)*
  - El **caso** es lo que SIRAM registra: el incidente. Lo da de alta un agente.
  - La **denuncia** la hace una entidad externa. SIRAM solo **relaciona denuncias existentes con casos**.
  - Un caso puede tener **cero, una o varias** denuncias. Un caso sin denuncia es válido.
  - Donde los requerimientos del PDF dicen «ABM de denuncias» (función 4, subsistema 4), en el
    vocabulario actual se lee **ABM de casos**. El PDF es anterior a esta distinción.
- **RN-06 · Primero el caso, después la denuncia.** *(decisión del equipo, 2026-10-08)* El agente
  registra el caso y **luego le carga** la denuncia. Nunca se crea un caso «desde» una denuncia.
  Al revisar una denuncia pendiente, el agente puede:
  - **Vincularla a un caso** → a uno existente, o a uno que registra en ese momento (primero el caso, después el vínculo).
  - **Desestimarla** → queda archivada sin caso asociado. **El motivo es obligatorio**
    (prototipo: fuera de alcance, duplicado exacto, datos insuficientes, no corresponde a la provincia).
  En ambos casos se guarda una observación de revisión con usuario y fecha.
- **RN-06b · En SIRAM no existe el borrado.** Ni de casos ni de denuncias. La «baja» de un caso es el
  estado **Desestimado**; la de una denuncia, desestimarla. Todo queda con su rastro. *(decisión del equipo, 2026-10-08)*
- **RN-07 · Las denuncias ciudadanas entran como propuesta.** Un agente autorizado las revisa,
  puede completarlas o corregirlas, y el resultado de la verificación queda registrado. *(R.10)*
  En la pantalla de validación se muestra **lo que declaró la persona, sin corregir**. *(P)*
- **RN-08 · El origen se guarda explícitamente.** Una denuncia ciudadana no tiene usuario ni
  organismo; sin el campo `origen` no se puede saber de dónde vino. *(DS)*

### Estados: tres ejes independientes *(DS)*
- **RN-09 · Estado del caso (eje 1, excluyente):** `sin_confirmar · confirmado · en_seguimiento ·
  finalizado · desestimado · unificado`. Un caso `unificado` apunta al que lo absorbió.
- **RN-10 · Motivo de espera (eje 2):** `contacto · respuesta · certificado`. **Solo existe si el
  caso está `en_seguimiento`.**
- **RN-11 · Marcas (eje 3, paralelas):** incompleto · posible duplicado · posible evento
  relacionado. Conviven entre sí y con cualquier estado. (La marca «origen ciudadano sin verificar»
  se eliminó el 2026-10-08: un caso siempre lo registra un agente, así que nunca está «sin verificar».)
- **RN-12 · Nunca colapsar los ejes en un solo campo**: se perdería información y la bandeja no
  podría filtrar por cada eje. En pantalla: estado = píldora en su columna; marcas = íconos
  pegados al identificador, a la izquierda.
- **RN-13 · La denuncia tiene su propio ciclo, más corto:** `recibida · en_revision · vinculada ·
  desestimada`. (`incorporada` se eliminó: con RN-06 incorporar y vincular son lo mismo.)

### Duplicados y eventos relacionados *(DS, R.13, R.14)*
- **RN-14 · Duplicado y evento relacionado son resultados distintos con desenlaces opuestos.**
  Lo que los distingue **es la persona**, no el lugar ni la fecha.
  - Duplicado → **consolidar**: un registro único, irreversible, conserva la trazabilidad de ambos originales.
  - Evento relacionado → **vincular**: los dos casos siguen vivos, reversible. **Nunca se fusionan:
    fusionarlos borraría una víctima del registro epidemiológico.**
  - Tercer desenlace: **no están relacionados** → se descarta la sospecha y se quita la marca.
- **RN-15 · Nunca hay fusión automática.** Consolidar exige una persona autorizada y una
  confirmación explícita («Confirmo que X e Y son la misma persona…»). *(R.14, P)*
- **RN-16 · La detección es por puntaje (0–100), no por regla binaria.** Señales y peso:

  | Señal | Peso | Nota |
  |---|---|---|
  | DNI idéntico | Decisiva | Obligatorio en todo registro nuevo (RN-21). Solo puede faltar en registros históricos |
  | Teléfono idéntico | Fuerte | Clave principal para cruzar contra registros históricos sin DNI |
  | Nombre parecido + edad cercana | Fuerte | Tolerante a abreviaturas («Micaela Sosa» / «M. Sosa») |
  | Fecha **del hecho** ±1 día | Media | Se transcribe a mano desde papel |
  | Misma cuadra | Media | Depende de la geocodificación (R.22) |
  | Mismo animal (color, tamaño, raza) | Refuerzo | No prueba duplicado; dispara la sospecha de evento relacionado |

- **RN-17 · «No evaluable» ≠ «no coincide».** Si falta el dato en uno de los registros, la señal
  es no evaluable y se muestra así. *(DS, P)*
- **RN-18 · Dos momentos de aviso.** Confianza alta (DNI o teléfono): se interrumpe **al guardar**,
  antes de crear el registro. Confianza media (lugar y fecha): solo se marca para revisar después. *(DS)*
- **RN-19 · Al consolidar, el agente elige campo por campo qué valor sobrevive.** Si un registro
  tiene el dato y el otro no, se conserva el que lo tiene. *(P)*

### Personas, animal y datos
- **RN-20 · Persona separada del rol.** Una persona puede ser víctima en un caso y denunciante en
  otro. El modelo debe representar «la madre denuncia por la hija mordida». *(HZ07, DS)*
- **RN-21 · El DNI es obligatorio en todo registro nuevo.** *(decisión del equipo, 2026-10-08;
  reemplaza la propuesta de DNI opcional)* No pueden existir denuncias ni casos nuevos sin DNI, de
  ningún origen: todos los formularios y canales de entrada lo exigen (el canal ciudadano de R.17
  incluido). **Única excepción: registros históricos** (p. ej. el formulario 2025 de R.18 o
  importaciones de datos viejos), que pueden no tenerlo. Esto deja atrás HZ01 para los datos nuevos.
  El DNI es la clave principal de deduplicación.
- **RN-22 · El animal no tiene identidad entre casos.** Los datos son descriptivos, no
  identificatorios. Lo que une dos casos del mismo perro es una coincidencia de tipo evento
  relacionado **confirmada por una persona**. El sistema propone, no resuelve identidades. *(DS)*
- **RN-23 · «No sabe» es un valor, distinto de «sin dato».** «No sabe» = la persona respondió que
  no sabe. «Sin dato» = la pregunta no se respondió o no se hizo. Cada campo con escala necesita
  su valor desconocido explícito y tablas de equivalencia entre formularios
  («Chico» F1 = «Pequeño» F2). *(HZ09, P)*
- **RN-24 · Equivalencias de normalización** (a validar con el cliente): Arañazo (F1) → Lesión
  compatible (F2) *(HZ05)*; F2 sin ciudad → Ushuaia *(HZ15)*; con quién estaba: Padre/Madre/Amigos/Otros (F2) → Acompañado (F1) *(HZ09)*.
- **RN-25 · Datos obligatorios del caso** (definen la marca *Incompleto*, R.08): fecha del hecho,
  ciudad, lugar, situación de la persona, animal propio/ajeno. *(E1)* La ficha muestra la
  completitud sobre los 50 campos («38 de 50»). *(P)*
- **RN-26 · Datos epidemiológicos clave:** edad, zona del cuerpo, tipo de lugar (ámbito). *(E1)*
  Filtros del mapa: tamaño y color del animal. *(E1)*

### Trazabilidad y seguridad
- **RN-27 · Toda modificación de un caso o una denuncia queda auditada** con fecha, hora y actor
  (usuario o sistema). *(R.29, DS)*
- **RN-28 · Las estadísticas se calculan solo sobre casos validados.** *(P)*
- **RN-29 · Datos sensibles** (DNI, historia clínica, contacto) protegidos según Ley 25.326. *(R.16)*
  Acceso por organismo + rol + permisos. *(R.03, R.21)*

---

## 7. Modelo de datos

**Archivo canónico:** `modelo/siram.mmd` (versión 0). Entidades: ORGANISMO, USUARIO, DENUNCIA,
CASO, PARTICIPACION, PERSONA, ANIMAL, DOCUMENTO, TIPO_DENUNCIA, COINCIDENCIA, AUDITORIA.

El boceto ER original del equipo (`recursos/boceto-equipo/`) está **superado** y no rige.

### Brechas conocidas (a resolver en esta etapa)
1. **19 campos de la matriz sin lugar propio** (hoy solo quedarían en `DENUNCIA.datos_crudos`):
   - Hecho: **H6 con quién estaba**, H7 iba con perros, **H8 actividad**, H9 tipo de interacción,
     H10 testigos/observaciones, H11 conoce al propietario.
   - Persona: P5 tarea si trabaja en la calle.
   - Animal: A6 el dueño se responsabilizó, A8 vacuna antirrábica, A10 castrado, A11 puede identificarlo.
   - Lesión: L5 sutura, L6 antibiótico, L7 vacuna antirrábica de la persona.
   - Denuncia: D1 denunció en Zoonosis, D2 denuncia policial, D3 motivo de no denunciar,
     D4 volvió a verlo suelto, D5 comentarios.
   **H6 y H8 son la «situación de la persona», dato obligatorio (RN-25).**
   El prototipo **ya muestra** varios de estos campos en la ficha del caso.
2. **Booleanos que no admiten «No sabe»** (`reincidente`, `atencion_medica`) — ver RN-23.
3. **Fecha de la denuncia (H0)** distinta de `recibida_en` (fecha de ingreso a SIRAM).
4. **DOCUMENTO sin atributos:** necesita al menos tipo (certificado médico, foto, denuncia policial),
   archivo y fecha.
5. **Lote de importación:** la auditoría del prototipo cita «lote ZU-2026-09-12» y R.25 pide
   informar qué filas no entraron y por qué; no hay entidad que lo guarde.
6. **Coordenadas:** falta definir cuándo y cómo se geocodifica (H4 es texto libre en F1 y calle+número en F2).
7. **Lesión múltiple:** hoy es un valor «Múltiples» en un enumerado. Si hay que registrar cada
   lesión por separado, pasa a ser entidad.
8. **Motivo de desestimación del caso:** CASO puede quedar `desestimado` pero no guarda por qué.
   Falta el campo y la lista de motivos (pendiente, ver §9).
9. **Registro histórico:** RN-21 exige DNI salvo en registros históricos; el modelo tiene que poder
   distinguirlos (p. ej. por origen o lote de importación) para aplicar la obligatoriedad.
10. **Vínculos entre casos:** COINCIDENCIA guarda la sospecha y la decisión; falta confirmar que
   alcanza para representar el vínculo vigente (y su reversión) de un evento relacionado.

---

## 8. Lenguaje de diseño (resumen para implementar)

Detalle completo en `recursos/diseno/De Delta a SIRAM.html`.

- **Base:** Delta `modulos/servicios-publicos`, commit `9a4cbb6a`, en `/home/dddario/front/delta`
  (ver también `componentes/theme`, `componentes/base`, `componentes/modulo`). Los componentes
  base de Delta envuelven paquetes npm privados (`@municipalidadushuaia/*`: `componentes-base-ui`,
  `formularios-rhf-mui`, `querys`, `micro-ux`, `utiles`, `calendario-headless`). **El equipo tiene
  acceso para leerlos** (todavía no están en este entorno). Son **referencia, no dependencia**: de
  ellos se toma el contrato de uso y el comportamiento, y SIRAM escribe sus propios componentes en su
  repo. No se instalan ni se importan.
- **Versiones de Delta (referencia):** React 18.2 · MUI 7.1 (`@mui/x-date-pickers` 8.1) ·
  react-hook-form 7.43 · TanStack Query 5.75 · Vite 6.2. Si se mantienen o se actualizan, se
  decide en el front (ver §9).
- **Cáscara:** barra superior fija de 89 px; menú lateral de 240 px colapsable a 65 px; solo el
  contenido scrollea; sin breadcrumbs ni footer. Ítem activo = píldora `border-radius: 0 30px 30px 0`.
  **Dos niveles de menú, nunca tres.**
- **Navegación:** Inicio (panel epidemiológico) · Casos (Bandeja, Nuevo caso, Pendientes de
  validación, Posibles duplicados, Eventos relacionados, Incompletos) · Consultas (Búsqueda, Mapa,
  Estadísticas, Exportar) · Administración (Usuarios, Organismos, Tipos de denuncia, Importar CSV).
  Los casos van al centro; la configuración al final.
- **Ventanas:** crear/editar/confirmar abren un **drawer lateral** (50 % escritorio, 100 % teléfono),
  Cancelar a la izquierda y Guardar a la derecha. Guardar deshabilitado hasta que haya cambios.
- **Formularios:** un fieldset con leyenda por bloque de la matriz; 20 px reservados para el error
  bajo cada campo; la ayuda va en un ícono junto a la etiqueta, nunca como texto bajo el campo.
- **Mensajes:** éxito/info se autocierran; advertencia/error exigen cierre manual.
- **Tokens:** acento `#00776F`, tinta `#16211F`, fondo `#F6F7F7`; colores semánticos para los
  ejes (ok, warn, crit, info, dup violeta, rel tierra). Todo color es un token.
- **Tema oscuro: obligatorio, pero en una segunda pasada.** Primero se implementa una versión base
  en tema claro. Después se agrega el oscuro revisando cada vista una por una. Desde el inicio todo
  color pasa por un token, para que el oscuro sea redefinir tokens y no reescribir vistas. Al
  terminar arranca según el sistema operativo y recuerda la elección.
- **Tipografía:** Archivo (títulos), Source Sans 3 (cuerpo, **15 px base**), IBM Plex Mono
  (datos y códigos). `tabular-nums` en fechas, DNI, edades y cantidades. Las tres son libres
  (Google Fonts). Delta usa **Gotham**, que es una licencia comprada y no se puede usar fuera de Delta.
- **Íconos:** un solo set para todo el producto, `@mui/icons-material` (el que usa Delta).
- **Dos audiencias:** el panel interno (denso, eficiente) usa este lenguaje. El canal ciudadano
  (persona recién mordida) tendrá otra piel y otro tono, en una etapa posterior.

---

## 9. Tensiones y pendientes abiertos

**Tensiones entre fuentes — resueltas el 2026-10-08:**
- **T-01 · Gatos → SÍ están dentro del alcance.** Perros y gatos (RN-02). Cierra HZ06.
- **T-02 · «Baja de denuncias» → era vocabulario viejo.** El PDF usaba «denuncia» donde hoy decimos
  caso (RN-05). Sigue abierto, ya con el vocabulario nuevo: cómo se refleja en SIRAM una denuncia
  de Zoonosis «desglosada y devuelta» a las 48 h sin certificado (HZ08).
- **T-03 · DNI → obligatorio** (RN-21).

**Pendientes con el cliente:**
- **A DEFINIR · ¿Cuáles son las causas posibles para desestimar un caso?** Ninguna fuente las define.
  Propuesta inicial (sin aprobar), con motivo obligatorio + observación libre:
  - *Fuera de alcance*: no fue un ataque de perro o gato a una persona (RN-02).
  - *Fuera de la provincia*: el hecho ocurrió fuera de Tierra del Fuego.
  - *Hecho inexistente o no verificable*: al verificarlo, no ocurrió o los datos eran falsos.
  - *Cargado por error*: registro accidental o de prueba.
  Excluidos a propósito: *duplicado* (su desenlace es consolidar → `unificado`, RN-14) y
  *datos insuficientes* (es la marca Incompleto; desestimarlo generaría subregistro).
- Qué es exactamente la «situación de la persona» (HZ11).
- Si SIRAM guarda los datos del denunciante o solo los del lesionado (HZ07; el modelo ya soporta ambos).
- **¿El formulario del canal ciudadano (R.17) es público o privado?** Su contenido se mantiene; solo
  se revisa quién accede. Si pasa a privado, hay que revisar lo que hoy supone acceso sin cuenta: el
  actor *Usuario externo* (§5), RN-07, RN-08, el origen `ciudadano` y la segunda audiencia de §8.
- **¿Participa realmente Zoonosis?** (pizarrón 08/10)
- **¿Qué denuncias se asocian a un caso?** ¿Solo las de Zoonosis, o también las de la policía? (hoy
  la policía solo aparece como dato del caso: D2 «denuncia policial»)
- **¿Hay problema en agregar el DNI al formulario?** RN-21 ya lo exige; falta que el cliente lo
  acepte, en particular en el F1 (formulario público de Schiavini).
- Reglas de visibilidad entre organismos (¿Zoonosis ve los casos de Salud?) y si un agente puede
  editar casos creados por otro organismo.
- Criterio de «urgente» en la cola de validación (el prototipo muestra «Urgentes (3)» sin definirlo).
- Qué hacer con «Mal estado de ADC» y «Otros» del F2 (HZ03); «Conoce al propietario del domicilio» (HZ16).
- Formato real de entrada: export del F1 (HZ13, HZ14), transcripción del F2 en papel (HZ12), y el F3.
- 2FA (R.05) y políticas de seguridad (Ley 25.326): sin definir.

**Pendientes de diseño (agente de diseño):**
- **«Fecha de novedad» de la bandeja.** Intención acordada: el **último movimiento** del caso (cualquier
  actualización del registro), para que suba lo que alguien tocó recientemente. No se modela todavía:
  se define cuando el modelo esté más maduro (candidato: derivarla de AUDITORIA). Mientras tanto el
  prototipo ordena por número de caso (orden de registro).
- **Columna «Lesión» / tipo de lesión (HZ05).** Hoy mezcla L1 (Mordedura, Arañazo) con T1 («Lesión
  compatible», categoría de denuncia del F2). Falta definir la lista de valores de SIRAM y la
  traducción de cada formulario.

**Pendientes técnicos (agente de código):**
- Librería de UI y estructura del front. Los componentes son propios, escritos tomando como
  referencia los paquetes funcionales de Delta (§8). Se evaluó **Refine** (MIT; React Query + MUI +
  React Hook Form, cercano a Delta) como base; **no está decidido**.
- Versiones: ¿se arranca con las de Delta (React 18.2, MUI 7.1) o con las actuales? ¿Qué se gana y
  qué se pierde? Como SIRAM no depende de los paquetes de Delta, no está atado a sus versiones;
  usar las mismas solo facilita leer y copiar patrones.
- Tecnología geográfica (RT.06) y servicio de geocodificación.

---

## 10. Mapa de recursos

Todo en `recursos/` (solo lectura). Si dos fuentes se contradicen, el orden de precedencia es:
**este documento › `modelo/siram.mmd` › «De las pantallas al modelo» › «De Delta a SIRAM» ›
prototipo › matriz de formularios › PDF de requerimientos.**

| Archivo | Qué es |
|---|---|
| `requerimientos/SIRAM.pdf` | Especificación de requerimientos (entrega del 11/09/2026): R.01–R.31, restricciones, RT.01–RT.08 |
| `requerimientos/SIRAM - …-5-Fabi.docx` | Versión anterior del mismo documento (04/09/2026) |
| `formularios/SIRAM - Comparacion de formularios….xlsx` | Matriz de 50 campos F1 vs F2, resumen de cobertura, hallazgos HZ01–HZ16, ficha de cada formulario |
| `diseno/De Delta a SIRAM.html` | Lenguaje de diseño: qué se hereda de Delta, qué se corrige, tokens, tres ejes, motor de coincidencias, navegación |
| `diseno/De las pantallas al modelo.html` | Modelo de datos derivado del prototipo, con la justificación de cada entidad |
| `diseno/Bandeja de casos SIRAM.html` | Prototipo navegable original (25/09). La versión en revisión es `diseno/prototipo-siram.html` (en la raíz de este repo) |
| `diseno/Bandeja-casos-SIRAM.html` | Versión anterior del prototipo (histórica) |
| `diseno/Estados del caso SIRAM.html` | Explicación visual de los tres ejes de estado |
| `modelo-er/siram-modelo-er.svg` (+ PNG) | Versión resumida del modelo para presentar (omite DOCUMENTO y TIPO_DENUNCIA) |
| `boceto-equipo/Diagrama-Boceto.drawio` (+ PNG) | ER original del equipo. **Superado, solo antecedente histórico** |
