# Pagination
When working with Brawl Stars API and its documentation you may
see that it supports 'pagination' that makes you able to avoid
loading too much data, when it's not need and overall speed up
the loading.

In our library, we also support it, but in the very convenient way
through asynchronous [`PagesIterator`](https://github.com/y9vad9/brawlstars-api/blob/master/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/pagination/PagesIterator.kt).

`PagesIterator` is able to go back and forward as in the official API.

Let's see the example:
```kotlin
fun foo(client: BrawlStarsClient, clubTag: ClubTag) {
    // limit defines elements per page
    val iterator = client.[[[getClubMembersLazily|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/BrawlStarsClient.kt#L115]]](
        clubTag = clubTag,
        limit = Count.createUnsafe(5)
    )
    
    while (iterator.hasNext()) {
        // if failed, retry
        val page = iterator.next().getOrNull() ?: continue
        println(page)
    }
    
    // now we can go back:
    while (iterator.hasPrevious()) {
        val page = iterator.previous().getOrNull() ?: continue
        println(page)
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
    
    flow.collect { page ->
        println(page)
    }
}
```

There's other extension that you can explore [here](https://github.com/y9vad9/brawlstars-api/blob/master/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/pagination/PagesIterator.kt#L61).