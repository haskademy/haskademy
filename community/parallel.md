# Parallel and Concurrent Programming in Haskell

This file contains questions and exercises.

To download the sample code, run:

```bash
$ stack unpack parconc-examples
```

in a directory.

## Week 1: Chapter 1-2

### Questions:

1. What's the difference between parallelism and concurrency?
2. What is determinism?
3. What is a drawback to parallel computation?
5. What are some ways to separate work for parallel computations? What are their tradeoffs?
6. What are sparks? What does it mean for a spark to be GC'd or fizzled?
7. What is NFData? Why do we have a class for it?
8. What does WHNF mean? What does NF mean?

### Exercises:

#### Laziness

Consider the following code block:

```haskell
take :: Int -> [a] -> [a]
take 0 _      = []
take _ []     = []
take n (x:xs) = x : take (n-1) xs

ints :: Int -> [Int]
ints x = x : ints (x+1)

map :: (a -> b) -> [a] -> [b]
map _ []     = []
map f (x:xs) = f x : map f xs

expr :: [Int]
expr = take 3 (map (+1) (ints 0))
```

1. What does `expr` look like in WHNF?
2. What does `expr` look like in NF?
3. Write out the evaluation steps that Haskell follows to evaluate `expr` to normal form. Remember that pattern matching forces evaluation. The first steps are:
    1. `take 3 (map (+1) (ints 0))`: outermost expression is `take 3 xs`, which causes a pattern match on `map (+1) (ints 0)`, which then does a pattern match on `ints 0`.
    2. `take 3 (map (+1) (0 : ints 1))` we evaluate `ints` to "weak head normal form." so it has a single `:`. `map` is now able to pattern match on that `:`.
    3. `take 3 ((0+1) : map (+1) (ints 1))`. Since `map` could pattern match on the head, it can be evaluated to weak head normal form.
    4. You've got the rest :)

#### Performance

This exercise will have you work through setting up a `stack` project and solving some common dependency issues.
We'll also get familiar with `criterion`, a framework for benchmarking performance.

1. Create a `stack` project for testing performance using the command:
    ```bash
    $ stack new parconc-bench
    ```
2. Copy the contents of `sudoku1.hs` file from the `parconc-examples` into `parconc-bench/app/Main.hs`
3. Copy the `Sudoku.hs` file from `parconc-examples` into `parconc-bench/src/Sudoku.hs`
4. Get the project building. This will mostly involve editing the `package.yaml` file that `stack` generates to have the right dependencies listed.
5. Add a `benchmark` in the `package.yaml` and [write benchmarks for the various parallelism strategies from the book](http://www.serpentine.com/criterion/tutorial.html)
6. One of the RTS flags is `+RTS -N $NUMBER_OF_CORES -RTS`. The number passed to `-N` tells the run time system how many hardware cores to run on. Experiment with this setting to see what gives you the best performance.

## Week 2: Chapter 3


