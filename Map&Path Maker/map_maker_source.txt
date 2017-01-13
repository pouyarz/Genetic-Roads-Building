from math import sqrt
from random import randint
x = ''# largest mapped x value
y = ''# largest mapped y value
num_individuals = 0# number of chromasomes










#get the number of chromasomes--\/
while num_individuals <= 1:# make numebr of chromasomes
    num_individuals = int(input("Enter desired number of individual chromasomes>"))
#get the number of chromasomes--/\









    
# open and clear files--\/
cycle = open("cycle.txt", 'r+')
cycle.truncate()
cycle.write('0')
cycle.close()
MAP_file = open("map.txt", "r+")
MAP_file.truncate()
cost_poly = open("cost_poly.txt", "r+")
cost_poly.truncate()
# open and clear files--/\










#construct basic map grid and get the outer x and y limits--\/
while ((type(x) != type(1)) or (type(y) != type(1))) or ((x<0) or (y<0)):
    print("Note: a 2 by 2 block grid has 4 blocks but 3 vertical\nlines and three horizontal lines, which intersect for\na total of 9 points.\nNumber of points = (#blocks on x axis + 1) * (#blocks on y axis + 1)") 
    x = int(input("Declair outer limit index of map x axis>"))
    y = int(input("Declair outer limit index of map y axis>"))

line = str(x) + '\n'
MAP_file.write(line)
line = str(y) + '\n'
MAP_file.write(line)
line = ''

population_size = (x + 1) * (y + 1)# number of points in grid
print('number of points:', population_size)# for testing
MAP = []# grid of points, point 1 is origin, lower left of map, last point is at upper right of map, count starts at origin from left to right, at the end of a row it loops around to the row above
nonneutral_wmap = []# nonneutral points in order of map occurence
illegal = []
destination = []
costly = []
nonneutral_IDC = []# non neutral points in sorted order of illegal, destination, cost

x_c = 0# x counter
y_c = 0# y counter
for c in range(0, population_size):# fill out basic map grid
    #print([c, x_c, y_c])# for testing
    MAP.append([c, x_c, y_c])# c is both index and point number, x_C and y_c are coordinates, point number is a function of coordinates
    x_c += 1
    if x_c > x:
        y_c += 1
        x_c = 0
    
#print(MAP)# for testing
#print(len(MAP))# for testing
#construct basic map grid and get the outer x and y limits--/\









#modify all "special" points in the list pof points before writeing it out to the file--\/
answer1 = input("Any points to modify? Y or y for yes>")
done_modifying_points = True
if answer1 == 'Y' or answer1 == 'y':
    done_modifying_points = False


while not done_modifying_points:
    x_mod = ''
    y_mod = ''
    cost = ''
    priority = ''
    name = ''
    TYPE = ''
    while (TYPE != "I") and (TYPE != "C") and (TYPE != "D"):# I for illegal, C for costly (there can be areas of - cost) and D for destination
            TYPE = input("Enter type, I for illegal, C for costly, D for destination>")
    if TYPE == "C":
            chain = []
            num_points = 0
            while type(cost) != type(1.1):
                cost = float(input("Enter cost>"))
                while num_points < 3:
                    num_points = int(input("Enter number of points in the polygon>"))
                for n in range(0, num_points):
                    cost_point = int(input("Enter point index>"))
                    chain.append([cost_point, MAP[cost_point][1], MAP[cost_point][2]])
                costly.append([cost, chain])
    while ((((type(x_mod) != type(1)) or (type(y_mod) != type(1))) or ((x_mod<0) or (y_mod<0)) or ((x_mod > x) or (y_mod > y))) and (TYPE != 'C')): #what do I set this equal to to not need to use the function?
        x_mod = int(input("x cord of point>"))
        y_mod = int(input("y cord of point>"))
        index = (y_mod *(x + 1))+ x_mod# to be figured out, there is clearly a pattern, even a simple one, but I do not pressently see it.#ah, I think thats it

        Radius = -1
        while Radius < 0:
            Radius = float(input("Enter radius of effect>"))# I am currently useing circles but I might beable to expand my collision detection to elipses which I think would be useful
        if TYPE == "D":
            independence = 1
            while type(priority) != type(1):
                priority = int(input("Enter the destination's priority, it must be an intiger>"))
            name = input("Enter name, multipul destination's can share the same name but the names must be identical for it to count>")
            while independence != 'Y' and independence != 'y' and independence != 'N' and independence != 'n':
                independence = (input("Is the point independent? Y or y for yes, N or n for now.\nIf a point is independant a path will grow from it>"))
    answer1 = input("Any other points to modify? Y or y for yes>")
    if TYPE != 'C':
        MAP[index].append(TYPE)
        MAP[index].append(Radius)
    if TYPE == "C":
        1+1#placeholder
    if TYPE == 'D':
        destination.append([index, Radius, priority, name])
        MAP[index].append(priority)
        MAP[index].append(name)
        if independence == 'Y' or independence == 'y':
            MAP[index].append("IND")
    if TYPE == 'I':
        illegal.append([index, Radius])
    if TYPE != 'C':
        nonneutral_wmap.append([index, TYPE])
    if answer1 != 'Y' and answer1 != 'y':
            done_modifying_points = True
