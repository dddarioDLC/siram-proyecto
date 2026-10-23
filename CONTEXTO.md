# SIRAM — Contexto del proyecto

> Fuente de verdad del dominio de SIRAM. La leen el agente de diseño y el de backend; el front recibe
> solo lo que necesita en `front/docs/` (ver §1).
> Última actualización: 2026-10-23. Lo mantiene el agente de diseño (ver §1).

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
| 23/10/2026 | Redefinición del dominio: cuatro canales de alta y el canal ciudadano vuelve como **solicitud** (ver «Narrativa de uso»). El modelo v2 queda en preparación (§7) |

**Qué propone SIRAM** (la respuesta a la necesidad, contada como narrativa de uso con ejemplos prácticos):
- Seguir un caso a través de sus estados.
- Sacar un caso del registro: desestimarlo si es falso, o consolidarlo si es un duplicado (RN-14).
- Relacionar casos: duplicado (el mismo incidente cargado dos veces) o evento relacionado (mismo animal).
- Ver un caso.
- Ver estadísticas (revisando el diseño propuesto).

El pizarrón muestra dos caras: el **formulario público** (para todos) y el **backoffice** (solo personal
interno). **Hoy SIRAM es el backoffice más un formulario público acotado** (ver «Narrativa de uso»
abajo): lo que envía un ciudadano es una solicitud, no un caso.

**Qué falta resolver para dar esas respuestas:** cómo detectar duplicados · cómo resolverlos ·
la línea de tiempo de auditoría · el panel de gráficos de resumen (qué datos le sirven más al
usuario) · el formulario público · la carga de CSV.

> El pizarrón es una explicación semiformal, anterior a la terminología de este documento: donde dice
> «desestimar (falso o duplicado)» o «relacionar (repetido, mismo atacante)» se lee como arriba.
> «La sección de casos por validar» pertenecía a la narrativa del formulario público (había que
> verificar lo que cargaba cualquiera); con la narrativa actual no aplica.

### Narrativa de uso *(decisión del equipo, 2026-10-23; reemplaza la del 2026-10-08)*

1. Un perro o un gato ataca a una persona (con lesión o sin ella, RN-03).
2. El hecho entra a SIRAM por uno de **cuatro canales**: Salud, Zoonosis, CADIC o el **canal
   ciudadano** (formulario público). Todos empiezan con el mismo **formulario inicial**, corto a
   propósito (un cuestionario de 30 preguntas es invasivo para alguien recién mordido): nombre,
   apellido, DNI, teléfono, correo, especie del animal y fecha del hecho. Qué es obligatorio depende
   del canal (RN-30).
3. **Si lo carga un ente, es un caso.** **Si lo carga un ciudadano, es una solicitud** (RN-31): lo
   que declaró la persona, tal cual, a la espera de que un agente la revise.
4. Al enviar, el ciudadano puede seguir, si quiere, con un **formulario detallado** opcional por
   secciones (dónde fue, cómo era el animal…). Cada sección se guarda al pasar a la siguiente. Es una
   sola oportunidad: el enlace vence y después solo los entes agregan datos (RN-32).
5. Un agente revisa la solicitud: la **descarta** o la **registra**, creando un caso o sumándola a uno
   que ya existe. Lo declarado se copia sin transcribir; si choca con datos existentes, se conserva la
   mayor información (RN-19).
6. Cada ente completa el caso con datos de su dominio (p. ej. Salud, la atención de la víctima,
   RN-34). Si faltan datos de contacto, Zoonosis o CADIC buscan a la persona por DNI en sus propias
   bases; el caso queda `en_seguimiento` con motivo `contacto` mientras tanto.
7. La **denuncia** sigue siendo un acto oficial del mundo real ante un ente (Zoonosis, policía…).
   SIRAM no la recibe ni la gestiona: si existe, la **anota en el caso** (RN-06).
8. Las coincidencias entre casos, las estadísticas y el mapa salen de los casos. El motor de
   coincidencias se está rediseñando (§6).

