# Retrieving Player Ranked Stage
Looking for a way to get player's ranked? No wonder – when you explore the official Brawl Stars API, you most likely cannot find
anything related to the Ranked gamemode and how to resolve it. This tutorial
was made to show you the way I explored and how can you do this on your own
using this library.

## Theory
Let's understand what the Brawl Stars API battlelog endpoint (`/players/{playerTag}/battlelog`) returns with the sample JSON in the expand tab below.

<code-block lang="json" collapsible="true" collapsed-title="response.json">
    {
  "items": [
    {
      "battleTime": "20241223T002956.000Z",
      "event": {
        "id": 15000300,
        "mode": "hotZone",
        "map": "Ring of Fire"
      },
      "battle": {
        "mode": "hotZone",
        "type": "soloRanked",
        "result": "defeat",
        "duration": 126,
        "starPlayer": {
          "tag": "#2QCGPJU8G",
          "name": "˹␌˼I§mä╰‿╯",
          "brawler": {
            "id": 16000061,
            "name": "GUS",
            "power": 10,
            "trophies": 10
          }
        },
        "teams": [
          [
            {
              "tag": "#2Y920PYVGP",
              "name": "Benny-Bops",
              "brawler": {
                "id": 16000017,
                "name": "TARA",
                "power": 11,
                "trophies": 10
              }
            },
            {
              "tag": "#2QCGPJU8G",
              "name": "˹␌˼I§mä╰‿╯",
              "brawler": {
                "id": 16000061,
                "name": "GUS",
                "power": 10,
                "trophies": 10
              }
            },
            {
              "tag": "#PL22JGPC",
              "name": "Wiviix",
              "brawler": {
                "id": 16000030,
                "name": "EMZ",
                "power": 11,
                "trophies": 10
              }
            }
          ],
          [
            {
              "tag": "#JVQ9989PP",
              "name": "⛩️•|d4vid|•⛩️",
              "brawler": {
                "id": 16000014,
                "name": "BO",
                "power": 9,
                "trophies": 10
              }
            },
            {
              "tag": "#9V8LCUC0G",
              "name": "sukara",
              "brawler": {
                "id": 16000081,
                "name": "LILY",
                "power": 10,
                "trophies": 10
              }
            },
            {
              "tag": "#LRCJQP89C",
              "name": "SK/LAYIONS",
              "brawler": {
                "id": 16000015,
                "name": "PIPER",
                "power": 9,
                "trophies": 10
              }
            }
          ]
        ]
      }
    },
    {
      "battleTime": "20241223T002731.000Z",
      "event": {
        "id": 15000300,
        "mode": "hotZone",
        "map": "Ring of Fire"
      },
      "battle": {
        "mode": "hotZone",
        "type": "soloRanked",
        "result": "defeat",
        "duration": 91,
        "starPlayer": null,
        "teams": [
          [
            {
              "tag": "#2Y920PYVGP",
              "name": "Benny-Bops",
              "brawler": {
                "id": 16000017,
                "name": "TARA",
                "power": 11,
                "trophies": 10
              }
            },
            {
              "tag": "#2QCGPJU8G",
              "name": "˹␌˼I§mä╰‿╯",
              "brawler": {
                "id": 16000061,
                "name": "GUS",
                "power": 10,
                "trophies": 10
              }
            },
            {
              "tag": "#PL22JGPC",
              "name": "Wiviix",
              "brawler": {
                "id": 16000030,
                "name": "EMZ",
                "power": 11,
                "trophies": 10
              }
            }
          ],
          [
            {
              "tag": "#JVQ9989PP",
              "name": "⛩️•|d4vid|•⛩️",
              "brawler": {
                "id": 16000014,
                "name": "BO",
                "power": 9,
                "trophies": 10
              }
            },
            {
              "tag": "#9V8LCUC0G",
              "name": "sukara",
              "brawler": {
                "id": 16000081,
                "name": "LILY",
                "power": 10,
                "trophies": 10
              }
            },
            {
              "tag": "#LRCJQP89C",
              "name": "SK/LAYIONS",
              "brawler": {
                "id": 16000015,
                "name": "PIPER",
                "power": 9,
                "trophies": 10
              }
            }
          ]
        ]
      }
    },
    {
      "battleTime": "20241222T231848.000Z",
      "event": {
        "id": 15000882,
        "mode": "knockout",
        "map": "H for..."
      },
      "battle": {
        "mode": "knockout",
        "type": "ranked",
        "result": "victory",
        "duration": 48,
        "trophyChange": 9,
        "starPlayer": {
          "tag": "#9P8J2LRRL",
          "name": "Deadpool",
          "brawler": {
            "id": 16000088,
            "name": "BUZZ LIGHTYEAR",
            "power": 11,
            "trophies": 1000
          }
        },
        "teams": [
          [
            {
              "tag": "#9P8J2LRRL",
              "name": "Deadpool",
              "brawler": {
                "id": 16000088,
                "name": "BUZZ LIGHTYEAR",
                "power": 11,
                "trophies": 1000
              }
            },
            {
              "tag": "#9V8LCUC0G",
              "name": "sukara",
              "brawler": {
                "id": 16000063,
                "name": "CHESTER",
                "power": 11,
                "trophies": 1103
              }
            },
            {
              "tag": "#9QQ8LLLVQ",
              "name": "oren safety",
              "brawler": {
                "id": 16000003,
                "name": "BROCK",
                "power": 11,
                "trophies": 1171
              }
            }
          ],
          [
            {
              "tag": "#9GU2LCP8",
              "name": "I Love Boomers",
              "brawler": {
                "id": 16000012,
                "name": "CROW",
                "power": 11,
                "trophies": 1003
              }
            },
            {
              "tag": "#YP2LYURL",
              "name": "StarPro",
              "brawler": {
                "id": 16000065,
                "name": "MANDY",
                "power": 11,
                "trophies": 1056
              }
            },
            {
              "tag": "#GJYJY22PQ",
              "name": "MR. Clean🐉",
              "brawler": {
                "id": 16000038,
                "name": "SURGE",
                "power": 11,
                "trophies": 1009
              }
            }
          ]
        ]
      }
    }
  ]
}
</code-block>

