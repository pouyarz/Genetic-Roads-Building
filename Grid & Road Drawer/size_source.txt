from PIL import Image, ImageDraw
im = Image.open('map.png')
h=im.height
w=im.width
print(im.size)

draw = ImageDraw.Draw(im)

file = open('best.txt')
x = int(file.readlines()[0])
print 'Max X =',x
file.close()
file = open('p.txt')
y = int(file.readlines()[1])
print "Max Y =",y
file.close()
file = open('p.txt')
t = int(file.readlines()[2])
print "Numbers of Line =",t
file.close()    


r=255/(y)

x=w/x
for i in range(x,w,x):   
    draw.line((i,0, i,h), fill=1000,width=1)

#y=int(input("enter y: "))
y=h/y

for i in range(y,w,y):   
    draw.line((0,i, w,i), fill=1000,width=1)

p=0
#r=r*2


im = Image.open('map.png')
draw = ImageDraw.Draw(im)

with open('best.txt', "rt") as f:
    f.next()
    f.next()
    f.next()
    for line in f:
        p+=r
        #print(r)
        
        z1 = int(line.split(',')[0])
        z2 = int(line.split(',')[1])
        z3 = int(line.split(',')[2])
        z4 = int(line.split(',')[3])
        draw.line( (x*z1,h-(y*z2),x*z3,h-(y*z4)), fill=(255-p,0+p,0,0),width=1)
        
        
im.save('drawn.png')    
im.show()