**Diseñado en el prototipo:** bandeja de casos, ficha del caso (50 campos + auditoría),
comparar registros (duplicado vs. relacionado), inicio con panel epidemiológico.
(La cola de «Pendientes de validación» está diseñada pero oculta del menú. Con la narrativa del
2026-10-23 vuelve a tener sentido como **cola de solicitudes**: su bloque «Lo que declaró la persona»
es casi el flujo acordado. Falta ubicarla en la navegación, §9.)
**En curso:** panel con gráficos (R.30). **Pendiente:** mapa (R.22), formulario público inicial y
detallado (R.17) con la ubicación por pin (RN-33). **Futuro, no se resuelve ahora:** importación
CSV (R.25).

---

## 4. Universo del discurso (glosario)

| Término | Significado en SIRAM |
|---|---|
| **Caso** | El **incidente** que SIRAM registra y gestiona (`C-2026-0418`): un ataque de perro o gato a una persona. Lo registra un agente de un ente, o nace de una solicitud ciudadana que un agente registra. Guarda su canal de origen. Es lo único que SIRAM trata: se sigue, se completa y se cuenta en las estadísticas. |
| **Solicitud** | Lo que envía un ciudadano por el formulario público: **lo que declaró, tal cual**, a la espera de revisión. **No es un caso**: un agente la descarta o la registra (crea un caso o la suma a uno existente). RN-31. |
| **Canal de origen** | Por dónde entró el caso: Salud, Zoonosis, CADIC o ciudadano (vía solicitud). Define qué datos fueron obligatorios al nacer (RN-30). |
| **Denuncia** | Un acto **oficial del mundo real**: la persona (o alguien vinculado, p. ej. el padre por un hijo menor) denuncia el hecho ante un ente gubernamental (Zoonosis, policía…). SIRAM no la recibe ni la gestiona; si existe, **se anota en un caso que ya existe** (ente, número, fecha). **Nunca existe sola en SIRAM y no es un caso.** |
| **Registro unificado** | El caso que sobrevive a una consolidación de duplicados, conservando la trazabilidad de los originales. |
| **Persona** | Un ser humano con sus datos (nombre, **DNI obligatorio**, edad, contacto, domicilio). Independiente de su rol. **Única por DNI** en todo SIRAM (RN-20). |
| **Participación** | El rol de una persona en un caso: **víctima** (el lesionado) o **denunciante** (quien hizo la denuncia oficial, p. ej. la madre por la hija mordida), con su vínculo. |
| **Dueño** | La persona responsable del animal del caso. Se relaciona con el **animal**, no con el caso, y es opcional (callejero, desconocido). No se dice «denunciado»: no todo caso tiene denuncia. RN-22. |
| **Historial** | Las versiones anteriores de un registro: quién cambió qué, cuándo, desde qué organismo, valor anterior y nuevo. Reemplaza a la «auditoría». RN-27. |
| **Coincidencia** | La sospecha, generada por el motor, de que dos casos están relacionados. Guarda tipo, puntaje 0–100, señales y la decisión del agente. *En revisión: el motor se rediseña (§6).* |
| **Posible duplicado** | Coincidencia donde **coincide la persona**: el mismo incidente cargado dos veces. |
| **Evento relacionado** | Coincidencia donde coinciden lugar, fecha y animal pero **las personas son distintas**: dos víctimas del mismo animal. |
| **Consolidar** | Fundir dos casos duplicados en uno. **Irreversible.** |
| **Vincular** | Enlazar dos casos como hermanos sin fundirlos. **Reversible.** |
| **Marca** | Señal paralela al estado (el «semáforo»): incompleto, posible duplicado, posible evento relacionado. |
| **Motivo de espera** | Por qué un caso en seguimiento está detenido: contacto, respuesta o certificado. |
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
| **Agente interno** | Personal de un ente (Salud, Zoonosis, CADIC). Registra casos, revisa solicitudes (las descarta o las registra), completa el caso con los datos de su dominio, contacta para completar y verificar (incluida la denuncia), da seguimiento y resuelve coincidencias. Sus permisos dependen de organismo + rol. |
| **Ciudadano** | Sin cuenta. Envía una **solicitud** por el formulario público (R.17) y, si quiere, completa el formulario detallado en ese momento, una sola vez. Después no vuelve a tener acceso. *(2026-10-23; antes «Usuario externo», en suspenso)* |
| **Analista / Comité** | Consulta, filtra y exporta (R.20, R.21). Aparece en los requerimientos sin definición formal. |
| **Sistema** | Actor automático: marca coincidencias (a futuro, importa lotes). Aparece en la auditoría como «Sistema». |

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

