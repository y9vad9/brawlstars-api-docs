# Clubs
Clubs are essential things in Brawl Stars. On this page you will learn how get club's information,
such as name, description, members and so on.

<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>core</code> library</a></step>
    <step><a href="Getting-started.md">Create Brawl Stars API Client instance</a></step>
</procedure>

## Basic usage
Here's basic usage:
```Kotlin
val club = client.getClub(ClubTag.createUnsafe("..."))
    .getOrElse { throw it } // just rethrow error for our case
    ?: error("No such club found")
```
To handle Brawl Stars specific errors, please refer to [](Handling-Brawl-Stars-Client-Error.md).

There you may get the following information:
- **tag**: Club's unique tag, used to identify the club in the game.
- **name**: The name of the club, visible to all players.
- **description**: A brief description of the club, provided by the club owner or leader.
- **requiredTrophies**: The minimum number of trophies required to join the club.
- **badgeId**: The unique identifier for the club's badge, representing its visual emblem (you may retrieve actual image via [](Brawlify-Api.md)).
- **trophies**: The total number of trophies contributed by all club members.
- **type**: The type of club, indicating whether it is open, closed, or requires approval to join.
- **members**: A list of club members, each with detailed information:
  - **tag**: Unique tag identifying the player in the club.
  - **name**: The player's in-game name.
  - **nameColor**: The color of the player's name, displayed in hexadecimal format.
  - **role**: The player's role in the club (e.g., Member, Vice President, President).
  - **trophies**: The number of trophies the player has contributed to the club.
  - **icon**: The player's profile icon, representing their account visually.
  
  Learn more about data provided in the `members` field in '[](Profile.md)' topic.

### Retrieving members-only
You may also get the list of members alone:
```Kotlin
val clubMembers = client.getClubMembers(ClubTag.createUnsafe("..."))
    .getOrElse { throw it } // just rethrow error for our case
    ?: error("No such club found")

clubMembers.forEach(::println)
```
You may also get them with pagination as in the original API:
```Kotlin
client.getClubMembersLazily(ClubTag.createUnsafe("...")).forEachPage {
  if (it.isSuccess)
    println(it.getOrThrow())
  else {
    println("Error happenned :( delaying it for 5000 ms")
    delay(5000L)
  }
}
```
Learn more about pagination [here](Pagination.md).

## Retrieving badge image of a club
<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>brawlify</code> module of the library</a></step>
    <step><a href="Getting-started.md">Create Brawlify Client instance</a></step>
</procedure>

When using [Brawlify API for icons](https://brawlapi.com/#/endpoints/icons) you can get the club's badge image url:
```Kotlin
suspend fun getImageUrl(
  client: BrawlifyClient, 
  badgeId: BadgeId
): BrawlifyUrl? {
  // retrieves all icons available on the brawlify
  // it's good idea to cache it on your side, because
  // brawlify does not provide alternative way of getting single icon.
  return client.getIcons()
    // return null on failure
    .getOrElse { return null }
    .clubsIcons
    // if it's a very-very new icon it might not be available
    .firstOrNull { id == badgeId }
    ?.imageUrl
}
```
Then you may download image in any way (library does not include this functionality, because it's not needed for everyone).
<seealso style="cards">
    <category ref="external">
      <a href="https://brawlapi.com/#/endpoints/icons" summary="Get Icons API endpoint summary with example output.">Brawlify Icons API</a>
    </category>
    <category ref="internal">
        <a href="Clubs.md"/>
        <a href="Handling-Brawl-Stars-Client-Error.md"/>
    </category>
</seealso> 
