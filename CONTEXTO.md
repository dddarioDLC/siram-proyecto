# SIRAM — Contexto del proyecto

> Fuente de verdad del dominio de SIRAM. La leen el agente de diseño y el de backend; el front recibe
> solo lo que necesita en `front/docs/` (ver §1).
> Última actualización: 2026-10-29. Lo mantiene el agente de diseño (ver §1).

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
3. **El prototipo es un antecedente, superado por la narrativa.** `diseno/prototipo-siram.html`
   (copia en revisión del original de `recursos/`, 25/09) muestra cómo se ven y se comportan las
   pantallas principales, con datos de ejemplo realistas. Fue la referencia mientras la narrativa no
   estaba cerrada; la narrativa partió de él y desde el 2026-10-23 lo sobrepasó. **Donde este
   documento tomó una decisión, gana el documento y es el prototipo el que tiene que cambiar.** Lo
   que la narrativa todavía no tocó tampoco vale por sí solo: antes de usarlo hay que verificar que
   siga siendo coherente con el enfoque actual del sistema. Sirve para ver las pantallas, no para
   deducir reglas ni datos.
4. **El modelo de datos está casi cerrado.** El modelo vigente es la **v2**, todavía en texto
   (este documento y `modelo/decisiones.md`); se vuelca a `modelo/siram.mmd` cuando esté cerrado
   (§7). Puede haber una v3 para detalles puntuales. Lo que hoy contiene `siram.mmd` es la v1, que
   nunca se publicó y quedó superada: no rige.
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
  tecnología geográfica (mapa y geocodificación: OpenStreetMap, Leaflet y Nominatim, RN-33; la base
  geográfica sigue a definir, PostGIS es el candidato natural) · HTTPS/TLS obligatorio.

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
| 28/10/2026 | Pizarrón del equipo, dos iteraciones (`recursos/pizarron-2026-10-28/`): muere PARTICIPACION, el animal tiene identidad y su seguimiento por caso vive en CASO_ANIMAL (RN-20, RN-22) |
| 29/10/2026 | Historial con tablas espejo (RN-27), reconciliación de animales (RN-22), permisos en el servidor por fila y por campo (RN-35), Personas y Animales en Consultas (§8) |

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
la línea de tiempo del historial (pantalla propia, RN-27) · el panel de gráficos de resumen (qué datos le sirven más al
usuario) · el formulario público · la carga de CSV.

> El pizarrón es una explicación semiformal, anterior a la terminología de este documento: donde dice
> «desestimar (falso o duplicado)» o «relacionar (repetido, mismo atacante)» se lee como arriba.
> «La sección de casos por validar» pertenecía a la narrativa del formulario público (había que
> verificar lo que cargaba cualquiera). Con la narrativa del 2026-10-23 vuelve como **cola de
> solicitudes** (ver abajo).

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
7. La **denuncia** sigue siendo un acto oficial del mundo real, ante Zoonosis. SIRAM no la recibe
   ni la gestiona: si existe, **Zoonosis la anota en el caso** (RN-06). Aparte, si el caso tiene una
   solicitud registrada, guarda lo que declaró la persona: si denunció y, si no, por qué (RN-07).
8. Las coincidencias entre casos, las estadísticas y el mapa salen de los casos. El motor de
   coincidencias se está rediseñando (§6).

**Diseñado en el prototipo:** bandeja de casos, ficha del caso (50 campos; desde el 2026-10-29 el historial va en pantalla propia y la ficha lleva un acceso a él, RN-27),
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
| **Canal de origen** | Por dónde entró el caso: Salud, Zoonosis, CADIC, ciudadano (vía solicitud) o **histórico** (registros viejos o importados, los únicos que pueden no tener DNI). Define qué datos fueron obligatorios al nacer (RN-30). |
| **Denuncia** | Un acto **oficial del mundo real**: la persona (o alguien vinculado, p. ej. el padre por un hijo menor) denuncia el hecho ante **Zoonosis**. SIRAM no la recibe ni la gestiona; si existe, **Zoonosis la anota en un caso que ya existe** (fecha, DNI de quien la hizo, número si lo hay). **Nunca existe sola en SIRAM y no es un caso.** RN-06. |
| **Registro unificado** | El caso que sobrevive a una consolidación de duplicados, conservando la trazabilidad de los originales. |
| **Persona** | Un ser humano con sus datos (nombre, **DNI obligatorio**, edad, contacto, domicilio). Independiente de su rol. **Única por DNI** en todo SIRAM (RN-20). |
| **Víctima** | La persona atacada. **Una sola por caso**: dos víctimas del mismo ataque son dos casos. RN-20. |
| **Contacto responsable** | La persona que informó el hecho en nombre de la víctima (p. ej. la madre por la hija), y a quien se contacta si la víctima no tiene contacto. Opcional: no existe si la víctima se registró sola. No se guarda la relación entre ambas. RN-20. *(Reemplaza a «Participación», eliminada el 2026-10-28.)* |
| **Dueño** | La persona responsable del animal **durante el caso**. Vive en CASO_ANIMAL, porque puede cambiar entre un caso y otro; es opcional (callejero, desconocido). No se dice «denunciado»: no todo caso tiene denuncia. RN-22. |
| **Animal** | La **identidad** de un animal (especie, chip). Puede aparecer en varios casos. RN-22. |
| **CASO_ANIMAL** | Lo que se supo de un animal **en un caso**: descripción, dueño, tenencia, vacunación, observación del día 10. Un caso puede tener varios animales (p. ej. tres perros sueltos que atacan juntos). RN-22. |
| **Descripción** | Los rasgos físicos del animal declarados en un caso (tamaño, sexo, edad aparente, color, raza…). Sirve a Zoonosis para identificarlo. No es parte del motor de coincidencias. RN-22. |
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
| **Sistema** | Actor automático: marca coincidencias (a futuro, importa lotes). Aparece en el historial como «Sistema» (usuario nulo, RN-27). |

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
  - La **denuncia** es un acto oficial del mundo real ante **Zoonosis**, hecho por la víctima o por
    alguien vinculado. SIRAM no la recibe ni la gestiona. (La policía no participa ni se modela; podría
    sumarse muy a futuro. *2026-10-29*)
  - Un caso puede tener **cero, una o varias** denuncias anotadas. Un caso sin denuncia es válido.
  - Donde los requerimientos del PDF dicen «ABM de denuncias» (función 4, subsistema 4), en el
    vocabulario actual se lee **ABM de casos**. El PDF es anterior a esta distinción.