### Caso y denuncia
- **RN-05 · SIRAM trata casos. CASO ≠ DENUNCIA.** *(decisión del equipo, 2026-10-08; ver «Narrativa de uso», §3)*
  - El **caso** es lo que SIRAM registra: el incidente. Lo da de alta **siempre un agente interno**:
    lo carga directamente un ente, o registra una solicitud ciudadana (RN-31). *(2026-10-23)*
  - La **denuncia** es un acto oficial del mundo real ante un ente gubernamental (Zoonosis,
    policía…), hecho por la víctima o por alguien vinculado. SIRAM no la recibe ni la gestiona.
  - Un caso puede tener **cero, una o varias** denuncias anotadas. Un caso sin denuncia es válido.
  - Donde los requerimientos del PDF dicen «ABM de denuncias» (función 4, subsistema 4), en el
    vocabulario actual se lee **ABM de casos**. El PDF es anterior a esta distinción.
- **RN-06 · La denuncia solo se anota en un caso que ya existe.** *(decisión del equipo, 2026-10-08)*
  No hay denuncias sueltas, ni cola de denuncias, ni se crea un caso «desde» una denuncia. La
  denuncia se anota cuando se conoce: al registrar el caso (p. ej. Zoonosis tomó la denuncia y
  registra el caso) o después, cuando un agente contacta a la persona y verifica si denunció.
  Se anota qué ente, número de trámite y fecha (H0) si se consiguen, y quién la hizo (RN-20).
- **RN-06b · En SIRAM no existe el borrado.** Ni de casos ni de denuncias. La «baja» de un caso es el
  estado **Desestimado**; una denuncia mal anotada se corrige, con su rastro en la auditoría.
  *(decisión del equipo, 2026-10-08)* Vale también para las solicitudes: solo hay **borrado
  lógico** (se marca, nunca se elimina de la base). *(2026-10-23)*
- **RN-07 · «¿Hizo denuncia?» es un dato del caso con tres respuestas** (RN-23): *sí* (con las
  denuncias anotadas), *no*, o *sin dato* (todavía no se le preguntó). Junto a *no* va el motivo de
  no denunciar (D3). Reemplaza a D1 «denunció en Zoonosis» y D2 «denuncia policial» como booleanos
  sueltos: cada una es una denuncia anotada con su ente. *(decisión del equipo, 2026-10-08)*
- **RN-08 · El canal ciudadano envía solicitudes, no casos.** El formulario público vuelve, pero lo
  que entra por él no es un caso ni una denuncia: es una solicitud que un agente revisa (RN-31,
  RN-32). *(decisión del equipo, 2026-10-23; reemplaza «canal ciudadano en suspenso» del 2026-10-08)*

### Estados: tres ejes independientes *(DS)*
- **RN-09 · Estado del caso (eje 1, excluyente):** `registrado · en_seguimiento · finalizado ·
  desestimado · unificado`. Un caso `unificado` apunta al que lo absorbió. Un caso nace
  `registrado`. (`sin_confirmar` y `confirmado` se eliminaron el 2026-10-08: venían del formulario
  público, donde había que confirmar lo que cargaba cualquiera; hoy un caso siempre lo registra un agente.)
- **RN-10 · Motivo de espera (eje 2):** `contacto · respuesta · certificado`. **Solo existe si el
  caso está `en_seguimiento`.**
- **RN-11 · Marcas (eje 3, paralelas):** incompleto · posible duplicado · posible evento
  relacionado. Conviven entre sí y con cualquier estado. (La marca «origen ciudadano sin verificar»
  se eliminó el 2026-10-08: un caso siempre lo registra un agente, así que nunca está «sin verificar».)
  Las listas de trabajo (Incompletos, Posibles duplicados, Eventos relacionados y sus contadores)
  muestran solo casos vigentes: sin `desestimado` ni `unificado`, igual que RN-28. Al consolidar,
  la marca de posible duplicado se resuelve y desaparece. *(decisión de diseño, 2026-10-09)*
