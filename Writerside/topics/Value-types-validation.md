# Value types and Validation
When working with the library, first thing you run into is **Validation**.
Every data that models entity from any source within library has strict
constraints, own type that tells about data and so on. They're [value objects from Domain-Driven Design](https://en.wikipedia.org/wiki/Value_object).

## Initiating such classes
All those types have one thing in common – ValueFactory in the [Companion object](https://kotlinlang.org/docs/object-declarations.html#companion-objects).

All it does is that the Companion has function that creates needed for you instance – `create`.
It returns [`kotlin.Result`](https://kotlinlang.org/api/core/kotlin-stdlib/kotlin/-result/) with the constructed value
or with exception of type [`CreationFailure`](https://github.com/y9vad9/brawlstars-api/blob/master/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/exception/CreationFailure.kt) by contract.

The creation of such types can be done in the following ways:
```kotlin
// basic way
val clubTag: ClubTag = ClubTag.create("...")
    .getOrElse { throwable: Throwable -> /* ... */ }

// shortcut
val clubTag = ClubTag.createOr("...") { failure: CreationFailure ->
    // ...
}

// unsafe way (requires [[[opt-in|https://kotlinlang.org/docs/opt-in-requirements.html]]])
// if value is invalid, it will throw an Exception
@OptIn(ValueConstructor.Unsafe::calss)
val clubTag: ClubTag = ClubTag.createUnsafe("...")
```

For more information, you may want to see [`ValueConstructor` sources](https://github.com/y9vad9/brawlstars-api/blob/master/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/ValueConstructor.kt).