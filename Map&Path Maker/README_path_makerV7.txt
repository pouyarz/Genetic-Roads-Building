Readme for path_makerV7.py
To use this program one needs to have python 3.5 installed.
https://www.python.org/downloads/
       cost_poly.txt
       map.txt
       organism.txt
       cycle.txt
       output.txt
To run this program either double click it or open it with the IDLE integrated development environment and press f5 to run the “module”.
This program has no user input and needs no interaction beyond being run. However, one must be careful that it be run before one runs the Evolver program. To do otherwise will modify the initially random chromosomes, over writing them with the previously run map’s chromosomes. The file Evolver reads is written by this file, that is the output.txt file. The files this program reads are written by map_makerV3.py.
The program is commented with sandwitching comments for its major sections that follow this format:
#comment about code section--\/
code section
#comment about code section--/\
