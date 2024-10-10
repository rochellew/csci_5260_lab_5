# CSCI 5260 – Lab 5 
#### n-Queens Problem
## 1.	Code Exploration
The code begins by importing some modules (`os`, `random`, `sys`, and `time`) which will all be used in the functions of the program (except `random`). 

Then, there are several variables that are initialized, including the refresh rate (referenced by `RATE`), the command to clear the screen (referenced by `CLEAR`), an empty dictionary showing the location of each queen on the board (referenced by `queens`), and the size of the board which will come from the command that executes the program (referenced by `size`). 

The board is then initialized based on the size the user entered (e.g., if the command to run the program is `python n_queens.py 4`, the board will be set up as a 4x4 grid.

There are three functions provided in the code, as well as one that I have added. Their descriptions are below.

* **`display_queens`** (I added this for readability in output): for every row, set the text to an empty string (`" "`). Then, for every column in that row, if the row and column in the `queens` dictionary (the key and value, respectively) are the same, set the text to `"Q "` (whitespace included here for readability); if not, set the text to `". "` (whitespace included here for readability).
* **`display`**: for every row, set the line text to an empty string (`" "`). Then, for every column in that row, set the cell text to the value of the board parameter variable's value at that row and column. The cell text is then added to the line text, followed by a white space, and the line output to the terminal.
* **`addToThreats`**: This function will update the “threat level” of the positions on the board in response to a placed queen. Since the queen piece threatens in 8 directions, each of them would need to be checked; however, this program places the queens row-by-row, thereby eliminating the need to check left and right (the horizontal threat). The `change` parameter will add one to the threat level, incrementing the marked cells' threats by 1 when `addToThreats` is called with a `change` value of 1. When the queens are removed and `addToThreats` is called with a `change` value of -1, the threat level is decremented.
    * **Vertical Threats**: The first `for` loop will mark all positions above the placed queen in her column as threatened. The second `for` loop marks all positions above the placed queen in her column as threatened. 
    * **Diagonal Threats**: 
      * **Down-right**: The first nested `if` statement inside the second `for` loop will mark all cells in the down-right diagonal (the backtracking handles the up-right diagonal, as when a queen is added to a row, the downward diagonals are marked again) as threatened. As row increases by 1 (`j` is the increment variable), and the column increases by 1 (`col+(j-row)`), the down-right diagonal cells are marked as threatened.
      * **Down-left**: The second nested `if` statement inside the second `for` loop will mark all cells in the down-left diagonal (again, the backtracking handles the up-left diagonal) as threatened. As row increases by 1 (`j` is the increment variable), and the column decreases by 1 (`col-(j-row)`), the down-left diagonal cells are marked as threatened.
* **`backtrack_search`**: This recursive function will determine whether each row is home to a queen that is NOT in a threatened cell (shown by 0 via the default `display` function, and by 'Q' in my `display_queens` function). 
    * `if row == size`:
      * The `if` statement checks if the rows are all populated with queens. If they are, that means a solution was found, and the board is displayed.
      * If this is not true, that means that the queens have not all been placed. A queen is then placed in the current row, iterating through the columns for whether the position to place her has no threat level. If there is no threat level in that cell, the queen is placed, and the `queens` dictionary is updated, and the board is displayed. Then, it will recursively try to place a queen in the next row.
        * Backtracking occurs now. This is where the change value goes from 1 to -1, decrementing the threat level as the queen is removed. Assuming the backtracking occurs, the program will check the next column and attempt to place a queen.
        * This happens until each row is populated, and `True` is returned, ending the backtracking.

The last thing that happens is a call to `backtrack_search` in the `if not backtrack_search():` statement. This will attempt to do the search for the solution to the puzzle, printing “NO SOLUTION!” if no solution could be found.

## 2.	Defining the CSP
* **Variables**: The rows in the game board are the variables in this CSP. 
    * {row1, row2, …, row~n~}
    * E.g., If 4 is used as the board size (resulting in a 4x4 grid), there would be 4 variables.
* **Domain**: The domain in this CSP is what values are possible for each of the variables. For each one of the rows, there can either is a queen or there is no queen.
    * {{queen, no_queen} …, {queen, no_queen}}.
* **Constraints**: The constraints in this CSP are based around the queens not being able to harm each other, and the rule is rooted in chess rules. 
    * Queens can move in 8 directions, so the queens must be placed in such a way that they are not in the 8 compass directions of each other (N, NE, E, SE, S, SW, W, NW). 

## 3.	Constraint Propagation & Un-propagation
In the n-Queens problem, and in this code specifically constraints are enforced (I think this is what propagation means) through the `addToThreats` function. Any cell that would violate that constraint is marked as a threat, and a queen cannot be placed there permanently. 

Un-propagation, or undoing the propagation strategy, happens when `addToThreats` is called with the `change` value in that function call going from 1 to -1. That will decrement the threat level when the `backtrack_search` function does its recursive search for the solution. 

This is necessary in this problem, as the threat level would only ever increment if the cells marked as threats never were marked as safe when the queens are moved around. This would result in no solution being found, as the queens would have nowhere to go on the board.

## 4.	Consistency Explanation
* **Node Consistency**: This checks that a variable meets any constraints placed on itself. 
    * If we had constraints for each of the queens to follow, it would check her domain for each value, and if both values meet the constraint, she would be labeled as node consistent.  The n-Queens problem is predicated on the relationship between more than one queen, so I do not think this is relevant for this problem.
* **Arc Consistency**:  This checks that the constraint on two variables is met and is stronger than node consistency.
    * For a given queen in a row, can a queen in a subsequent row meet her constraints? The domain values for this problem indicate that the subsequent-row-queen can meet the constraints of the previous-row-queen by simply not being in a threatening direction. This seems to make the most sense for a consistency check for the n-Queens problem.
* **Path Consistency**: This checks that the constraint of three variables is met and is the strongest consistency described here. 
    * Instead of checking if two queens are safe from each other, path consistency would check three queens at a time, adding to the complexity of the backtracking search. I think that if the board were larger and there were more rows (variables) that it could be useful, but it seems like overkill.

## 5.	Arc Consistency in the Code
I do not think this code is maintaining arc consistency. There really isn’t any pruning of invalid placements happening. The program does not check the next row for the queen placements, instead placing the queen and backtracking if a future queen causes problems. An arc consistent version of this code would never place a queen in a subsequent row that would cause an immediate backtrack. 

To implement arc consistency in the code, you would need  to remove all threatening positions in subsequent rows before placing the queen after checking if there is a valid place to put her and backtrack if there is no valid place.   


