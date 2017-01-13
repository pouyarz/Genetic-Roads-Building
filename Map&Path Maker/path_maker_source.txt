from math import sqrt


organism = open('organism.txt', 'r+')
MAP = open('map.txt', 'r+')
cost_lines = open("cost_poly.txt", 'r+')











#make a list of lists, each sub list is the points that are "on" for a chromasome--\/
number_of_chromasomes = int(organism.readline().strip())
#print("number of chromasomes>", number_of_chromasomes)
chromasome_length = int(organism.readline().strip())
#print("chromasome length>",chromasome_length)

all_active_points = []#the active poitns for a chromasome will be in the n-1 index where n is the number of that chromasome (1st, 2nd, 3rd, etc...) of all points that are possible path points for a given chromasome
all_chromasomes = []#this is used in recording the best chromasome each time the program is run
for c in range(0, number_of_chromasomes):
    active_points_for_this_chromasome = []#go one chromasome at a time
    temp = organism.readline().strip()#get the chromasome string from the file
    all_chromasomes.append(temp)
    current_point = 0#start at the first point
    for x in temp:#look through each gene
        if x == '1':#check if the gene == 1 (it is on)
            active_points_for_this_chromasome.append(current_point)#if it is add it to the list of active points, which will not include illegal points, which are 0, and off points
        current_point += 1
    all_active_points.append(active_points_for_this_chromasome)#this chromasomes active points are an item in the list of all active points

#for i in range(0 , len(all_active_points)):# for testing
    #print("\nactive points for chromasome %s>" % (i+1),'\n', all_active_points[i])# for testing
organism.close()#one no longer needs the organism file
#input()
#make a list of lists, each sub list is the points that are "on" for a chromasome--/\










#Learn the aligances and type of each point on the grid--\/
max_x_of_grid = int(MAP.readline().strip())#get the max x and y for the grid 
max_y_of_grid = int(MAP.readline().strip())
center_x_of_grid = max_x_of_grid/2#get the center of the grid which is used to assist in growth point priority disputes
center_y_of_grid = max_y_of_grid/2





#make the blank lists--\/
Grow_from_points = []#points that can grow paths
Destination_points = []#points that are of class destination
Alligance_of_destination_points = []#alligance of the destiantion points (their name)
Alligance_of_all_points = []#wysiwyg
XY_cords_of_destination_points = []#wysiwyg
XY_cords_of_all_points = []#wysiwyg
Point_priority = []#the priority value of a grow point, uesed to determin whogrows first and how expensive that path is
Distance_from_center = []#used in disputes of growth priority
Point_priority_of_all_points = []#points that are not grow points may have a priority, but it is used for determining the cost of a path if a path ends there
#make the blank lists--/\




