# Java 1.8 Packet Translation Guide

This guide pairs every console-edition packet identifier with the closest matching
Minecraft: Java Edition 1.8 packet. Use it when building a protocol translation
layer so that Bedrock/Console clients can interoperate with Java servers. Each
entry calls out the Java protocol state and numeric ID alongside any translation
notes. When no direct analogue exists in Java 1.8 the table explains how the
feature can be emulated or whether a custom plugin channel is required.

## Legend

| Column | Meaning |
| --- | --- |
| **LCE ID** | Numeric value from `EPacketType` (see [`packet-ids.md`](./packet-ids.md)). |
| **LCE Packet** | Generated packet class used by the console edition. |
| **Direction** | Perspective from the LCE client. |
| **Java 1.8 Equivalent** | Packet name and state from the vanilla Java 1.8 protocol. |
| **Translation Notes** | Important field mappings, divergences, or fallbacks. |

## Connection and Session Packets

| LCE ID | LCE Packet | Direction | Java 1.8 Equivalent | Translation Notes |
| --- | --- | --- | --- | --- |
| 0 | `ClientboundKeepAlivePacket` | Server → Client | Play S→C Keep Alive (0x00) | Field payloads match; forward ID as 32-bit integer. |
| 1 | `ClientboundLoginPacket` | Server → Client | Play S→C Join Game (0x01) | Map dimension, difficulty, gamemode flags; strip console-only world options. |
| 2 | `ClientboundPreLoginPacket` | Server → Client | Login S→C Login Success (0x02) | Embed UUID/username; omit console entitlements. |
| 7 | `ServerboundInteractPacket` | Client → Server | Play C→S Use Entity (0x02) | Translate interaction type enum and sneaking flag. |
| 8 | `ClientboundSetHealthPacket` | Server → Client | Play S→C Update Health (0x06) | Convert hunger/saturation scale to floats. |
| 9 | `ClientboundRespawnPacket` | Server → Client | Play S→C Respawn (0x07) | Re-map dimension + difficulty; respect hardcore bit semantics. |
| 14 | `ServerboundPlayerActionPacket` | Client → Server | Play C→S Player Digging (0x07) | Translate action reasons; adapt block position packing. |
| 15 | `ServerboundUseItemPacket` | Client → Server | Play C→S Player Block Placement (0x08) | Align facing enum and cursor offsets. |
| 17 | `ClientboundPlayerSleepPacket` | Server → Client | Play S→C Use Bed (0x0A) | Requires forging placeholder beds for mini-games that lack world beds. |
| 19 | `ServerboundPlayerCommandPacket` | Client → Server | Play C→S Entity Action (0x0B) | Convert sprint/sneak flags and riding jump boost. |
| 27 | `ServerboundPlayerInputPacket` | Client → Server | Play C→S Steer Vehicle (0x0C) | Forward float inputs; drop jump/unjump bits unsupported by mount. |
| 135 | `ServerboundAcceptTeleportationPacket` | Client → Server | Play C→S Teleport Confirm (0x00 in 1.9+) | Java 1.8 lacks this packet; translate into immediate position ack or piggyback onto movement packet. |
| 205 | `ServerboundClientCommandPacket` | Client → Server | Play C→S Client Status (0x16) | Map respawn and statistics requests. |
| 226 | `ServerboundKeepAlivePacket` | Client → Server | Play C→S Keep Alive (0x00) | Mirror token back to server. |
| 255 | `DisconnectPacket` | Server → Client | Play S→C Disconnect (0x40) | Forward JSON chat component; convert console kick codes to plain text. |

## Chat, UI, and Social Packets

