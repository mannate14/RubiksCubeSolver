# Rubik's Cube Solver

This project is a C++ implementation of a Rubik's Cube solver that explores multiple cube representations and several classic and advanced solving algorithms. The codebase is modular, allowing benchmarking and comparison of different modeling and solving strategies.

## Features

- **Multiple Cube Representations:**
  - **3D Array Model:** Intuitive, easy-to-understand, and visualizes the cube as a 3D array.
  - **1D Array Model:** Flattens the cube into a single array for faster access and simpler indexing.
  - **Bitboard Model:** Highly efficient, uses bit manipulation for compact storage and fast operations.

- **Solving Algorithms Implemented:**
  - **Depth-First Search (DFS):** Explores all possible moves up to a certain depth.
  - **Breadth-First Search (BFS):** Finds the shortest solution by exploring all states level by level.
  - **Iterative Deepening DFS (IDDFS):** Combines the space efficiency of DFS with the optimality of BFS.
  - **Iterative Deepening A* (IDA*):** Uses a heuristic (Pattern Database) to guide the search, making it highly efficient for large state spaces.

- **Pattern Database Heuristic:**
  - Uses a precomputed database for the corner pieces to provide fast, accurate lower bounds for the number of moves to solve the cube.
  - Includes tools to generate and store the pattern database.

## Project Structure

- `Model/`
  - `RubiksCube3dArray.cpp`, `RubiksCube1dArray.cpp`, `RubiksCubeBitboard.cpp`: Different cube representations.
  - `RubiksCube.h`: Abstract base class for all cube models.
- `Solver/`
  - `DFSSolver.h`, `BFSSolver.h`, `IDDFSSolver.h`, `IDAstarSolver.h`: Implementations of the various solving algorithms.
- `PatternDatabases/`
  - `CornerPatternDatabase.*`, `CornerDBMaker.*`: Pattern database logic and tools.
- `main.cpp`: Entry point, contains example/test code for shuffling and solving the cube using different models and algorithms.

## How It Works

1. **Modeling the Cube:**  
   The cube can be instantiated in any of the three representations. Each model implements the same interface, so they can be used interchangeably with the solvers.

2. **Shuffling:**  
   The cube can be randomly shuffled using a sequence of moves.

3. **Solving:**  
   - Choose a solver (DFS, BFS, IDDFS, or IDA*).
   - Pass the cube (in any representation) to the solver.
   - The solver returns a sequence of moves to solve the cube.

4. **Pattern Database:**  
   - The IDA* solver uses a pattern database for the corners, which can be generated using the `CornerDBMaker` tool.
   - The database is stored in a file and loaded at runtime for fast heuristic lookups.

## Example Usage

```cpp
#include "Model/RubiksCubeBitboard.cpp"
#include "Solver/IDAstarSolver.h"

int main() {
    std::string dbFile = "Databases/cornerDepth5V1.txt";
    RubiksCubeBitboard cube;
    auto shuffleMoves = cube.randomShuffleCube(13);
    cube.print();

    IDAstarSolver<RubiksCubeBitboard, HashBitboard> solver(cube, dbFile);
    auto solutionMoves = solver.solve();

    solver.rubiksCube.print();
    for (auto move : solutionMoves) std::cout << cube.getMove(move) << " ";
    std::cout << std::endl;
}
```

## Algorithms: When and Why

- **DFS:** Good for small depths, but not optimal for Rubik's Cube due to exponential state space.
- **BFS:** Guarantees shortest solution but is memory-intensive.
- **IDDFS:** Balances memory and optimality, but can be slow for deep solutions.
- **IDA\* (Final/Best):**  
  - Uses a heuristic (Pattern Database) to prune the search space.
  - Achieves state-of-the-art performance for optimal solutions.
  - This is the primary algorithm used for the final solving step in this project.

## How to Build

1. Make sure you have CMake and a C++ compiler installed.
2. Run:
   ```sh
   mkdir build
   cd build
   cmake ..
   make
   ```
3. Run the executable:
   ```sh
   ./rubiks_cube_solver
   ```

## Notes

- The project is modular: you can easily swap out cube models and solvers.
- The pattern database file is required for IDA* and should be generated or downloaded before running the solver.