- **RN-12 · Nunca colapsar los ejes en un solo campo**: se perdería información y la bandeja no
  podría filtrar por cada eje. En pantalla: estado = píldora en su columna; marcas = íconos
  pegados al identificador, a la izquierda.
- **RN-13 · La denuncia no tiene estados.** Es un dato anotado en el caso. (El ciclo `recibida ·
  en_revision · vinculada · desestimada` se eliminó el 2026-10-08 junto con la cola de denuncias.)

### Duplicados y eventos relacionados *(DS, R.13, R.14)*

> **En revisión (2026-10-23).** El motor de coincidencias de esta sección (RN-16 a RN-18 y la
> entidad COINCIDENCIA) se propuso sin validarlo con el equipo ni con el cliente. El equipo diseña
> uno nuevo; después se compara con este para rescatar lo que sirva. El **problema** de los
> duplicados sigue vigente, y RN-19 ya se reusa al registrar solicitudes (RN-31).
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
  **La persona es única por DNI** (clave sustituta + DNI único; los históricos pueden no tenerlo):
  todos sus casos apuntan a ella, lo que permite listarlos. Un error de carga se propaga a todos,
  pero queda en el historial (RN-27) y se revierte. *(decisión 2026-10-23)*
- **RN-21 · El DNI es obligatorio en todo registro nuevo.** *(decisión del equipo, 2026-10-08;
  reemplaza la propuesta de DNI opcional)* No pueden existir denuncias ni casos nuevos sin DNI, de
  ningún origen: todos los formularios y canales de entrada lo exigen (incluido el canal ciudadano de
  R.17, si vuelve). **Única excepción: registros históricos** (p. ej. el formulario 2025 de R.18 o
  importaciones de datos viejos), que pueden no tenerlo. Esto deja atrás HZ01 para los datos nuevos.
  El DNI es la clave principal de deduplicación.
- **RN-22 · El animal no tiene identidad entre casos.** Los datos son descriptivos, no
  identificatorios. Lo que une dos casos del mismo perro es una coincidencia de tipo evento
  relacionado **confirmada por una persona**. El sistema propone, no resuelve identidades. *(DS)*
  El **dueño** es una persona relacionada con el animal del caso (no con el caso) y es opcional.
  Absorbe H11 «conoce al propietario» y A6 «el dueño se responsabilizó» (a confirmar en el modelo v2).
  *(decisión 2026-10-23)*
- **RN-23 · «No sabe» es un valor, distinto de «sin dato».** «No sabe» = la persona respondió que
  no sabe. «Sin dato» = la pregunta no se respondió o no se hizo. Cada campo con escala necesita
  su valor desconocido explícito y tablas de equivalencia entre formularios
  («Chico» F1 = «Pequeño» F2). *(HZ09, P)*
- **RN-24 · Equivalencias de normalización** (a validar con el cliente): Arañazo (F1) → Lesión
  compatible (F2) *(HZ05)*; F2 sin ciudad → Ushuaia *(HZ15)*; con quién estaba: Padre/Madre/Amigos/Otros (F2) → Acompañado (F1) *(HZ09)*.
- **RN-25 · Datos obligatorios del caso** (definen la marca *Incompleto*, R.08): fecha del hecho,
  ciudad, lugar, situación de la persona, animal propio/ajeno. *(E1)* La ficha muestra la
  completitud sobre los 50 campos («38 de 50»). *(P)* Con la obligatoriedad por canal (RN-30) hay
  que separar **obligatorio para nacer** (lo que exige el formulario inicial) de **obligatorio para
  estar completo** (lo que define la marca Incompleto). Pendiente, §9.
- **RN-26 · Datos epidemiológicos clave:** edad, zona del cuerpo, tipo de lugar (ámbito). *(E1)*
  Filtros del mapa: tamaño y color del animal. *(E1)*

### Trazabilidad y seguridad
- **RN-27 · Toda modificación queda en un historial** con fecha, hora, actor (usuario o sistema)
  y su organismo, **valor anterior y valor nuevo**. *(R.29, DS)* La fila viva es la última versión;
  las anteriores pasan a un historial **por entidad** (caso, persona, animal…). Reemplaza la entidad
  AUDITORIA. Todo registro guarda además su marca de tiempo de creación, automática. El volumen
  (≈200 casos/año) no es un problema. *(decisión 2026-10-23; detalle pendiente, §9)*
