# Rankings

<procedure title="Pre-conditions" collapsible="true">
    <step><a href="Getting-started.md">Install <code>core</code> and, if needed, <code>brawlify</code> modules of the library</a></step>
    <step><a href="Getting-started.md">Create Brawl Stars API & Brawlify API Clients (if needed) instances</a></step>
</procedure>

In the game there are [different leaderboards](https://brawlstats.com/leaderboards) based on:

- Brawlers and Country
- Players and Country
- Club and Country
- Ranked and Country (unsupported by the official API)

Working with it is very easy within the library, let start with the top-100 the best players:

```Kotlin
// all 100 players at once
bsClient
    .getPlayersRankings(
        countryCode = CountryCode.GERMANY
    )
    .getOrElse { throw it } // rethrow for simplicity of example
    ?.forEach(::println) // just printing them
    // for our example it shouldn't be null, but client returns
    // nullable because some of the countries might not be supported
    ?: println("Specified country does not supported in the rankings")

// or via PagesIterator:
val bestPlayersIterator: PagesIterator<Player.Ranking> = bsClient
    .getPlayersRankingsLazily(
        countryCode = CountryCode.GLOBAL
    )

bestPlayersIterator.forEachPage { result: Result<List<Player.Ranking>> ->
     // retry until success
     println(result.getOrElse { return@forEachPage })
}
```
You may read more about PagesIterator [here](Pagination.md).

The response contains:

```Kotlin
@Serializable
public data class Ranking(
    val tag: PlayerTag,
    val name: PlayerName,
    val icon: PlayerIcon,
    val trophies: Trophies,
    val rank: RankingPosition,
    val club: Club.View,
)
```

{collapsible="true" collapsed-title="Player.Ranking"}

- To learn how to get avatars of the players – visit [this section](Profile.md#retrieving-image-of-a-profile).
- To learn how to get badges of the clubs – visit [this section](Clubs.md#retrieving-badge-image-of-a-club)

Now let's move to the clubs:

```Kotlin
// all 100 players at once
bsClient
    .getClubsRankings(
        countryCode = CountryCode.UKRAINE,
    )
    .getOrElse { throw it } // rethrow for simplicity of example
    ?.forEach(::println) // just printing them
    // for our example it shouldn't be null, but client returns
    // nullable because some of the countries might not be supported
    ?: println("Specified country does not supported in the rankings")

// or via PagesIterator:
val bestClubsIterator: PagesIterator<Club.Ranking> = bsClient
    .getClubsRankingsLazily(
        countryCode = CountryCode.GLOBAL,
        count = Count.createUnsafe(20),
    )

bestClubsIterator.asFlow()
    .catch { e: Exception -> /* ... */ }
    .collect { List<Club.Ranking> ->
        // retry until success
        println(result.getOrElse { return@forEachPage })
    }
```
You may read more about PagesIterator [here](Pagination.md).

The output is the following:
```Kotlin
@Serializable
public data class Ranking(
    val clubTag: ClubTag,
    val name: ClubName,
    val trophies: Trophies,
    val rank: RankingPosition,
)
```
{collapsible="true" collapsed-title="Club.Ranking"}

Unfortunately, you will need to get club one by one to show any visuals. 😕

And finally the best players per brawler:
```Kotlin
// all 100 players at once
bsClient
    .getBrawlerRankings(
        countryCode = CountryCode.POLAND,
        brawlerId = BrawlerId.SHELLY,
    )
    .getOrElse { throw it } // rethrow for simplicity of example
    ?.forEach(::println) // just printing them
    // for our example it shouldn't be null, but client returns
    // nullable because some of the countries might not be supported
    ?: println("Specified country does not supported in the rankings")

// or via PagesIterator:
val bestShellyPlayersIterator: PagesIterator<Brawler.Ranking> = bsClient
    .getBrawlerRankingsLazily(
        countryCode = CountryCode.GLOBAL,
        count = Count.createUnsafe(20),
    )
    // you may also map values with PagesIterator!
    // maps to player name
    .map { it.name } 

bestShellyPlayersIterator.asFlow()
    .catch { e: Exception -> /* ... */ }
    .collect { List<Brawler.Ranking> ->
        // retry until success
        println(result.getOrElse { return@forEachPage })
    }
```
You may read more about PagesIterator [here](Pagination.md).


Here is what you get:
```Kotlin
@Serializable
public data class Ranking(
    val id: BrawlerId,
    val name: PlayerName,
    val tag: PlayerTag,
    val icon: PlayerIcon,
    val trophies: Trophies,
    val club: Club.View,
    val rank: RankingPosition,
)
```
{collapsible="true" collapsed-title="Brawler.Ranking"}

<seealso style="cards">
    <category ref="internal">
        <a href="Pagination.md" />
    </category>
    <category ref="external">
        <a href="https://developer.brawlstars.com/#/documentation">Brawl Stars API Documentation</a>
    </category>
</seealso> 