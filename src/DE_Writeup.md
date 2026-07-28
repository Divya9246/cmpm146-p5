# DE Mario Level Generator

This writeup covers the shared selection code and the Design Element (DE) portion of the project. A design element is one complete level feature, such as a hole, platform, enemy, coin, staircase, or pipe.

## Selection

I used elitism and tournament selection together.

Elitism copies the best 10% of the population directly into the next generation to prevent the best levels from being lost.

Tournament selection chooses three random individuals and selects the one with the highest fitness. I run one tournament for each parent, generate their children, and repeat until the new population is full. This keeps selection simple while still giving weaker individuals a chance to reproduce.

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