- **RN-28 · Las estadísticas se calculan sobre los casos vigentes:** se excluyen los `desestimado`
  y los `unificado` (estos ya están contados en el caso que los absorbió). *(decisión del equipo,
  2026-10-08; reemplaza «solo casos validados», que venía del formulario público)*
- **RN-29 · Datos sensibles** (DNI, historia clínica, contacto) protegidos según Ley 25.326. *(R.16)*
  Acceso por organismo + rol + permisos. *(R.03, R.21)*

### Canales de alta y solicitudes *(decisión del equipo, 2026-10-23)*
- **RN-30 · Obligatoriedad del formulario inicial por canal.**

  | Campo | Ciudadano | Zoonosis | CADIC | Salud |
  |---|---|---|---|---|
  | Nombre, apellido, DNI, especie (perro/gato) | ✔ | ✔ | ✔ | ✔ |
  | Teléfono | ✔ | ✔ | — | — |
  | Correo | ✔ | — | — | — |
  | Fecha del hecho | ✔ | ✔ | ✔ | — (la víctima no suele dejarla en la guardia) |

  La obligatoriedad vive en el formulario de cada canal, no en la base (que admite el campo vacío);
  por eso el caso guarda su **canal de origen**. El DNI es obligatorio en todos porque es la clave
  que permite buscar a la persona en las bases de cada ente cuando faltan datos de contacto.
- **RN-31 · La solicitud.** Lo que envía un ciudadano se guarda **tal cual** en una sola tabla de
  staging; **nunca escribe en PERSONA ni en CASO** sin un agente de por medio (así nadie puede pisar
  los datos de otro escribiendo su DNI).
  - Estados: `pendiente` (al enviarla) · `descartada` · `registrada` (con referencia al caso).
  - Un caso tiene 0..N solicitudes (una persona puede enviar dos veces el mismo hecho);
    registrada ⇔ tiene caso.
  - Al registrarla, sus datos se copian sin transcribir. Si chocan con datos existentes (p. ej. la
    persona ya existe con otro teléfono), se conserva la mayor información (RN-19).
  - Guarda fecha de **envío** (creación, automática) y de **resolución**. Si la solicitud creó el
    caso o se sumó a uno existente es un **dato derivado** de la fecha de resolución, no un estado.
  - No se borra nunca (RN-06b): la registrada queda como **procedencia** del caso. No lleva
    historial: tiene un solo autor y no es un dato confirmado.
- **RN-32 · El formulario detallado del ciudadano.** Opcional, por secciones, una sola vez, justo
  después de enviar el inicial.
  - Cada sección se guarda al pasar a la siguiente (guardado incremental): el primer envío crea la
    solicitud (`POST`), las secciones la completan (`PATCH`).
  - Lo ata a la solicitud un **token aleatorio, no adivinable** (nunca el número de caso: evita
    enumeración / IDOR), que **vence del lado del servidor** (≈1 h) y solo permite completar esa
    solicitud (mínimo privilegio). El navegador lo olvida al cerrar la pestaña.
- **RN-33 · Ubicación del hecho.** Tres datos: **punto** en el mapa, **dirección** en texto y
  **referencias** libres («casa roja», «plaza en la esquina»).
  - Patrón de apps de viaje: la persona ubica el pin y la geocodificación inversa propone la
    dirección. El punto siempre lo pone una persona, así que no se guarda cómo se obtuvo (salvo
    importaciones futuras, donde será automático).
  - OpenStreetMap con Leaflet (mapa) y Nominatim (geocodificación). La política de Nominatim
    alcanza para el volumen de SIRAM si: un pedido al soltar el pin (no por tecla), User-Agent
    propio, atribución visible, URL del servicio en configuración, y pedidos a través de un proxy
    con caché en el back. Para importaciones masivas, Nominatim propio o API Georef.
  - Cierra la brecha §7.6.