- **RN-06 · La denuncia solo se anota en un caso que ya existe.** *(decisión del equipo, 2026-10-08)*
  No hay denuncias sueltas, ni cola de denuncias, ni se crea un caso «desde» una denuncia. La
  denuncia se anota al registrar el caso (Zoonosis tomó la denuncia y registra el caso) o después,
  cuando Zoonosis la vincula a un caso que ya existe.
  **Solo Zoonosis anota denuncias, y solo las hechas en Zoonosis** *(decisión 2026-10-29)*: Salud,
  CADIC y el canal ciudadano no la tocan. Como la denuncia es suya, Zoonosis tiene sus datos.
  Se anota la **fecha de la denuncia** (H0: cuándo se hizo, distinta de la del hecho y de la de
  registro del caso) y **quién la hizo** (su DNI), obligatorios: es la denuncia real. El número de
  trámite es opcional (no se sabe si existe ni su formato). Mientras Zoonosis sea el único ente, el
  ente no es un dato a cargar. *(detalle de campos en §9)*
- **RN-06b · En SIRAM no existe el borrado.** Ni de casos ni de denuncias. La «baja» de un caso es el
  estado **Desestimado**; una denuncia mal anotada se corrige, con su rastro en el historial (RN-27).
  *(decisión del equipo, 2026-10-08)* Vale también para las solicitudes: solo hay **borrado
  lógico** (se marca, nunca se elimina de la base). *(2026-10-23)*
- **RN-07 · «¿Hizo denuncia?» y su motivo son lo que declaró la persona, y viven en el caso.**
  *(decisión 2026-10-29; reemplaza la del 2026-10-08)*
  - **Solo los carga el canal ciudadano.** Llegan en la solicitud y se copian al caso cuando un agente
    la registra (RN-31); ningún agente los carga ni los modifica (permiso por campo, RN-35). Si el
    caso no tiene solicitud, o la persona no respondió, quedan *sin dato*. Si el caso tiene varias
    solicitudes con respuestas distintas, **gana la más reciente** (es lo último que declaró la
    persona); vale para estos dos campos, no reemplaza a RN-19 en general. *(2026-10-29)*
  - **¿Hizo denuncia?** (D1): *sí*, *no* o *sin dato* (RN-23). Es la respuesta de la persona, no el
    registro de la denuncia.
  - **Motivo de no denunciar** (D3), solo si respondió *no*. Valores del F1: el animal es propio o
    conocido · no sabía dónde denunciar · no sabía que había que denunciar · no me tomaron la
    denuncia · razones económicas · no pasa nada después con la denuncia · otros.
  - Vienen del formulario de Schiavini (F1) y miden el **subregistro**, el problema de origen de
    SIRAM (§2): por qué los casos no llegan a Zoonosis.
  - La **DENUNCIA** anotada (RN-06) es otra cosa: lo que registró Zoonosis. Los dos datos pueden
    cruzarse («dijo que sí y no hay denuncia en Zoonosis»), pero ninguno se deriva del otro.
  - D2 «denuncia policial» queda afuera: la policía no se modela (RN-05).
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
- **RN-20 · Persona separada del rol.** Una persona puede ser víctima en un caso y contacto
  responsable en otro. El modelo debe representar «la madre registra por la hija mordida». *(HZ07, DS)*
  **Desde el 2026-10-28 los roles son dos FK del caso:** **víctima** (obligatoria, una por caso) y
  **contacto responsable** (opcional). Muere PARTICIPACION, y con ella el campo `vinculo`: la
  relación entre ambas personas no se guarda. Quien hizo la denuncia oficial no es un rol: queda
  como DNI en la denuncia (§9).
  **La persona es única por DNI** (clave sustituta + DNI único; los históricos pueden no tenerlo):
  todos sus casos apuntan a ella, lo que permite listarlos. Un error de carga se propaga a todos,
  pero queda en el historial (RN-27) y se revierte. *(decisión 2026-10-23)*
