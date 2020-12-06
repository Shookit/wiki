Math
====

Math symbols
------------

Math notation is the worst. Every domain has its own special language and interpretation. Here are some common ones.

- `↦`: maps to; used when defining functions and performing maps
- `~`: has the distribution (of) (domain: statistics)
    - `X∼N(0,1)`: the stochastic (random) variable X has the distribution `N(0,1)` (i.e.,the standard normal distribution)
    - `X∼Y`: X has the same distribution as Y, if X and Y are stochastic variables
- `∈`:  is an element of
- `|`: such that
- `σ`: standard deviation (domain: statistics, but this is pervasive)
- `⊆`: is a subset of
- `Ǝ`: exists
- `∀`: true for all
- `𝔸, 𝔹, ℂ`: Blackboard bold, a notation for representing number sets (`ℝ`=real, `𝕎`=integer, etc.)
- `𝐚, 𝐛, 𝐜`: Normal bolded variable denotes vectors
- `ŷ`: hat variables denotes unit vector


Complex numbers
---------------

```
z=x+iy
```

Mathematicians struggled with the concept of a negative number for years; it's a mathematically-defined construct. Complex numbers are this as well. They involve a rotation; each 90-degree rotation is 'i', somewhat similar to radians. However, in the real-imaginary plane, only real numbers are along the number line; any rotation other than i^0, i^2, i^4, etc. has some imaginary component. The multiplicative progression of "-1 is 1, -1...". For imaginary numbers it is "i, -1, -i, 1, i...". Hence, 4 rotations is like not rotating at all.

Imaginary numbers allow us to do two multiplications and to get -1. Rotating 90 degrees each time allows us to do this (rather than multiplying by a negative number, which is a 180 degree rotation along the real number line).


Linear algebra
--------------

### Definitions

- `Vectors` (row or column): 1-D matrix, symbolized by lowercase letters w/ vector arrows
- `Matrices`: 2-D matrix, symbolized by uppercase letters
- `Particular solution: constant part of a solution to a linear system.
- `Unrestricted combination: part of solution which is dependent on some variables (if exists = infinite solutions)
- `Homogeneous system`: system w/o constant values. We can study how to reduce a linear systems by instead studying how to reduce the associated homogeneous system; General = Particular + Homogeneous
- `Dot product` = `inner product` = `scalar product`
- `eigenvector`: 1D vector which can be multiplied by a square matrix and give the original square matrix (multiplied by some scalar). If `A * x = lamba * x` (A being matrix, x being vector, lamba being some scalar), x is an eigenvector for A, lambda is an eigenvalue for A.
- `eigenvalue`: the scalar mentioned above
- `Norm`: length of a vector
    - `||x|| = ||x||_2`: euclidean norm of x (root of squares)
    - `||x||_1`: manhattan norm = sum of absolute values of the vector
    - `||x||_p`: `sum(|xi|^p)^1/p`

### Concepts

- `R^2`: all combinations of ordered 2-tuples such that x1 and x2 are members of R (2d space): `R^2={(x1,x2)|x1,x2∈R}`
- `R^n`: all combinations of ordered n-tuples such that all are members of R (nd space)
- `2p1+p2` means double row 1, add it to row 2
- Vectors from `R^n` are orthogonal, that is, perpendicular, if and only if their dot product is zero.
- `covariance`: For each data item, multiply the difference between the x value and the mean of x, by the the difference between the y value and the mean of y. Add all these up, and divide by (n-1)". ("If positive, both dimensions increase together, vice-versa")
- `covariance matrix`:  instead of just doing one covariance, do one for every combination of dimensions not to be confused with cross-correlation/autocorrelation


### Echelon form

- In each row of a system, the first variable with a nonzero coefficient is the row’s leading variable.
- A matrix or linear system is in reduced echelon form if, in addition to being in echelon form, each leading entry is a one and is the only nonzero entry in its column.
- Two matrices that are inter-reducible by elementary row operations are row equivalent.
- Every matrix is row equivalent to one and only one reduced echelon form matrix
- In an echelon form linear system the variables that are not leading are free.
- A system is in echelon form if each leading variable is to the right of the leading variable in the row above it (except for the leading variable in the first row).

Statistics
----------

### Coefficient of variation

Basically a normalized standard deviation, allows you to compare standard deviations between data sets w/ different means. stdev/mean.


### T- and Z-tests

- Z-test is a statistical hypothesis test that follows a normal distribution while T-test follows a Student’s T-distribution.
- A T-test is appropriate when you are handling small samples (n `<` 30) while a Z-test is appropriate when you are handling moderate to large samples (n `>` 30).
- T-test is more adaptable than Z-test since Z-test will often require certain conditions to be reliable. Additionally, T-test has many methods that will suit any need.
- T-tests are more commonly used than Z-tests.
- Z-tests are preferred over T-tests when standard deviations are known.
- The t distribution looks similar to the normal curve. As the df increase, the t distribution approaches the standard normal distribution (=0.0, =1.0). The standard normal curve is a special case of the t distribution when df=. For practical purposes, the t distribution approaches the standard normal distribution relatively quickly, such that when df=30 the two are almost identical.


Calculus Notation
-----------------

Start with a function, calculate the difference in value between two points and divide by the size of the interval between the two. You can represent this as such:

```
f(x2) - f(x1) / x2 - x1
```

or

```
∆f(x) / ∆x (where ∆, delta, is the greek capital D and indicates an interval)
```

Now, take the limit as ∆x goes to zero, and you have the differential. This is indicated by using a lower case d instead of the ∆.

```
df(x) / dx
```

Now, if this operation is treated as an operator applied to a function, it is usually represented as

```
d / dx (applied to f(x))
```

Note that (typically in physics), you can also use the letter δ to indicate very small intervals and in general you would use the symbol ∂ to represent partial differentials. They are all variations of the letter D.

Exponential moving average
--------------------------

```
Scurr=alpha*Ycurr+(1-alpha)*Sprev
Y = series
S = EMA
alpha = weight (close to 0 = favors previous EMA value, close to 1 favors current series value)
```

Variables vs parameters
-----------------------

- variable = some defined, changing value (independent or dependent)
- parameter is a "variable", but isn't necessarily actually ever observed or based on real values (like weights in a model)