#modify all "special" points in the list pof points before writeing it out to the file--/\









# passive illegal point calculator--\/
#passive illegal points are points in an illegal zone but which do not generate an illegality zone
#I will make illegal areas prioritize over destination areas so we can make it so that one cannot pass through buildings, if we wish to put an illegal zone in a building.
illegal_list = []
off_the_front_il = len(illegal)
for a in range(0 , len(illegal)):
    cur_point = int(illegal[a][0])
    #print(cur_point)# for testing
    illegal_list.append(int(cur_point))
for a in range(0 , len(illegal)):
    cur_radius = float(illegal[a][1])
    cur_point = int(illegal[a][0])
    cur_x = MAP[cur_point][1]
    cur_y = MAP[cur_point][2]
    if cur_radius > 0:
        for pot_y in range(0, y+1):
            for pot_x in range(0, x+1):
                #print('current tested cords:', pot_x, pot_y)# for testing
                if sqrt(((pow((cur_x - pot_x),2)) + (pow((cur_y - pot_y),2)))) < cur_radius:
                    il_ind = (pot_y *(x + 1))+ pot_x
                    if not il_ind in illegal_list:
                        #print('Illegal point')
                        illegal_list.append(il_ind)
                        #print(il_ind)



for a in range(off_the_front_il, len(illegal_list)):
    illegal.append([illegal_list[a], 0])
# passive illegal point calculator--/\










# passive destination point calculator-\/
destination_list = []
off_the_front_dest = len(destination)
for a in range(0 , len(destination)):
    cur_point = int(destination[a][0])
    #print(cur_point)# for testing
    destination_list.append(int(cur_point))
for a in range(0 , len(destination)):
    cur_radius = float(destination[a][1])
    cur_point = int(destination[a][0])
    cur_x = MAP[cur_point][1]
    cur_y = MAP[cur_point][2]
    cur_priority = MAP[cur_point][5]
    cur_name = MAP[cur_point][6]
    if cur_radius > 0:
        for pot_y in range(0, y+1):
            for pot_x in range(0, x+1):
                #print('current tested cords:', pot_x, pot_y)# for testing
                if sqrt(((pow((cur_x - pot_x),2)) + (pow((cur_y - pot_y),2)))) <= cur_radius:
                    dest_ind = (pot_y *(x + 1))+ pot_x
                    if not dest_ind in destination_list:
                        #print('destination point')# for testing
                        destination_list.append([dest_ind, cur_priority, cur_name])
                        #print(dest_ind)

for a in range(off_the_front_dest, len(destination_list)):
    destination.append([destination_list[a][0], destination_list[a][1], destination_list[a][2]])
# passive destination point calculator-/\










#print(destination_list)# for testing        
#print(MAP)# for testing
#print(nonneutral)# for testing
#print('illegal:',illegal)# for testing
#print('destination:', destination)# for testing
#print('costly:', costly)# for testing





# modify map list to add new illegal points--\/
for a in range(0, len(illegal)):
    if len(MAP[illegal[a][0]]) < 4:
        MAP[illegal[a][0]].append('I')
        MAP[illegal[a][0]].append(illegal[a][1])
# modify map list to add new illegal points--/\





# modify map list to add new destination points, illegal points take precedentover desitnation points--\/
for a in range(0, len(destination)):
    if len(MAP[destination[a][0]]) < 4:
        MAP[destination[a][0]].append('D')
        MAP[destination[a][0]].append(0)
        MAP[destination[a][0]].append(destination[a][1])
        MAP[destination[a][0]].append(destination[a][2])