number_of_illegal_points = 0#I only calculate this once because it is the same for each chromasome
indexes_of_illegal_points = []#I only acumulate these once becasue they are in the same position for each chromasome
for d in range(0, chromasome_length):#for each point on the grid, which happens to share number with the chromasome length
    temp = MAP.readline().strip()#get a line from the map file where each line is one point
    to_be_put_on_a_list = []
    temp3 = temp
    FROM = 0
    TO = temp.find(',')
    part_of_temp = ''
    all_x = ''
    all_y = ''
    for x in range(0, 3):#for the first three parts of the grabed line the index # the x cord and the y cord, get those three thigns
        if x == 0 or x == 1:
            part_of_temp = temp3[FROM:TO]
        elif x == 2 and 'D' not in temp3 and 'I' not in temp3:#if neither D or I are in the points line that means it is a neither a destination or illegal point
            part_of_temp = temp3[FROM:]
        else:
            part_of_temp = temp3[FROM:TO]
        if x == 1:
            all_x = int(part_of_temp)
        if x == 2:
            all_y = int(part_of_temp)
        temp3 = (temp3[TO+1:])
        FROM = 0
        TO = temp3.find(',')
        if all_x != '' and all_y != '':
            XY_cords_of_all_points.append([all_x, all_y])
            #print("The x and y of point %s are:" % (d), all_x, all_y)
    if 'D' in temp:#if D is in the line it is a destination point
        #print('point', d, 'is a destination point')
        number_of_illegal_points +=1#destiantions points are always added back on to the list of active points but it is pointless to have the chromasome turn these on or off
        indexes_of_illegal_points.append(d)
        number_of_items = (temp.count(',')) +1
        FROM = 0
        TO = temp.find(',')
        part_of_temp = ''
        for x in range(0, 7):
            part_of_temp = temp[FROM:TO]
            if x == 0 or x == 1 or x == 2 or x == 5:
                part_of_temp = int(part_of_temp)
            if x == 4:
                part_of_temp = float(part_of_temp)
            to_be_put_on_a_list.append(part_of_temp)
            temp = (temp[TO+1:])
            FROM = 0
            TO = temp.find(',')
        if number_of_items == 7:#the first 7 places will be the same for grow and non grow points
            to_be_put_on_a_list[6] = temp[0]
        Destination_points.append(to_be_put_on_a_list[0])
        Alligance_of_destination_points.append(to_be_put_on_a_list[6])
        Alligance_of_all_points.append(to_be_put_on_a_list[6])
        XY_cords_of_destination_points.append([to_be_put_on_a_list[1],to_be_put_on_a_list[2]])
        Distance_from_center.append(sqrt(((pow((to_be_put_on_a_list[1]-center_x_of_grid),2))+(pow((to_be_put_on_a_list[2]-center_y_of_grid),2)))))
        if number_of_items == 8:# it is an independent point, a path will grow from it
            #print('point', d, 'is also a grow point')
            Grow_from_points.append(to_be_put_on_a_list[0])
            Point_priority.append(to_be_put_on_a_list[5])
            Point_priority_of_all_points.append(to_be_put_on_a_list[5])
    elif "I" in temp and "D" not in temp:
        #print('point', d, 'is an illegal point')
        Alligance_of_all_points.append(-1)
        Point_priority_of_all_points.append(-1)
        number_of_illegal_points +=1
        indexes_of_illegal_points.append(d)
    else:
        #print('point', d, 'is a neurtal point')
        Alligance_of_all_points.append(0)
        Point_priority_of_all_points.append(0)
    #print()
#input()
#Learn the aligances and type of each point on the grid--/\










#what has been established so far--\/
#print('The grow points for this map are as follows:\n',Grow_from_points, '\nThe priority of these points are as follows:\n', Point_priority, '\nThe destination points are as follows:\n', Destination_points, '\nThe Alligance (name) of these points are as follows:\n', Alligance_of_destination_points, '\nThe coordinates of these points are as follows:\n', XY_cords_of_destination_points)
#input()
#what has been established so far--/\











#learn the names of all destinations involved in this map--\/
Destination_points_letters = []
for p in range(0, len(Destination_points)):
    Destination_points_letters.append(Alligance_of_all_points[Destination_points[p]])
#print("A list of all the named destinations that are in this map:", Destination_points_letters)
#learn the names of all destinations involved in this map--/\










#get the lines that make up the polygons for this map--\/
cost_polygons = []
number_of_cost_zones = int(cost_lines.readline().strip())
#print("There are/is %s cost polygon(s) in this map" % (number_of_cost_zones))
for c in range(0, number_of_cost_zones):#for each cost zone
    current_polygon = []
    cost_for_polygon = float(cost_lines.readline().strip())#read its zone cost
    string_of_indicies = cost_lines.readline().strip()#read its line of index numbers (the names of the points that make it up)
    parts = []
    To = string_of_indicies.find(',')
    while To != -1:
        parts.append(int(string_of_indicies[:To]))
        From = To + 1
        string_of_indicies = string_of_indicies[From:]
        To = string_of_indicies.find(',')
    parts.append(int(string_of_indicies))
    start = parts[0]#the polygons are all loops, the last point connects to the first point, thus it is important to note which point is first
    last_index_of_parts = len(parts) - 1
    for sp in range(0, len(parts)-1):
        end = parts[sp+1]
        current_polygon.append([start, end])
        start = end
    current_polygon.append([end, parts[0]])
    cost_polygons.append([cost_for_polygon, current_polygon])
#print("The index lists for the", number_of_cost_zones, "cost polygone(s) in this map are/is:\n")
#for I in range(0, len(cost_polygons)):
    #print(cost_polygons, '\n')
#input()
#get the lines that make up the polygons for this map--/\









