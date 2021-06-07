---
layout: default
---


[Home](./index.html)

_This project was mostly an exercise in developing an Object Oriented method for optimising a Colonel Blotto game strategy._

# Colonel Blotto

Blotto is a famous example of game theory applied to a complex problem. It is possible to solve for the mixed Nash equilibrium strategy for the game, however given a large number of castles:

1. Solving the problem is computationally very difficult.
2. If you can narrow your range of opponents, the Nash equilibrium strategy isn't the most profitable (i.e. a Nash equilibrium strategy is unexploitable and not unprofitable, but doesn't necessarily maximise).

Here's my solution to the problem, broken into bits:

## Import my favourite three packages

```python
import random as rd
import pandas as pd
import numpy as np

```

## Some settings which will come into play later

```python
game_castles = 10
game_players = 2
number_of_soldiers = 100
winner_stays_on_rounds = 10000
random_sims = 100
```

## Set up the Player and Castle class in order to initialise game objects and call methods

```python
#######################
### DEFINE A CASTLE ###
#######################

class Castle():
    number_of_castles = 0
    def __init__(self, points):
        Castle.increment_number_of_castles()
        self.points = points
        self.soldiers = []
        for i in range(game_players):
            self.soldiers.append(0)
    @classmethod
    def increment_number_of_castles(cls):
        cls.number_of_castles += 1
    def determine_scores(self):
        self.winners = [player for player, soldier in enumerate(self.soldiers) if soldier == max(self.soldiers)]
        self.points_per_winner = self.points / len(self.winners)
        self.scores = []
        for i in range(game_players):
            if i in self.winners:
                self.scores.append(self.points_per_winner)
            else:
                self.scores.append(0)
        return self.scores
        
#######################
### DEFINE A PLAYER ###
#######################

class Player():
    number_of_players = 0
    def __init__(self, name):
        Player.increment_number_of_players()
        self.name = name
        self.points = 0
    def calculate_score(self, castles):
        self.score = 0
        for castle in range(game_castles):
            self.score += castles[castle].scores[self.name - 1]
    @classmethod
    def increment_number_of_players(cls):
        cls.number_of_players +=1
        
##############################
### INITIALISE THE PLAYERS ###
##############################

def initialise_players(game_players = game_players):
    players = []
    for i in range(game_players):
        players.append(Player(name = i + 1))
    return players

##############################
### INITIALISE THE CASTLES ###
##############################

def initialise_castles(game_castles = game_castles):
    castles = []
    for i in range(game_castles):
        castles.append(Castle(points = i + 1))
    return castles
```

## Define a function to produce random n-player allocations

```python
#############################
### RANDOMISE ALLOCATIONS ###
#############################

def randomise_allocations(game_castles = game_castles, game_players = game_players, number_of_soldiers = number_of_soldiers):
    players = initialise_players(game_players = game_players)
    castles = initialise_castles(game_castles = game_castles)
    for player in players:
        for soldier in range(number_of_soldiers):
            castle = rd.randint(0, game_castles - 1)
            castles[castle].soldiers[player.name - 1] += 1
    return castles, players
           
def play_once(game_castles = game_castles, game_players = game_players, number_of_soldiers = number_of_soldiers): 
    castles, players = randomise_allocations()
    for i in range(game_players):
        print(f"Player {i+1}'s allocation is {[castle.soldiers[i] for castle in castles]}")
    for i in range(game_castles):
        castles[i].determine_scores()
    for i in range(game_players):
        players[i].calculate_score(castles)
    for i in range(game_players):
        print(f"Player {i+1}'s score is {players[i].score}")
```

## One way that you might approach the game is to optimise according to a winner stays on strategy


```python
#######################
### WINNER STAYS ON ###
#######################

def play_winner_stays_on(game_castles = game_castles, game_players = game_players, number_of_soldiers = number_of_soldiers, winner_stays_on_rounds = winner_stays_on_rounds):
    castles, players = randomise_allocations()
    for i in range(game_castles):
        castles[i].determine_scores()
    for i in range(game_players):
        players[i].calculate_score(castles)
    for rounds in range(winner_stays_on_rounds):
        winner_name = players[[players[i].score for i in range(game_players)].index(max([players[i].score for i in range(game_players)]))].name
        winning_allocation = [castle.soldiers[winner_name - 1] for castle in castles]
        castles, players = randomise_allocations()
        for castle in castles:
            castle.soldiers[winner_name - 1] = winning_allocation[castle.points - 1]
        for i in range(game_castles):
            castles[i].determine_scores()
        for i in range(game_players):
            players[i].calculate_score(castles)
        print(f"Round {rounds}")
        print(f"Winner is Player {winner_name}")
        print(f"Winning alocation is {winning_allocation}")
```

