# egaetan Code4Life PostMortem

I will describe here my heuristic AI which finished 3 at the Code 4 Life competition on [codingame.com](https://www.codingame.com/home).

[A replay](https://www.codingame.com/replay/228368051?utm_source=twitter&utm_medium=replay&utm_content=1341520-228368051&utm_campaign=Share%20options)

## Methodology
I chose Java at first because it is the language I know best. I usually rewrite my code in C if needed during the contest, but this time I rewrote it in Java, in order to write more readable code.

I didn't use locale arena, wanted to in order to fine tune some magic numbers but not enough time... I should prepare one before the next contest... or maybe not.

I submitted quite a lot during the week, best way of receiving feedback of improvement or not. But the ranking itself was not enough to keep or drop an evolution. So I watched carefully lots of my matches, to know if the AI did exactly what I expected it to do or not and more to find out what are the consequences of a new behavior.


## Choice of Algorithm
I didn't write an evolutionary algorithm neither a minimax and try to mimic the way I would play to this game.

My first versions with heuristics were very competitive, I wasn’t very happy with my code but continue to improve it step by step.
When times come to clean things up, I had to choose what algorithm to choose for the ends of the contest. 

The branching factor is not very large, but the real reward of an action could arrive very late. The simultaneous play doesn't help me to try a sort of minimax either. 

I love to play board game in general, and thought I can analyze what should I do in this special case easier than tweaking an evaluation function.

So I choose to keep a heuristic. It implies I will write a lot of if/sort/filter/etc... So I keep Java and the stream API to have the most natural way of converting my mind into code.

## Code

I wrote several classes extending a basic Bag (primitive obsession hater)
* Bag (set of molecules)
* Carry (molecules carried by a player)
* Expertise (self-explanatory)
* Sample (-)

This helps a lot in having functions signature un-ambiguous.

My bot speaks a lot, it helps when watching replays to undersantd quickly where his decision came from.

## Heuristics

In this game, the small advantage you have at a time can very quickly disappear. The principal goal is to always complete the highest sample without being blocked at anytime.

### Rank of samples to pick
I didn't succeed to have some nice decision tree here. (A local arena could have help maybe)
Some magic numbers (12/18) and some salt to avoid picking rank3 when all you have is expertise in A and E.


### Cloud
I try to take sample in the cloud first; I filter the sample that can be done from the impossible one.
I prefer the samples that contribute to projects, then the more health.
I drop everything I can’t do in the current turn.


### Molecule Picking
One of the capital points.
I compute for all samples I carry, a picking order. It is a list of Samples ordering, each one associated with the required amount of molecule to be completed.

I try to block the opponent. I compute depth2 if I can block all of his samples, if yes then go.
I then try to reduce the number of samples he can complete, following more or less the same rule but at depth1 only.
If no blocking strategy, I will try to complete my samples. 
I added a special condition to fulfill the biggest sample when the opponent was away from molecules.

I mainly choose to pick first molecules from the color I need the most.

### Anticipation
One of the biggest improvements in leaderboard ranking comes from anticipating. 
I choose to pick more molecules than needed when I leave the molecules module.
I here prefer to take the molecules opponent will need.


### Laboratory
I wait here, if the opponent is blocked and my points ahead, until he dropped all of his samples.

### From a module to another
I choose amost always to go from laboratory to samples even if I have got a sample with me.
The second biggest fail in my heuristics after the rank of samples is here :
I tried a lot to go from laboratory to molecules when I don't have any sample in order to anticipate even more.
I could not describe enough the conditions to switch from Lab->Sample to Lab->Molecules
I count the turn before the end to try to mark a last one if possible

### Dropping undiagnsotiqued
I tried a little bit to drop undagnostiqued sample at the end. In order to have some success, I should carry lot more molecules than what I usually do and see above for a fail.


### Statistics
Each turn I computed some statistics on the remaining unknown samples... 
It only helps me not to use it. I could not find really useful data in.

## Java8 Stream
I wrote some tests that I execute at turn 0 in order to consume the cost of Streams API.


## Conclusion

In the end, I had around 2,500 lines of Java, 280 if/then/else, +100 call to stream() and still 23 TODO to complete. There was some unused or obsolete code for sure, but it is still a huge mass of code for a week of contest. Most of it could be reduced, I did write 5 different lines for each color until late and didn't rewrite lot of them. The code itself outside two or three complex functions is simple and could almost be naturally read.

I found very nice the game rules, and the change in mid contest was not that bad. The chat and people there, I kept it open as much as I could, maybe to much ? I didn't organize a codinghub this time, I will for sure for next context.
I thought at the first sight, I will try some machine learning, it seems to me a genetic programming could generate a pretty good decision tree for such a problem, but I do not have enough time to try it, without either knowing if such an approach could generate a solution in a week. I would love to try it... in a multi ?
