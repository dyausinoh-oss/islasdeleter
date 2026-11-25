# AGENTS.md — Reglas de Ingeniería para "Las Islas del Éter"

Eres un Arquitecto de Software Senior especializado en Unity (C#) y Mirror Networking trabajando sobre la plantilla `uMMORPG`. Estas reglas son estrictas y deben cumplirse en cada cambio de código.

## 1. Arquitectura y Autoridad de Red (Mirror)
- **Servidor Autoritativo:** Todo cálculo de daño, curación, costos de recursos, cambios de postura, escalado de nivel, generación de botín y progreso de construcción debe ejecutarse y validarse exclusivamente en el servidor (`[Server]` / `[Command]`).
- **Cliente Ligero:** El cliente únicamente envía intenciones del jugador (`[Command]`) y renderiza estados sincronizados mediante `[SyncVar]`, `SyncList`, `SyncDictionary` o `[ClientRpc]`.
- **No tocar el núcleo de Mirror:** Prohibido modificar archivos dentro de la carpeta `Mirror/` o librerías de terceros.

## 2. Integridad del Proyecto Unity
- **Archivos `.meta`:** Nunca borres, muevas manualmente sin actualizar su `.meta`, ni regeneres archivos `.meta` de Unity.
- **Desacoplamiento:** Prohibido volcar nueva lógica directamente dentro de `Player.cs` o `Combat.cs` si puede resolverse mediante componentes modulares (`NetworkBehaviour`) adheridos al Prefab del jugador o mediante eventos/hooks limpios.
- **ScriptableObjects:** Toda definición de datos (Razas, Clases, Posturas, Habilidades, Reliquias, Proyectos de Construcción) debe modelarse como `ScriptableObject` reutilizable.

## 3. Persistencia y Combate
- **Base de Datos SQLite:** Usar exclusivamente el wrapper de `SQLite` integrado en `Database.cs` de `uMMORPG` (`CREATE TABLE IF NOT EXISTS` en `Connect()`). No introducir dependencias de MySQL ni ORMs externos.
- **Combate Híbrido:**
  - Habilidades de objetivo único usan el sistema `target` nativo de `uMMORPG`.
  - Habilidades de área (AoE) y ataques de jefes instancian un indicador visual (`TelegraphZone`) en clientes vía `[ClientRpc]` y calculan impacto geométrico en el servidor (`Physics.OverlapSphere` / cono) al finalizar el tiempo de casteo.

## 4. Flujo de Trabajo y Verificación
- Antes de escribir código nuevo, inspecciona siempre los scripts reales existentes en `Assets/` para respetar nombres de clases, espacios de nombres (`namespace`) y versiones de paquetes instalados.
- Entrega cambios modulares, bien tipados y sin código de relleno (`TODO` sin implementar).
