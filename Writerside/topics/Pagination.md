# Pagination
When working with Brawl Stars API and its documentation you may
see that it supports 'pagination' that makes you able to avoid
loading too much data, when it's not need and overall speed up
the loading.

In our library, we also support it, but in the very convenient way
through asynchronous [`PagesIterator`](https://github.com/y9vad9/brawlstars-api/blob/master/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/pagination/PagesIterator.kt).

`PagesIterator` is able to go back and forward as in the official API. In addition, it provides
`kotlin.Result` class in case of any failures that you may want to handle

Let's see the example:
```kotlin
fun printMembersName(client: BrawlStarsClient, clubTag: ClubTag) {
    // limit defines elements per page
    val iterator: PagesIterator<PlayerName> = client.[[[getClubMembersLazily|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/BrawlStarsClient.kt#L115]]](
        clubTag = clubTag,
        // how much items per page will be loaded
        limit = Count.createUnsafe(5)
    ).[[[map|https://github.com/y9vad9/brawlstars-api/blob/1d796f44771805de5f288e0201a2106bebe4d33e/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/pagination/PagesIterator.kt#L83]]] { member -> member.name }
    
    while (iterator.hasNext()) {
        // if failed, retry
        val name = iterator.next().getOrNull() ?: continue
        println(name.raw)
    }
    
    // now we can go back:
    while (iterator.hasPrevious()) {
        val name = iterator.previous().getOrNull() ?: continue
        println(name.raw)
    }
}
```
You may also use extensions like [`asFlow`](https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/pagination/PagesIterator.kt#L61)
to consume it as a flow:
```Kotlin
suspend fun foo(client: BrawlStarsClient, clubTag: ClubTag) {
    // limit defines elements per page
    val flow = client.[[[getClubMembersLazily|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/BrawlStarsClient.kt#L115]]](
        clubTag = clubTag,
        limit = Count.createUnsafe(5)
    ).[[[asFlow()|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/pagination/PagesIterator.kt#L61]]]
    
    // for flow, asFlow() extension throws exceptions inside flow
    // so, better to catch it
    flow.retryWhen { cause, attempt -> 
        cause.printStackTrace()
        (attempt < 10).also { it (it) delay(1000L) }
    }.collect { page ->
        println(page)
    }
}
```

There are other extensions that you can explore [here](https://github.com/y9vad9/brawlstars-api/blob/master/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/pagination/PagesIterator.kt#L61).