# Brawlify API
The `BrawlifyClient` is a Kotlin-based client for interacting with the Brawlify API. It provides access to various Brawl Stars data such as events, maps, icons, and game modes.
This client simplifies making HTTP requests to the API, handling JSON parsing, and providing a type-safe interface for developers.
In addition, both Brawl Stars API and Brawlify API share some of the entities, like `EventId`.

<note>
This is very basic overview of the API, I plan to extend it in the future once I get in handy with it fully. Until then,
if you're willing to help with it – your contributions are welcome.

You will also have a look on the integration of it into regular Brawl Stars API in most of the tutorials.
</note>

---

## Creating an Instance of `BrawlifyClient`

To use the `BrawlifyClient`, you'll need an `HttpClientEngine`. You can also customize the JSON deserialization behavior and HTTP client configuration if necessary. Here's an example of how to create an instance:

```kotlin
import io.ktor.client.engine.cio.CIO
import kotlinx.serialization.json.Json

val client = BrawlifyClient(
    engine = CIO,
    // not all fields are included in API due to
    // lack of information about them (for example, some of them
    // are null in any case)
    // in addition, for forward compatibility it's always good
    // to have it
    json = Json { ignoreUnknownKeys = true },
    configBlock = {
        // Additional HttpClient configuration here
    }
)
```

---

## Methods Overview

### 1. **Get Events**
Retrieve current and upcoming events in Brawl Stars.

**Method:**
```kotlin
suspend fun getEvents(): Result<GetEventsResponse>
```

- **Returns:** A `Result` containing the response with active and upcoming events or an error.
- **Usage Example:**
  ```kotlin
  val events = client.getEvents()
  events.onSuccess { println(it.active) } // prints active events
  ```

[API Documentation](https://brawlapi.com/#/endpoints/events)

---

### 2. **Get Maps**
Retrieve all available maps in Brawl Stars.

**Method:**
```kotlin
suspend fun getMaps(): Result<List<BrawlifyMap>>
```

- **Returns:** A `Result` containing a list of maps or an error.
- **Usage Example:**
  ```kotlin
  val maps = client.getMaps()
  maps.onSuccess { it.forEach { map -> println(map.name) } }
  ```

[API Documentation](https://brawlapi.com/#/endpoints/maps)

---

### 3. **Get Map by Brawl Stars Event ID**
Retrieve a specific map using its event ID.

**Method:**
```kotlin
suspend fun getMap(eventId: EventId): Result<BrawlifyMap?>
```

- **Parameters:**
    - `eventId`: The unique identifier for the event that can be obtained via Brawl Stars API (battles list, events rotation) 
    or via Brawlify API itself.
- **Returns:** A `Result` containing the map or `null` if not found.
- **Usage Example:**
  ```kotlin
  val map = client.getMap(eventId)
  map.onSuccess { println(it?.name) }
  ```

[API Documentation](https://brawlapi.com/#/endpoints/maps)

---

### 4. **Get Icons**
Retrieve available icons for players and clubs.

**Method:**
```kotlin
suspend fun getIcons(): Result<GetIconsResponse>
```

- **Returns:** A `Result` containing icons for players and clubs.
- **Usage Example:**
  ```kotlin
  val icons = client.getIcons()
  icons.onSuccess {
      println(it.playersIcons.size)
      println(it.clubsIcons.size)
  }
  ```

[API Documentation](https://brawlapi.com/#/endpoints/icons)

---

### 5. **Get Game Modes**
Retrieve a list of game modes with detailed information.

**Method:**
```kotlin
suspend fun getGameModes(): Result<List<BrawlifyFullGameMode>>
```

- **Returns:** A `Result` containing a list of game modes or an error.
- **Usage Example:**
  ```kotlin
  val gameModes = client.getGameModes()
  gameModes.onSuccess { it.forEach { mode -> println(mode.name) } }
  ```

[API Documentation](https://brawlapi.com/#/endpoints/gamemodes)

---

### 6. **Get Game Mode by ID**
Retrieve a specific game mode using its ID.

**Method:**
```kotlin
suspend fun getGameMode(id: BrawlifyGameModeId): Result<BrawlifyFullGameMode?>
```

- **Parameters:**
    - `id`: The unique identifier for the game mode.
- **Returns:** A `Result` containing the game mode or `null` if not found.
- **Usage Example:**
  ```kotlin
  val gameMode = client.getGameMode(id)
  gameMode.onSuccess { println(it?.name) }
  ```

[API Documentation](https://brawlapi.com/#/endpoints/gamemodes)
