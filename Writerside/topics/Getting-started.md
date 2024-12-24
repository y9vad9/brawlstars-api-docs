# Getting started

Let's do it quick!

## Implementation

First of all, you need the following dependencies:

```Kotlin
repositories {
    mavenCentral()
}

dependencies {
    // Brawl Stars API (jvm, js)
    commonMainImplementation("com.y9vad9.bsapi:core:$version")
    // Brawlify API (jvm, js)
    commonMainImplementation("com.y9vad9.bsapi:core:$version")
}
```

## Usage

### Brawl Stars API

#### Fetching Player Data

1. **Define your client:**

   Use the library’s prebuilt API client to interact with endpoints:

   ```kotlin
   val client = BrawlStarsClient(apiKey = "your-api-key", engine = CIO)
   ```
   - Regarding `engine` parameter, please refer to
   the [Ktor Documentation on client engines](https://ktor.io/docs/client-engines.html).
   - As for `apiKey` parameter, you need to obtain it [here](https://developer.brawlstars.com/#/).

2. **Fetch player details:**

   ```kotlin
   // error-proof way of passing player's tag, to avoid,
   // for example, unnecessary API calls and validation on your side:
   val playerTag: PlayerTag = PlayerTag.createOr("9V8LCUC0G") { // accepts both with hashtag and without
       error("Invalid player tag!")
   }
   val player = client.getPlayer(playerTag)
   player.onSuccess {
       println("Player Name: ${it.name}, Trophies: ${it.trophies}")
   }.onFailure {
       println("Error fetching player: ${it.message}")
   }
   ```
   Library enforces type safety with validated value classes. For
   the reference, take a look
   at [PlayerTag](https://github.com/y9vad9/brawlstars-api/blob/master/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/player/value/PlayerTag.kt).

#### Fetching Battle Data

1. **Retrieve battle logs:**

   ```kotlin
   // if you're sure about input tag, you can use unsafe-way 
   // of institating
   val playerTag: PlayerTag = PlayerTag.createUnsafe("9V8LCUC0G")
   
   val battles = client.getBattleLogs(playerTag)
   battles.onSuccess {
       it.forEach { battle ->
           if (battle.type.isRankedGameMode) {
              println("This is ranked game mode!")
              val stage = battle.getRankedStage(playerTag)
  
              if (stage >= RankedStage.DIAMOND_ONE)
                print("In this match was picking stage.")
           }
   
           println("Battle Mode: ${battle.mode}, Result: ${battle.result}")
       }
   }.onFailure {
       println("Error fetching battles: ${it.message}")
   }
   ```
   
### Brawlify
1. **Initialize Client**:
   ```kotlin
   val brawlifyClient = BrawlifyClient(engine = CIO)
   ```
2. **Get events**:
   ```kotlin
   brawlifyClient.getEvents()
    .onSuccess { events ->
        println("active: ${events.active}")
        println("upcoming: ${events.upcoming}")
    }
   ```
   
<seealso style="cards">
    <category ref="external">
        <a href="https://developer.brawlstars.com/#/documentation">Brawl Stars API Documentation</a>
        <a href="https://brawlapi.com/#/">Brawlify API Documentation</a>
    </category>
</seealso> 