| LCE ID | LCE Packet | Direction | Java 1.8 Equivalent | Translation Notes |
| --- | --- | --- | --- | --- |
| 3 | `ClientboundChatPacket` | Server → Client | Play S→C Chat Message (0x02) | Convert console formatting tokens to JSON chat components. |
| 16 | `ClientboundSetCarriedItemPacket` | Server → Client | Play S→C Held Item Change (0x09) | Direct slot index mapping. |
| 18 | `ClientboundAnimatePacket` | Server → Client | Play S→C Animation (0x0B) | Align animation IDs (swing, hurt, etc.). |
| 20 | `ClientboundAddPlayerPacket` | Server → Client | Play S→C Spawn Player (0x0C) | Inject skin/profile properties absent on console. |
| 35 | `ClientboundRotateHeadPacket` | Server → Client | Play S→C Entity Head Look (0x19) | Convert yaw to byte range expected by Java. |
| 38 | `ClientboundEntityEventPacket` | Server → Client | Play S→C Entity Status (0x1A) | Remap status codes; ignore console-only enumerants. |
| 39 | `ClientboundSetEntityLinkPacket` | Server → Client | Play S→C Attach Entity (0x1B) | Align rider/vehicle IDs; handle detach sentinel `-1`. |
| 40 | `ClientboundSetEntityDataPacket` | Server → Client | Play S→C Entity Metadata (0x1C) | Translate metadata indexes to Java watcher schema. |
| 41 | `ClientboundUpdateMobEffectPacket` | Server → Client | Play S→C Entity Effect (0x1D) | Map amplifier duration units; translate hidden bit. |
| 42 | `ClientboundRemoveMobEffectPacket` | Server → Client | Play S→C Remove Entity Effect (0x1E) | Direct effect ID mapping. |
| 43 | `ClientboundSetExperiencePacket` | Server → Client | Play S→C Set Experience (0x1F) | Convert XP progress float range. |
| 44 | `ClientboundUpdateAttributesPacket` | Server → Client | Play S→C Entity Properties (0x20) | Map attribute UUIDs and names to Mojang defaults. |
| 62 | `ClientboundSoundPacket` | Server → Client | Play S→C Sound Effect (0x29) | Translate sound keys; map volume/pitch units. |
| 63 | `ClientboundLevelParticlesPacket` | Server → Client | Play S→C Particle (0x2A) | Convert particle IDs and extended data. |
| 70 | `ClientboundGameEventPacket` | Server → Client | Play S→C Change Game State (0x2B) | Align reason enums (bed invalid, rain strength, etc.). |
| 71 | `ClientboundAddGlobalEntityPacket` | Server → Client | Play S→C Spawn Global Entity (0x2C) | Used for lightning in both protocols. |
| 100 | `ClientboundContainerOpenPacket` | Server → Client | Play S→C Open Window (0x2D) | Translate container type IDs; respect window titles. |
| 101 | `ClientboundContainerClosePacket` | Server → Client | Play S→C Close Window (0x2E) | No field translation required. |
| 102 | `ServerboundContainerClickPacket` | Client → Server | Play C→S Click Window (0x0E) | Convert slot, button, mode semantics. |
| 103 | `ClientboundContainerSetSlotPacket` | Server → Client | Play S→C Set Slot (0x2F) | Translate `ItemStack` NBT tags. |
| 104 | `ClientboundContainerSetContentPacket` | Server → Client | Play S→C Window Items (0x30) | Batch slot payload identical. |
| 105 | `ClientboundContainerSetDataPacket` | Server → Client | Play S→C Window Property (0x31) | Map property IDs to furnace/brewing progress. |
| 106 | `ClientboundContainerAckPacket` | Server → Client | Play S→C Confirm Transaction (0x32) | Reverse ack boolean meaning to match Java. |
| 107 | `ServerboundSetCreativeModeSlotPacket` | Client → Server | Play C→S Creative Inventory Action (0x10) | Copy slot + stack data verbatim. |
| 108 | `ServerboundContainerButtonClickPacket` | Client → Server | Play C→S Enchant Item (0x11) | Reinterpret button ID as enchantment index. |
| 132 | `ClientboundBlockEntityDataPacket` | Server → Client | Play S→C Update Block Entity (0x35) | Match block entity action IDs. |
| 134 | `ClientboundSetPassengersPacket` | Server → Client | Play S→C Attach Entity (0x1B) | For multi-passenger vehicles, emit detach followed by reattach list. |
| 139 | `ClientboundBossEventPacket` | Server → Client | Play S→C Boss Bar (1.9+, custom) | No vanilla 1.8 equivalent; emulate using Wither boss mob metadata or custom plugin channel. |
| 150 | `CraftItemPacket` | Client → Server | Play C→S Click Window (0x0E) | Encode recipe selection as shift-click or crafting matrix updates. |
| 151 | `TradeItemPacket` | Client → Server | Play C→S Click Window (0x0E) | Translate villager offer index to slot/button combination. |
| 153 | `ServerSettingsChangedPacket` | Server → Client | Plugin Message (`MC|Host`) | Send via custom payload; define plugin namespace for host options. |
| 154 | `TexturePacket` | Server → Client | Plugin Message (`MC|TPack`) | Deliver resource pack URL/sha1 via standard channel. |
| 156 | `UpdateProgressPacket` | Server → Client | Plugin Message | Use custom channel to report progress percentages. |
| 157 | `TextureChangePacket` | Server → Client | Plugin Message | Notify Java clients using resource-pack reissue or custom namespace. |
| 158 | `UpdateGameRuleProgressPacket` | Server → Client | Plugin Message | Convey progress metrics via scoreboard or boss bar fallback. |
| 159 | `KickPlayerPacket` | Client → Server | Play C→S Client Status (0x16) | Java has no client-issued kick; send `/kick` command through plugin channel if necessary. |
| 160 | `TextureAndGeometryPacket` | Server → Client | Plugin Message | Combine resource pack + geometry data in compressed payload. |
| 161 | `TextureAndGeometryChangePacket` | Server → Client | Plugin Message | Delta updates; treat as reissue of custom payload. |
| 167 | `GameCommandPacket` | Client → Server | Plugin Message (`MC|Command`) | Execute host commands via plugin-defined namespace. |
| 168 | `MapSelectInfoPacket` | Server → Client | Plugin Message | Encode map info into JSON for custom UI mods. |
| 169 | `VotePacket` | Server → Client | Plugin Message | Mirror to scoreboard/team packets or plugin channel. |
| 170 | `PlayerReadyPacket` | Server → Client | Plugin Message | Drive lobby readiness; fallback to scoreboard objectives. |
| 171 | `ClientboundPowerupPacket` | Server → Client | Plugin Message | Represent via boss bar titles or scoreboard toggles. |
| 200 | `ClientboundAwardStatPacket` | Server → Client | Play S→C Statistics (0x37) | Convert statistic identifiers to Java keys. |
| 201 | `PlayerInfoPacket` | Server → Client | Play S→C Player List Item (0x38) | Map action enums and latency. |
| 202 | `ClientboundPlayerAbilitiesPacket` | Server → Client | Play S→C Player Abilities (0x39) | Align flying/walking speeds. |
| 206 | `ScoreboardPacket` | Server → Client | Play S→C Scoreboard Objective/Update (0x3B-0x3D) | Emit the appropriate scoreboard packet trio. |
| 209 | `ClientboundSetPlayerTeamPacket` | Server → Client | Play S→C Teams (0x3E) | Map team display names and collision rules. |
| 210 | `GameModePacket` | Server → Client | Play S→C Change Game State (0x2B) | Use reason 3/4 to toggle gamemode. |
| 211 | `ClientboundMapItemDataPacket` | Server → Client | Play S→C Maps (0x34) | Translate icon and color data arrays. |
| 212 | `ClientboundOpenSignEditorPacket` | Server → Client | Play S→C Sign Editor Open (0x36) | Provide block position. |
| 213 | `ClientboundPlayerCombatPacket` | Server → Client | Plugin Message | Vanilla 1.8 lacks combat event packet; replicate via chat/scoreboard updates. |
| 214 | `ClientboundPlayerPositionPacket` | Server → Client | Play S→C Player Position and Look (0x08) | Ensure relative/absolute flags align. |
| 216 | `ClientboundResourcePackPacket` | Server → Client | Play S→C Resource Pack Send (0x40) | Provide URL + SHA1. |
| 217 | `ClientboundSetBorderPacket` | Server → Client | None (World Border 1.8.3+) | Java 1.8 lacks dedicated world border packets; emulate with plugins or disable feature. |
| 218 | `ClientboundSetCameraPacket` | Server → Client | Play S→C Camera (0x43) | Forward target entity ID. |
| 219 | `ClientboundSetTitlesPacket` | Server → Client | Play S→C Title (0x45-0x48) | Titles introduced in 1.8; map action + text durations. |
| 220 | `ClientboundTabListPacket` | Server → Client | Play S→C Player List Header/Footer (0x47) | Wrap strings as JSON components. |
| 221 | `ServerboundChatAutoCompletePacket` | Client → Server | Play C→S Tab-Complete (0x14) | Translate partial text and block position hints. |
| 222 | `ServerboundChatPacket` | Client → Server | Play C→S Chat Message (0x01) | Convert formatting codes to section symbols. |
| 223 | `ServerboundContainerAckPacket` | Client → Server | Play C→S Confirm Transaction (0x0F) | Invert accepted flag. |
| 224 | `ServerboundContainerClosePacket` | Client → Server | Play C→S Close Window (0x0D) | No additional translation. |
| 225 | `ServerboundCustomPayloadPacket` | Client → Server | Play C→S Plugin Message (0x17) | Namespace string length differs; adjust prefix. |
| 228 | `ServerboundResourcePackPacket` | Client → Server | Play C→S Resource Pack Status (0x19) | Map status enum to 1.8 result codes. |
| 229 | `ServerboundSetCarriedItemPacket` | Client → Server | Play C→S Held Item Change (0x09) | Direct slot index mapping. |
| 230 | `ServerboundSignUpdatePacket` | Client → Server | Play C→S Update Sign (0x12) | Convert text lines to JSON components. |
| 231 | `ServerboundTeleportToEntityPacket` | Client → Server | Play C→S Spectate (0x18) | Use for spectator teleportation. |
| 232 | `ServerboundPreLoginPacket` | Client → Server | Login C→S Login Start (0x00) | Provide username/UUID; drop console entitlements. |
| 233 | `ServerboundSwingPacket` | Client → Server | Play C→S Animation (0x0A) | Mirror arm swing animation. |
| 250 | `ClientboundCustomPayloadPacket` | Server → Client | Play S→C Plugin Message (0x3F) | Adjust channel string length prefix. |
| 253 | `ClientboundChangeDifficultyPacket` | Server → Client | Play S→C Change Game State (0x2B) | Use reason 7 to broadcast difficulty. |
| 254 | `GetInfoPacket` | Client → Server | Status C→S Request (0x00) | Compose status ping handshake with server ID. |

