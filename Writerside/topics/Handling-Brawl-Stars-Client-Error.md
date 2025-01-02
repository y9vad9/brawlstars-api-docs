# Brawl Stars API Errors
Any request to the API via `BrawlStarsClient` class will return
`Result<T>` with either wrapped custom type of error named `ClientError` or
other like [`kotlinx.io.IOException`](https://github.com/Kotlin/kotlinx-io/blob/ff2c5496ce98ec1623b2d0a07161f86b93906c1a/core/common/src/-CommonPlatform.kt)
(ktor-specific failure on network exceptions).

Here's example of handling resulting failures:
```kotlin
val player = result.getOrElse { throwable ->
    when (throwable) {
        // Handle network-related failures (e.g., lost connection, timeout).
        is IOException -> {
            println("Network error occurred: ${throwable.message}")
            // Retry logic or notify the user.
        }
        // Handle API-specific failures.
        is BrawlStarsAPIException -> {
            when (throwable) {
                is BrawlStarsAPIException.AccessDenied -> {
                    println("Access denied: ${throwable.message}")
                }
                is BrawlStarsAPIException.LimitsExceeded -> {
                    println("Request throttled: ${throwable.message}")
                    // Suggest increasing token limits or waiting before retrying.
                }
                is BrawlStarsAPIException.InternalServerError -> {
                    println("Internal server error: ${throwable.message}")
                    // Optionally log for monitoring systems.
                }
                is BrawlStarsAPIException.UnderMaintenance -> {
                    println("API under maintenance: ${throwable.message}")
                    // Retry after some time or inform the user about downtime.
                }
                is BrawlStarsAPIException.RawHttpError -> {
                    println("Unexpected HTTP error: ${throwable.httpStatusCode}")
                    // Log or report the status code.
                }
            }
        }
        // Catch-all for unexpected errors.
        else -> {
            println("Unexpected error occurred: ${throwable.message}")
            // Log or rethrow the exception.
        }
    }
} ?: run {
    // Handle null results (e.g., not-found player)
    println("Player not found.")
}
```