# Battles

One of the most important things in working with Brawl Stars API is to retrieve player's BattleLog. It's not
because you might need to retrieve some kind of statistics related to the battles, but because information
like player's rank in ranked game mode can be only retrieved through battle log API.

<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>core</code> library</a></step>
    <step><a href="Getting-started.md">Create Brawl Stars API Client instance</a></step>
</procedure>

## Basic usage

To retrieve a battle log, first thing you need is `BrawlStarsClient` – you learn how to obtain it on
the [](Getting-started.md) page. Then simply call `getPlayerBattlelog`:

```kotlin
val battles: List<[[[RawBattle|https://github.com/y9vad9/brawlstars-api/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/event/battle/RawBattle.kt]]]> = client.getBattlelog()
    .getOrElse { throwable -> return println("Failure: $throwable") }
    ?: return println("Player not found")
    
battles.forEach {
    // prints battle time + result (victory, defeat, draw)
    println("${it.battleTime}: ${it.result.resultKind} with +${it.result.trophyChange}")
}
```
Based on the battle's kind it will have different data, for example:
- `BattleResult.players` is not null if it's a solo showdown match;
- `BattleResult.teams` is not null if it's a duo, trio, 5-vs-5 modes;
- `BattleResult.trophyChange` is not null only if it's trophies game mode (not friendly and not mapmaker), but might
contain zero for `BattleResultKind.DRAW` result.
- `BattleResult.rank` contains only in the showdown matches or similar game modes (that also have a concept of it).