## World, Chunk, and Entity Synchronisation

| LCE ID | LCE Packet | Direction | Java 1.8 Equivalent | Translation Notes |
| --- | --- | --- | --- | --- |
| 4 | `ClientboundSetTimePacket` | Server → Client | Play S→C Time Update (0x03) | Invert daylight cycle boolean to match Java sign convention. |
| 5 | `ClientboundSetEquippedItemPacket` | Server → Client | Play S→C Entity Equipment (0x04) | Align equipment slot ordering (hand, helmet, etc.). |
| 6 | `ClientboundSetSpawnPositionPacket` | Server → Client | Play S→C Spawn Position (0x05) | Direct block coordinate mapping. |
| 10 | `ServerboundMovePlayerPacket` | Client → Server | Play C→S Player (0x03) | Forward on-ground flag; convert absolute doubles. |
| 11 | `ServerboundMovePlayerPacket::Pos` | Client → Server | Play C→S Player Position (0x04) | Reuse same base translation with absent rotation. |
| 12 | `ServerboundMovePlayerPacket::Rot` | Client → Server | Play C→S Player Look (0x05) | Provide yaw/pitch only. |
| 13 | `ServerboundMovePlayerPacket::PosRot` | Client → Server | Play C→S Player Position and Look (0x06) | Provide both position and rotation. |
| 22 | `ClientboundTakeItemEntityPacket` | Server → Client | Play S→C Collect Item (0x0D) | Map collector entity ID to player. |
| 23 | `ClientboundAddEntityPacket` | Server → Client | Play S→C Spawn Object (0x0E) | Align object type mapping; convert velocity scaling. |
| 24 | `ClientboundAddMobPacket` | Server → Client | Play S→C Spawn Mob (0x10) | Translate entity type IDs and metadata layout. |
| 25 | `ClientboundAddPaintingPacket` | Server → Client | Play S→C Spawn Painting (0x10 legacy) | Provide motive enum names. |
| 26 | `ClientboundAddExperienceOrbPacket` | Server → Client | Play S→C Spawn Experience Orb (0x11) | No special handling. |
| 28 | `ClientboundSetEntityMotionPacket` | Server → Client | Play S→C Entity Velocity (0x12) | Convert fixed-point velocity units. |
| 29 | `ClientboundRemoveEntitiesPacket` | Server → Client | Play S→C Destroy Entities (0x13) | Provide entity ID array. |
| 30 | `ClientboundMoveEntityPacket` | Server → Client | Play S→C Entity Relative Move (0x15) | Translate delta units from fixed-point. |
| 34 | `ClientboundTeleportEntityPacket` | Server → Client | Play S→C Entity Teleport (0x18) | Convert absolute doubles. |
| 50 | `ChunkVisibilityPacket` | Server → Client | Play S→C Chunk Data (0x21) | Java expects contiguous chunk sections; drop visibility mask metadata. |
| 51 | `BlockRegionUpdatePacket` | Server → Client | Play S→C Multi Block Change (0x22) | Batch block positions into 64-bit packed coordinates. |
| 52 | `ClientboundChunkBlocksUpdatePacket` | Server → Client | Play S→C Multi Block Change (0x22) | Use when 1-2 block updates; else degrade to single block change. |
| 53 | `ClientboundBlockUpdatePacket` | Server → Client | Play S→C Block Change (0x23) | Direct coordinate + block state mapping. |
| 54 | `ClientboundBlockEventPacket` | Server → Client | Play S→C Block Action (0x24) | Translate event IDs; e.g., note block pitch. |
| 55 | `ClientboundBlockDestructionPacket` | Server → Client | Play S→C Block Break Animation (0x25) | Use breaker entity ID and progress (0–9). |
| 60 | `ClientboundExplodePacket` | Server → Client | Play S→C Explosion (0x27) | Convert vector floats; block positions as integer deltas. |
| 61 | `ClientboundLevelEventPacket` | Server → Client | Play S→C Effect (0x28) | Map event IDs (door open, potion break). |
| 132 | `ClientboundBlockEntityDataPacket` | Server → Client | Play S→C Update Block Entity (0x35) | Duplicate entry for completeness. |
| 133 | `ClientboundMoveVehiclePacket` | Server → Client | Play S→C Entity (0x14) | Treat vehicles as generic entity movement updates. |
| 136 | `ServerboundMoveVehiclePacket` | Client → Server | Play C→S Steer Vehicle (0x0C) | Provide position/rotation for mounts; fallback to passenger movement packets. |
| 137 | `ServerboundUseItemOnPacket` | Client → Server | Play C→S Player Block Placement (0x08) | Include block position and face data. |
| 138 | `ServerboundPaddleBoatPacket` | Client → Server | Play C→S Steer Vehicle (0x0C) | Encode paddle state into strafe/forward floats. |
| 166 | `XZPacket` | Server → Client | None | Console-specific mini-game coordinate stream; replicate via plugin message if required. |
| 168 | `MapSelectInfoPacket` | Server → Client | None | Use custom payload; optionally send via maps/scoreboard. |
| 235 | `ClientboundBlockCollectionDestructionPacket` | Server → Client | Play S→C Block Break Animation (0x25) | Emit multiple animations sequentially. |
| 236 | `ClientboundCooldownPacket` | Server → Client | Play S→C Set Cooldown (1.9+) | Not present in 1.8; emulate using scoreboard timers or action bar titles. |
| 239 | `ClientboundMGPlayerSettingsUpdatePacket` | Server → Client | None | Mini-game configuration; deliver via custom plugin channels. |

