# Placebo 26.2 port status

This is **port preparation**, not a verified 26.2 release. It begins from
Shadows-of-Fire/Placebo branch `26.1` at commit
`8fcfa1e6e947e8f0f12044ad31ce4bef2145982a`.

The Gradle properties now target Minecraft 26.2, NeoForge 26.2.0.88, and
Java 25. The metadata restricts Minecraft and NeoForge to the 26.2 series.
ModDevGradle now uses `disableRecompilation = true`, following its documented
binary pipeline. This bypasses the `HolderSet$1.contents()` access-level
error reported while recompiling Minecraft itself on Windows. It does not
fix or hide errors in Placebo's own Java source.
The standard `test` source set is retained because ModDevGradle expects it
during project setup; `enableTests=false` disables the test task instead.
The old 26.1.2 JEI and Jade versions were removed; they can only be restored
with compatible releases. The library code itself has **not** passed a 26.2
compile or client/server runtime test.
PatreonPreview's HUD title calls were updated to the `Gui#hud` route described
by the 26.2 migration primer. Other source migrations remain to be checked.

The first Windows `compileJava` run identified six errors. The criterion
trigger import now uses `net.minecraft.advancements.triggers`, the structure
processor registration returns its `MapCodec` (matching the 26.2 registry),
and the obsolete direct-buffer overload in `TickableTextList` has been
removed. Its `GuiGraphicsExtractor` rendering overloads remain. These edits
have not yet been checked by a new compiler run.

The build in this workspace reached `createMinecraftArtifacts`, then
NeoFormRuntime exited with `NoSuchElementException` in its constructor
because `ProcessHandle.current().info().command()` is empty in the process
sandbox. A **temporary local build-tool patch, excluded from this source
archive**, got beyond that failure. The next step attempted to download
Minecraft's version manifest from `launchermeta.mojang.com`, which this
workspace cannot reach. The temporary patch was removed. Compilation never
reached Placebo's Java code, so compatibility remains unverified.

To continue on a normal Java 25 development machine:

1. Run `./gradlew clean build` (Windows: `gradlew.bat clean build`). The next
   errors, if any, should come from Placebo's remaining Java sources or dependencies.
2. Fix compiler errors against NeoForge 26.2. Follow the official
   26.1.x to 26.2 migration primer, particularly GUI rendering changes:
   https://docs.neoforged.net/primer/docs/26.2/
3. Test the config UI, menu sync, networking, dynamic registries and data
   loading on both client and dedicated server. Regenerate resources with
   `./gradlew runData` where applicable.
4. Once Placebo builds and works, run `./gradlew publishToMavenLocal` and
   point HNN's `placeboVersion` at this build's real version. HNN itself
   still needs further API porting.

Retain the original license and copyright in `LICENSE`.
