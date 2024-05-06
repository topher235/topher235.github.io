---
layout: default
categories: devlog
---

## Minor Changes
Anytime I dive into a new codebase, my first thought is to come up with something very simple I can change to set a baseline for the effort it might take to do something. It’s good practice diving into the file structure and writing in the new language (if it is new). My first few minor changes were

* Changing the starter pokemon options
* Skipping the Professor Birch introduction
* Setting the player’s default name and gender
* Turning on Exp Share for the whole team indefinitely

### Changing the Starter Pokemon

The very first update I attempted was changing the starter pokemon options. The 3 pokemon I settled on were Gengar, Vibrava, and Pichu. There is a community post about exactly where and how to do this – just a single file and you can swap out any of the starters with any Pokemon by specifying the correct value. After implementing a change, we test! I spun up the rebuilt game and tested each of the starters, quickly realizing Gengar was not a viable option. At level 5, Gengar has 3 available moves: Hypnosis, Spite, and Lick. Hypnosis and Spite are not damaging moves and Lick does not affect Zigzagoon (a normal type).

This was my first realization that Pokemon have predefined moves at each level which are not always “good” move sets. This led me down a rabbit hole to the file that contained the definition of moves for each pokemon from level 1 to 100. I swapped Spite for Tackle and then we were on our way to being able to defeat the Zigzagoon.

Ultimately, I ended up reversing these changes so as to not mess with the game’s balance too much. The fire, water, and grass type selection is a classic rock-paper-scissors formula that I wanted to preserve.

### Setting the Player’s Default Gender and Name

Changing the starter Pokemon options was a nice introduction to the file structure of the codebase and how to test a change, but there was still a lot to get used to with the codebase. Still mostly unfamiliar with how the codebase and programming language worked, I decided to move onto editing the default Player gender and name.

In my haste and being used to higher level programming languages, I defined the player’s name as a data type that didn’t exist in the one I was trying to learn. Slowing myself down and taking a step back, I looked at examples elsewhere to figure out the data types. The game defines a list of default names for each gender, and I took the lazy way out by removing all predefined options and replacing it with the single option I wanted.

Changing the default gender is a single variable change. Easy peasy.

### Skipping Professor Birch’s Introduction

We are both veteran Pokemon players, so I wanted to save us a few minutes by removing the Professor’s intro speech. This was the final simple modification I decided I would do before diving deeper. Thinking of the start of the game as a series of steps, skipping the speech was as simple as swapping that step for one that would set the player’s default gender and name.

### Exp Share

For those unfamiliar, when you battle, any pokemon that at least made an appearance will split the experience. With the exp share item, the pokemon holding it doesn’t have to make an appearance in order to receive a slice of that exp pie. In order to level a very weak pokemon, you’d start with it and then immediately switch to a stronger pokemon. Having the exp share saves time from having to constantly switch pokemon.

One of the most controversial takes I have about newer pokemon games is that I love that exp share is turned on for the whole team. My enjoyment of the games stems from the comforting gameplay. I have a busy life and I’d rather not spend a lot of time grinding to level up new catches. Luckily, someone has done most of the hard work to bring this feature to Pokemon Emerald. I took their work and modified it slightly: rather than an item that will turn exp share on and off for the whole team, I updated it to be on indefinitely.

## The Subplot

Before getting into the details of everything I changed or added, let’s talk about the minor subplot I added to the story. An NPC would leave the player hard to read letters through other NPCs in the game or just lying around. In my notes, I referred to this as an “encoded” letter, but in reality, there is no cipher to decode them – the contents were literally random letters and numbers.

Along the way, the player hears ramblings about this “friend” who is always looking for them, but always just misses them. There is no mention of how the player knows this NPC or what they are like. At one point, the player meets a friend of the NPC who can decipher the letter’s contents, but it takes some time, so the player needs to come back. Once the first letter has been deciphered, it contains a riddle leading to the special Ho-Oh and Lugia event.

After the player has defeated the Elite 4, they receive a call from the NPC’s friend telling them to travel around Route 119. Once passing in front of the waterfall in that route, a cutscene triggers causing the NPC to finally show up. That NPC takes the scribbled letters back and gives the player new letters.

Each letter is written by real friends and family of the player. Once the player reads each of these letters, the NPC will propose to the player. If they accept, the player receives the Diamond Ring item.

<img width="320" height="240" src="/assets/img/demo-diamond-ring.png" />

I had more complex plans for the deciphering mechanic, but after realizing how much time that would take, I cut back the deciphering to a single letter and shifted that to be a red herring. I’d hoped the player would assume each letter would need to be deciphered in the same manner, and would largely ignore the bulk of the letters, taking them in one at a time.

## Custom Letter Items and UI

By far, the most challenging feature I implemented was a custom item and accompanying UI. I began by using a community tutorial for adding a new UI. To learn what was happening, I implemented the tutorial in its original form and slowly made changes to various bits of the code. My subconscious train of thought was to look at a section of the code, predict what would happen if I changed it, and then test the change. From there I would either be happy with my prediction or it would leave me to figure out why it was wrong. Failure leads to the most growth.

During one of my first attempts at testing this, I predicted that the variables named “height” and “width” would affect the font size. It turned out that those were the height and width of the screen

Once I had figured out how the UI code worked, my next challenge was to work it into my letter item workflow. I updated the custom Letter item to render that UI, and now I could display the letter’s contents on the screen. Since GameBoy screens are tiny, the contents needed to be separated into multiple pages. I updated the UI code to handle user input from the left and right buttons to redraw the UI with the appropriate page’s contents. This would make it appear as though the user were scrolling between pages. After implementing pages, I added a safeguard to ensure the user could not go beyond the first or last pages.

