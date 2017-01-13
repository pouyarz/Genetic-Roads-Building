Readme for map_makerV3.py
To use this program one needs to have python 3.5 installed.
https://www.python.org/downloads/
the following .txt files need to be in the same directory as the program:
	cost_poly.txt
	map.txt
	organism.txt
	cycle.txt
To run this program either double click it or open it with the IDLE integrated development environment and press f5 to run the “module”..
The program has only limited error detection capabilities so entering invalid data such as a string when a number is expected may cause the program to ask you for the data again or it may crash and you will need to restart the program.
First one will be prompted to enter the number of chromosomes one desires use.
Then one is asked for the max x and y values of the grid one is working with.
One will then have the opportunity to enter the special points by answering Y or y to the question:
Any points to modify? Y or y for yes>
From there one follows the rest of the prompts exactly. For example, if one desires to enter a destination point, one must answer D to the prompt:
Enter type, I for illegal, C for costly, D for destination>
d will not be accepted.
While the prompts are often self-explanatory of some need of explanation is the Cost Zone entering system.
The index of a point is:
index = (y of the point*(max x of grid + 1)) + x of the point
So if the max x of a grid is 19, origin has an index of 0 and point 1,1 will have an index of 21.
There must be more than 2 destinations on the grid, any cost zone must have more than 3 points in it and there must be at least one “independent” point for any “roads” to grow.
Prompts exist for every opportunity for data entry.
If one attempts to one can enter data that causes errors to occur, such as giving -x and y values, but as in as one gives valid inputs the program will function correctly, at least it has in all cases I have run it.
This program should be run before path maker and evolver as it needs to overwrite any data left over from previous runs.
The program is commented with sandwitching comments for its major sections that follow this format:
#comment about code section--\/
code section
#comment about code section--/\