#establish priority in the growth points--\/
Grow_order = []
for i in range(0, len(Grow_from_points)):
    Grow_order.append([Grow_from_points[i], Point_priority[i], Distance_from_center[i]])
Grow_order = sorted(Grow_order, key = lambda x: int(x[1]))#sort them based on their point priority first
Grow_order.reverse()
Sorted_priorities = []
Sorted_points = []
for i in range(0, len(Grow_order)):#look through all the points and find if thre are equal priorities, if there are, priority is established by closeness to the map center, and then, lowness of index
    current_priority = Grow_order[i][1]
    temp = [Grow_order[i]]
    if current_priority not in Sorted_priorities:
        for h in range(i+1, len(Grow_order)):
            if Grow_order[h][1] == current_priority:
                temp.append(Grow_order[h])
        if len(temp) > 1:
            temp = sorted(temp, key = lambda x: float(x[2]))
    Sorted_priorities.append(current_priority)
    for z in range(0, len(temp)):
        Sorted_points.append([temp[z][0]])
for c in range(0, len(Sorted_points)):
    Sorted_points[c].append([Alligance_of_all_points[Sorted_points[c][0]]])
#print("The final priority of the grow points is:", Sorted_points)
#input()
#establish priority in the growth points--/\










#make a list of all points one can end, at least for starters--\/
to_consider_destinations = Destination_points_letters
#print("One can validly end a path on any point with any of the following alliances, given certain conditions\n", to_consider_destinations)
#input()
#make a list of all points one can end, at least for starters--/\










#path generation and cost caluclation--\/
current_paths = []
current_sub_path = []
current_active_points = []
current_valid_active_points = []
chromasome_costs = []
path_cateloug = []
for pc in range(0, len(all_active_points)):
    path_cateloug.append([])
print("to_consider_destinations", to_consider_destinations)
print("Destination_points_letters", Destination_points_letters)
#chromasome selection--\/
for c in range(0, len(all_active_points)):
    for dest in range(0, len(Sorted_points)):
        #print(Sorted_points[dest][1][0])
        Sorted_points[dest][1] = [Sorted_points[dest][1][0]]
#chromasome selection--/\
    print('Chromasome>', c)
    net_cost_for_chromasome = 0#for each chromasome the cost starts at 0
    current_active_points = all_active_points[c]#active points are points you can grow a path to
    #print('\nCurrent chromasome\n>', current_active_points)
    for d in range(0, len(Destination_points)):#add destination points to the active points for they can be ended on
        current_active_points.append(Destination_points[d])
    current_active_points.sort()#reestablish order of index
    #print("Current points under consideration\n")
    for x in range(0, len(current_active_points)):#for each point in the list of active points list has format: [point number, point allegance, point x, point y]
        current_active_points[x] = [current_active_points[x], [Alligance_of_all_points[current_active_points[x]]], XY_cords_of_all_points[current_active_points[x]][0], XY_cords_of_all_points[current_active_points[x]][1]]
        #print(current_active_points[x])
    #input()
#grow point selection--\/
    for g in range(0, len(Sorted_points)):#for each point one can grow paths from, this makes all paths in a chromasome
