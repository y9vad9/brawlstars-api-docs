# Profiles
One of the basic things that you're most likely to use is to get certain user's information (name, brawlers, club, etc.).
How to do this and some information related to it you will learn on this page.

<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>core</code> library</a></step>
    <step><a href="Getting-started.md">Create Brawl Stars API Client instance</a></step>
</procedure>

## Basic usage
Here's basic usage:
```Kotlin
val player: Player = client.getPlayer(PlayerTag.createUnsafe("..."))
    .getOrElse { throw it } // just rethrow error for our case
    ?: error("No such player found")
```
To handle Brawl Stars specific errors, please refer to [](Handling-Brawl-Stars-Client-Error.md).

There you may get the following information:
- **tag**: Player's unique tag, consisting of 7 to 12 symbols.
- **name**: Player's in-game name, which is publicly visible in matches, lobbies, club, etc.
- **nameColor**: The color of the player's name, displayed in hexadecimal format.
    
    In Brawl Stars API it's represented in the following way: `0xffcb5aff` (for Brawlify API it's different). You
    may check the original formatting via `HexColor.isHexLiteral` (whether it starts with 0x) or `HexColor.isHexText` (whether
    it starts with '#').

    You may refer to the [HexColor](https://github.com/y9vad9/brawlstars-api/blob/f39785e06a630c562d0f81708b3f4d60dbd35691/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/common/value/HexColor.kt)
    class for more information.
- **icon**: The player's profile icon, representing their account visually (you may retrieve actual image via [](Brawlify-Api.md)).
- **trophies**: The total number of trophies the player currently holds.
- **highestTrophies**: The highest trophy count the player has ever achieved (before trophies reset or loosing trophies).
- **expLevel**: The player's [experience level](https://brawlstars.fandom.com/wiki/Experience), representing their overall progress in the game.
- **expPoints**: The number of [experience points](https://brawlstars.fandom.com/wiki/Experience) accumulated by the player. 
- **isQualifiedFromChampionshipChallenge**: Indicates if the player has qualified for the Championship Challenge.
- **threeVsThreeVictories**: The total number of victories the player has achieved in [3v3 game modes](https://brawlstars.fandom.com/wiki/Category:3v3_Events).
- **soloVictories**: The total number of victories the player has achieved in Solo [Showdown mode](https://brawlstars.fandom.com/wiki/Showdown).
- **duoVictories**: The total number of victories the player has achieved in Duo [Showdown mode](https://brawlstars.fandom.com/wiki/Showdown).
- **bestRoboRumbleTime**: The player's best survival time in [Robo Rumble](https://brawlstars.fandom.com/wiki/Robo_Rumble).
- **bestTimeAsBigBrawler**: The player's best survival time as the Big Brawler in [Big Game](https://brawlstars.fandom.com/wiki/Big_Game).
- **club**: Information about the player's club, or `null` if the player is not part of a club.
    - **tag**: Club's unique tag.
    - **name**: Club's name.
- **brawlers**: The list of [brawlers](https://brawlstars.fandom.com/wiki/Category:Brawlers) owned by the player, each with their stats and details.

## Retrieving image of a profile
<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>brawlify</code> module of the library</a></step>
    <step><a href="Getting-started.md">Create Brawlify Client instance</a></step>
</procedure>

When using [Brawlify API for icons](https://brawlapi.com/#/endpoints/icons) you can get the player's icon image url:
```Kotlin
suspend fun getImageUrl(
  client: BrawlifyClient, 
  iconId: IconId
): BrawlifyUrl? {
  // retrieves all icons available on the brawlify
  // it's good idea to cache it on your side, because
  // brawlify does not provide alternative way of getting single icon.
  return client.getIcons()
    // return null on failure
    .getOrElse { return null }
    .playersIcons
    // if it's a very-very new icon it might not be available
    .firstOrNull { id == iconId }
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