## Resource & Status Protocols

| LCE ID | LCE Packet | Direction | Java 1.8 Equivalent | Translation Notes |
| --- | --- | --- | --- | --- |
| 152 | `DebugOptionsPacket` | Client → Server | Plugin Message | Debug toggles require custom handling, usually disabled on Java servers. |
| 155 | `ChunkVisibilityAreaPacket` | Server → Client | None | Use Bukkit/Spigot API to control view distance; otherwise ignore. |
| 166 | `XZPacket` | Server → Client | None | Listed above; emphasise no vanilla mapping. |
| 171 | `ClientboundPowerupPacket` | Server → Client | None | Provided via custom scoreboard/boss bar combos. |
| 254 | `GetInfoPacket` | Client → Server | Status C→S Ping (0x01) | Compose status ping with payload +1; respond with standard Pong (0x01). |

## Implementation Notes

1. **Custom Payloads** – Console-exclusive mechanics (mini-games, lobby voting, host
   settings) must be implemented with plugin channels (`Play C→S Plugin Message`
   and `Play S→C Plugin Message`). Reserve namespace prefixes such as
   `lce:` to avoid clashing with existing Forge/Bukkit channels.
2. **World Border Features** – Java 1.8 predates the dedicated world border packet
   suite introduced in 1.8.3. For packets like `ClientboundSetBorderPacket` either
   upgrade the Java server or disable the feature for cross-play sessions.
3. **Teleport Confirmation** – Because Java 1.8 lacks a teleport confirm packet,
   inject position acknowledgements by echoing the latest server position in the
   next movement packet or modify the server with a lightweight plugin.
4. **Statistics and Scoreboards** – LCE statistics/events often correspond to
   vanilla scoreboard or statistics packets. Ensure identifiers align with the
   Mojang naming conventions (e.g., `stat.jump`).
5. **Encoding Differences** – Java 1.8 uses VarInts extensively whereas the
   console protocol keeps many identifiers as fixed-width integers. Your
   translation layer must wrap/unwrap VarInts accordingly during serialization.

Use this mapping alongside the packet field references in
[`networking-overview.md`](./networking-overview.md) to design a bidirectional
translation layer.
