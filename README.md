# Recursive-Hexadoku-solver
Sudoku solver works for a 9 X 9 Sudoku. It is using a recursive backtrack algorithm
The algorithm uses a brute force method to solve the problem. The program displays the solution in an Applet. The code can
be found in the HexadoSolver.java file.

Development environment: 
JDK1.8 + eclipse4.4.2 + IE 11

Algorithm:
Because the Sudoku is a NP complete problem, the recursive backtrack algorithm is a common method to solve this problem. 
The core of the algorithm is an exhaustive search of all possible results until it finds a solution.
Note that the number of solutions may not be unique, and the running time of the algorithm will increase as the increase of the dimension of the Sudoku