In the JSON above you might see that there's three matches: two of type `soloRanked` and one regular `ranked`. Let's
define what do they stand for:
- `soloRanked` as well as `duoRanked` and `trioRanked`: stands for Ranked GameMode.
- `ranked` that might seem confusing, stands for regular battles for trophies.

But how exactly it helps us with understanding what rank does player have? After a small testing, I found out
that value of `trophies` within `teams` represents the actual player's rank:
- `1` equals Bronze I
- `2` equals Bronze II
- `3` equals Bronze III
- `4` equals Silver I
- and so on...

Even though you can't actually obtain the rank in Ranked GameMode of specific player, if you're able
to track player's battlelog – you still can do this.

Now, let's see how you can deal with it within library:
## Practice
When you use our library, you can easily determine is it a ranked game:
```Kotlin
val client: BrawlStarsClient = TODO()
val playerTag: PlayerTag = TODO()

val battleLog = client.[[[getPlayerBattlelog|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/BrawlStarsClient.kt#L79]]](playerTag)
    .getOrElse { error("Invalid tag!") }
    
// they're already sorted by time descending
val latestRankedBattle = battleLog.firstOrNull {
    it.event.type.[[[isRankedGameMode|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/event/value/BattleType.kt#L48]]]
}

// opt-in for this API as you must be sure that game is actually ranked
@OptIn(ContextualApi::class)
val stage: RankedStage = latestRankedBattle.[[[getRankedStage|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/event/Battle.kt#L73]]](playerTag)

if (stage >= [[[RankedStage.DIAMOND_ONE|https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/event/value/RankedStage.kt#L23]]])
    println("Player can ban brawlers!")
else println("Player cannot ban brawlers")
```
> Look for extensions, they might provide you with most of the needed features.
> For example, you may discover [`RankedStage.isMaster`](https://github.com/y9vad9/brawlstars-api/blob/45cb691a7e3930b3ee2610f3aaf9b570a856488f/core/src/commonMain/kotlin/com/y9vad9/bsapi/types/event/value/RankedStage.kt#L45) that will tell for you whether player is qualified for Masters.
{style="note"}

All you need is to regularly check battle log and save information in some kind of database to
have information about player's rank.

It might seem inconvenient, but for now it's the only way, except scrapping some of the websites that do the same.