- **RN-21 · El DNI es obligatorio en todo registro nuevo.** *(decisión del equipo, 2026-10-08;
  reemplaza la propuesta de DNI opcional)* Todos los formularios y canales de entrada lo exigen,
  incluido el canal ciudadano (RN-30). La base lo admite vacío **solo** para la excepción: **registros
  históricos** (p. ej. el formulario 2025 de R.18 o
  importaciones de datos viejos), que pueden no tenerlo. Esto deja atrás HZ01 para los datos nuevos.
  El DNI es la clave principal de deduplicación.
- **RN-22 · El animal tiene identidad; lo que se sabe de él en cada caso vive en CASO_ANIMAL.**
  *(decisión del equipo, 2026-10-28; reemplaza «el animal no tiene identidad entre casos», DS)*
  - **ANIMAL** es la identidad: especie y **chip** (si lo tiene). Un animal puede aparecer en
    varios casos, y un caso puede tener **varios animales** (p. ej. tres perros sueltos que atacan
    juntos: cada uno tiene su propio seguimiento).
  - **CASO_ANIMAL** (entidad asociativa) guarda lo que puede variar de un caso a otro: **dueño
    durante el caso** (opcional), **tenencia** (A5: propio, ajeno conocido, desconocido),
    **vacunado**, el **domicilio o zona del animal** (A7: dónde vive, no dónde atacó; puede cambiar
    entre casos, *2026-10-29*), la **observación del día 10** (seguimiento de Zoonosis) y
    **observaciones generales** del seguimiento, opcionales, en texto libre y de carga de Zoonosis (p. ej. «el perro está
    dentro de un terreno al que no tenemos acceso»); son otro campo que la del día 10. También las
    **fotos del animal** (X2), que ayudan a identificarlo (§7.4). *(2026-10-29)* Es el lugar del
    criterio de `finalizado` en revisión (§9).
  - **DESCRIPCIÓN** (1:1 con CASO_ANIMAL, entidad aparte): rasgos físicos declarados (tamaño,
    sexo, edad aparente, color, raza…). Se separa por opcionalidad en bloque (un animal propio e
    identificado puede no necesitarla), ciclo de vida (la carga la solicitud o el ente; el
    seguimiento, Zoonosis) y cohesión (responde «¿cómo es?», no «¿qué pasó después?»). La usa
    Zoonosis para identificar al animal; no es parte del motor de coincidencias.
  - Un animal sin chip igual se registra, con su descripción.
  - **Reconciliación de animales:** si Zoonosis descubre que dos ANIMAL son el mismo, uno absorbe
    al otro y los casos pasan a apuntar al que sobrevive. Mismo patrón que el caso `unificado`
    (RN-09). *(detalle 2026-10-29)*
    - Es **manual**. El disparador típico es el chip único: al cargarle a un animal un chip que ya
      tiene otro, la base lo rechaza y un agente compara los dos registros. (El chip es único pero
      opcional: muchos animales pueden no tenerlo.)
    - **Sobrevive el que tiene chip.** Si ninguno lo tiene, el más viejo: sin chip, ANIMAL solo
      guarda la especie, así que la elección no cambia datos y basta un desempate fijo.
    - **El absorbido no se borra ni se vacía** (RN-06b): conserva sus datos, deja de estar vigente
      y apunta al que lo absorbió (`absorbido_por`).
    - Absorber es, sobre todo, **repuntar la FK de CASO_ANIMAL** al sobreviviente. La descripción
      vive en CASO_ANIMAL / DESCRIPCIÓN, así que no se pierde. Cada cambio queda en el historial de
      su entidad (RN-27): así un caso sabe por qué su animal ahora es otro.
  - Los permisos (p. ej. Salud no ve dueño ni descripción) se resuelven con control de acceso por
    campo en la API, no partiendo tablas (RN-35).
- **RN-23 · «No sabe» es un valor, distinto de «sin dato».** «No sabe» = la persona respondió que
  no sabe. «Sin dato» = la pregunta no se respondió o no se hizo. Cada campo con escala necesita
  su valor desconocido explícito y tablas de equivalencia entre formularios
  («Chico» F1 = «Pequeño» F2). *(HZ09, P)*
- **RN-24 · Equivalencias de normalización** (a validar con el cliente): Arañazo (F1) → Lesión
  compatible (F2) *(HZ05)*; F2 sin ciudad → Ushuaia *(HZ15)*. (La de «con quién estaba», HZ09, cayó con H6 el 2026-10-29.)