While searching for something else entirely, I stumbled onto the graphics for the actual mail items. I’m not much of an artist, so instead of spending the time learning or hiring someone, I used these existing mail item graphics as the background of the letter UI. Those graphics already had a nice color palette and lines that immediately looked good with the contents.

<video width="320" height="240" controls>
  <source src="/assets/img/demo-letter-item.mov" type="video/mp4">
</video>

## Custom Trainer Battles

This whole project was a constant battle between what I wanted to do versus what I could achieve in a reasonable amount of time. I had many ideas about custom trainer battles I could implement, from one-off battles to re-challenging to a new gym-like area with many custom trainers. In the end, I settled on several one-off battles that would feature a trainer with a unique personality, Pokemon to fit that personality, and an on-theme reward.

It didn’t feel unique enough to just add a vanilla trainer with some pokemon and a reward. I wanted it to present a challenge to the player, so I created a new trainer type whose team level would be dynamically calculated. In general, a trainer’s Pokemon are a set level no matter where you are in the game. So, if you wait long enough, a challenging battle will become easy, since you’ll eventually out-level them. My custom trainer type would feature a team whose levels were dependent on the player’s Pokemon’s levels.

The actual algorithm was very simple:

1. Calculate the average level of the player’s team
2. Determine a +/- level range for the opponent’s team
3. Calculate a random number within the level range for each of the opponent’s Pokemon

I purposefully left an exploit here. Let’s do some calculations. If the player’s team consisted of 1 level 100 and 1 level 1 Pokemon, then the trainer’s team would be around level 50. If the player’s team consisted of 1 level 100 and 5 level 1 Pokemon, then the trainer’s team would be around level 18. By creating a large level deviation and stacking your team with low level Pokemon, you can cause the opponent’s team to be lower level as well, leading to an easier fight.

In addition to the actual trainers, I also modified various NPC’s speech in the story to talk about these new trainer’s. This helped to place the new trainers into the world without feeling like they were dropped in and were out of place.

The following were my custom trainers:

**Elio:** An old man battling youngsters, teaching them to reach their maximum potential

  * **Pokemon team:** Slakoth, Makuhita, and Lotad
  * **Rewards:** 2x of Max Elixir, Max Ether, Max Potion, and Max Revive

**Faun:** a young girl who is very egotistical about her beauty

  * **Pokemon team:** Beautifly, Nuzleaf, Delcatty, Gardevoir, and Roselia
  * **Rewards:** Level 20 Milotic

**Asdef:** A scientist who has been researching national Pokemon

  * **Pokemon team:** Machamp, Alakazam, Charizard, Gyrados, and Pidgeotto
  * **Rewards:** Level 35 Mewtwo

<video width="320" height="240" controls>
  <source src="/assets/img/demo-dynamic-trainer.mov" type="video/mp4">
</video>

## Easter Eggs

Some of the most creative decisions I got to make were around the easter eggs.

If the player was savvy enough to check their PC item storage, they would find 10 Rare Candies, allowing them free levels whenever they choose. 

I added an NPC whose dialogue is about incense and would offer the player some incense. The player would receive the Sea Incense and Lax Incense items.

I created an 8-bit illustration of a redbull can and changed the PP Up icon to it

<img width="320" height="240" src="/assets/img/demo-pp-up.png" />

At the beginning of our relationship, Sarah and I had a date at a restaurant during their late night happy hour. Having just worked out, we both had eyes that were larger than our stomach’s. We each got a small pizza and Sarah ordered an additional plate of nachos. This might have been the largest plate of nachos to grace the planet, so of course, she barely made a dent in it. To call back to this, I added 2 NPCs who would offer the player a plate of leftover nachos. The player would receive the Leftovers item and I created a new icon for the Leftovers item, which was a plate of nachos.

<video width="320" height="240" controls>
  <source src="/assets/img/demo-leftovers.mov" type="video/mp4">
</video>

## Testing
Coming from a job and a philosophy that values automated tests, testing my changes to the game was… annoying, to say the least. The only way to test the game is to play the game. The best system I came up with was to create a new NPC in the starting town that could teleport me around the map. I could turn on or off certain checks the game would perform that would tell the game I had or had not completed something. This allowed me to go right to the section I needed to test without needing to play the game up to that point.

From what little research I did regarding testing options, there are debug tools to help view variable and flag values, but each of them seemed like more trouble to set up than worth it. For larger or more complex changes, I imagine those tools are very useful. In the grand scheme of changes, what I was doing was fairly simple and small.

<video width="320" height="240" controls>
  <source src="/assets/img/demo-testing-npc.mov" type="video/mp4">
</video>

## Creating a Cutscene
From a user perspective, if done well, not many features can make a game feel more immersive than a cutscene. To help immerse the player in the subplot elements, I implemented 3 new cutscenes.

1. “Mysterious” NPC leaves his friend’s house.
2. Mom gives the player a Mystic Ticket.
3. The proposal.

The game has a camera character that occupies the same space as the player. In order to create a cutscene, I needed to halt player movement, tell the camera character the steps to take, and then tell the camera character what steps to take to get back to the player.

Choreographing these cutscenes took a lot of work and testing. In many cases, there were multiple coordinates a player could occupy, which would affect where the camera’s character needed to walk in order for the cutscene to appear centered. Mom’s cutscene was the easiest to direct as there was no off-screen movement. 

* The player walks into the house
* Mom walks to the player
* The player faces Mom
* Display a textbox for Mom’s dialogue
* Give the player the Mystic Ticket
* Mom walks back to her original position

<video width="320" height="240" controls>
  <source src="/assets/img/demo-mom-cutscene.mov" type="video/mp4">
</video>

