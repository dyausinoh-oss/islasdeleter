# Documento de Diseño de Juego y Arquitectura Técnica (GDD v2.0): "Las Islas del Éter"

## 1. Visión General del Proyecto
- **Género:** MMORPG / Co-op Action-Tab RPG Estilizado (inspirado en *World of Warcraft* y *Guild Wars 2*).
- **Motor y Stack:** Unity (C#), Mirror Networking, base sobre plantilla `uMMORPG`, persistencia en SQLite.
- **Estilo Visual:** Fantasía estilizada vibrante (proporciones heroicas, colores saturados, sombras suaves).
- **Filosofía de Diseño (Anti-Fricción):**
  - **Cero FOMO:** Sin misiones diarias obligatorias; recompensas semanales acumulables.
  - **Progresión Horizontal:** El equipo base es accesible; el progreso a largo plazo desbloquea **Reliquias** (modificadores de habilidades), cosméticos y mejoras comunitarias.
  - **Sin colas eternas:** Todas las clases son híbridas con cambio de postura fuera de combate.
  - **Juego inmediato con amigos:** Escalado dinámico de estadísticas en grupo sin importar la diferencia de nivel.

---

## 2. Mundo e Historia: "La Fractura" y "El Bastión"
- **Premisa:** Un cataclismo mágico (*La Fractura*) fragmentó el continente en cientos de islas flotantes inexploradas.
- **Facción Única (*La Liga de los Pioneros*):** Todos los jugadores pertenecen a la misma alianza para no dividir a la comunidad.
- **Hub Social (*El Bastión*):** Una ciudad flotante neutral donde convergen todos los jugadores. Los recursos extraídos de las islas instanciadas se entregan aquí para reconstruir edificios, puentes, herrerías y portales de forma permanente para todo el servidor.

---

## 3. Bucle de Juego (Gameplay Loop) y Economía
1. **Preparación en El Bastión:** El jugador ajusta su postura, equipa sus 3 Reliquias y habla con el *Cartógrafo* para abrir un portal hacia una isla instanciada (en solitario o en *Party* de hasta 4 jugadores).
2. **Expedición (10–15 min):** Limpieza de grupos de enemigos, resolución de un evento dinámico de purificación y combate contra el Jefe Final.
3. **Recompensas (3 monedas claras sin basura en inventario):**
   - **Fragmentos de Éter (Individual):** Para comprar y mejorar las 3 ranuras de **Reliquias**.
   - **Madera y Piedra Etérea (Comunitario):** Se entregan al *Intendente* de El Bastión para avanzar la barra global de construcción del servidor.
   - **Esencia Cosmética (Individual):** Para desbloquear apariencias de armas, capas y monturas.

---

## 4. Razas Jugables (Liga de los Pioneros)
| Raza | Apariencia (Estilo WoW) | Pasiva Racial (Balanceada) |
| :--- | :--- | :--- |
| **Valkirios** (Humanos del Éter) | Proporciones heroicas, runas celestes brillantes en la piel. | **Ingenio del Bastión:** +10% velocidad de recolección y artesanía (`gathering_speed_multiplier = 1.10f`). |
| **Silvanos** (Elfos de Raíz) | Altos, orejas largas, piel con tonos corteza/musgo y ojos luminosos. | **Paso Ligero:** +5% velocidad fuera de combate (`out_of_combat_move_speed = 1.05f`) y -30% daño de caída. |
| **Forjados de Piedra** (Enanos Rúnicos) | Bajos, muy robustos, barbas trenzadas y articulaciones de roca rúnica. | **Piel de Granito:** +10% resistencia elemental (`elemental_resistance_bonus = 0.10f`) y +6 ranuras de inventario. |

---

## 5. Clases Híbridas y Sistema de Habilidades (24 Habilidades)

### Estructura Universal de la Barra (8 Slots por Postura)
- **Slot 1:** Generador rápido (0 s CD, genera +15 recurso).
- **Slot 2:** Generador secundario / AoE (5–6 s CD, genera +20 recurso).
- **Slot 3:** Gastador principal (consume 35–40 recurso, impacto/curación fuerte).
- **Slot 4:** Gastador táctico (consume 30–45 recurso, área persistente/control).
- **Slot 5:** Movilidad / Defensa activa (12–14 s CD, para esquivar áreas rojas en el suelo).
- **Slot 6:** Control / Interrupción (15–18 s CD, corta casteos de jefes o aturde).
- **Slot 7:** Utilidad grupal (30–45 s CD, beneficio directo a toda la *Party*).
- **Slot 8:** Definitiva (75–90 s CD, habilidad insignia del rol).

---

### Clase 1: Centinela Rúnico (Martillo y Escudo | Recurso: *Fervor Rúnico* 0–100)
- **Pasiva de Clase — Armadura Resonante:** Gastar *Fervor Rúnico* otorga una carga de *Resonancia* (máx. 5) que aumenta un 2% la armadura (Baluarte) o el daño físico (Verdugo).

| Slot | Postura 1: Baluarte (Tanque) | Postura 2: Verdugo (Daño Cuerpo a Cuerpo) |
| :--- | :--- | :--- |
| **1** | **Golpe de Escudo** (0 s): Golpe frontal (+15 Fervor, alta amenaza). | **Tajo Rúnico** (0 s): Corte rápido con martillo (+15 Fervor). |
| **2** | **Onda Telúrica** (6 s): Cono frontal que ralentiza (+20 Fervor). | **Martillo Giratorio** (6 s): Daño en área alrededor (+20 Fervor). |
| **3** | **Égida de Éter** (-40 Fervor): Escudo absorbente por 6 s. | **Ejecución Arcana** (-40 Fervor): Golpe fuerte; +50% daño si objetivo <30% vida. |
| **4** | **Sello de Provocación** (-30 Fervor): Provoca enemigos cercanos y reduce su daño 10%. | **Fisura Incandescente** (-35 Fervor): Línea de fuego en el suelo por 5 s. |
| **5** | **Carga Inquebrantable** (14 s): Embestida rápida con inmunidad al control 2 s. | **Salto Sísmico** (14 s): Salto dirigido para esquivar áreas en el suelo. |
| **6** | **Reprimenda de Escudo** (15 s): Interrumpe casteo enemigo y silencia 2 s. | **Impacto Aturdidor** (18 s): Aturde 3 s e interrumpe casteo. |
| **7** | **Grito de Reagrupación** (40 s): +20% vida máxima temporal a la *Party* por 8 s. | **Estandarte de Guerra** (45 s): +15% daño crítico grupal por 10 s. |
| **8** | **Bastión Viviente** (90 s): -60% daño recibido y redirige daño de aliados por 8 s. | **Juicio del Cataclismo** (75 s): Martillo gigante orbital en área. |

- **Reliquias de Ejemplo:**
  - *Escudo Espejo:* **Égida de Éter** refleja 30% del daño absorbido como daño arcano.
  - *Furia Sísmica:* **Fisura Incandescente** reduce 1 s el CD de **Salto Sísmico** por enemigo golpeado.

---

### Clase 2: Tejedor de Mareas (Báculo Elemental | Recurso: *Flujo Elemental* 0–100)
- **Pasiva de Clase — Corriente Vital:** Lanzar un hechizo de agua reduce en 25% el tiempo de lanzamiento de tu siguiente hechizo de viento/rayo (y viceversa).

| Slot | Postura 1: Restaurador (Sanador) | Postura 2: Tempestad (Daño Mágico a Distancia) |
| :--- | :--- | :--- |
| **1** | **Pulso de Rocío** (0 s): Sanación rápida a un aliado (+15 Flujo). | **Descarga Estática** (0 s): Proyectil de rayo (+15 Flujo). |
| **2** | **Brisa Renovadora** (5 s): Curación en el tiempo (HoT) que salta a 2 aliados (+20 Flujo). | **Torrente Ascendente** (5 s): Daño de agua; aplica *Empapado* (+15% daño rayo, +20 Flujo). |
| **3** | **Oleada Curativa** (-40 Flujo): Curación directa potente de emergencia. | **Relámpago Encadenado** (-40 Flujo): Impacta al objetivo y salta a 3 enemigos. |
| **4** | **Lluvia Purificadora** (-45 Flujo): Área circular en el suelo que cura aliados por 6 s. | **Vórtice Marino** (-45 Flujo): Remolino en el suelo que daña y atrae enemigos por 5 s. |
| **5** | **Forma de Niebla** (12 s): Intangibilidad y +60% velocidad por 2.5 s. | **Impulso de Vendaval** (12 s): Salto hacia atrás dejando ráfaga ralentizadora. |
| **6** | **Burbuja de Estasis** (18 s): Encierra a un enemigo 3 s e interrumpe su ataque. | **Cizalla de Viento** (15 s): Interrupción instantánea a distancia. |
| **7** | **Tótem de Marea Calma** (35 s): Disipa efectos negativos del grupo y regenera recursos. | **Vientos Favorables** (40 s): +20% velocidad de movimiento y celeridad grupal por 8 s. |
| **8** | **Diluvio Ancestral** (90 s): Tormenta curativa masiva en área por 6 s. | **Ira del Huracán** (75 s): Canalización continua de rayos en área grande por 4 s. |

- **Reliquias de Ejemplo:**
  - *Lluvia Ácida:* **Lluvia Purificadora** también inflige daño mágico por segundo a enemigos dentro del área.
  - *Ojo de la Tormenta:* Permite moverse mientras canalizas **Ira del Huracán**.

---

### Clase 3: Acechador Etéreo (Ballesta y Dagas | Recurso: *Carga Etérea* 0–100)
- **Pasiva de Clase — Oportunismo:** Golpear por la espalda o a un enemigo bajo control/trampa garantiza golpe crítico (enfriamiento interno de 8 s).

| Slot | Postura 1: Francotirador (Daño Físico a Distancia) | Postura 2: Estratega (Soporte Táctico / Evasión) |
| :--- | :--- | :--- |
| **1** | **Disparo Perforante** (0 s): Virote rápido (+15 Carga). | **Dardo Aturdidor** (0 s): Disparo ligero que ralentiza (+15 Carga). |
| **2** | **Trampa de Fragmentación** (6 s): Explosión en área al contacto (+20 Carga). | **Bomba de Esporas Etéreas** (6 s): Nube que reduce precisión enemiga (+20 Carga). |
| **3** | **Tiro de Precisión** (-40 Carga): Disparo cargado de alto daño individual. | **Inyección de Adrenalina** (-35 Carga): Dardo aliado que otorga escudo y +15% daño por 6 s. |
| **4** | **Lluvia de Virotes** (-45 Carga): Descarga aérea en área circular por 4 s. | **Señuelo Holográfico** (-40 Carga): Clon ilusorio que provoca enemigos cercanos por 5 s. |
| **5** | **Voltereta Sombría** (12 s): Esquiva direccional con 100% evasión por 1 s. | **Paso de Fase** (12 s): Teletransporte corto instantáneo hacia adelante. |
| **6** | **Disparo Mordaza** (15 s): Silencia e interrumpe casteos a distancia. | **Red Magnética** (18 s): Inmoviliza un grupo pequeño de enemigos por 3 s. |
| **7** | **Bengala Reveladora** (30 s): Ilumina zona; enemigos reciben +15% daño. | **Bomba de Humo Protectora** (40 s): Aliados dentro reciben -25% daño en área. |
| **8** | **Andanada del Vacío** (75 s): 10 virotes oscuros consecutivos de alto impacto. | **Distorsión Temporal** (90 s): Cúpula que acelera 30% los enfriamientos del grupo por 8 s. |

- **Reliquias de Ejemplo:**
  - *Clon Explosivo:* Al expirar o ser destruido, **Señuelo Holográfico** explota aturdiendo 1.5 s.
  - *Virotes Tóxicos:* **Tiro de Precisión** aplica veneno acumulable durante 6 s.

---

## 6. Primera Instancia (Vertical Slice): "El Observatorio Sumergido"
Diseñada para probar en 10 minutos todas las mecánicas clave del juego:
1. **Enemigos Base (3 Arquetipos):**
   - *Autómata Rúnico:* Golpes frontales pesados (prueba amenaza del Tanque/Estratega).
   - *Invocador de la Fractura:* Castea *"Descarga Corrupta"* (2.5 s) que sana aliados (prueba interrupción en **Slot 6**).
   - *Enjambre Etéreo:* Grupos de 5 enemigos débiles (prueba habilidades AoE en **Slots 2 y 4**).
2. **Jefe Final — Kaelen, el Vigía Corrompido:**
   - *Impacto Orbital (Telemetría):* Dibuja un círculo rojo bajo los jugadores; tras 2 s cae un rayo letal (prueba esquiva en **Slot 5**).
   - *Pulso de la Fractura:* Onda inevitable cada 30 s que daña a todo el grupo (prueba sanación y utilidades de **Slot 7**).

---

## 7. Especificación Técnica de Arquitectura (Unity + Mirror + SQLite)

### Sistema A: `PlayerStanceController` (`NetworkBehaviour`)
- `StanceData : ScriptableObject` (`stanceId`, `role`, `statMultipliers`, `ScriptableSkill[8] defaultSkillBar`, `RuntimeAnimatorController`).
- `[SyncVar(hook = nameof(OnStanceChanged))] public int activeStanceIndex;`
- `[Command] public void CmdSwitchStance(int targetIndex)`: Valida `!player.isInCombat`, cooldown interno (`>= 5.0f` s), resetea recurso de clase, actualiza estadísticas y reemplaza los 8 slots activos.

### Sistema B: `PlayerRelicSystem` (`NetworkBehaviour`)
- Patrón Decorador/Strategy sobre `SkillContext`.
- `RelicData : ScriptableObject` (`relicId`, `targetSkillId`, `effectType`, `valueModifier`, `customVfxPrefab`).
- `[SyncList<string>] public readonly SyncList<string> equippedRelicIds` (máx. 3 elementos, editable solo en El Bastión).

### Sistema C: `PartyDynamicScaler` (`NetworkBehaviour`)
- Normalización dinámica ejecutada en `[Server]` dentro del cálculo de daño/curación (`DealDamageAt`).
- Coeficiente $K = rac{	ext{BaseStat}(L_{	ext{target}})}{	ext{BaseStat}(L_{	ext{player}})}$ aplicado sobre el daño y curación saliente para permitir juego cooperativo entre niveles dispares, manteniendo recompensas escaladas a $L_{	ext{player}}$.

### Sistema D: `BastionWorldState` (Singleton `NetworkBehaviour` + SQLite)
- Persiste en SQLite (`CREATE TABLE IF NOT EXISTS bastion_projects (project_id TEXT PRIMARY KEY, current_amount INTEGER, goal_amount INTEGER, completed INTEGER)`).
- `SyncDictionary<string, int> projectProgress` sincronizado a todos los clientes.
- Al alcanzar `current_amount >= goal_amount`, dispara `[ClientRpc] RpcActivateStructure(string projectId)` para habilitar los GameObjects, NPCs y portales de la mejora en El Bastión.

---

## 8. Plan de Ejecución por Fases para OpenAI Codex
- **FASE 1 (Auditoría):** Inspeccionar `Assets/` de `uMMORPG` (`Player.cs`, `Skill.cs`, `Combat.cs`, `Party.cs`, `Database.cs`) y entregar mapa de dependencias sin modificar archivos.
- **FASE 2 (Posturas):** Implementar `StanceData` y `PlayerStanceController.cs` + barra de 8 habilidades.
- **FASE 3 (Reliquias y Combate Híbrido):** Implementar `PlayerRelicSystem` y `TelegraphZone` para áreas esquivables.
- **FASE 4 (Escalado y Bastión):** Implementar `PartyDynamicScaler` y persistencia SQLite de `BastionWorldState`.
