# On the Critical Density of Minesweeper Boards

This repository contains all code files for the Minesweeper solver used in the paper mentioned in the title above.

## Technical Information

All code is written in Python 3, and is compatible with the latest version of Python 3 (3.11.4 at the time of writing).

The libraries/modules used are documented in [`requirements.txt`](requirements.txt), which include:
- `random`, `math`, `itertools`, `copy` (built-in Python modules)
- `bidict`
- `python-sat` 
- `networkx` 

## Usage
### Installation
Clone this repository via HTTPS
```
git clone https://github.com/fwyr/minesweeping.git
```
or via SSH
```
git clone git@github.com:fwyr/minesweeping.git
```

or via installing the ZIP folder.

Change your working directory to the root directory of the project.
```
cd minesweeping
```

### Execution
The solver is run by executing the `main.py` program using Python 3.
```
python3 main.py
```

Variables to do with board generation and the number of games played can be modified in [`main.py`](main.py)  — namely `games_to_play`, `rows`, `cols`, and `mines`.


## Solver 

The solver functions on 2 methods — (1) pure logic using an SAT solver, and (2) guessing using probability of safety and progress for unknown cells.

The logic of the solver is dependent on the Minisat22 SAT solver, an open-source SAT solver that operates under the MIT license ([minisat.se](http://minisat.se/)), and implemented using the `python-sat` module ([PySAT](https://pysathq.github.io/)). Clausal formulas extracted from the board are represented in conjunctive normal form (CNF), and "fed" into the Minisat22 solver to obtain solved cells, information which is continually used to advance forward in the game as more and more cells are opened.

The guessing engine of the solver is only used when the SAT solver is unable to solve any cell present on the board. The solver makes use of probability-based guessing by calculating two values for each uncovered cell: safety and probability. 

For safety, a combinatorial argument is used. For unopened cells adjacent to at least 1 opened cell, the probability that such a cell is "safe" is calculated by the sum of the values of all opened neighbouring cells divided by the number of opened neighbouring cells. For example, consider an unopened cell, which has 2 neighbouring cells, A and B. If the sum of cell values of A and B is equal to 1, then the safety probability of the unopened cell is 1/2. This calculation remains the same for unopened cells that are not adjacent any opened cells.

For probability, if the considered cell is adjacent to at least 1 opened cell, brute force is used to check which mine numbers will lead to progress. The solver iterates through a range of possible cell values, and checks for the number of values in which the SAT solver will be able to progress. The progress probability is then given by the number of scenarios with progress divided by the total number of scenarios.

If the considered cell is not adjacent to any opened cells, the progress probability is given by:

$${\text{unopened cells} - 1 - \text{adjacent cells} \choose {\text{remaining mines}}} \div {\text{unopened cells} - 1 \choose {\text{remaining mines}}}$$

## License
This repository is licensed under the MIT license. See [`LICENSE`](LICENSE) for more information.