If some game is of ranked type, you may obtain the rank of the player:
```kotlin
fun printDoesPlayerHaveDiamond(battle: RawBattle, tag: PlayerTag) {
    if (battle.isRankedGameMode) {
        val rankedStage: RankedStage = battle.getRankedStage(tag)
        // or battle.result.teams.findPlayer(tag)!!.trophies.asRankedStageUnsafe()
        if (rankedStage >= RankedStage.DIAMOND_1)
            println("Yes, he is at $rankedStage")
        else println("No, he is only at $rankedStage")
    }
}
```
> Look for extensions, they might provide you with most of the needed features.
> For example, you may discover [
`RankedStage.isMaster`](https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/event/value/RankedStage.kt#L45)
> that will tell for you whether player is qualified for Masters.
> {style="note"}

You may refer to the [`RankedStage`](https://github.com/y9vad9/brawlstars-api/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/event/value/RankedStage.kt)
sources for more information.

## Retrieving visuals
<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>brawlify</code> module of the library</a></step>
    <step><a href="Getting-started.md">Create Brawl Stars API Client instance</a></step>
</procedure>

To retrieve the image of a map for the battle, do the following:
```Kotlin
suspend getMapImageUrl(
  client: BrawlifyClient, 
  battle: RawBattle
): BrawlifyUrl? {
  return client.getMap(battle.event.id)
    .getOrElse { return null }
    ?.let { it.imageUrl }
}
```
In addition, you might get the representative image of the game mode:
```Kotlin
suspend getGameModeImageUrl(
  client: BrawlifyClient, 
  battle: RawBattle
): BrawlifyUrl? {
  return client.getMap(battle.event.id)
    .getOrElse { return null }
    ?.let { it.gameMode.imageUrl }
}
```
To get avatars of the battle's members – visit [this page](Profile.md#retrieving-image-of-a-profile).

## Typed Battles
<warning>
This API is currently considered as Experimental, may provide incorrect results for certain corner cases.
</warning>

Taking into account that `RawBattle` is quite inconvenient – you need to check everything by hand and test the API
on different types of games with different types of results when working with
battlelog, you may want to use another API that library offers – `List<RawBattle>.typed()`.

```kotlin
val typedList: List<Battle> = client.getPlayerBattlelog()
    .map { it?.typed() }
    .getOrElse { error("...") }
    ?: error("...")
    
typedList.forEach { battle: Battle ->
    // just to showcase the amount of possible types
    when (battle) {
        // solo showdown in friendly gamemode
        is FriendlySoloShowdownBattle -> TODO()
        // duo/trio showdown in friendly gamemode
        is FriendlyTeamShowdownBattle -> TODO()
        // solo showdown in trophies gamemodes
        is TrophiesSoloShowdownBattle -> TODO()
        // duo, trio showdown in trophies gamemodes
        is TrophiesTeamShowdownBattle -> TODO()
        
        // ranked gamemode
        is RegularRankedBattle -> TODO()
        // ranked game in friendly gamemode
        is FriendlyRankedBattle -> TODO()
        
        // 3-vs-3/5-vs-5 game modes in friendly mode
        is FriendlyTeamsBattle -> TODO()
        // 3-vs-3/5-vs-5 trophies gamemodes
        is TrophiesTeamsBattle -> TODO()
    }
}
```
<note>
Returning list might contain fewer elements than in original, because of merging for Ranked Games into a one Battle.
</note>

<tip>
There's also extension for <code>PagesIterator</code> to do basically the same. Even though battle log
does not support pagination from API, you may want to store battles yourself in some kind of Database. That's when
it comes in handy!

Here's usage example:
<code-block lang="Kotlin">
<![CDATA[
val rawBattles = PagesIterator(
  limit = Count.createUnsafe(20),
) { count, cursors ->
  if (cursors.before != null)
    Result.failure(IllegalStateException("Should not reach this point"))

  if (cursors.after != null)
    // your logic to load data before
    Result.success(listOf(...))

  Result.failure(...)
}.typed()
]]>
</code-block>
It does not support going back (<code>previous()</code>), only forward (<code>next()</code>)

In addition, on long-run it should be more precise, because, for example, Friendly ranked games require full context
to be resolved as such type. Important to mention is that it's up to iterator when to load data on its own behalf, so per <code>next</code>
call there can be more to the original iterator.
</tip>

The basic info you can get from `Battle` are `battleTime: Instant`, `event: Event` and `duration: Duration`. All other
information is considered mode-specific.

In addition, you might be interested in extensions that might provide you with more information without pen testing 
the Brawl Stars API:
- `Battle.isMapMaker` (or `!Event.isOfficial`) – whether the battle is community-map in friendly mode / daily MapMaker;
- `Battle.isFriendly()` – whether the battle is of type friendly;
- `Battle.isForTrophies()` – whether the battle is of type that use trophies.

Example of how to distinguish game for trophies from non-trophies:
```kotlin
fun printIsForTrophies(battle: Battle) {
    if (battle.isForTrophies()) {
        println("For trophies!")
        // also smart cast based on the Kotlin Contracts:
        println("Trophy change: ${battle.trophyChange}")
    }
}
```

### Showdown
Even though the name might imply showdown-only battles, we also consider game modes that have same mechanics of ranking
position of player / team as a Showdown. You may make sure that game is solo/duo/trio Showdown using `event.mode.isShowdown`.

The only property you may find, except basic provided in the `Battle`, is `rank: RankingPosition`. Based on the fact of solo/duo/trio mode,
it will also provide list of either `players: List<PlayerView<out EntityTag>` or `teams: BattleTeams`.

```Kotlin
fun printMembers(battle: ShowdownBattle) {
    val message = when (battle) {
        is TeamShowdownBattle -> battle.teams
        is SoloShowdownBattle -> battle.players
    }.toString()
    
    println(message)
}
```

### TeamsBattle
This type of battles applies to the all battles that are 3-vs-3 or 5-vs-5.
The difference from the Showdown matches is that they don't have placing position,
but on the other hand have `result: BattleResultKind` (match result status: victory, defeat or draw).
Always have `teams: BattleTeams` of two teams (for friendly matches they can be incomplete).

In addition, `TeamsBattle`s have `starPlayer: StarPlayer<out EntityTag, out TrophiesOrFriendlyBrawlerView>?` that
can be only null if it's an AFK match (with no impact from any player, like damage or goals in brawl ball) with neither
victory nor defeat, but with `BattleResultKind.DRAW`.

All other cases should be treated as `RankedBattle` due to specifics of battle-rounds definition.

### RankedBattle
This type of battles represents Ranked Battle with wrapped (if available in the list) rounds. Contains the following
information:
- `starPlayer: StarPlayer<out EntityTag, out RankedOrFriendlyBrawlerView>?`: StarPlayer of the game, may be not
present if game was cancelled by the Brawl Stars (if, for example, before technical pause) or if it was finished in any
other unforeseeable way.
- `val rounds: List<Round>`: rounds of the game with the `result: BattleResultKind` and the `duration: Duration`.
- `val teams: BattleTeams`: battle teams (might be incomplete if it's a friendly game)
- `val type: BattleType`: contains information about exact type (soloRanked, duoRanked or trioRanked).
- `val result: BattleResultKind?`: contains the verdict after all rounds, if game is finished.
- `val isAssumed`: Friendly `RankedBattle`s is only assumed, we will talk about it lower.
- `val duration: Duration`: returns the duration of all rounds, might be not that precise if not all rounds
were loaded into the original list of `List<RawBattle>` or its alternative.

**Extension**:
- `RankedBattle.isFinished`: tells whether the game is finished.

#### RegularRankedBattle
This type of `RankedBattle` represents competitive seasonal ranked battle. In this kind, you may retrieve
players ranks in 'Ranked':
```Kotlin
println(
    teams.flatten().joinToString("\n") { 
        "${it.name.raw}: $it.name.rankedStage"
    }
)
```
<note>
`rankedStage` is the same as in the `RawBattle`, no actual difference.
</note>

#### FriendlyRankedBattle
Represents a friendly ranked game battle, which may be assumed based on incomplete data.

Any friendly ranked game battle is **ASSUMED**, meaning that there's no actual indicator on the Brawl Stars API side –
it is inferred by other battles. It's for the most part reliable data, excluding very exclusive cases (when you're
directly trying to break the algorithm, but even in this situation, most known cases are covered).

_________

### Would be good to know

##### How friendly ranked battles are inferred? {collapsible="true"}
Friendly ranked game battles are inferred by the following rules:

- `battle.mode` is playable ranked game (showdown, for example, is excluded)
- Every ranked battle has last game with `battle.result == victory || defeat` or the same, but with no starPlayer
  defined. `battle.result == draw` might be also the case if the team is the same, map is the same, it has no star
  player in its rounds (if there's no rounds – it will be just FriendlyTeamsBattle).

  But if friendly battle had no action (no damage or other impact from players in the game) it might interfere with this
  logic.
  We consider it as almost impossible (it can be done only on purpose). Friendly ranked battles
  shouldn't affect most people and such possibly wrong assumption shouldn't cause much of a trouble.

##### Known limitations to the friendly ranked battles {collapsible="true"}
- If it's the last battle's round in the provided list (it requires previous rounds for context), it will be marked as regular `TeamsBattle`.
- In very-very unique cases (mostly when you're trying to achieve ranked game-like case on purpose) when there's match
with `BattleResultKind.DRAW` with no `starPlayer` defined (basically fully AFK game from two teams), it may mark it as
a `FriendlyRankedBattle` if it's the last game available. **This behaviour might be changed over time.**

##### How regular ranked battles are inferred? {collapsible="true"}
Apart from the straight-forward way that might tell us that particular battle is ranked – some of them might be just
'rounds' for them, meaning that they are also important for the full picture.

In the same way as `FriendlyRankedBattle` (learn it below) infers, we just look for a previous battles from the last one with specified
'starPlayer' in API (in ranked rounds there's no defined starPlayer) and then down the list while there's the same mode,
event, teams, map and no 'starPlayer' event. But in that case, we aren't dependent on
the rounds that might be out of the actual list – we will still receive ranked game. For `RankedBattle`s `typed` function
might include non-finished battles if they have this behaviour, but without battle with starPlayer.