- **RN-34 · Cada ente completa el caso con datos de su dominio.** Los formularios de los entes
  difieren y suman al mismo caso. **Salud** registra la atención de la víctima: fecha de la
  consulta, vacuna antirrábica, antitetánica, zona de la mordedura, antibiótico, sutura y
  observaciones. Se relaciona con la **víctima del caso** (su participación), no con la persona:
  si colgara de la persona, se mezclarían los tratamientos de mordeduras distintas. Cubre L5–L7 de
  §7.1. Falta relevar los datos propios de Zoonosis y CADIC.

---

## 7. Modelo de datos

**Archivo canónico:** `modelo/siram.mmd` (versión 1, ver `modelo/decisiones.md`). Entidades: ORGANISMO, USUARIO, DENUNCIA,
CASO, PARTICIPACION, PERSONA, ANIMAL, DOCUMENTO, TIPO_EVENTO, COINCIDENCIA, AUDITORIA.

> **Modelo v2 en preparación (2026-10-23).** La narrativa nueva (§3) cambia el modelo: SOLICITUD
> (nueva), DUEÑO del animal, PERSONA única por DNI, CASO con canal de origen y ubicación (punto,
> dirección, referencias), historial por entidad en lugar de AUDITORIA, atención de Salud ligada a
> la víctima del caso, y marca de tiempo de creación en todo registro. `siram.mmd` **no se actualiza
> todavía**: faltan definir DENUNCIA, COINCIDENCIA y los datos de cada ente, y el modelo es el
> contrato con el back.

El boceto ER original del equipo (`recursos/boceto-equipo/`) está **superado** y no rige.

### Brechas conocidas (a resolver en esta etapa)
1. **16 campos de la matriz sin lugar propio** (D1–D3 se resolvieron con la denuncia anotada, RN-07):
   - Hecho: **H6 con quién estaba**, H7 iba con perros, **H8 actividad**, H9 tipo de interacción,
     H10 testigos/observaciones, H11 conoce al propietario.
   - Persona: P5 tarea si trabaja en la calle.
   - Animal: A6 el dueño se responsabilizó, A8 vacuna antirrábica, A10 castrado, A11 puede identificarlo.
   - Lesión: L5 sutura, L6 antibiótico, L7 vacuna antirrábica de la persona. → Van a la atención
     de Salud (RN-34).
   - Seguimiento: D4 volvió a verlo suelto, D5 comentarios.
   **H6 y H8 son la «situación de la persona», dato obligatorio (RN-25).**
   El prototipo **ya muestra** varios de estos campos en la ficha del caso.
2. **Booleanos que no admiten «No sabe»** (`reincidente`, `atencion_medica`) — ver RN-23.
3. ~~Fecha de la denuncia (H0)~~ Resuelta en v1: `DENUNCIA.fecha`.
4. **DOCUMENTO sin atributos:** necesita al menos tipo (certificado médico, foto, denuncia policial),
   archivo y fecha.
5. **Lote de importación:** la auditoría del prototipo cita «lote ZU-2026-09-12» y R.25 pide
   informar qué filas no entraron y por qué; no hay entidad que lo guarde.
6. ~~**Coordenadas:** falta definir cuándo y cómo se geocodifica (H4 es texto libre en F1 y calle+número en F2).~~
   Resuelta por RN-33 (pin + dirección + referencias). Queda abierto solo para importaciones.
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
- **Navegación:** Inicio (panel epidemiológico) · Casos (Bandeja, Nuevo caso,
  Posibles duplicados, Eventos relacionados, Incompletos) · Consultas (Búsqueda, Mapa,
  Estadísticas, Exportar) · Administración (Usuarios, Organismos, Tipos de evento, Importar CSV).
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
- **Dos audiencias:** el panel interno (denso, eficiente) usa este lenguaje. El formulario público
  (persona recién mordida) volvió el 2026-10-23 y necesita otra piel y otro tono: pendiente de diseño.
  Los ejemplos de un campo pueden ir de placeholder, pero se repiten en la ayuda del ícono, porque
  el placeholder desaparece al escribir.

---

## 9. Tensiones y pendientes abiertos

**Tensiones entre fuentes — resueltas el 2026-10-08:**
- **T-01 · Gatos → SÍ están dentro del alcance.** Perros y gatos (RN-02). Cierra HZ06.
- **T-02 · «Baja de denuncias» → era vocabulario viejo.** El PDF usaba «denuncia» donde hoy decimos
  caso (RN-05). Sigue abierto, ya con el vocabulario nuevo: si una denuncia de Zoonosis
  «desglosada y devuelta» a las 48 h sin certificado (HZ08) se anota igual en el caso, y cómo.
