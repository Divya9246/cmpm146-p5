# Evolving Mario Levels

## Partners and Contributions

Partners: Divya Machiraju and Jenny Wong

Divya Machiraju: I worked on the Grid encoding. I implemented Grid crossover, Grid mutation, Grid fitness, and better random initialization. I also ran Grid experiments and wrote the Grid part of this writeup.

Jenny Wong: Jenny worked on selection and the DE encoding. She implemented generate_successors() with elitism and tournament selection, improved DE fitness and mutation, fixed empty-genome crossover, ran DE experiments, and wrote the DE part of this writeup.

# DE Mario Level Generator

This writeup covers the shared selection code and the Design Element (DE) portion of the project. A design element is one complete level feature, such as a hole, platform, enemy, coin, staircase, or pipe.

## Selection

I used elitism and tournament selection together.

Elitism copies the best 10% of the population directly into the next generation to prevent the best levels from being lost.

Tournament selection chooses three random individuals and selects the one with the highest fitness. I run one tournament for each parent, generate their children, and repeat until the new population is full. This keeps selection simple while still giving weaker individuals a chance to reproduce.

## Grid Encoding

In the Grid encoding, each level is a full 16 by 200 grid of tiles. Each tile can be empty space, ground, a block, a coin, a pipe, an enemy, and so on.

## Grid Crossover

I used two-point column crossover. I pick two random cut columns and copy a full column range from the second parent into the child. The rest of the child comes from the first parent. I leave the first and last columns alone so Mario and the flag stay protected. I used columns instead of random tiles so structures stay more together.

## Grid Mutation

After crossover, I mutate the child. Each tile has a 5% chance of changing, except the first and last columns. I used weighted tile choices so empty space and ground are more common. Pipe tiles have weight 0 so mutation does not create floating pipes. On the floor, mutation only chooses ground or a hole.

## Grid Fitness

My goal was to make solvable levels that are not too empty or too flat, and that still have some decoration and jumps.

The main weights I used were:

```text
8.0 × solvability
+ 1.0 × meaningfulJumpVariance
+ 0.8 × decorationPercentage
+ 0.5 × jumps
+ 0.5 × pathPercentage
+ 0.4 × negativeSpace
- 0.6 × emptyPercentage
- 1.0 × linearity
```

Solvability is the strongest reward. Empty space and linearity are penalized.

## Grid Initialization
I changed random_individual() so it does not make completely random noise. It starts mostly empty with a solid ground row, then adds some blocks, coins, enemies, grounded pipes, and small holes. Mario and the flag are placed at the start and end.

## Grid Testing
I ran Grid with pop_limit = 24. Over about 87 generations, the best fitness went from about 18.6 to about 45.2. The best Grid level was solvable. It had emptyPercentage around 0.705 and decorationPercentage around 0.207.

## Favorite Grid Level
My selected Grid level is levels/grid_run1.txt, also saved as Machiraju_Wong.txt. It is solvable and has a mix of blocks, coins, enemies, pipes, and holes. It looks fuller than the DE levels from our longer test run.


## DE Crossover

The provided crossover chooses one random cut in each parent's list of design elements. The first child combines the beginning of Parent A with the end of Parent B. The second child combines the beginning of Parent B with the end of Parent A.

Because the cuts can be at different positions, the children can contain different types and numbers of design elements. I kept this crossover and only made its cut selection safe when a parent has an empty genome.

## DE Mutation

Each child has a 10% chance to mutate. When mutation happens:

- 60% of the time, one existing design element is modified.
- 20% of the time, one random design element is added.
- 20% of the time, one design element is removed.

Modification changes one property, such as position, width, height, material, stair direction, or power-up status. Enemy modification moves the enemy horizontally. All numeric changes remain inside the level boundaries.

This mutation can adjust existing features and also change the number of features in a level.

## Fitness

My goal was to generate solvable levels with meaningful jumps and a modest amount of decoration.

The fitness score is:

```text
10.0 × solvability
+ 1.0 × meaningful jumps
+ 5.0 × decoration percentage
- 0.5 × linearity
- 0.5 for each staircase above five
```

Solvability receives the largest fixed reward. Meaningful jumps reward useful obstacles, decoration rewards visual features, and the two penalties discourage flat or staircase-heavy levels.

## Testing

I checked that successor generation preserves the population size for both Grid and DE individuals. I also checked empty-parent crossover, all three mutation actions, legal element positions, and 16-by-200 level rendering.

In a short DE run, the best fitness increased from 15.84 at generation 1 to 21.00 at generation 5. The evolutionary loop took about 25.6 seconds, plus 2.6 seconds for initial population scoring, for about 28.2 seconds total.

## Favorite Level

My selected level is `levels/favorite_DE.txt`. It is solvable according to the provided pathfinding metric and contains 11 meaningful jumps. It has gaps, pipes, platforms, stairs, rewards, and enemies without relying on only one feature type.

I selected it because it combines several recognizable Mario features while preserving a path to the goal. It was found at generation 5. The level was inspected as text and measured with the provided metrics, but it was not manually played in Unity during this test run.

## Comparison

Grid made denser levels with more decoration. DE made cleaner levels built from whole features like pipes, stairs, platforms, and gaps. Both encodings could make solvable levels with the same selection code. Jenny’s favorite_DE.txt looks cleaner and more structured, while the Grid level looks fuller and denser.

## Final Favorite Level

Our final favorite level is the Grid level: Machiraju_Wong.txt.

We chose it because it is solvable, had higher fitness in our Grid run, and has more decoration than the emptier DE test levels. We could not fully playtest in Unity on this Mac, so we used the provided metrics and looked at the level text files.