#grow point selection--/\
        #print("A path is growing from point>",Sorted_points[g][0]) 
        current_path_cost = 0
        current_grow_point = Sorted_points[g][0]#the firstgrow head is the destination that is growing a path
        Illegal_alligances = Sorted_points[g][1]#alligances that if a point has the grow head is not allowed to grow to it
        #print("Starting destiantion>", Sorted_points[g])
        #print("Illegal alligances for this starting deatination>", Illegal_alligances)
        start_point = Sorted_points[g][0]#the start point may be important in adding alligances later, but maybe not
        #print("The currently growing point is", current_grow_point, "and it may not grow to any point which has the following aligances:\n", Illegal_alligances)
        #input()
        done_growing_path = False#the path is not done growing
        Grow_head = start_point
        current_sub_path.append(Grow_head)
        for p in range(0, len(current_active_points)):
            if Grow_head == current_active_points[p][0]:
                X_grow_head = current_active_points[p][2]
                Y_grow_head = current_active_points[p][3]
        default = [((max_y_of_grid*(max_x_of_grid + 1))+max_x_of_grid + 1), max_x_of_grid*max_y_of_grid]#this will always be larger than the farthest two points on a grid can be from one another
        current_nearest_point = default
        #current_baseline_cost = 0
        while not done_growing_path:
            for choice in range(0, len(current_active_points)):
                point_legal = True
                #print('current point considered for growth\non path started at point %s' % (Sorted_points[g]),'\n>', current_active_points[choice])
                for al in range(0, len(current_active_points[choice][1])):
                    #print('It is %s that %s is in %s' % (current_active_points[choice][1][al] in Illegal_alligances,current_active_points[choice][1][al],Illegal_alligances ))
                    #input()
                    if current_active_points[choice][1][al] in Illegal_alligances:#check to see if the point is legal by compareing each of it's alligances to the illegal alligances list
                        #print('It is illegal to grow to this point')
                        point_legal = False
                if point_legal:#if it is
                    distance_to_point = (sqrt(((pow((X_grow_head - current_active_points[choice][2]),2))+(pow((Y_grow_head - current_active_points[choice][3]),2)))))#get the distance
                    if distance_to_point < current_nearest_point[1] and current_active_points[choice][0] not in current_sub_path:#if that distance is lower than the current best, it is the new candidate growth point
                        current_nearest_point = [current_active_points[choice], distance_to_point]
            #print("current grow head>", Grow_head)
            #print("to consider destinaton", to_consider_destinations)
            #print('New grow head>', current_nearest_point[0][0])
            #print(current_nearest_point[0])
            Grow_head = current_nearest_point[0][0]
            current_path_cost += distance_to_point 
            current_nearest_point = default
            current_sub_path.append(Grow_head)
            



            
#check if the currently grown to point is a valid stopping point--\/
            in_illegal = False
            #print("Current grow head>", Grow_head)
            for check in range(0, len(current_active_points)):#look through all the points that are active
                if Grow_head == current_active_points[check][0]:#when the current growth head is found
                    #print("current grow head's alligances--\/")
                    if current_active_points[check][1][0] == 0:#if the current growth heads alligance is 0 (it has no alligance)
                        #print("No current alligances")
                        #print("Alligance %s added to %s" % (Sorted_points[g][1], Grow_head))
                        current_active_points[check][1][0] = (Sorted_points[g][1])# give it a new alligance, to the current paths starting point's alligance
                    elif len(current_active_points[check][1]) == 1:#if the current growth heads alligance is somethign other than 0 but there is only 1 other alligance     
                        #if (((current_active_points[check][1][0] in to_consider_destinations) or ((current_active_points[check][1][0] in Destination_points_letters))) and current_active_points[check][1][0] != Sorted_points[g][1]):
                        if ((current_active_points[check][1][0] in to_consider_destinations) or ((current_active_points[check][1][0] in Destination_points_letters))):
                            #print("Destination check:\n%s in %s: %s\n" % (current_active_points[check][1][0], to_consider_destinations, current_active_points[check][1][0] in to_consider_destinations))
                            #print("Alligance only check:\n%s in %s: %s\n" % (current_active_points[check][1][0], Destination_points_letters, current_active_points[check][1][0] in Destination_points_letters))
                            for ilp in range(0, len(current_active_points[check][1][0])):
                                if current_active_points[check][1][0][ilp] in Illegal_alligances:
                                    in_illegal = True
                            if not in_illegal:
                                done_growing_path = True#any point with an alligance is either a destination or path point, and is thus a valid ending point
                        current_active_points[check][1].append(Sorted_points[g][1][0])# give it an additional alligance, to the current paths starting point's alligance
                    else:#else the current growth head has reached another destiantion
                        done_growing_path = True
                        current_active_points[check][1].append(Sorted_points[g][1])# give it an additional alligance, to the current paths starting point's alligance
            if done_growing_path:
                end_point = Grow_head
                for ed in range(0, len(Sorted_points)):
                    if Sorted_points[ed][0] == end_point:
                        Sorted_points[ed][1].append(Sorted_points[g][1][0])
                #print("Done growing a path from point %s" % (start_point))
                current_paths.append(current_sub_path)
                current_path_cost = current_path_cost * (Point_priority_of_all_points[Sorted_points[g][0]])#The cost of a path is its length*the points priority +additional. Each path within all the paths made form a chromasome has a cost
                #print("The path is done growing and ended at point>", end_point)
                #print("The baseline length cost of this path =", current_path_cost)
                #print("path from destination point %s>\n" % (Sorted_points[g][0]), current_sub_path, "\nat a cost of", current_path_cost, 'units')
                print("destiantion points>", Destination_points, '\n', 'path from %s to %s' % (start_point, end_point))
                net_cost_for_chromasome += current_path_cost
                current_path_cost = 0
                #input()
                
                