## A better way

An issue with the above 'winner-stays-on' methodology is that a static strategy that can beat the previous strategy has shown itself only to be better than one strategy (obviously). What you want is a strategy that beats lots of other strategies. For example, you might expect a strategy that allocates more soldiers to higher value castles to do better than a random strategy.

In addition to being able to beat random strategies, you want a strategy that can beat lots of strategies that you might expect to play. A fixed strategy is never dominant (i.e. you can always win against a strategy if you know what it is), and so any chosen fixed strategy must be optimised to do well against strategies that could come up.

The methodology defined below combines lists some random strategies with some strategies that have been chosen for this game by real clever people, and similar 'good' strategies (defined as real strategies that have been tinkered with a little bit).

```python
##########################
### RANDOM ALLOCATIONS ###
##########################

def generate_random_df(game_castles = game_castles, random_sims = random_sims):
    lst_columns = [f"Castle {i}" for i in range(1,game_castles + 1)] + ["Points"]
    df_random = pd.DataFrame(columns = lst_columns)
    for sim in range(random_sims):
        castles = randomise_allocations()[0]
        allocations = [castle.soldiers[0] for castle in castles]
        df_random = df_random.append(pd.DataFrame([allocations + [np.nan]], columns = lst_columns), ignore_index = True)
        #df_random = df_random.append(pd.DataFrame([castles + [np.nan]], columns = lst_columns), ignore_index = True)
        print(f"Sim {sim}")
    return df_random

df_random = generate_random_df()

########################
### GOOD ALLOCATIONS ###
########################

def generate_good_df(path = r"PATH\good_allocations.csv", game_castles = game_castles):
    lst_columns = [f"Castle {i}" for i in range(1,game_castles + 1)] + ["Points"]
    df_good = pd.read_csv(path, header = None)
    df_good[game_castles] = np.nan
    df_good.columns = lst_columns
    return df_good

df_good = generate_good_df()

##############
### POINTS ###
##############

def play_protagonist_v_antagonist(df_protagonist, df_antagonist):
    if len(df_protagonist.columns) != len(df_antagonist.columns):
        print("Players aren\'t playing the same number of castles!")
        pass
    game_count = 0
    total_games = len(df_protagonist.index) * len(df_antagonist.index)
    game_players = 2
    game_castles = len(df_protagonist.columns) - 1
    for protagonist in df_protagonist.index:
        players = initialise_players()
        points = 0
        for antagonist in df_antagonist.index:
            castles = initialise_castles()
            for castle in range(game_castles):
                castles[castle].soldiers = df_protagonist.loc[protagonist, df_protagonist.columns[castle]], df_antagonist.loc[antagonist, df_antagonist.columns[castle]]
                castles[castle].determine_scores()
            players[0].calculate_score(castles)
            points += players[0].score
            game_count += 1
        df_protagonist.loc[protagonist, df_protagonist.columns[-1]] = points / len(df_antagonist.index)
        print(f"Played {game_count} games of {total_games}")
    df_protagonist.sort_values(df_protagonist.columns[-1], ascending = False, inplace = True)
    return df_protagonist

#############################
### CHANGING THINGS A BIT ###
#############################

def shuffle_things_a_bit(lst_allocation, moves):
    lst_positions = [item for sublist in [[i] * int(lst_allocation[i]) for i in range(len(lst_allocation))] for item in sublist]
    for move in range(moves):
        lst_positions[rd.randint(0,len(lst_positions) - 1)] = rd.randint(0,len(lst_allocation) - 1)
    lst_reallocation = [lst_positions.count(i) for i in range(len(lst_allocation))]
    return lst_reallocation

def augment_df_good(df_good, moves):
    for row in df_good.index:
        df_good.loc[row,df_good.columns[:-1]] = shuffle_things_a_bit(list(df_good.loc[row,df_good.columns[:-1]]),moves)
    return df_good


##################################
### ADD SOME SHUFFLED VARIANTS ###
##################################

df_good = df_good.append(augment_df_good(df_good, 2), ignore_index = True)
df_good = df_good.append(augment_df_good(df_good, 10), ignore_index = True)
df_good = df_good.append(augment_df_good(df_good, 10), ignore_index = True)

df_protagonist = play_protagonist_v_antagonist(df_random.append(df_good, ignore_index = True), df_good)
```

This code outputs a dataframe ordered by average number of points scored against the list of 'good' and 'similar-to-good' strategies.

<p> <br /> <br /> <br /> <br /><br /> <br /><br /> </p>

* * *

<p align="center"> <i> Run, Pheidippides, one race more! The meed is thy due! </i> </p>
<p align="center"> <img src="https://pheidippidesbane.github.io/Projects/images/android-chrome-256x256.png" alt="Antlers"> </p>