# modify map list to add new destination points, illegal points take precedentover desitnation points--/\




# update map grid and write to the file--\/
for c in range(0, population_size-1):
    current = MAP[c]
    line = ''
    for p in range(0,(len(MAP[c])-1)):
        line = line + ("%s," % (MAP[c][p]))
    line = line + ("%s\n" % (MAP[c][p+1]))
    #print(line)# for testing
    MAP_file.write(line)
c += 1
line = ''
for p in range(0,(len(MAP[c]))-1):
    line = line + ("%s," % (MAP[c][p]))
p += 1
line = line + ("%s" % (MAP[c][p]))
#print(line)# for testing
MAP_file.write(line)
MAP_file.close()
# update map grid and write to the file--/\









# Now generateing the initial chromasomes--\/
dest_points = []
for d in range(0, len(destination)):
    dest_points.append(destination[d][0])
il_points = []
for i in range(0, len(illegal)):
    il_points.append(illegal[i][0])


#print('illegal points:', illegal)# for testing
#print('destination points:', destination)# for testing

organism_file = open("organism.txt" , "r+")
organism_file.truncate()
line = str(num_individuals) + '\n'
organism_file.write(line)
line = str(population_size) + '\n'
organism_file.write(line)
line = ''
cin = []
bin_counter = 1
for ind in range(0, num_individuals):
    binary = (bin(randint(0, 2**population_size))[2:])
    #print("original binary-\\/\n%s" % (binary))# for testing
    if len(binary) < population_size:
        forrunner = population_size - len(binary)
        #print(forrunner, "characters missing-\\/")# for testing
        for z in range(0,forrunner):
            line = line + '0'
    binary = line + binary
    line = ''
    #print(binary, "check number 2")# for testing
    cin = []
    if (len(illegal) != 0) or (len(destination) != 0):
        binin = 0
        cin = []
        for char in binary:
            if char == '1':
                if (binin in il_points) or (binin in dest_points):
                    cin.append(binin)
            binin += 1
    #print(binary, "check number 3")# for testing
    #print(cin)# for testing
    if len(cin) != 0:
        working = ''
        ind_counter = 0
        for char in binary:
            if ind_counter in cin:
                #print(ind_counter in cin)# for testing
                working = working + '0'
            else:
                working = working + char
            ind_counter += 1
        #print(binary, "check number 4")# for testing
        #print('working:\n%s' % (working))# for testing
        binary = working
    #print(binary, 'number of characters:', len(binary), 'number', bin_counter, '\n')# for testing
    if bin_counter < num_individuals:
        line = binary + '\n'
        organism_file.write(line)
        line = ''
    else:
        line = binary
        organism_file.write(line)
        line = ''
    bin_counter += 1
organism_file.close()
# Now generateing the initial chromasomes--/\








#creat cost polygon file content--\/
#write number of chains from length of chain list
cost_poly.write(str(len(costly)) + '\n')
if len(costly) > 1:
    for i in range(0, len(costly)-1):
        #I can figure this out, it is effortful but it is doable and I want to do it and doign it will result in a better outcome than not doign it and I can see it as a problem.
        line = str(costly[i][0]) + '\n'
        for si in range(0, len(costly[i][1])-1):
            line = line + '%s,' % (str(costly[i][1][si][0]))
            #print(line)
        si = si + 1
        line = line + '%s\n' % (str(costly[i][1][si][0]))
        #print(line)
        cost_poly.write(line)
    i += 1
    line = str(costly[i][0]) + '\n'
    for si in range(0, len(costly[i][1])-1):
        line = line + '%s,' % (str(costly[i][1][si][0]))
    si = si + 1
    line = line + '%s' % (str(costly[i][1][si][0]))
elif (len(costly) == 1):
    line = str(costly[0][0]) + '\n'
    for si in range(0, len(costly[0][1])-1):#I think this kind of last line different content might be achivable with if statments. I do not just want to write functional code, I want to write efficietn and elegantcode, I want to write code with good algorithms in mind.
        line = line + '%s,' % (str(costly[0][1][si][0]))
    si = si + 1
    line = line + '%s' % (str(costly[0][1][si][0]))
if(len(costly) != 0):
    cost_poly.write(line)
cost_poly.close()
#creat cost polygon file content--/\


#done
print("Done")