#check if the currently grown to point is a valid stopping point--/\





#begin added cost calculation---\/
                line_segment_number = 0
                #print("number of individual connections in the currently considered path =", len(current_sub_path) -1)
                current_collisions = []
                #print()
                added_cost = 0
                for ls in range(0, len(current_sub_path)-1):#for each of the line segments in the path #this finds all costs to add to one path in achromasome
                    current_points = [current_sub_path[ls],current_sub_path[ls+1]]
                    XY_of_path_point_1 = XY_cords_of_all_points[current_points[0]]
                    XY_of_path_point_2 = XY_cords_of_all_points[current_points[1]]
                    #print("The current path segment is between the following two cords:")
                    #print("x,y of first point in path", XY_of_path_point_1)
                    #print("x,y of second point in path", XY_of_path_point_2)
                    if (XY_of_path_point_2[0] == XY_of_path_point_1[0]):
                        path_line_verticle = True
                        x_path_value = XY_of_path_point_2[0]
                        slope_of_path_line = 'undefined'
                    else:
                        path_line_verticle = False
                        slope_of_path_line =  (XY_of_path_point_2[1] - XY_of_path_point_1[1]) / (XY_of_path_point_2[0] - XY_of_path_point_1[0])
                    #print("the slope of the path line is:", slope_of_path_line)    
                    #input()
                    for cz in range(0, len(cost_polygons)):#cz = cost zone, for each cost zone
                        current_cost = cost_polygons[cz][0]
                        cost_line_verticle = False
                        path_line_verticle = False
                        #print("current cost zone's cost =", current_cost)
                        for cpl in range(0, len(cost_polygons[cz][1])):#cpl = cost polygon line, for each line of the current polygon (usually far fewer than the numberof path points
                            intersection = ''
                            current_cpl = cost_polygons[cz][1][cpl]
                            #print("current cost polygon line>", current_cpl)
                            XY_of_cost_point_1 = XY_cords_of_all_points[current_cpl[0]]
                            XY_of_cost_point_2 = XY_cords_of_all_points[current_cpl[1]]
                            if (XY_of_cost_point_2[0] == XY_of_cost_point_1[0]):
                                cost_line_verticle = True
                                x_cost_value = XY_of_path_point_2[0]
                                slope_of_cost_line = 'undefined'
                            else:
                                cost_line_verticle = False
                                slope_of_cost_line =  (XY_of_cost_point_2[1] - XY_of_cost_point_1[1]) / (XY_of_cost_point_2[0] - XY_of_cost_point_1[0])
                            #print("the slope of the cost line is:", slope_of_cost_line)
                            #input()
                            if slope_of_path_line != slope_of_cost_line:
                                #check for overlap--\/If the lines overlap on the x axis there might be collision
                                if(((max(XY_of_cost_point_1[0],XY_of_cost_point_2[0]))>(min(XY_of_path_point_2[0],XY_of_path_point_1[0])))and((max(XY_of_path_point_2[0],XY_of_path_point_1[0]))>(min(XY_of_cost_point_1[0],XY_of_cost_point_2[0])))):
                                #check for overlap--/\
                                    if slope_of_cost_line == 'undefined':
                                        #print("cost zone line is verticle")
                                        path_line_intercept = XY_of_path_point_1[1] - (slope_of_path_line*XY_of_path_point_1[0])
                                        potential_intersection_x = x_cost_value
                                        potential_intersection_y = (potential_intersection_x*slope_of_path_line) + path_line_intercept
                                        if (min(XY_of_cost_point_1[0],XY_of_cost_point_2[0])<= potential_intersection_x <= max(XY_of_cost_point_1[0],XY_of_cost_point_2[0])) and (min(XY_of_path_point_2[0],XY_of_path_point_1[0])<= potential_intersection_x <= max(XY_of_path_point_2[0],XY_of_path_point_1[0])):
                                            if (min(XY_of_cost_point_1[1],XY_of_cost_point_2[1])) <= potential_intersection_y <= (max(XY_of_cost_point_1[1],XY_of_cost_point_2[1])):
                                                intersection = [potential_intersection_x, potential_intersection_y]                                                                                                                                     
                                    if slope_of_path_line == 'undefined':
                                        #print("path line is verticle")
                                        cost_line_intercept = XY_of_cost_point_1[1] - (slope_of_cost_line*XY_of_cost_point_1[0])
                                        potential_intersection_x = x_path_value
                                        potential_intersection_y = (potential_intersection_x*slope_of_cost_line) + cost_line_intercept
                                        if(min(XY_of_cost_point_1[0],XY_of_cost_point_2[0])<= potential_intersection_x <= max(XY_of_cost_point_1[0],XY_of_cost_point_2[0])) and (min(XY_of_path_point_2[0],XY_of_path_point_1[0])<= potential_intersection_x <= max(XY_of_path_point_2[0],XY_of_path_point_1[0])):
                                            if (min(XY_of_path_point_1[1],XY_of_path_point_2[1])) <= potential_intersection_y <= (max(XY_of_path_point_1[1],XY_of_path_point_2[1])):
                                                intersection = [potential_intersection_x, potential_intersection_y]                                                                                                                                         
                                    if slope_of_cost_line != 'undefined' and slope_of_path_line != 'undefined':
                                        #print("neither line is verticle")
                                        #print("slope of cost line", slope_of_cost_line)
                                        #print("slope of path line", slope_of_path_line)
                                        path_line_intercept = XY_of_path_point_1[1] - (slope_of_path_line*XY_of_path_point_1[0])
                                        cost_line_intercept = XY_of_cost_point_1[1] - (slope_of_cost_line*XY_of_cost_point_1[0])#I think there are redundant lines of code in here, I want to do thigns well, not just functionaly
                                        potential_intersection_x = (path_line_intercept - cost_line_intercept)/(slope_of_cost_line - slope_of_path_line)
                                        if(min(XY_of_cost_point_1[0],XY_of_cost_point_2[0])<= potential_intersection_x <= max(XY_of_cost_point_1[0],XY_of_cost_point_2[0])) and (min(XY_of_path_point_2[0],XY_of_path_point_1[0])<= potential_intersection_x <= max(XY_of_path_point_2[0],XY_of_path_point_1[0])):
                                            intersection = [potential_intersection_x, (slope_of_cost_line*potential_intersection_x)+ cost_line_intercept]
                                if intersection != '':
                                    current_collisions.append(intersection)
                                    #print("collision")
                                #else:
                                    #print("no collision")
                                if len(current_collisions) > 0:#for each colision with a cost zone there will be some length, optamistically straight line, in the cost zone, that is experianing cost, its cost = cost of zone *distance betwee nthe two points
                                        #print("\n\n\npoints at which collision with a cost zone of cost %s occured:\n" % (current_cost))
                                        #for cc in range(0, len(current_collisions)):
                                            #print(current_collisions[cc])
                                        if len(current_collisions) % 2 == 0:
                                            for cc in range(0, len(current_collisions),2):
                                                first_point = current_collisions[cc]
                                                second_point = current_collisions[cc+1]
                                                distance_between_points = (sqrt(((pow((first_point[0] - second_point[0]),2))+(pow((first_point[1] - second_point[1]),2)))))
                                                added_cost += distance_between_points*current_cost
                                        elif len(current_collisions) > 1:
                                            for cc in range(0, len(current_collisions)-1,2):
                                                first_point = current_collisions[cc]
                                                second_point = current_collisions[cc+1]
                                                distance_between_points = (sqrt(((pow((first_point[0] - second_point[0]),2))+(pow((first_point[1] - second_point[1]),2)))))
                                                added_cost += distance_between_points*current_cost
                                            cc += 1
                                            first_point = current_collisions[cc]
                                            #print("start point>", start_point)
                                            #print("end point>", start_point)
                                            distance_to_start = (sqrt(((pow((first_point[0] - XY_cords_of_all_points[start_point][0]),2))+(pow((first_point[1] - XY_cords_of_all_points[start_point][1]),2)))))
                                            distance_to_end = sqrt(((pow((first_point[0] - XY_cords_of_all_points[end_point][0]),2))+(pow((first_point[1] - XY_cords_of_all_points[end_point][1]),2))))
                                            if distance_to_start <= distance_to_end:
                                                added_cost += distance_to_start*current_cost
                                            else:
                                                added_cost += distance_to_end*current_cost
                                        else:
                                            first_point = current_collisions[0]
                                            #print("start point>", start_point)
                                            #print("end point>", start_point)
                                            distance_to_start = (sqrt(((pow((first_point[0] - XY_cords_of_all_points[start_point][0]),2))+(pow((first_point[1] - XY_cords_of_all_points[start_point][1]),2)))))
                                            distance_to_end = sqrt(((pow((first_point[0] - XY_cords_of_all_points[end_point][0]),2))+(pow((first_point[1] - XY_cords_of_all_points[end_point][1]),2))))
                                            if distance_to_start <= distance_to_end:
                                                added_cost += distance_to_start*current_cost
                                            else:
                                                added_cost += distance_to_end*current_cost
                #print("Added cost for this path for all collisions with cost points =", added_cost)
                current_collisions = []
