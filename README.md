

# Things
![Alt Text](gif/Things.gif)


## Game Play
As a player you are a God of Things, you can 
  - be entertained by simply watching Things interact/evolve autonomously
  - directly interfere with the behaviour of Things, making decisions/actions on their behalf.
  - you can modify the environment in real time (raise mountains, rain-down meteors), causing casualties, famine, calamity, or provide for your things abundantly.
  - Your power is only limited by the Faith that your Things have in you. 
  - The game ends when all Things lose Faith in you.
  - Multiplayer/AI-Player options (i.e. other Gods).

## Evolutionary Environment:
 - Spatial [2D, 2.5D, 3D] + Time
 - Contains objects of class of Thing which have genetic/probabilistic attribiutes/behaviours, procreate and transform over generations given the environment, selection pressures, their/other behaviour and decisions they/others/the player, God, make.

## Class Thing
 - Characteristics/features/attributes of this thing as probabilities in the range [0.0,1.0]
 - Probabilities can also be randomly sampled to determine actions, or used as scalar parameters used in other functions/actions.
 - As God, the player can create their own Genotypes (effectively by adjusting means and standard deviations of the distribution of features with a slide bar [0<->1]). This doesnt produce deterministic Things, instead the phenotype of a Thing is randomly sampled given the genotype means and standard deviations (which can be set by the user).


### Genotype And Phenotype
All features expressed in the trait-phenotype are randomly sampled from the Genotype

The Genotype is simply a mean and standard for each feature. A thing inherits a 50/50 mix of their parents' genotypes plus some mutation.

### Features as Trait Dimenions (probabilities in the range [0.0,1.0]):
  - vengefulness - forgiving # i.e. tendency to counter attack/forgive if attacked
  - greedy/selfish/machiavellianism - sharing/caring/lovingkindness # i.e. tendency to keep or distribute a resource
  - aggression - kindness # amplifies/diminishes the probability of attack, increases the ferocity and duration
  - agreeableness # tendency to go along with the pack/clan, dampens leadership challenges
  - fearfulness - gregariousness # tendency to explore, tendency to view others/the enironment as threats
  - introversion - extroversion # tendency to seek others to interact with
  - unfriendliness - friendliness # tendency for two or more things to form a pack/clan. 
  - in_out_group_bias # tendency for a thing to see other things external to their pack/clan as threats
  - sex_drive # tendency to (consensually) procreate with another
  - desire_for_power # high desire for power will mean the thing tries to democratically or other wise rule a pack/clan
  - self_care # tendency to feed oneself/rest, has an upside of self-sacrifice group dynamics. 

# States:
_Current_ state parameters for the Thing, not the same as traits, which are phenotypes of a genome. Both Continuous [0.0,1.0] and Discrete/Categorical state types:
  - energy
  - hunger
  - thirst
  - love
  - fear
  - anger
  - current_motion_speed
  - movement_vector (unit vector[[0.0,1.0],[],[]])
  - happiness 
   - unhappiness could cause the thing to leave its family, friends, pack/clan. 
   - happiness/unhapiness is infectious, iteratively, 
   - my_happines_t1 = (0.1*group_mean_t0)+(0.9*my_happines_t0)
  - awake
  - eating
  - drinking
  - moving
  - position
  - attacking
  - mojo_toward[index_of_things] # [0.0,1.0] # continuous
  - is_friends_with[index_of_things] # [0,1] # boolean
  - is_communing_with[index_of_things] # [0,1] # boolean
  ...

# State Thresholds (basic mechanics)
When state levels reach above/below thresholds, actions are unlocked/triggered both deterministically and probabilistically.

#### Nomenclature:
 - any function with "sample_" is written to signal this behaviour will be probabilistically sampled.
 - any other function name is taken as being deterministic.
 - states can also be separately sampled to determine actions
 - or can also be used as a scalar (i.e. speed).

### Pseudo-Code Mechanics
```yaml
{ 
    "energy" : 
        {
            "<" : 
                    {
                        .1 : [sample_rest((1-0.1))],
                        .2 : [limit_speed(.2)],
                        .5 : [limit_speed(.5)]
                    },
            ">" : 
                    {
                        .5  : [sample_wake_up_if_asleep(.5)],
                        .75 : [sample_wake_up_if_asleep(.75)],
                        .9  : [wake_up_if_asleep()]
                    },
        },
    "hunger" : 
        {
            "<" : 
                    {
                        .001 : [die()],
                        .05 :  [cry_for_help()],
                        .1   : [find_food()],
                        .2   : [sample_find_food(1-0.2)],
                        .5   : [sample_find_food(1-0.5)],
                        .75  : [sample_find_food(1-0.75)]
                    },
            ">" : 
                    {
                        .5  : [increase_energy(0.5), sample_stop_eating_if_eating(.5)],
                        .75 : [increase_energy(0.75), sample_stop_eating_if_eating(.75)],
                        .80 : [increase_energy(0.80), sample_stop_eating_if_eating(.8)],
                        .90 : [increase_energy(0.90), sample_stop_eating_if_eating(.9), sample_increase_hapiness(.90)],
                        .99 : [increase_energy(0.99), sample_stop_eating_if_eating(.99)]
                    },
        },
    "thirst" : 
        {
            "<" : 
                    {
                        .001 : [die()],
                        .1   : [find_water()],
                        .2   : [sample_find_water(1-0.2)],
                        .5   : [sample_find_water(1-0.5)],
                        .75  : [sample_find_water(1-0.75)]
                    },
            ">" : 
                    {
                        ...
                    }
        },
    "love" : 
        {
            "<" : 
                    {
                        .001 : [die()],
                        .1   : [decrease_happiness()], # lovelessness leads to unhapiness
                        .2   : [sample_decrease_hapiness(1-.2)],
                        .2   : [sample_find_water(1-0.2)],
                        .5   : [sample_commune_with_other_thing(1-0.5)],
                        .75  : [sample_find_water(1-0.75)]
                    },
            ">" : 
                    {   
                        .5  : [sample_propose_friendship(.5)],
                        .85 : [sample_propose_sex(.85)],
                        .90 : [sample_propose_sex(.90)],
                        .95 : [sample_propose_sex(.95)]
                    }
        }

        # TODO, continue mechanics!

}

```

## More TODOS

### Actions:
e.g:
- thing_a.sample_give_to(thing_b, gift)
- thing_a.sample_reaction(attacked_by(thing_b))
- thing_a.sample_assist(thing_b, gift) etc..
...
#### Reactions
- thing_a.sample_reaction(attack_by(thing_b)) 
...

### Perception
Make perception capabilities heritable (genetic)
- field_of_view [0.0,1.0], where 1 is 360 degrees (fov centered at direction of tavel),
- depth_of_view [0.0,1.0], how far a Thing can see,
- sense_of_touch [0.0,1.0], how sensitive a thing is to contact with other things/objects. Increase happiness when socializing/having sex, but decrease when being attacked.
- sense_of_smell [0.0,1.0], important for detecting food sources/other Things. Bad smells -> unhappiness, good smells -> happiness.
- hearing [0.0,1.0], range of hearing.
- taste [0.0,1.0], tasty food increases happiness of a Thing.
...

### Cognition
- Assign to each thing a Contained LLM
- The LLM is both internal (internal dialogue/reflections given states) and external - Things talk to eachother!
- let the things talk/reason about their environment/interactions. 

### Memory
- Thing memory could be simply be the LLM context
- Thing memory is geo-spatial (locations of resources/things in the environment)