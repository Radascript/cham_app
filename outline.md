# As a game: Cham App Front End

Vue.js? Swift/Objective-C/Unity?

## View 1: Landing

### Elements:

- Name: text field entry. 3-20 characters. Must be unique if joining existing
- Create new game button
- Enter code for existing game (4 char limit)
- Join button

### Logic: 

Create game:

1. Check name params
2. If ok, Create new game
3. Assign unique code
4. Join user in
5. Take to View 2.

Join game:

1. Search for game
2. If found, Check that there are <10 players
3. If ok, check unique name
4. If ok, join user in
5. Take to View 2.

## View 2: Waiting room

### Elements

- List of names of people in the game
- START button. Lights up after 4 users are in
- CANCEL button

### Logic: 

Start timer. Delete game after X min on this screen?

- Update as users join
- Light up START button when 4th user joins
- When CANCEL is clicked: delete game, take to View 1
- When START is clicked, take to View 3

## View 3: Active Game

### Elements:

Everyone sees:

- Category
- List of names and the clues they gave

Non-chameleons see: 

- Secret word

### Logic: 

Start timer. Delete game after X min on this screen?

1. Randomizes order, chameleon, moderator
2. Moderator picks category, picks word
3. Displays category for everyone. Displays word or "chameleon" as needed
4. Promps users in order to give clues
5. Opens chat up for 60s discussion??
6. Opens voting
7. Shows results
8. If chameleon not caught, game is over. If cham is caught, prompt cham to guess
9. Prompt moderator to judge if cham guess is correct (eg slightly diff spelling)
10. Game is over, move to Done screen


## View 4: Post Mortem

### Elements:

- Summary results of last game
- View1 options, with prefilled Name

### Logic: 

Same as Screen 1, but also:
Archive game after 30min on this screen

# As a game: Cham App Back End:

## MongoDB documents?
in JSON format

```
active:
    game_id: 8499304, #UUID, id field
    game_code: 'KSSL' # unique at the time but may repeat
    game_state:
        {
            users: {1: "Gill", 2: "Jerrod"},
            chameleon: 1, # id of chameleon
            mederator: 4, # id of moderator
            category: 'movies', # str if selected, NULL if not
            secret: 'Pulp Fiction', # str if selected, NULL if not
            clues: {1: "pounder", 2: NULL}, # str if given, NULL if not
            guess: 'Pulp fiction', # str if given, NULL if not
            guess_correct: TRUE, # TRUE if moderator selected true, FALSE if not, NULL otherwise
            cham_chosen: 2, # int if chosen, NULL otherwise
            cham_chosen_correct: TRUE, 
            winner: 'cham' # 'cham', 'team', NONE
        }
```

archive: same as above, but archived

# As a bot: 

## Full list of commands:
- `!cham-in `or `!cham-in @tag` or `!cham-in @tag @tag2 @tag3` (by anyone, adds players to pool)
- `!cham-start `(by anyone, starts game, assigns moderator)
- `!cham-secret category/word `(by moderator, stores category, word, sends message category, sends DMs)
- `!cham-clue` (by each player, records clue for the player. if moderator sends it ignore)
- `!cham-vote `(by each player, records vote. if moderator sends it ignore)
- `!cham-guess` (by chameleon, records guess. if non-chameleon sends it ignore)
- `!cham-abort `(by anyone, cancels the current game)

## Sample flow: 

`!cham-in`

`!cham-start`

Moderator is assigned.
**chambot**: Jerrod is the moderator! Jerrod, please submit the category and word like so: !cham-secret=category/word. Eg !cham-secret=movies/Once Upon a Time in Hollywood

Moderator submits category and word like so:
`!cham-secret=category/word`
**chambot**: The category is Movies!
**chambot** will DM each player category/word or "you are chamelion!"
**chambot** will go:
**chambot**: the order is: Gill, Bruno, Megan, Rada!

Players submit by saying:
`!cham-clue Murder`
when all submitted, chambot will go:

Please discuss, and after Xmin or when you're ready vote with !cham-vote @tag. Eg, !cham-vote @Jerrod
!cham-vote @Jerrod

**chambot**: You voted for Bruno! Bruno is not the chameleon. Congratulations chameleon Gill! Game is over. Secret word was Once Upon a Time in Hollywood. XXXXXXXXXXXX
or
**chambot**: You voted for Bruno! Bruno is the chameleon. Bruno, please take a guess at secret word with !cham-guess Guess

`!cham-guess` Aliens
**chambot**: You guessed Aliens. The secret word was Once Upon a Time in Hollywood. Game is over! XXXXXXXXXXXX
The rest is manual just because typos etc.