- **T-03 · DNI → obligatorio** (RN-21).

**Pendientes con el cliente:**
- **EN REVISIÓN · Criterio para pasar un caso a `finalizado`.** Candidato (2026-10-08, en revisión):
  **el animal que mordió sigue vivo a los 10 días** del hecho (período de observación antirrábica).
  Si se confirma, hay que responder: qué pasa si el animal no se puede observar (ajeno, desconocido,
  no ubicado); qué pasa si muere o enferma en esos 10 días (no finaliza: es una alerta); y si
  «en observación» es un nuevo motivo de espera de `en_seguimiento`. No se modela hasta confirmarlo.
- **A CONFIRMAR · Lista de entes** donde se puede haber hecho una denuncia (Zoonosis, policía…).
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
- ~~¿Vuelve el formulario público (R.17)?~~ Sí, como solicitud (RN-08, RN-31, RN-32). *(2026-10-23)*
- **DENUNCIA: no se sabe cómo la guardan los entes** ni si tiene un identificador. No se modela el
  formulario real de la denuncia. **Propuesta en revisión** (2026-10-23), pensada para poder ubicar
  la denuncia en el sistema del ente:

  | Campo | Tipo | Obligatorio |
  |---|---|---|
  | DNI del denunciante | Texto con validación de formato; referencia lógica, **sin** enlace a PERSONA | Sí |
  | Fecha de la denuncia | Fecha | Sí |
  | Ente | Lista (a confirmar con el cliente) | Sí |
  | Número de trámite | Texto libre, sin validación (no se sabe si existe ni su formato) | No |

  DNI + fecha + ente funcionan como clave de búsqueda compuesta. Si falta alguno, la denuncia no se
  anota, pero el caso registra igual «¿hizo denuncia? → sí» (RN-07).
- **Roles de las personas (propuesta en revisión, 2026-10-23).** Tres tipos: **víctima**,
  **responsable** y **dueño** (del animal, RN-22). «Responsable» reemplaza a «denunciante» en
  PARTICIPACION: es **quien registró el hecho** cuando no es la víctima (p. ej. la madre por la hija,
  o un amigo de una víctima adulta), que no es necesariamente quien hizo la denuncia oficial (ese
  queda como DNI en DENUNCIA). **La relación entre ambos no importa.** Si la víctima no tiene datos
  de contacto, se contacta al responsable.
  - Sin cubrir: (a) el nombre «responsable» sugiere estar «a cargo», y la definición no lo exige;
    revisar si otro nombre la describe mejor (p. ej. «informante»). (b) Si la relación no importa,
    ¿se elimina `PARTICIPACION.vinculo`? (c) Víctima y responsable se relacionan con el caso; el
    dueño, con el animal: dos lugares distintos en el modelo v2.
  - El formulario inicial pregunta «¿Es usted quien sufrió el ataque?». Si sí, una sola persona
    (víctima). Si no, se cargan los datos del responsable y además los de la víctima.
  - De la víctima son obligatorios todos los datos del formulario inicial **salvo el contacto**
    (un niño no lo tiene). Lo que se mide en SIRAM son las víctimas y los ataques.
  - **Una víctima por caso:** dos víctimas del mismo ataque son dos casos (cada una tiene su
    seguimiento y su atención). Coincide con RN-14: los eventos relacionados se vinculan, nunca se
    fusionan.
- **Datos propios de Zoonosis y CADIC** para completar un caso (los de Salud están en RN-34).
- **Personas sin DNI argentino** (p. ej. turistas extranjeros mordidos en Ushuaia): RN-21 exige DNI
  y la validación de formato lo asume. ¿Pasaporte? ¿Tipo + número de documento? A futuro, no se
  resuelve ahora. *(2026-10-23)*
- **Caso sin contacto que nunca se ubica** (p. ej. alta de Salud sin teléfono ni correo, y la
  búsqueda por DNI no da resultado): ¿queda `en_seguimiento` para siempre? ¿cuenta en estadísticas?