#the indentation and order of these next few lines needs to be examined and made correct. I am concerned that somewhere, perhaps many places, costs are being added unnessisarily and repeatedly
                net_cost_for_chromasome += added_cost#add all collision costs for each polygon
                #print("The current cost for this chromasome =", net_cost_for_chromasome)
        path_cateloug[c].append(current_sub_path)
        current_sub_path = []
    #print("\n\nThe final cost for chromasome %s is %s" % (c,net_cost_for_chromasome),'\n\n')
    chromasome_costs.append(int(net_cost_for_chromasome))
#end added cost calculation---/\
#path generation and cost caluclation--/\









#make visual output--\/
#for n in range(0, len(chromasome_costs)):
    #print("Chromasome %s had a cost of %s for the following paths:\n" % (n, chromasome_costs[n]))
    #for p in range(0, len(path_cateloug[n])):
        #print(path_cateloug[n][p],'\n')
#make visual output--/\









#make sorted list of paths and costs--\/
List_of_paths_and_costs = []
for n in range(0, len(chromasome_costs)):
    temp_list = ['',[],'']
    temp_list[0] = chromasome_costs[n]
    temp_list[2] = all_chromasomes[n]
    for p in range(0, len(path_cateloug[n])):
        temp_list[1].append(path_cateloug[n][p])
    List_of_paths_and_costs.append(temp_list)
