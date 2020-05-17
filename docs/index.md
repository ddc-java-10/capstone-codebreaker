---
title: Codebreaker
description: An online game of logic and deduction
layout: default
links:
  mastermind: "https://en.wikipedia.org/wiki/Mastermind_(board_game)"
  bulls_and_cows: "https://en.wikipedia.org/wiki/Bulls_and_Cows"
---

## Summary

_Codebreaker_ is an Android-based game resembling [Mastermind] or [Bulls and Cows], in which 2 players take turns creating and guessing a secret code. In Codebreaker, play proceeds via a cloud-based service that keeps track of the game (acting as referee, scorekeeper, and timekeeper), so that a game can be played asynchronously (e.g. 1 player creating a code, and the 2<sup>nd</sup> player guessing the solution over the next several hours, without both players using the game app at the same time) or in a nearly realtime fashion.

## Intended users &amp; user stories

* People who enjoy logic puzzles with a competitive aspect.

    > As someone who prides myself on analytical thinking, I want to be able to play games that let me demonstrate that ability in competition with others.

* People who enjoy challenging games, but who want to be able to set the parameters of the game in order to budget their time.

    > As a gameplayer with a demanding job, I want to be able to play games that let me control the expected length or difficulty, so that I can fit in short games during a break&mdash;and play longer, more difficult games when I have more free time.

## Game details

* **Playing the game**

    1. A player begins by starting the app, setting game preferences (length of code, pool of characters that can be used, maximum round duration), and entering the pool of waiting players. If another player with compatible preferences is already waiting in the pool, the 2 players are matched, and the game begins with the 1<sup>st</sup> of 2 rounds of play. 

    2. The player whose arrival to the pool allowed the game to start becomes the _codemaker_ for the 1<sup>st</sup> round of play. 

    3. A round of play starts with the codemaker creating a secret code of the agreed-upon length, using the selected pool of characters; the codemaker may do so manually, or by allowing the app to generate a random code. After the code is created and sent to the server, the codemaker takes no further action until the other player (the codebreaker) guesses the code correctly, or until time expires.

    4. The codebreaker is informed that a secret code has been created, and is prompted to guess the code. 

    5. The codebreaker then attempts to guess the code. After each guess, the server responds (and the app displays) the results of the guess: how many of the characters in the guess are correctly placed, and how many other characters in the guess are in the code, but they are not in the correct position in the guess.

        For example, if the code is `ABCDEF`, and the codebreaker guesses `AXBYCZ`, then the app would show that 1 character of the guess is in the correct position, and 2 others in the guess are also in the secret code, but those 2 are not in the correct positions. (Note that `A` is in the secret code, and is in the correct position in the guess, while `B` and `C` are also in the code, but their positions in the guess are not correct.) _The specific characters themselves are not revealed to the codebreaker, who must make additional guesses to deduce which are the correct characters._

    6. The codebreaker repeats step 5 until the secret code is guessed correctly, or until time expires. 

        * If the secret code is guessed successfully before time expires, then the number of guesses and the amount of time required are recorded on the server, and used (after completion of both rounds of play) to determine the winner.
        
        * If time expires before the codebreaker makes any guesses, then the codebreaker is assumed to have abandoned the game, and the codemaker wins by default. 
        
        * If time expires after the codebreaker makes at least 1 guess, then the results of the last guess are used (after completion of both rounds of play) in determining the winner.
        
    7. The roles are now reversed: The codebreaker from the 1<sup>st</sup> round becomes the codemaker for the 2<sup>nd</sup> round. The second round follows steps 3&ndash;6.

* **Winning the game**

    After completion of both rounds, the winner is determined as follows:

    * If only 1 of the players successfully guessed the other's secret code, then that player is the winner.

    * If each of the 2 players successfully guessed the other's secret code, then the winner is the player who required the fewest guesses to do so.

    * If neither player successfully guessed the other's secret code, and neither player abandoned the game, then the winner is based on the score for the final guess. Assuming that the length of the secret code is _N_, then the score for a guess is the sum of:

        * _N_ points for each character in the guess that is also in the secret code, and which is in the same position in the guess as it is in the secret code.
        
        * 1 point for each character in the guess that is also in the secret code, but which is not in the same position in the guess and the secret code.
        
        Note that it is possible for a game to end in a tie.

## Client component

* **Functionality**

    * The client app allows the user to see their personal win-loss-tie records, browse player rankings, browse the pool of players waiting for games, set game preferences, and join the pool of players.

    * When a round begins, the client app allows the codemaker to set (or generate) the secret code.

    * While a round is in progress, the client app allows the codebreaker to record guesses, sending each guess to the server.

    * While a game is in progress, both players will see the history of guesses and results in the current and completed (if applicable) rounds.

* *Device or external services used*

    * Google Sign In

## Server component

* **Functionality**

    A server component acts as a game coordinator, referee, and record-keeper: 

    * Matching up players waiting in the pool with newly arrived players.

    * Checking guesses for correctness and sending the results back to the codebreaker's client app. (Note that the code itself is not sent to the codebreaker client until the round is over.)

    * Determining the winner, based on the criteria stated above.

    * Maintaining a registry of game results.

    * Tabulating player rankings, based on wins, ties, and losses. These rankings are categorized by game difficulty (size of character pool, and length of secret code), for fairness in comparisons.

    * Responding to queries (of rankings, pool members, etc.) from the client app with the requested data.

* **Device or external services used**

    * Google OAuth 2.0 provider

## Possible enhancements

* Local (disconnected) solitaire mode: The app generates the secret code, and the user attempts to guess it.

* Smart match-ups: The server takes rankings and users' rank-range preferences into account when matching players from the pool.

* Pre-arranged games: Users are able to browse/search the user registry, view rankings and (limited) profile info, and challenge a selected player to a game; players challenged in this fashion may accept or decline challenges.

* SMS-, e-mail- and Android alert-based notications of player match and round changes.

## Links

* _Wikipedia.com_, "[Mastermind]" (<{{ page.links.mastermind }}>)

* _Wikipedia.com_, "[Bulls and Cows]" (<{{ page.links.bulls_and_cows }}>)


[Mastermind]: {{ page.links.mastermind }}

[Bulls and Cows]: {{ page.links.bulls_and_cows }}