- **¿Participa realmente Zoonosis?** (pizarrón 08/10)
- ~~¿Qué denuncias se asocian a un caso?~~ Cualquier denuncia oficial ante un ente (Zoonosis,
  policía…), anotada con su ente (RN-06, RN-07). Falta la lista de entes con el cliente.
- **¿Hay problema en agregar el DNI al formulario?** RN-21 ya lo exige; falta que el cliente lo
  acepte, en particular en el F1 (formulario público de Schiavini).
- Reglas de visibilidad entre organismos (¿Zoonosis ve los casos de Salud?) y si un agente puede
  editar casos creados por otro organismo.
- Qué hacer con «Mal estado de ADC» y «Otros» del F2 (HZ03); «Conoce al propietario del domicilio» (HZ16).
- Formato real de entrada: export del F1 (HZ13, HZ14), transcripción del F2 en papel (HZ12), y el F3.
- 2FA (R.05) y políticas de seguridad (Ley 25.326): sin definir.

**Pendientes de diseño (agente de diseño):**
- **Motor de coincidencias nuevo** (lo diseña el equipo; después se compara con RN-16 a RN-18) y
  qué pasa con COINCIDENCIA y con el vínculo entre casos (§7.10). *(2026-10-23)*
- **Historial en detalle:** revisarlo con casos de ejemplo; en particular, cómo se ve en un caso el
  cambio de una persona compartida con otros casos (RN-20, RN-27).
- **Obligatorio para nacer vs. para estar completo** (RN-25, RN-30).
- **Retención de solicitudes descartadas:** el borrado lógico conserva DNI y contacto; la Ley 25.326
  pide minimización. ¿Plazo de retención?
- **Cola de solicitudes en la navegación** (§8: dos niveles, nunca tres) y el formulario público
  (inicial + detallado + pin).
- **«Fecha de novedad» de la bandeja.** Intención acordada: el **último movimiento** del caso (cualquier
  actualización del registro), para que suba lo que alguien tocó recientemente. No se modela todavía:
  se define cuando el modelo esté más maduro (candidato: derivarla de AUDITORIA). Mientras tanto el
  prototipo ordena por número de caso (orden de registro).
- **Columna «Lesión» / tipo de lesión (HZ05).** Hoy mezcla L1 (Mordedura, Arañazo) con T1 («Lesión
  compatible», categoría de denuncia del F2). Falta definir la lista de valores de SIRAM y la
  traducción de cada formulario. Atado a esto: el catálogo **«Tipo de evento»** (T1; antes «Tipo de
  denuncia», renombrado el 2026-10-08) clasifica al caso. Propuesta de valores, a validar: ataque con
  mordedura · lesión compatible · intento sin lesión · lesión derivada (RN-03). Falta deslindarlo del
  tipo de lesión (L1) para que no repitan lo mismo. Traducción del F2: «Mordedura de ADC» → ataque
  con mordedura; «Lesión compatible con ataque de ADC» → lesión compatible.

**Pendientes técnicos (agente de código):**
- Librería de UI y estructura del front. Los componentes son propios, escritos tomando como
  referencia los paquetes funcionales de Delta (§8). Se evaluó **Refine** (MIT; React Query + MUI +
  React Hook Form, cercano a Delta) como base; **no está decidido**.
- Versiones: ¿se arranca con las de Delta (React 18.2, MUI 7.1) o con las actuales? ¿Qué se gana y
  qué se pierde? Como SIRAM no depende de los paquetes de Delta, no está atado a sus versiones;
  usar las mismas solo facilita leer y copiar patrones.
- Tecnología geográfica (RT.06) y servicio de geocodificación.
- **Abuso del canal ciudadano (a revisar, no se resuelve ahora).** Alguien podría enviar miles de
  solicitudes basura. La integridad está a salvo (una solicitud no llega a un caso sin un agente),
  pero la carga de trabajo no. **Hay que revisar rate limiting** (por IP y/o por DNI);
  CAPTCHA como complemento. *(2026-10-23)*

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
| `pizarron-2026-10-08/` | Fotos del pizarrón del equipo (08/10/2026): propuesta, lo que falta, dudas técnicas y de dominio. Semiformal, anterior a la terminología; destilado en §3, §8 y §9 |
