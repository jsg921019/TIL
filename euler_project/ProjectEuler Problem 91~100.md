# Problem 91



> The points P (*x*1, *y*1) and Q (*x*2, *y*2) are plotted at integer co-ordinates and are joined to the origin, O(0,0), to form ΔOPQ.
>
> ![img](https://projecteuler.net/project/images/p091_1.png)
>
> There are exactly fourteen triangles containing a right angle that can be formed when each co-ordinate lies between 0 and 2 inclusive; that is,
> 0 ≤ *x*1, *y*1, *x*2, *y*2 ≤ 2.
>
> ![img](https://projecteuler.net/project/images/p091_2.png)
>
> Given that 0 ≤ *x*1, *y*1, *x*2, *y*2 ≤ 50, how many right triangles can be formed?
>
> 

### Answer

```Python
from itertools import product
bound = 50
count = 0
for x1,y1,x2,y2 in product(range(bound+1), repeat=4): 
    if (x1,y1) > (x2, y2) :
        a,b,c = sorted([x1**2 + y1**2, x2**2 + y2**2, (x1-x2)**2 + (y1-y2)**2])
        if a!=0 and a + b == c : count+=1
print(count)
```

> 14234



### Answer : Faster

```python
from itertools import product
from math import gcd
bound = 50
count = 3*(bound)**2
for x,y in product(range(1,bound+1), repeat=2): 
    y_, x_ = x//gcd(x, y), y//gcd(x, y)
    count += min((50-y)//y_, x//x_) + min((50-x)//x_, y//y_)
print(count)
```

> 14234



# Problem 92



> A number chain is created by continuously adding the square of the digits in a number to form a new number until it has been seen before.
>
> For example,
>
> 44 → 32 → 13 → 10 → **1** → **1**
> 85 → **89** → 145 → 42 → 20 → 4 → 16 → 37 → 58 → **89**
>
> Therefore any chain that arrives at 1 or 89 will become stuck in an endless loop. What is most amazing is that EVERY starting number will eventually arrive at 1 or 89.
>
> How many starting numbers below ten million will arrive at 89?



### Answer

```Python

```

> 







# Problem 93



> 



### Answer

```Python

```

> 







# Problem 94



> 



### Answer

```Python

```

> 







# Problem 95



> 



### Answer

```Python

```

> 







# Problem 96



> 



### Answer

```Python

```

> 







# Problem 97



> 



### Answer

```Python

```

> 







# Problem 98



> 



### Answer

```Python

```

> 







# Problem 99



> 



### Answer

```Python

```

> 







# Problem 100



> 



### Answer

```Python

```

> 

