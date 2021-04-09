# Problem 81



> In the 5 by 5 matrix below, the minimal path sum from the top left to the bottom right, by **only moving to the right and down**, is indicated in bold red and is equal to 2427.
> $$
> \begin{pmatrix}
> \color{red}{131} & 673 & 234 & 103 & 18\\
> \color{red}{201} & \color{red}{96} & \color{red}{342} & 965 & 150\\
> 630 & 803 & \color{red}{746} & \color{red}{422} & 111\\
> 537 & 699 & 497 & \color{red}{121} & 956\\
> 805 & 732 & 524 & \color{red}{37} & \color{red}{331}
> \end{pmatrix}
> $$
> Find the minimal path sum from the top left to the bottom right by only moving right and down in [matrix.txt](https://projecteuler.net/project/resources/p081_matrix.txt) (right click and "Save Link/Target As..."), a 31K text file containing an 80 by 80 matrix.



### Answer

```Python
a=np.array([list(eval(l)) for l in open('p081_matrix.txt').readlines()])
a= np.pad(a,((1,0),(1,0)))

for i in range(1,a.shape[0]):
    for j in range(1,a.shape[1]):
        a[i][j] += min([k for k in (a[i-1][j], a[i][j-1]) if k !=0], default=0 ) 

print(a[-1][-1])
```

> 427337





# Problem 82



> The minimal path sum in the 5 by 5 matrix below, by starting in any cell in the left column and finishing in any cell in the right column, and only moving up, down, and right, is indicated in red and bold; the sum is equal to 994.
> $$
> \begin{pmatrix}
> 131 & 673 & \color{red}{234} & \color{red}{103} & \color{red}{18}\\
> \color{red}{201} & \color{red}{96} & \color{red}{342} & 965 & 150\\
> 630 & 803 & 746 & 422 & 111\\
> 537 & 699 & 497 & 121 & 956\\
> 805 & 732 & 524 & 37 & 331
> \end{pmatrix}
> $$
> Find the minimal path sum from the left column to the right column in [matrix.txt](https://projecteuler.net/project/resources/p082_matrix.txt) (right click and "Save Link/Target As..."), a 31K text file containing an 80 by 80 matrix.



### Answer1

```Python
m=[list(eval(l)) for l in open('p082_matrix.txt').readlines()]

opt = [[row[0]] for row in m]

for col in range(1, len(m[0])):
    for row in range(len(m)):
        opt[row].append(m[row][col] + opt[row][col - 1])
        
for row in range(1, len(m)):
    if opt[row - 1][col] + m[row][col] < opt[row][col]:
        opt[row][col] = opt[row - 1][col] + m[row][col]

for row in reversed(range(len(m) - 1)):
    if opt[row + 1][col] + m[row][col] < opt[row][col]:
        opt[row][col] = opt[row + 1][col] + m[row][col]
        
print (min(row[-1] for row in opt))
```

> 260324

###  Answer2

```python
def p082():
    rows = [map(int, s.strip().split(',')) for s in file("/tmp/matrix.txt")]
    cost = [r[0] for r in rows]  # initially the values at 1st column
    prev = cost[0]
    for x in xrange(1, len(rows[0])):  # 1 .. len-1
        for y in xrange(len(rows)):            # 0 .. len-1
            prev = cost[y] = min(prev, cost[y]) + rows[y][x]
        for y in xrange(len(rows)-2, -1, -1):  # len-2 .. 0
            prev = cost[y] = min(cost[y], prev + rows[y][x])
    print min(cost)
```

> 260324





# Problem 83



> In the 5 by 5 matrix below, the minimal path sum from the top left to the bottom right, by moving left, right, up, and down, is indicated in bold red and is equal to 2297.
> $$
> \begin{pmatrix}
> \color{red}{131} & 673 & \color{red}{234} & \color{red}{103} & \color{red}{18}\\
> \color{red}{201} & \color{red}{96} & \color{red}{342} & 965 & \color{red}{150}\\
> 630 & 803 & 746 & \color{red}{422} & \color{red}{111}\\
> 537 & 699 & 497 & \color{red}{121} & 956\\
> 805 & 732 & 524 & \color{red}{37} & \color{red}{331}
> \end{pmatrix}
> $$
> Find the minimal path sum from the top left to the bottom right by moving left, right, up, and down in [matrix.txt](https://projecteuler.net/project/resources/p083_matrix.txt) (right click and "Save Link/Target As..."), a 31K text file containing an 80 by 80 matrix.



### Answer : Dijkstra Algorithm

```python
import numpy as np
import heapq

matrix = np.genfromtxt('p083_matrix.txt', delimiter=',', dtype = int)

U = [(matrix[0,0],0,0)]
S = {}
while U:
    v,i,j = heapq.heappop(U)
    neighbors = [(i,j-1), (i,j+1), (i-1,j), (i+1,j)]
    for r,c in neighbors:
        if len(matrix)> r >=0 and len(matrix)> c >=0  and (r,c) not in S: 
            heapq.heappush(U, (v+matrix[r,c], r, c))
            S[(r,c)] = v+matrix[r,c]

print(S[(len(matrix)-1, len(matrix)-1)])
```

> 425185





# Problem 84



>In the game, *Monopoly*, the standard board is set up in the following way:
>
>![p084_monopoly_board.png](https://projecteuler.net/project/images/p084_monopoly_board.png)
>
>A player starts on the GO square and adds the scores on two 6-sided dice to determine the number of squares they advance in a clockwise direction. Without any further rules we would expect to visit each square with equal probability: 2.5%. However, landing on G2J (Go To Jail), CC (community chest), and CH (chance) changes this distribution.
>
>In addition to G2J, and one card from each of CC and CH, that orders the player to go directly to jail, if a player rolls three consecutive doubles, they do not advance the result of their 3rd roll. Instead they proceed directly to jail.
>
>At the beginning of the game, the CC and CH cards are shuffled. When a player lands on CC or CH they take a card from the top of the respective pile and, after following the instructions, it is returned to the bottom of the pile. There are sixteen cards in each pile, but for the purpose of this problem we are only concerned with cards that order a movement; any instruction not concerned with movement will be ignored and the player will remain on the CC/CH square.
>
>- Community Chest (2/16 cards):
>	1. Advance to GO
>	2. Go to JAIL
>- Chance (10/16 cards):
>	1. Advance to GO
>	2. Go to JAIL
>	3. Go to C1
>	4. Go to E3
>	5. Go to H2
>	6. Go to R1
>	7. Go to next R (railway company)
>	8. Go to next R
>	9. Go to next U (utility company)
>	10. Go back 3 squares.
>
>The heart of this problem concerns the likelihood of visiting a particular square. That is, the probability of finishing at that square after a roll. For this reason it should be clear that, with the exception of G2J for which the probability of finishing on it is zero, the CH squares will have the lowest probabilities, as 5/8 request a movement to another square, and it is the final square that the player finishes at on each roll that we are interested in. We shall make no distinction between "Just Visiting" and being sent to JAIL, and we shall also ignore the rule about requiring a double to "get out of jail", assuming that they pay to get out on their next turn.
>
>By starting at GO and numbering the squares sequentially from 00 to 39 we can concatenate these two-digit numbers to produce strings that correspond with sets of squares.
>
>Statistically it can be shown that the three most popular squares, in order, are JAIL (6.24%) = Square 10, E3 (3.18%) = Square 24, and GO (3.09%) = Square 00. So these three most popular squares can be listed with the six-digit modal string: 102400.
>
>If, instead of using two 6-sided dice, two 4-sided dice are used, find the six-digit modal string.



### Answer1 : Markov chain

```python
import numpy as np

dice1, dice2 = np.arange(1,5), np.arange(1,5).reshape(4,1)
combination = np.broadcast(dice1, dice2)
probdice = 1/combination.size
cc= {2, 17, 33}
ch= {7, 22, 36}
pc = 1/16

transition = np.zeros(120*120).reshape(120,120)

for a,b in combination:
    k = int(a==b)
    for j in range(len(transition)):
        double = 40*(j//40 +1)
        i = (j+a+b)%40
        if (i == 30) or (k and double == 120): transition[10, j] += probdice
        else :
            if i in cc:
                transition[i+k*double, j] += probdice*14*pc
                transition[0+k*double,  j] += probdice*pc
                transition[10+k*double, j] += probdice*pc
            elif i in ch:
                transition[i+k*double, j] += probdice*6*pc
                transition[0+k*double,  j] += probdice*pc
                transition[10+k*double, j] += probdice*pc
                transition[11+k*double, j] += probdice*pc
                transition[24+k*double, j] += probdice*pc
                transition[39+k*double, j] += probdice*pc
                transition[5+k*double, j] += probdice*pc
                transition[(10*((i+5)//10) + 5)%40 +k*double, j] += probdice*2*pc
                if i <12 : transition[12+k*double, j] += probdice*pc
                else : transition[28+k*double, j] += probdice*pc
                transition[(i-3)%40+k*double, j] += probdice*pc
            else: transition[i+k*double, j] += probdice

#Solve Ax = B
A = (transition-np.identity(len(transition)))[:-1]
A = np.pad(A,((0,1),(0,0)),constant_values= 1)
B = np.zeros(len(A)); B[-1] = 1
sol = np.linalg.solve(A,B).reshape(3,-1).sum(axis=0)
for i in sol.argsort()[::-1] : print(f'{i} : {sol[i]*100: .3f} %')
```

>10 :  6.971 %
>15 :  3.597 %
>24 :  3.275 %



### Answer2

```python
import numpy as np

def roll2b(orig, cur, dn, pf):
  for i in range(1, D+1):
    for j in range(1, D+1):
      pp = (1/D)**((dn+1)*2)*pf
      tgt = (cur + i + j)%40
      if i==j and dn < 2:
        roll2b(orig,tgt, dn+1, pf)
      elif i == j and dn == 2:
        p[orig][10] += pp
      else:
        if tgt == 7:
          p[orig][tgt] += pp*6/16
          for k in (4, 10, 0, 11, 24, 39, 5, 12):
            p[orig][k] += pp*(1/16)
          for k in (15,):
            p[orig][k] += pp*(2/16)
        elif tgt == 22:
          p[orig][tgt] += pp*6/16
          for k in (19, 10, 0, 11, 24, 39, 5, 28):
            p[orig][k] += pp*(1/16)
          for k in (25,):
            p[orig][k] += pp*(2/16)
        elif tgt == 36:
          p[orig][tgt] += pp*6/16
          for k in (33, 10, 0, 11, 24, 39, 5, 12):
            p[orig][k] += pp*(1/16)
          for k in (5,):
            p[orig][k] += pp*(2/16)
        elif tgt in (2, 17, 33):
          p[orig][tgt] += pp*14/16
          for k in (10, 0):
            p[orig][k] += pp*(1/16)
        elif tgt == 30:
          p[orig][10] += pp
        else:
          p[orig][tgt] += pp

p = [[0]*40 for _ in range(40)]
D=4

for i in range(40):
  if i != 30:
    roll2b(i, i, 0, 1)

p = np.array(p)
w, v = np.linalg.eig(p.T)
print(v[:,0])
print(np.argsort(-abs(np.real(v[:,0])))[:3])
```

> [10 15 24]





# Problem 85



> By counting carefully it can be seen that a rectangular grid measuring 3 by 2 contains eighteen rectangles:
>
> ![img](https://projecteuler.net/project/images/p085.png)
>
> Although there exists no rectangular grid that contains exactly two million rectangles, find the area of the grid with the nearest solution.



### Answer

```python
n, m, ans, min_d = 1, 1, 0, 8000000
while m >= n:
    m = int((8000000/(n*(n+1)))**0.5)
    for i in (m-1, m, m+1):
        d = abs(8000000-n*m*(n+1)*(m+1))
        if d < min_d : min_d = d; ans = m*n
    n + =1
print(ans)
```

> 2772





# Problem 86



> A spider, S, sits in one corner of a cuboid room, measuring 6 by 5 by 3, and a fly, F, sits in the opposite corner. By travelling on the surfaces of the room the shortest "straight line" distance from S to F is 10 and the path is shown on the diagram.
>
> ![img](https://projecteuler.net/project/images/p086.png)
>
> However, there are up to three "shortest" path candidates for any given cuboid and the shortest route doesn't always have integer length.
>
> It can be shown that there are exactly 2060 distinct cuboids, ignoring rotations, with integer dimensions, up to a maximum size of M by M by M, for which the shortest route has integer length when M = 100. This is the least value of M for which the number of solutions first exceeds two thousand; the number of solutions when M = 99 is 1975.
>
> Find the least value of M such that the number of solutions first exceeds one million.



### Answer1

```python
from Euler.algorithm import PTG
import numpy as np

guess, target = 2000, 1000000
M=np.zeros(guess+1, dtype= np.int32)

for l in PTG(6*guess):
    a, b, _ = sorted(l)
    n=1
    while n*b <= guess: M[n*b] += (n*a)//2; n+=1    
    if b/a <= 2:
        n=1
        while n*a <= guess: M[n*a] += n*a-(n*b-1)//2; n+=1
print(((M.cumsum()>=target).nonzero())[0][0])
```

> 1818



### Answer2

```python
import numpy as np
M, count= 100, 2060

def pair(x):
    l=[]
    for d in range(1, int(x**0.5)+1):
        if x%d == 0: l.append([d,x//d])
    return np.array(l)

while count<1000000:
    M += 1
    if M % 2 == 0 : x = np.apply_along_axis(lambda x: x[1]-x[0], 1, pair(M**2//4))
    else : x = np.apply_along_axis(lambda x: (x[1]-x[0])//2, 1, pair(M**2))
    x= x[(2*M >= x) & (x > 0)]
    a = np.where(x>=M, M-(x-1)//2, x//2)
    count += a.sum()

print(M,count)
```

> 1818 1000457





# Problem 87



> The smallest number expressible as the sum of a prime square, prime cube, and prime fourth power is 28. In fact, there are exactly four numbers below fifty that can be expressed in such a way:
>
> 28 = 22 + 23 + 24
> 33 = 32 + 23 + 24
> 49 = 52 + 23 + 24
> 47 = 22 + 33 + 24
>
> How many numbers below fifty million can be expressed as the sum of a prime square, prime cube, and prime fourth power?



### Answer

```python
from Euler.algorithm import seive
from bisect import bisect

lim = 50_000_000
primes = seive(int(lim**0.5))

p2 = [p**2 for p in primes]
p3 = [p**3 for p in primes[:bisect(primes, lim**(1/3))] ]
p4 = [p**2 for p in p2[:bisect(p2, lim**(1/2))] ]
ans=set()

for pp4 in p4:
    left4 = lim-pp4
    for pp3 in p3[:bisect(p3,left4)]:
        left3 = left4-pp3
        for pp2 in p2[:bisect(p2, left3)]:
            ans.add(pp2+pp3+pp4)

print(len(ans))
```

> 1097343



# Problem 88



> A natural number, N, that can be written as the sum and product of a given set of at least two natural numbers, {*a*1, *a*2, ... , *a**k*} is called a product-sum number: N = *a*1 + *a*2 + ... + *a**k* = *a*1 × *a*2 × ... × *a**k*.
>
> For example, 6 = 1 + 2 + 3 = 1 × 2 × 3.
>
> For a given set of size, *k*, we shall call the smallest N with this property a minimal product-sum number. The minimal product-sum numbers for sets of size, *k* = 2, 3, 4, 5, and 6 are as follows.
>
> *k*=2: 4 = 2 × 2 = 2 + 2
> *k*=3: 6 = 1 × 2 × 3 = 1 + 2 + 3
> *k*=4: 8 = 1 × 1 × 2 × 4 = 1 + 1 + 2 + 4
> *k*=5: 8 = 1 × 1 × 2 × 2 × 2 = 1 + 1 + 2 + 2 + 2
> *k*=6: 12 = 1 × 1 × 1 × 1 × 2 × 6 = 1 + 1 + 1 + 1 + 2 + 6
>
> Hence for 2≤*k*≤6, the sum of all the minimal product-sum numbers is 4+6+8+12 = 30; note that 8 is only counted once in the sum.
>
> In fact, as the complete set of minimal product-sum numbers for 2≤*k*≤12 is {4, 6, 8, 12, 15, 16}, the sum is 61.
>
> What is the sum of all the minimal product-sum numbers for 2≤*k*≤12000?



### Answer

```python
def recurse(p, s, n, start):
    k = n + p - s
    if k > kmax: return
    if p < N[k]: N[k] = p
    for x in xrange(start, 2*kmax//p+1):
        recurse(p*x, s+x, n+1, x)

kmax = 12000
N = [2*kmax] * (kmax+1)
recurse(1, 0, 0, 2)
print sum(set(N[2:]))
```

> 7587457



# Problem 89



> For a number written in Roman numerals to be considered valid there are basic rules which must be followed. Even though the rules allow some numbers to be expressed in more than one way there is always a "best" way of writing a particular number.
>
> For example, it would appear that there are at least six ways of writing the number sixteen:
>
> IIIIIIIIIIIIIIII
> VIIIIIIIIIII
> VVIIIIII
> XIIIIII
> VVVI
> XVI
>
> However, according to the rules only XIIIIII and XVI are valid, and the last example is considered to be the most efficient, as it uses the least number of numerals.
>
> The 11K text file, [roman.txt](https://projecteuler.net/project/resources/p089_roman.txt) (right click and 'Save Link/Target As...'), contains one thousand numbers written in valid, but not necessarily minimal, Roman numerals; see [About... Roman Numerals](https://projecteuler.net/about=roman_numerals) for the definitive rules for this problem.
>
> Find the number of characters saved by writing each of these in their minimal form.
>
> Note: You can assume that all the Roman numerals in the file contain no more than four consecutive identical units.



### Answer

```python
import re
count = 0
for s in open('roman.txt','r'):
	l = len(s)
	s = re.sub('IIII','IV',s)
	s = re.sub('XXXX','XL',s)
	s = re.sub('CCCC','CD',s)
	s = re.sub('VIV','IX',s)
	s = re.sub('LXL','XC',s)
	s = re.sub('DCD','CM',s)
	count += l - len(s)
print (count)
```

> 743



# Problem 90



> Each of the six faces on a cube has a different digit (0 to 9) written on it; the same is done to a second cube. By placing the two cubes side-by-side in different positions we can form a variety of 2-digit numbers.
>
> For example, the square number 64 could be formed:
>
> ![img](https://projecteuler.net/project/images/p090.png)
>
> In fact, by carefully choosing the digits on both cubes it is possible to display all of the square numbers below one-hundred: 01, 04, 09, 16, 25, 36, 49, 64, and 81.
>
> For example, one way this can be achieved is by placing {0, 5, 6, 7, 8, 9} on one cube and {1, 2, 3, 4, 8, 9} on the other cube.
>
> However, for this problem we shall allow the 6 or 9 to be turned upside-down so that an arrangement like {0, 5, 6, 7, 8, 9} and {1, 2, 3, 4, 6, 7} allows for all nine square numbers to be displayed; otherwise it would be impossible to obtain 09.
>
> In determining a distinct arrangement we are interested in the digits on each cube, not the order.
>
> {1, 2, 3, 4, 5, 6} is equivalent to {3, 6, 4, 1, 2, 5}
> {1, 2, 3, 4, 5, 6} is distinct from {1, 2, 3, 4, 5, 9}
>
> But because we are allowing 6 and 9 to be reversed, the two distinct sets in the last example both represent the extended set {1, 2, 3, 4, 5, 6, 9} for the purpose of forming 2-digit numbers.
>
> How many distinct arrangements of the two cubes allow for all of the square numbers to be displayed?



### Answer

```python
from itertools import combinations

pairs = [(0, 1), (0, 4), (0, 6), (1, 6), (2, 5), (3, 6), (4, 6), (6, 4), (8, 1)]

count = 0
for A, B in combinations(combinations(list(range(9))+[6], 6), 2):
    if all((x in A and y in B) or (x in B and y in A) for x, y in pairs):
        count += 1

print(count)
```

> 1217