- **RN-25 · Datos obligatorios del caso** (definen la marca *Incompleto*, R.08): fecha del hecho,
  ciudad, lugar. *(E1)* La marca mide los campos **del caso**, no los de CASO_ANIMAL: un caso puede
  resolverse del todo sin que sus animales tengan todo cargado. Por eso «animal propio/ajeno» salió
  de esta lista (ahora es la tenencia de cada CASO_ANIMAL, RN-22). *(2026-10-29)*
  (La «situación de la persona» se quitó el 2026-10-29:
  la Entrevista 1 no la definía, su mapeo a H6/H8 era inferido (HZ11), y ambos campos se
  descartaron.) La ficha muestra la
  completitud sobre los 50 campos («38 de 50»). *(P)* Con la obligatoriedad por canal (RN-30) hay
  que separar **obligatorio para nacer** (lo que exige el formulario inicial) de **obligatorio para
  estar completo** (lo que define la marca Incompleto). Pendiente, §9.
- **RN-26 · Datos epidemiológicos clave:** edad, zona del cuerpo, tipo de lugar (ámbito). *(E1)*
  Filtros del mapa: tamaño y color del animal. *(E1)*
  La **zona del cuerpo vive solo en TRATAMIENTO** y la carga Salud (RN-34): la información del caso y
  la de Salud van por separado. Se acepta que los casos que no pasan por Salud no la tengan.
  *(2026-10-29; sale `CASO.zona_cuerpo` de la v1)* Lo mismo con la **gravedad de la lesión** (L2:
  excoriación, herida superficial, herida profunda, amputación): es un dato clínico, vive en
  TRATAMIENTO. *(sale `CASO.gravedad` de la v1)*

### Trazabilidad y seguridad
- **RN-27 · Toda modificación queda en un historial** con fecha, hora, actor (usuario o sistema)
  y su organismo, **valor anterior y valor nuevo**. *(R.29, DS)* La fila viva es la última versión;
  las anteriores pasan a un historial **por entidad** (caso, persona, animal…). Reemplaza la entidad
  AUDITORIA. Todo registro guarda además su marca de tiempo de **creación** y de **última
  modificación**, automáticas *(2026-10-29)*: CASO, PERSONA, ANIMAL, CASO_ANIMAL, DESCRIPCIÓN,
  TRATAMIENTO, DENUNCIA, SOLICITUD, DOCUMENTO, COINCIDENCIA, ORGANISMO, USUARIO y TIPO_EVENTO.
  La última modificación es un dato derivado del historial (si alguna vez no coinciden, gana el
  historial); se guarda para no consultarlo cada vez que se ordena o se muestra la fecha de novedad.
  En SOLICITUD no es derivada: como no lleva historial (RN-31), es el único registro de cuándo se
  tocó. Las tablas de historial no la llevan (una fila de historial nunca se edita). El volumen
  (≈200 casos/año) no es un problema. *(decisión 2026-10-23)*
  **Forma del historial** *(decisión del equipo, 2026-10-29)*: una **tabla espejo** por entidad
  (CasoHistorico, PersonaHistorico, CasoAnimalHistorico…), el patrón de `django-simple-history`.
  - Cada fila es la **foto completa** del registro (*snapshot*), no solo el campo que cambió: así se
    responde «¿cómo estaba el día X?» con una lectura. El valor anterior y el nuevo se obtienen
    comparando dos fotos seguidas.
  - Cada fila guarda además: **tipo de operación** (creación, modificación, baja lógica), fecha y
    hora, **usuario** (o «Sistema») y el **organismo del usuario en ese momento**, copiado en la
    fila: el usuario puede cambiar de organismo, y una fila de historial nunca se edita.
  - **Caso de contexto, opcional:** desde qué caso se hizo el cambio. Es opcional porque una persona
    o un animal también se editan desde su propia vista (§8, Consultas). Así, en un caso se ve si el
    cambio de una persona compartida se hizo ahí o en otro lado (RN-20).
  - La creación también escribe una fila. El borrado no existe (RN-06b).
  - **USUARIO no se borra: se desactiva** *(2026-10-29)*. Los casos y el historial lo referencian, así
    que borrarlo perdería quién hizo cada cosa. Un usuario inactivo no puede entrar, pero sigue
    apareciendo donde actuó. Lo desactiva y lo **reactiva** el administrador (§5); la desactivación
    queda en el historial como baja lógica.
  - **Llevan historial también ORGANISMO, USUARIO y TIPO_EVENTO** *(2026-10-29)*: cambian poco y el
    costo es bajo. En USUARIO es trazabilidad de accesos (quién cambió el rol o el organismo de quién,
    RN-29). Su historial **excluye las credenciales** (hash de contraseña, secretos de 2FA): copiarlas
    en cada foto no aporta nada y multiplica un dato sensible.
- **RN-28 · Las estadísticas se calculan sobre los casos vigentes:** se excluyen los `desestimado`
  y los `unificado` (estos ya están contados en el caso que los absorbió). *(decisión del equipo,
  2026-10-08; reemplaza «solo casos validados», que venía del formulario público)*
- **RN-29 · Datos sensibles** (DNI, historia clínica, contacto) protegidos según Ley 25.326. *(R.16)*
  Acceso por organismo + rol + permisos. *(R.03, R.21)*
