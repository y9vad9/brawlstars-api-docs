# Events Rotation
<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>core</code> and, if needed, <code>brawlify</code> modules of the library</a></step>
    <step><a href="Getting-started.md">Create Brawl Stars API & Brawlify API Clients (if needed) instances</a></step>
</procedure>
When you're playing Brawl Stars competitive, it's all about events and as
you probably know, they're replacing each other time by time. 

| Event Slot     | Duration        |
|----------------|-----------------|
| Trophies       | 24 hours        |
| Ranked         | 30 days         |
| Map Maker      | 24 hours        |
| Special Events | Event-dependent |

<note>
It's information from official site from page about 
<a href="https://support.supercell.com/brawl-stars/en/articles/game-modes-9.html">Game modes</a>.
I didn't validate it myself. You may learn about some of the game modes there or on the 
<a href="https://brawlify.com/gamemodes/">Brawlify</a>.
</note>

In the library there are two ways of getting the events – through
either [Brawl Stars API](https://developer.brawlstars.com/#/documentation) or [Brawlify API](https://brawlapi.com/#/endpoints/events).

Brawl Stars will provide you with basic information about events:
```kotlin
val rotation: List<ScheduledEvent> = brawlStarsClient
    .getEventsRotation()
    .getOrElse { error("here is your logic handling failures") }

println(
    rotation.joinToString("\n") {
        buildString {
         appendLine("startTime: ${it.startTime}")
         appendLine("endTime: ${it.endTime}")
         appendLine("slotId: ${it.slotId}")
         appendLine("event.id: ${it.event.id}")
         appendLine("event.map: ${it.event.map}")
         appendLine("event.mode: ${it.event.mode}")
         appendLine("event.modifiers: ${it.event.modifiers}")
        }
    }
)
```
That's all what you can get from the official API. On other hand, Brawlify API has more complete information and,
moreover, upcoming events instead of only current ones:
```kotlin
val (active, upcoming) = brawlifyClient.getEvents()
    .getOrElse { /* handle failures */ }

println("Active events:")
println(active)
println("Upcoming events:")
println(upcoming)
```
Here's data you're capable to get:
<code-block lang="kotlin" collapsible="true" collapsed-title="Entities">
@Serializable
public data class BrawlifyEvent(
    val slot: Slot,
    val predicted: Boolean,
    val startTime: Instant,
    val endTime: Instant,
    val map: BrawlifyMap,
) {
    @Serializable
    public data class Slot(
        val id: EventSlotId,
        val name: BrawlifyEventName,
        val emoji: BrawlifyEventEmoji,
        val listAlone: Boolean,
        val hideable: Boolean,
        val hideForSlot: EventSlotId? = null,
    )
}

@Serializable
public data class BrawlifyMap(
    val id: EventId,
    val new: Boolean,
    val disabled: Boolean,
    val name: MapName,
    val version: BrawlifyVersion,
    /**
     * The link pointing to the information page on [brawlify](https://brawlify.com).
     */
    val link: BrawlifyUrl,
    /**
     * The link pointing to the image of the in-game map.
     */
    val imageUrl: BrawlifyUrl,
    val credit: PlayerName? = null,
    val environment: Environment,
    val gameMode: BrawlifyGameMode,
    @Serializable(with = InstantFromUnixMillisecondsSerializer::class)
    val dataUpdated: Instant,
    @Serializable(with = InstantFromUnixMillisecondsSerializer::class)
    val lastActive: Instant?,
) {
    @Serializable
    public data class Environment(
        val id: BrawlifyEnvironmentId,
        val name: BrawlifyEnvironmentName,
        val hash: BrawlifyHash,
        val version: BrawlifyVersion,
        /**
         * The link pointing environmental specific image that
         * represents [Environment] in-game.
         */
        val imageUrl: BrawlifyUrl,
    )
}
</code-block>
In addition, as you may see, it also provides the statistics for the teams! It does not have limitations like
Brawl Stars official API, so the recommendation will be to use Brawlify for such tasks to minimize risk of running into
limitations of Brawl Stars API.


<seealso style="cards">
    <category ref="external">
        <a href="https://developer.brawlstars.com/#/documentation">Brawl Stars API Documentation</a>
        <a href="https://brawlify.com/gamemodes/">Brawlify API Documentation</a>
    </category>
</seealso> 