List_of_paths_and_costs = sorted(List_of_paths_and_costs, key = lambda x: int(x[0]))
#for c_p in range(0, len(List_of_paths_and_costs)):
    #print("chromasome cost>", List_of_paths_and_costs[c_p][0])
    #print("paths\n",List_of_paths_and_costs[c_p][1][0])
    #print("chromasome:",List_of_paths_and_costs[c_p][2])
best = List_of_paths_and_costs[0]#get lowest cost, chromasome and paths
#make sorted list of paths and costs--/\







#for point in range(0, len(XY_cords_of_all_points)):
#    print('x and y cords for point %s' % (point), '\n', XY_cords_of_all_points[point])
#print(XY_cords_of_all_points)





#count number of lines to draw and make a list of lines x and y points--\/
print('best', best)
number_of_line_segments = 1
xyxy = []
print('Number of paths for the best chromasome =', len(best[1]))
for p in range(0, len(best[1])):
    #print("path %s" % (p), '\n', best[1][p])
    current_path = best[1][p]
    for ls in range(0, len(current_path)-1):
        number_of_line_segments += 1
        xyxy.append([(XY_cords_of_all_points[current_path[ls]][0]),(XY_cords_of_all_points[current_path[ls]][1]),(XY_cords_of_all_points[current_path[ls+1]][0]),(XY_cords_of_all_points[current_path[ls+1]][1])])


                                                                   
#for p in range(0, len(best[1])):#why didn't this work?
#    print('best[1]>', best[1])
#    for ls in range(0, len(best[1])):
#        number_of_line_segments += 1
#        print("best[p][ls]>",best[1][ls])
        #xyxy.append([(XY_cords_of_all_points[best[p][ls]][1]),(),(),()]
    
