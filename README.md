# scoreboards
completely experimental. not suit for production

# What's the purpose of `scoreboards`?
We focus to fix the (in my opinion) terrible way of handling scoreboards most (public) scoreboard APIs do, namely:
most scoreboard APIs tend to update the __entire__ scoreboard every X ticks, even if the line is static and will practically never change.

Well, how do we fix this? The way we aim to fix this is to give the option to individually update the entries, through for example, listeners and/or only updating 
a specific entry every X ticks (keyword - specific, not the entire board).

# Examples
```kt
        Scoreboards
            .registerOnJoin { player ->
                val context = ScoreboardContext(player)

                context.add("${ChatColor.GRAY}${ChatColor.STRIKETHROUGH}----------------") // this line is static, and it will never update.

                context.add<PlayerMoveEvent> { // this line will update everytime the player itself moves. 
                    it.to.x
                        .roundToInt()
                        .toString()
                }

                context.listen<PlayerMoveEvent>() 
                    .handle { // this line will update everytime the player moves.
                        it.to.y
                            .roundToInt()
                            .toString()
                    }
                    .hide { // but wait! if the player's Y level is above 50, it won't be displayed. 
                        it.to.y >= 50
                    }
                    .create()

                context.repeating()
                    .cooldown(20L) // this means the entry here will update every 20 ticks (1 second, in minecraft)
                    .hide { // hide the entry if the player's health is above 15
                        player.health >= 15.0
                    }
                    .handle { // if the player's health is not above 15, display their health with the "Low health!" message.
                        "Low health! ${player.health}"
                    }
                    .create()

                context.add("${ChatColor.GRAY}${ChatColor.STRIKETHROUGH}----------------") // like the example at the top, this is a static line and will never update.

                return@registerOnJoin context
            }
```