- **RN-35 · Los permisos se aplican en el servidor, en dos dimensiones.** *(propuesta del equipo,
  2026-10-29; las reglas concretas se validan con el cliente, §9)*
  - **Por fila:** qué registros ve cada organismo y rol (en DRF, `get_queryset`).
  - **Por campo:** qué datos de cada registro ve (en DRF, el serializer según el rol). P. ej.
    Zoonosis ve **todas** las personas, porque busca víctimas para completar el contacto
    (narrativa, paso 6), y lo que no deba ver se recorta por campo.
  - **El front muestra lo que llega** y no decide por rol: si un campo no vino, la sección no se
    muestra. La regla vive en un solo lugar (el back) y el front no necesita conocer el modelo (§1).
    Ocultar algo solo en pantalla no lo protege.

### Canales de alta y solicitudes *(decisión del equipo, 2026-10-23)*
- **RN-30 · Obligatoriedad del formulario inicial por canal.**

  | Campo | Ciudadano | Zoonosis | CADIC | Salud |
  |---|---|---|---|---|
  | Nombre, apellido, DNI, especie (perro/gato) | ✔ | ✔ | ✔ | ✔ |
  | Teléfono | ✔ | ✔ | — | — |
  | Correo | ✔ | — | — | — |
  | Fecha del hecho | ✔ | ✔ | ✔ | — (la víctima no suele dejarla en la guardia) |

  La obligatoriedad vive en el formulario de cada canal, no en la base (que admite el campo vacío);
  por eso el caso guarda su **canal de origen**: `ciudadano · salud · zoonosis · cadic · historico`.
  **`historico`** *(2026-10-29)* marca los registros viejos (formulario 2025, importaciones): es la
  justificación de que el caso pueda no tener DNI (RN-21) y sirve de filtro. El DNI es obligatorio en todos porque es la clave
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
  observaciones. Se relaciona con el **caso**, no con la persona: si colgara de la persona, se
  mezclarían los tratamientos de mordeduras distintas, y como hay una sola víctima por caso, el
  caso identifica el tratamiento. *(2026-10-28; antes «la participación de la víctima»)* Cubre
  L5–L7 de §7.1. **Todo TRATAMIENTO lo carga solo Salud**: el ciudadano no responde L5–L7 (en el F1
  los respondía porque no había acceso a los datos de Salud). *(2026-10-29)* Lo propio de Zoonosis es el seguimiento del animal (CASO_ANIMAL, RN-22). Falta
  relevar lo de CADIC.

---

## 7. Modelo de datos

**Archivo canónico:** `modelo/siram.mmd`. Hoy contiene la **v1 (2026-10-08), que nunca se publicó y
quedó superada** por la v2 (sus PARTICIPACION y AUDITORIA ya no existen; ANIMAL y DENUNCIA cambiaron).
El modelo vigente es la **v2**, descripta en el recuadro, en este documento y en `modelo/decisiones.md`;
se vuelca a `siram.mmd` cuando esté cerrada.

> **Modelo v2 en preparación (2026-10-23, ampliado 2026-10-28).** La narrativa nueva (§3) y el
> pizarrón del 28/10 cambian el modelo: SOLICITUD (nueva); PERSONA única por DNI; muere
> PARTICIPACION y CASO pasa a tener FK víctima (obligatoria) y FK contacto responsable (opcional);
> CASO con canal de origen y ubicación (punto, dirección, referencias); ANIMAL con identidad (chip)
> y CASO_ANIMAL (nueva, N animales por caso: dueño, tenencia, vacunado, observación del día 10) con
> DESCRIPCIÓN (nueva, 1:1); TRATAMIENTO de Salud (nuevo) ligado al caso; historial por entidad en
> lugar de AUDITORIA; marca de tiempo de creación en todo registro. `siram.mmd` **no se actualiza
> todavía**: faltan definir DENUNCIA, COINCIDENCIA, el certificado médico y lo de CADIC, y el
> modelo es el contrato con el back.

El boceto ER original del equipo (`recursos/boceto-equipo/`) está **superado** y no rige.

### Brechas conocidas (a resolver en esta etapa)
1. **Campos de la matriz sin lugar propio** *(revisado 2026-10-29)*:
   - **Ubicados:** A7 domicilio o zona del animal → CASO_ANIMAL (RN-22); A8 vacuna antirrábica →
     vacunado en CASO_ANIMAL; L5–L7 → TRATAMIENTO (RN-34); D1 y D3 → declarados en CASO (RN-07).
   - **Descartados** (decisión 2026-10-29): P5 tarea si trabaja en la calle, H7 iba con perros,
     H9 tipo de interacción, H11 conoce al propietario del domicilio, A6 el dueño se responsabilizó,
     A10 castrado, D2 denuncia policial, D4 volvió a verlo suelto, D5 comentarios, X3 firma y
     ratificación, H6 con quién estaba, H8 actividad al momento del ataque, H10 otros datos /
     testigos / observaciones del hecho (por ahora no hacen falta observaciones sobre el caso).
   - **Observaciones** *(2026-10-29)*: no en CASO, pero sí en DENUNCIA (opcional, para Zoonosis; el
     F2 tiene uno) y en CASO_ANIMAL (generales del seguimiento, aparte de la observación del día 10).
   - **Derivados:** L4 recibió atención médica = el caso tiene un TRATAMIENTO (que carga solo Salud,
     RN-34); no se guarda ni se le pregunta al ciudadano. *(2026-10-29)*
   - **Documentos** *(2026-10-29)*: X1 certificado médico → DOCUMENTO del caso, lo carga Zoonosis
     (que lo adjunte el ciudadano es posible pero improbable; no se diseña por ahora). X2 fotos: las
     del animal → CASO_ANIMAL; las del lugar se descartan. Ver §7.4.
   - **Descartado también:** A12 mordedor reincidente (lo declarado por la persona). La reincidencia
     dentro de SIRAM se ve en los casos del animal (RN-22). *(2026-10-29)*
   - **A11 puede identificar al animal** → se queda: es una pregunta del formulario detallado del
     ciudadano (RN-32) y se guarda por animal en CASO_ANIMAL. Las secciones y preguntas de ese
     formulario se definen más adelante. *(2026-10-29)*
   - **A medias:** L1 (HZ05, pendiente con TIPO_EVENTO en §9).