#count number of lines to draw and make a list of lines x and y points--/\










#make output file for best chromasome--\/
output = open("output.txt", 'r+')
output.truncate()
cycle = open("cycle.txt", 'r+')
previous_iteration = int(cycle.readline().strip())
#cylce format:
#current iteration number
#iteration of best chromasome
#chromasome's cost
#chromasome
#max x
#max y
#number of lines to draw
#paths x,y, combinations
if previous_iteration == 0:
    cycle.truncate(0)
    cycle.seek(0)
    cycle.write('1\n')
    cycle.write('1\n')
    cycle.write(str(best[0])+'\n')
    cycle.write(str(best[2])+'\n')
    cycle.write(str(max_x_of_grid)+'\n')
    cycle.write(str(max_y_of_grid)+'\n')
    cycle.write(str(number_of_line_segments)+'\n')
    for xy in range(0, len(xyxy)-1):
        cycle.write(str(xyxy[xy])[1:-1]+'\n')
    xy +=1
    cycle.write(str(xyxy[xy])[1:-1])
else:    
    current_iteration = previous_iteration + 1
    current_best_iteration = cycle.readline()
    current_best_cost = cycle.readline()
    current_best_chromasome = cycle.readline()
    cycle.readline()#the max x and y will be the same for each cylce of a map
    cycle.readline()
    number_of_segments_in_current_best_path = cycle.readline()
    current_best_line_segments = cycle.read()
    cycle.truncate(0)
    cycle.seek(0)
    if best[0] < int(current_best_cost):#find if the current best is better than the best so far
        cycle.write(str(current_iteration)+'\n')
        cycle.write(str(current_iteration)+'\n')
        cycle.write(str(best[0])+'\n')
        cycle.write(str(best[2])+'\n')
        cycle.write(str(max_x_of_grid)+'\n')
        cycle.write(str(max_y_of_grid)+'\n')
        cycle.write(str(number_of_line_segments)+'\n')
        for xy in range(0, len(xyxy)-1):
            cycle.write(str(xyxy[xy])[1:-1]+'\n')
        xy +=1
        cycle.write(str(xyxy[xy])[1:-1])
    else:
        cycle.write(str(current_iteration)+'\n')
        cycle.write(current_best_iteration)
        cycle.write(current_best_cost)
        cycle.write(current_best_chromasome)
        cycle.write(str(max_x_of_grid)+'\n')
        cycle.write(str(max_y_of_grid)+'\n')
        cycle.write(number_of_segments_in_current_best_path)
        cycle.write(current_best_line_segments)
cycle.close()








#make output file for Nima's program--\/
#make output file for best chromasome--/\
#format:
# number of current generation
# number of chromosomes
# chromosome length
# number of illegal points
# index of illegal point genes seperated by ,s
# in order cost of each chromasome seperated by ,s
# each chromasome on its own line, the last line is the last chromasome
output.write(str(current_iteration)+'\n')
output.write(str(number_of_chromasomes)+'\n')
output.write(str((max_x_of_grid+1)*(max_y_of_grid+1))+'\n')
output.write(str(number_of_illegal_points)+'\n')
line = ''
if number_of_illegal_points > 0:
    line = line + str(indexes_of_illegal_points)[1:-1]
output.write(line +'\n')
line = ''
for c_p in range(0, len(List_of_paths_and_costs)-1):
    line = line + str(List_of_paths_and_costs[c_p][0])+','
c_p += 1
line = line + str(List_of_paths_and_costs[c_p][0])+'\n'
output.write(line)
line = ''
for c_p in range(0, len(List_of_paths_and_costs)-1):
    for gene in range(0, len(List_of_paths_and_costs[c_p][2])-1):
        line = line + str(List_of_paths_and_costs[c_p][2][gene]) + ','
    gene += 1
    line = line + str(List_of_paths_and_costs[c_p][2][gene]) + '\n'
c_p += 1
for gene in range(0, len(List_of_paths_and_costs[c_p][2])-1):
    line = line + str(List_of_paths_and_costs[c_p][2][gene]) + ','
gene += 1
line = line + str(List_of_paths_and_costs[c_p][2][gene])
output.write(line)
output.close()
#make output file for Nima's program--/\






