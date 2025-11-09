# Packet ID Catalogue

The table below enumerates every packet identifier defined in `EPacketType`. Each row links the
numeric ID used on the wire to the generated packet class and its general responsibility. Refer to
the corresponding header in `src/Minecraft.Client/net/minecraft/network/protocol/game/` for the
full payload layout.[^packet-type]

| ID | Enum label | Packet class | Direction | Notes |
| --- | --- | --- | --- | --- |
| 0 | `_ClientboundKeepAlivePacket` | `ClientboundKeepAlivePacket` | Server → Client | Server heartbeat response automatically scheduled by `Connection::tick()`. |
| 1 | `_ClientboundLoginPacket` | `ClientboundLoginPacket` | Server → Client | Delivers post-authentication login data during session startup. |
| 2 | `_ClientboundPreLoginPacket` | `ClientboundPreLoginPacket` | Server → Client | Pre-login handshake payload sent before the full login state. |
| 3 | `_ClientboundChatPacket` | `ClientboundChatPacket` | Server → Client | Displays chat messages and system text. |
| 4 | `_ClientboundSetTimePacket` | `ClientboundSetTimePacket` | Server → Client | Updates the in-world time counter. |
| 5 | `_ClientboundSetEquippedItemPacket` | `ClientboundSetEquippedItemPacket` | Server → Client | Synchronises the item currently held by an entity. |
| 6 | `_ClientboundSetSpawnPositionPacket` | `ClientboundSetSpawnPositionPacket` | Server → Client | Establishes the player's spawn position. |
| 7 | `_ServerboundInteractPacket` | `ServerboundInteractPacket` | Client → Server | Reports entity interactions (e.g. attack/use). |
| 8 | `_ClientboundSetHealthPacket` | `ClientboundSetHealthPacket` | Server → Client | Updates health, hunger, and saturation values. |
| 9 | `_ClientboundRespawnPacket` | `ClientboundRespawnPacket` | Server → Client | Notifies the client of respawn events and dimension changes. |
| 10 | `_ServerboundMovePlayerPacket` | `ServerboundMovePlayerPacket` | Client → Server | Player movement base packet with position and rotation. |
| 11 | `_ServerboundMovePlayerPosPacket` | `ServerboundMovePlayerPacket::Pos` | Client → Server | Movement variant containing only position. |
| 12 | `_ServerboundMovePlayerRotPacket` | `ServerboundMovePlayerPacket::Rot` | Client → Server | Movement variant containing only rotation. |
| 13 | `_ServerboundMovePlayerPosRotPacket` | `ServerboundMovePlayerPacket::PosRot` | Client → Server | Movement variant containing both position and rotation. |
| 14 | `_ServerboundPlayerActionPacket` | `ServerboundPlayerActionPacket` | Client → Server | Reports actions such as digging start/stop; reordered in `Connection::tick()`. |
| 15 | `_ServerboundUseItemPacket` | `ServerboundUseItemPacket` | Client → Server | Informs the server when the player uses the held item. |
| 16 | `_ClientboundSetCarriedItemPacket` | `ClientboundSetCarriedItemPacket` | Server → Client | Updates the hotbar slot currently selected. |
| 17 | `_ClientboundPlayerSleepPacket` | `ClientboundPlayerSleepPacket` | Server → Client | Signals player entering or leaving a bed. |
| 18 | `_ClientboundAnimatePacket` | `ClientboundAnimatePacket` | Server → Client | Triggers entity animation events. |
| 19 | `_ServerboundPlayerCommandPacket` | `ServerboundPlayerCommandPacket` | Client → Server | Reports command-like state changes (sprinting, sneaking). |
| 20 | `_ClientboundAddPlayerPacket` | `ClientboundAddPlayerPacket` | Server → Client | Spawns a remote player entity. |
| 22 | `_ClientboundTakeItemEntityPacket` | `ClientboundTakeItemEntityPacket` | Server → Client | Announces that an item entity has been collected. |
| 23 | `_ClientboundAddEntityPacket` | `ClientboundAddEntityPacket` | Server → Client | Spawns a generic (non-mob) entity. |
| 24 | `_ClientboundAddMobPacket` | `ClientboundAddMobPacket` | Server → Client | Spawns a mob entity with AI data. |
| 25 | `_ClientboundAddPaintingPacket` | `ClientboundAddPaintingPacket` | Server → Client | Spawns a painting entity. |
| 26 | `_ClientboundAddExperienceOrbPacket` | `ClientboundAddExperienceOrbPacket` | Server → Client | Spawns an experience orb entity. |
| 27 | `_ServerboundPlayerInputPacket` | `ServerboundPlayerInputPacket` | Client → Server | Sends analog input values (movement, strafe, jump). |
| 28 | `_ClientboundSetEntityMotionPacket` | `ClientboundSetEntityMotionPacket` | Server → Client | Updates entity velocity. |
| 29 | `_ClientboundRemoveEntitiesPacket` | `ClientboundRemoveEntitiesPacket` | Server → Client | Removes one or more entities by ID. |
| 30 | `_ClientboundMoveEntityPacket` | `ClientboundMoveEntityPacket` | Server → Client | Delta moves an entity. |
| 34 | `_ClientboundTeleportEntityPacket` | `ClientboundTeleportEntityPacket` | Server → Client | Teleports an entity to an absolute position. |
| 35 | `_ClientboundRotateHeadPacket` | `ClientboundRotateHeadPacket` | Server → Client | Adjusts an entity's head rotation. |
| 38 | `_ClientboundEntityEventPacket` | `ClientboundEntityEventPacket` | Server → Client | Broadcasts miscellaneous entity events (hurt, death, etc.). |
| 39 | `_ClientboundSetEntityLinkPacket` | `ClientboundSetEntityLinkPacket` | Server → Client | Updates entity rider/vehicle relationships. |
| 40 | `_ClientboundSetEntityDataPacket` | `ClientboundSetEntityDataPacket` | Server → Client | Synchronises tracked entity metadata. |
| 41 | `_ClientboundUpdateMobEffectPacket` | `ClientboundUpdateMobEffectPacket` | Server → Client | Applies or updates status effects. |
| 42 | `_ClientboundRemoveMobEffectPacket` | `ClientboundRemoveMobEffectPacket` | Server → Client | Removes status effects. |
| 43 | `_ClientboundSetExperiencePacket` | `ClientboundSetExperiencePacket` | Server → Client | Updates experience level and progress. |
| 44 | `_ClientboundUpdateAttributesPacket` | `ClientboundUpdateAttributesPacket` | Server → Client | Synchronises entity attribute values. |
| 50 | `_ChunkVisibilityPacket` | `ChunkVisibilityPacket` | Server → Client | Conveys chunk visibility state for streaming optimisation. |
| 51 | `_BlockRegionUpdatePacket` | `BlockRegionUpdatePacket` | Server → Client | Sends batched block updates for a region. |
| 52 | `_ClientboundChunkBlocksUpdatePacket` | `ClientboundChunkBlocksUpdatePacket` | Server → Client | Delivers targeted block updates within a chunk. |
| 53 | `_ClientboundBlockUpdatePacket` | `ClientboundBlockUpdatePacket` | Server → Client | Updates a single block state. |
| 54 | `_ClientboundBlockEventPacket` | `ClientboundBlockEventPacket` | Server → Client | Plays block events (e.g. note blocks, pistons). |
| 55 | `_ClientboundBlockDestructionPacket` | `ClientboundBlockDestructionPacket` | Server → Client | Animates block breaking progress. |
| 60 | `_ClientboundExplodePacket` | `ClientboundExplodePacket` | Server → Client | Reports explosions and knockback. |
| 61 | `_ClientboundLevelEventPacket` | `ClientboundLevelEventPacket` | Server → Client | Plays world-level events (sound/particle). |
| 62 | `_ClientboundSoundPacket` | `ClientboundSoundPacket` | Server → Client | Streams positional sound events. |
| 63 | `_ClientboundLevelParticlesPacket` | `ClientboundLevelParticlesPacket` | Server → Client | Spawns particle effects. |
| 70 | `_ClientboundGameEventPacket` | `ClientboundGameEventPacket` | Server → Client | Broadcasts high-level game events (e.g. bed use, weather). |
| 71 | `_ClientboundAddGlobalEntityPacket` | `ClientboundAddGlobalEntityPacket` | Server → Client | Spawns non-standard global entities (e.g. lightning). |
| 100 | `_ClientboundContainerOpenPacket` | `ClientboundContainerOpenPacket` | Server → Client | Opens inventory containers. |
| 101 | `_ClientboundContainerClosePacket` | `ClientboundContainerClosePacket` | Server → Client | Closes inventory containers. |
| 102 | `_ServerboundContainerClickPacket` | `ServerboundContainerClickPacket` | Client → Server | Reports slot interactions within an open container. |
| 103 | `_ClientboundContainerSetSlotPacket` | `ClientboundContainerSetSlotPacket` | Server → Client | Updates a single slot's contents. |
| 104 | `_ClientboundContainerSetContentPacket` | `ClientboundContainerSetContentPacket` | Server → Client | Replaces entire container contents. |
| 105 | `_ClientboundContainerSetDataPacket` | `ClientboundContainerSetDataPacket` | Server → Client | Updates container properties (progress bars, etc.). |
| 106 | `_ClientboundContainerAckPacket` | `ClientboundContainerAckPacket` | Server → Client | Acknowledges processed container transactions. |
| 107 | `_ServerboundSetCreativeModeSlotPacket` | `ServerboundSetCreativeModeSlotPacket` | Client → Server | Updates creative inventory slots. |
| 108 | `_ServerboundContainerButtonClickPacket` | `ServerboundContainerButtonClickPacket` | Client → Server | Notifies the server of button presses in a container UI. |
| 132 | `_ClientboundBlockEntityDataPacket` | `ClientboundBlockEntityDataPacket` | Server → Client | Synchronises block entity NBT data. |
| 133 | `_ClientboundMoveVehiclePacket` | `ClientboundMoveVehiclePacket` | Server → Client | Moves vehicles controlled by the player. |
| 134 | `_ClientboundSetPassengersPacket` | `ClientboundSetPassengersPacket` | Server → Client | Updates passenger lists for vehicles. |
| 135 | `_ServerboundAcceptTeleportationPacket` | `ServerboundAcceptTeleportationPacket` | Client → Server | Confirms a server-issued teleport. |
| 136 | `_ServerboundMoveVehiclePacket` | `ServerboundMoveVehiclePacket` | Client → Server | Sends vehicle motion updates from the client. |
| 137 | `_ServerboundUseItemOnPacket` | `ServerboundUseItemOnPacket` | Client → Server | Reports block interaction with an item. |
| 138 | `_ServerboundPaddleBoatPacket` | `ServerboundPaddleBoatPacket` | Client → Server | Reports boat paddle input. |
| 139 | `_ClientboundBossEventPacket` | `ClientboundBossEventPacket` | Server → Client | Updates boss bar UI state. |
| 150 | `_CraftItemPacket` | `CraftItemPacket` | Client → Server | Submits crafting recipe selections. |
| 151 | `_TradeItemPacket` | `TradeItemPacket` | Client → Server | Handles villager trading transactions. |
| 152 | `_DebugOptionsPacket` | `DebugOptionsPacket` | Client → Server | Transmits developer/debug toggles. |
| 153 | `_ServerSettingsChangedPacket` | `ServerSettingsChangedPacket` | Server → Client | Notifies clients about host setting changes. |
| 154 | `_TexturePacket` | `TexturePacket` | Server → Client | Transfers initial texture data. |
| 155 | `_ChunkVisibilityAreaPacket` | `ChunkVisibilityAreaPacket` | Server → Client | Expands on chunk streaming with visibility areas. |
| 156 | `_UpdateProgressPacket` | `UpdateProgressPacket` | Server → Client | Updates long-running progress indicators. |
| 157 | `_TextureChangePacket` | `TextureChangePacket` | Server → Client | Signals texture replacements. |
| 158 | `_UpdateGameRuleProgressPacket` | `UpdateGameRuleProgressPacket` | Server → Client | Tracks rule-sync progress. |
| 159 | `_KickPlayerPacket` | `KickPlayerPacket` | Client → Server | Initiates a targeted disconnect. |
| 160 | `_TextureAndGeometryPacket` | `TextureAndGeometryPacket` | Server → Client | Delivers combined texture/geometry blobs. |
| 161 | `_TextureAndGeometryChangePacket` | `TextureAndGeometryChangePacket` | Server → Client | Announces incremental texture/geometry updates. |
| 166 | `_XZPacket` | `XZPacket` | Server → Client | Custom mini-game coordinate updates (console-specific). |
| 167 | `_GameCommandPacket` | `GameCommandPacket` | Client → Server | Sends mini-game or host commands. |
| 168 | `_MapSelectInfoPacket` | `MapSelectInfoPacket` | Server → Client | Communicates map selection metadata. |
| 169 | `_VotePacket` | `VotePacket` | Server → Client | Manages lobby voting state. |
| 170 | `_PlayerReadyPacket` | `PlayerReadyPacket` | Server → Client | Signals readiness in multiplayer lobbies. |
| 171 | `_ClientboundPowerupPacket` | `ClientboundPowerupPacket` | Server → Client | Updates power-up status (Battle mini-game). |
| 200 | `_ClientboundAwardStatPacket` | `ClientboundAwardStatPacket` | Server → Client | Increments tracked statistics. |
| 201 | `_PlayerInfoPacket` | `PlayerInfoPacket` | Server → Client | Updates scoreboard/player list entries. |
| 202 | `_ClientboundPlayerAbilitiesPacket` | `ClientboundPlayerAbilitiesPacket` | Server → Client | Synchronises ability flags (flight, invulnerability). |
| 205 | `_ServerboundClientCommandPacket` | `ServerboundClientCommandPacket` | Client → Server | Sends client commands (respawn, stats). |
| 206 | `_ScoreboardPacket` | `ScoreboardPacket` | Server → Client | Updates scoreboard objectives and scores. |
| 209 | `_ClientboundSetPlayerTeamPacket` | `ClientboundSetPlayerTeamPacket` | Server → Client | Modifies team assignments. |
| 210 | `_GameModePacket` | `GameModePacket` | Server → Client | Negotiates local game mode settings. |
| 211 | `_ClientboundMapItemDataPacket` | `ClientboundMapItemDataPacket` | Server → Client | Streams map pixel data (deduplicated during send). |
| 212 | `_ClientboundOpenSignEditorPacket` | `ClientboundOpenSignEditorPacket` | Server → Client | Opens the sign editing UI. |
| 213 | `_ClientboundPlayerCombatPacket` | `ClientboundPlayerCombatPacket` | Server → Client | Notifies the client about combat events. |
| 214 | `_ClientboundPlayerPositionPacket` | `ClientboundPlayerPositionPacket` | Server → Client | Teleports or corrects player position. |
| 216 | `_ClientboundResourcePackPacket` | `ClientboundResourcePackPacket` | Server → Client | Requests resource pack download/usage. |
| 217 | `_ClientboundSetBorderPacket` | `ClientboundSetBorderPacket` | Server → Client | Updates world border properties. |
| 218 | `_ClientboundSetCameraPacket` | `ClientboundSetCameraPacket` | Server → Client | Changes the active camera target. |
| 219 | `_ClientboundSetTitlesPacket` | `ClientboundSetTitlesPacket` | Server → Client | Configures title/subtitle overlays. |
| 220 | `_ClientboundTabListPacket` | `ClientboundTabListPacket` | Server → Client | Customises the multiplayer tab list. |
| 221 | `_ServerboundChatAutoCompletePacket` | `ServerboundChatAutoCompletePacket` | Client → Server | Requests tab-completion suggestions. |
| 222 | `_ServerboundChatPacket` | `ServerboundChatPacket` | Client → Server | Sends chat messages or commands. |
| 223 | `_ServerboundContainerAckPacket` | `ServerboundContainerAckPacket` | Client → Server | Confirms processed inventory transactions. |
| 224 | `_ServerboundContainerClosePacket` | `ServerboundContainerClosePacket` | Client → Server | Reports closing an inventory screen. |
| 225 | `_ServerboundCustomPayloadPacket` | `ServerboundCustomPayloadPacket` | Client → Server | Delivers plugin/custom payloads. |
| 226 | `_ServerboundKeepAlivePacket` | `ServerboundKeepAlivePacket` | Client → Server | Responds to keep-alive requests. |
| 227 | `_ServerboundPlayerAbilitiesPacket` | `ServerboundPlayerAbilitiesPacket` | Client → Server | Sends local ability state changes. |
| 228 | `_ServerboundResourcePackPacket` | `ServerboundResourcePackPacket` | Client → Server | Responds to resource pack requests. |
| 229 | `_ServerboundSetCarriedItemPacket` | `ServerboundSetCarriedItemPacket` | Client → Server | Reports hotbar slot selection changes. |
| 230 | `_ServerboundSignUpdatePacket` | `ServerboundSignUpdatePacket` | Client → Server | Submits edited sign text. |
| 231 | `_ServerboundTeleportToEntityPacket` | `ServerboundTeleportToEntityPacket` | Client → Server | Requests teleportation to another entity. |
| 232 | `_ServerboundPreLoginPacket` | `ServerboundPreLoginPacket` | Client → Server | Client-side pre-login handshake data. |
| 233 | `_ServerboundSwingPacket` | `ServerboundSwingPacket` | Client → Server | Reports arm swing animation triggers. |
| 234 | `_ClientboundDamageIndicatorPacket` | `ClientboundDamageIndicatorPacket` | Server → Client | Displays Battle mini-game damage indicators. |
| 235 | `_ClientboundBlockCollectionDestructionPacket` | `ClientboundBlockCollectionDestructionPacket` | Server → Client | Animates destruction for a group of blocks. |
| 236 | `_ClientboundCooldownPacket` | `ClientboundCooldownPacket` | Server → Client | Updates per-item cooldown timers. |
| 239 | `_ClientboundMGPlayerSettingsUpdatePacket` | `ClientboundMGPlayerSettingsUpdatePacket` | Server → Client | Synchronises mini-game player options. |
| 250 | `_ClientboundCustomPayloadPacket` | `ClientboundCustomPayloadPacket` | Server → Client | Receives plugin/custom payloads. |
| 253 | `_ClientboundChangeDifficultyPacket` | `ClientboundChangeDifficultyPacket` | Server → Client | Changes the world difficulty. |
| 254 | `_GetInfoPacket` | `GetInfoPacket` | Client → Server | Exchanges host or lobby metadata. |
| 255 | `_DisconnectPacket` | `DisconnectPacket` | Server → Client | Performs an orderly disconnect with reason codes. |

[^packet-type]: `src/Minecraft.Client/net/minecraft/network/PacketType.h`, lines 3-127.