2. ~~**Booleanos que no admiten «No sabe»** (`reincidente`, `atencion_medica`) — ver RN-23.~~ Ninguno
   queda: `atencion_medica` se deriva de TRATAMIENTO y `reincidente` se descartó. *(2026-10-29)*
3. ~~Fecha de la denuncia (H0)~~ Resuelta en v1: `DENUNCIA.fecha`.
4. **DOCUMENTO sin atributos:** necesita al menos tipo (certificado médico, foto),
   archivo y fecha. El pizarrón del 28/10 deja abierto el **certificado médico**: ¿se modela (con
   sus datos) o solo se guarda el archivo? *(2026-10-29: lo más probable es que sea solo una
   referencia al archivo; sin cerrar.)* Ya decidido *(2026-10-29)*: el certificado se adjunta al
   **caso** y lo carga Zoonosis; las fotos son **del animal** y se adjuntan a CASO_ANIMAL (las del
   lugar se descartaron). Falta definir los atributos.
5. **Lote de importación:** el historial del prototipo cita «lote ZU-2026-09-12» y R.25 pide
   informar qué filas no entraron y por qué; no hay entidad que lo guarde.
6. ~~**Coordenadas:** falta definir cuándo y cómo se geocodifica (H4 es texto libre en F1 y calle+número en F2).~~
   Resuelta por RN-33 (pin + dirección + referencias). Queda abierto solo para importaciones.
7. **Lesión múltiple:** hoy es un valor «Múltiples» en un enumerado. Si hay que registrar cada
   lesión por separado, pasa a ser entidad.
8. **Motivo de desestimación del caso:** CASO puede quedar `desestimado` pero no guarda por qué.
   Falta el campo y la lista de motivos (pendiente, ver §9).
9. ~~**Registro histórico:** RN-21 exige DNI salvo en registros históricos; el modelo tiene que poder
   distinguirlos (p. ej. por origen o lote de importación) para aplicar la obligatoriedad.~~
   Resuelta: `canal_origen = historico` (RN-30). *(2026-10-29)*
10. **Vínculos entre casos:** COINCIDENCIA guarda la sospecha y la decisión; falta confirmar que
   alcanza para representar el vínculo vigente (y su reversión) de un evento relacionado. Con el
   animal con identidad (RN-22), dos casos que comparten un ANIMAL ya son del mismo animal: revisar
   si eso reemplaza parte del vínculo.

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
  Posibles duplicados, Eventos relacionados, Incompletos) · Consultas (Búsqueda, Personas, Animales,
  Mapa, Estadísticas, Exportar) · Administración (Usuarios, Organismos, Tipos de evento, Importar CSV).
  Personas y Animales van en Consultas porque se **consultan**, no se trabajan (lo único que SIRAM
  trabaja es el caso). Patrón lista–detalle: la tabla tiene pocas columnas (nombre, cantidad de casos)
  y el detalle muestra los datos y **todos sus casos** (RN-20, RN-22). DNI y contacto, solo en el
  detalle (minimización, RN-29). *(2026-10-29)*
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
  *(2026-10-28: la observación del día 10 ya tiene lugar en CASO_ANIMAL, RN-22. Con varios animales
  por caso, ¿el caso finaliza cuando todos pasan el día 10?)*
- ~~**A CONFIRMAR · Lista de entes** donde se puede haber hecho una denuncia (Zoonosis, policía…).~~
  Solo Zoonosis (RN-06). La policía no se modela; muy a futuro, podría sumarse. *(2026-10-29)*
- **A DEFINIR · ¿Cuáles son las causas posibles para desestimar un caso?** Ninguna fuente las define.
  Propuesta inicial (sin aprobar), con motivo obligatorio + observación libre:
  - *Fuera de alcance*: no fue un ataque de perro o gato a una persona (RN-02).
  - *Fuera de la provincia*: el hecho ocurrió fuera de Tierra del Fuego.
  - *Hecho inexistente o no verificable*: al verificarlo, no ocurrió o los datos eran falsos.
  - *Cargado por error*: registro accidental o de prueba.
  Excluidos a propósito: *duplicado* (su desenlace es consolidar → `unificado`, RN-14) y
  *datos insuficientes* (es la marca Incompleto; desestimarlo generaría subregistro).
- ~~Qué es exactamente la «situación de la persona» (HZ11).~~ Se quitó de los obligatorios: H6 y H8
  se descartaron (RN-25). *(2026-10-29)*
- ~~Si SIRAM guarda los datos del denunciante o solo los del lesionado (HZ07).~~ Guarda víctima y
  contacto responsable (RN-20); del denunciante, solo el DNI en la denuncia. *(2026-10-28)*
- ~~¿Vuelve el formulario público (R.17)?~~ Sí, como solicitud (RN-08, RN-31, RN-32). *(2026-10-23)*
- **DENUNCIA: no se sabe cómo la guardan los entes** ni si tiene un identificador. No se modela el
  formulario real de la denuncia. **Propuesta en revisión** (2026-10-23), pensada para poder ubicar
  la denuncia en el sistema del ente:

  | Campo | Tipo | Obligatorio |
  |---|---|---|
  | DNI del denunciante | Texto con validación de formato; referencia lógica, **sin** enlace a PERSONA | Sí |
  | Fecha de la denuncia | Fecha | Sí |
  | Número de trámite | Texto libre, sin validación (no se sabe si existe ni su formato) | No |
  | Observaciones | Texto libre, para la gente de Zoonosis *(2026-10-29)* | No |

  DNI + fecha funcionan como clave de búsqueda en el sistema de Zoonosis. *(2026-10-29: el ente salió
  de la tabla porque por ahora es siempre Zoonosis; si se suma otro, vuelve como campo obligatorio.
  Lo que declara la persona sobre su denuncia es otro dato, RN-07.)*
- ~~**Roles de las personas.**~~ Resuelto el 2026-10-28 (RN-20, RN-22): **víctima** y **contacto
  responsable** son dos FK del caso; muere PARTICIPACION y con ella `vinculo` (la relación no
  importa); el **dueño** va en CASO_ANIMAL. Lo que sigue vale igual:
  - El formulario inicial pregunta «¿Es usted quien sufrió el ataque?». Si sí, una sola persona
    (víctima). Si no, se cargan los datos del contacto responsable y además los de la víctima.
  - De la víctima son obligatorios todos los datos del formulario inicial **salvo el contacto**
    (un niño no lo tiene). Lo que se mide en SIRAM son las víctimas y los ataques.
  - **Una víctima por caso:** dos víctimas del mismo ataque son dos casos (cada una tiene su
    seguimiento y su atención). Coincide con RN-14: los eventos relacionados se vinculan, nunca se
    fusionan.
- **Datos propios de Zoonosis y CADIC** para completar un caso (los de Salud están en RN-34).
- **CONSULTAR CON SALUD · Esquema de la vacuna antirrábica.** La profilaxis post-exposición es un
  esquema de varias dosis en días distintos, no una sola. ¿Les interesa registrar el esquema
  (dosis y fechas) o solo si se inició? Hoy el tratamiento lo trata como un sí/no. *(2026-10-28)*
- **Observación del animal: solo el décimo día** (decidido 2026-10-28). Zoonosis registra si el
  animal sigue bien al día 10, no el estado en cada visita; alcanza con una fila de CasoAnimal que
  se actualiza. El registro por visita fue un supuesto sin base en el dominio y se descartó.
  **Repreguntar en la segunda entrevista con Zoonosis**; puede cambiar según lo que respondan.
- **Personas sin DNI argentino** (p. ej. turistas extranjeros mordidos en Ushuaia): RN-21 exige DNI
  y la validación de formato lo asume. ¿Pasaporte? ¿Tipo + número de documento? A futuro, no se
  resuelve ahora. *(2026-10-23)*
- **Caso sin contacto que nunca se ubica** (p. ej. alta de Salud sin teléfono ni correo, y la
  búsqueda por DNI no da resultado): ¿queda `en_seguimiento` para siempre? ¿cuenta en estadísticas?
- ~~**¿Participa realmente Zoonosis?** (pizarrón 08/10)~~ Sí: es canal de alta, anota las denuncias
  (RN-06) y hace el seguimiento del animal (RN-22). *(2026-10-29)*
- ~~¿Qué denuncias se asocian a un caso?~~ Solo las hechas en Zoonosis, anotadas por Zoonosis
  (RN-06). *(2026-10-29; antes «cualquier denuncia oficial ante un ente»)*
- **¿Hay problema en pedir el DNI en el formulario público?** RN-21 y RN-30 ya lo exigen; falta que
  el cliente lo acepte. *(2026-10-29: el formulario público ahora es el de SIRAM, no el F1 de
  Schiavini.)*
- Reglas de visibilidad entre organismos (¿Zoonosis ve los casos de Salud?) y si un agente puede
  editar casos creados por otro organismo. El mecanismo ya está propuesto (RN-35, por fila y por
  campo); faltan las reglas concretas. Llevar a la entrevista con Salud: ¿le sirve ver la
  observación del día 10 del animal? Puede cambiar el tratamiento antirrábico de la víctima.
- Qué hacer con «Mal estado de ADC» y «Otros» del F2 (HZ03); «Conoce al propietario del domicilio» (HZ16).
- Formato real de entrada: export del F1 (HZ13, HZ14), transcripción del F2 en papel (HZ12), y el F3.
- 2FA (R.05) y políticas de seguridad (Ley 25.326): sin definir.

**Pendientes de diseño (agente de diseño):**
- **Motor de coincidencias nuevo** (lo diseña el equipo; después se compara con RN-16 a RN-18) y
  qué pasa con COINCIDENCIA y con el vínculo entre casos (§7.10). *(2026-10-23)* Dos cosas que el
  motor nuevo tiene que tener en cuenta *(2026-10-29)*: la señal «mismo animal (color, tamaño, raza)»
  de RN-16 choca con RN-22 (DESCRIPCIÓN no es parte del motor); y con el animal con identidad, dos
  casos que comparten un ANIMAL ya son del mismo animal, lo que cambia la definición de evento
  relacionado (glosario, RN-14).
- ~~**Historial en detalle**~~ Resuelto el 2026-10-29: tablas espejo con foto completa, organismo
  del momento y caso de contexto opcional (RN-27); animal absorbido (RN-22). Falta diseñar la
  **pantalla de historial**: va a ser una pantalla propia (decidido 2026-10-29), sin definir todavía.
- **Estados del caso:** revisarlos junto con la narrativa general (RN-09, RN-10). *(2026-10-29)*
- **Obligatorio para nacer vs. para estar completo** (RN-25, RN-30).
- **Retención de solicitudes descartadas:** el borrado lógico conserva DNI y contacto; la Ley 25.326
  pide minimización. ¿Plazo de retención?
- **Cola de solicitudes en la navegación** (§8: dos niveles, nunca tres) y el formulario público
  (inicial + detallado + pin).
- ~~**«Fecha de novedad» de la bandeja.**~~ Es la **última modificación** del caso, el campo
  automático de RN-27, para que suba lo que alguien tocó recientemente. *(2026-10-29; antes «derivarla
  de AUDITORIA»)* El prototipo todavía ordena por número de caso (orden de registro).
- **Columna «Lesión» / tipo de lesión (HZ05).** Hoy mezcla L1 (Mordedura, Arañazo) con T1 («Lesión
  compatible», categoría de denuncia del F2). Falta definir la lista de valores de SIRAM y la
  traducción de cada formulario. Atado a esto: el catálogo **«Tipo de evento»** (T1; antes «Tipo de
  denuncia», renombrado el 2026-10-08) clasifica al caso. Propuesta de valores, a validar: ataque con
  mordedura · lesión compatible · intento sin lesión · lesión derivada (RN-03). Falta deslindarlo del
  tipo de lesión (L1) para que no repitan lo mismo. Traducción del F2: «Mordedura de ADC» → ataque
  con mordedura; «Lesión compatible con ataque de ADC» → lesión compatible.
  **TIPO_EVENTO se mantiene en la v2** con esos cuatro valores; cada caso tiene **uno**.
  *Pendiente de confirmación* (con el cliente): los valores, el deslinde con L1, y si es catálogo
  administrable (tabla con FK desde CASO, como sugiere el menú Administración › Tipos de evento) o un
  enumerado dentro de CASO. Es una dimensión chica: agregarla, quitarla o cambiar su forma más
  adelante no altera el resto del modelo. *(2026-10-29)*

**Pendientes técnicos (agente de código):**
- Librería de UI y estructura del front. Los componentes son propios, escritos tomando como
  referencia los paquetes funcionales de Delta (§8). Se evaluó **Refine** (MIT; React Query + MUI +
  React Hook Form, cercano a Delta) como base; **no está decidido**.
- Versiones: ¿se arranca con las de Delta (React 18.2, MUI 7.1) o con las actuales? ¿Qué se gana y
  qué se pierde? Como SIRAM no depende de los paquetes de Delta, no está atado a sus versiones;
  usar las mismas solo facilita leer y copiar patrones.
- Tecnología geográfica (RT.06): mapa y geocodificación ya elegidos (RN-33); falta la base geográfica
  (¿PostGIS?).
- **Abuso del canal ciudadano (a revisar, no se resuelve ahora).** Alguien podría enviar miles de
  solicitudes basura. La integridad está a salvo (una solicitud no llega a un caso sin un agente),
  pero la carga de trabajo no. **Hay que revisar rate limiting** (por IP y/o por DNI);
  CAPTCHA como complemento. *(2026-10-23)*

---

## 10. Mapa de recursos

Todo en `recursos/` (solo lectura). Si dos fuentes se contradicen, el orden de precedencia es:
**este documento › `modelo/siram.mmd` › «De las pantallas al modelo» › «De Delta a SIRAM» ›
prototipo › matriz de formularios › PDF de requerimientos.** Mientras la v2 no esté volcada en
`siram.mmd`, lo que ese archivo contiene (la v1, superada) no rige (§7).

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
| `pizarron-2026-10-28/` | Modelo del equipo en pizarrón, dos iteraciones (azul y correcciones en rojo): muere Participación, Caso Animal, Descripción, Tratamiento, Certificado médico. Destilado en RN-20, RN-22, RN-34 y §7 |
