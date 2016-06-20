#Data Science from Scratch

### Vector opperation in python

Use numpy. but if you want to DIY


~~~ python
#if two vectors are given
def vector_add(v, w):
	return [v_i + w_i for v_i, w_i in zip(v, w)]
	
#multiple vectors
def vector_sum(vectors):
	return reduce(vector_add, vectors)
~~~

Dot product measures how far the vector v extends in the w direction. (length of the vector you'd get if you projected v onto w)

~~~ python
#dot product

def dot(v, w):
	"""v_1 * w_1 + ... v_n * w_n """ 
	return sum(v_i * w_i 
				 for v_i, w_i in zip(v, w))

def sum_of_squres(v):
	"""v_1 * v_1 + ... + v_n * v_n"""
	return dot(v, v)
~~~

magnitude, or length, of the vector

~~~ python
import math

def magnitude(v):
	return math.sqrt(sum_of_squares(v))
~~~

distances

~~~ python
def squared_distance(v, w):
	"""(v_1 - w_1) ** 2 + ... + (v_n - w_n) ** 2 """
	return sum_of_squares(vector_subtract(v, w));

def distance(v, w):
	"""(v_1 - w_1) ** 2 + ... + (v_n - w_n) ** 2 """
	return math.sqrt((vector_subtract(v, w));
~~~

---

### Statistics

#### Central Tendencies

- mean, median, and mode
- quantile

~~~ python
def quantile(x, p):
	p_index = int(p * len(x))
	return sorted(x)[p_index]
~~~

#### Dipersion

- range
- variance
- std devation
All three are susceptible to the outliers. Use interquartile range


~~~ python
def de_mean(x):
	"""translate x by subtracting its mean) """
	x_bar = mean(x)
	return [x_i - x_bar for x_i in x]

def variance(x):
	n = len(x)
	deviation = de_mean(x)
	return sum_of_squares(deviations) / (n - 1)
	
def std_devation(x):
	return math.sqrt(variance(x))

def interquartile_range(x):
	return quantile(x, 0.75) - quantile(x, 0.25)
~~~

#### Correlation

variance measures how a single variable deviates from its mean, covariance measures how two variables vary in tandem from their means.

- A "large" positive covariance means that x tends to be large when y is large and small when y is small.
- A "large" negative covariance means x tends to be small when y is large and vice versa.
- A covariance near 0 means no relationship

covariance is harder to interpret... use correlation, which divides by std deviation of both variables

~~~ python
def covariance(x, y):
	n = len(x)
	return dot(de_mean(x), de_mean(y)) / (n - 1)

def correlation(x, y):
	stdev_x = std_deviation(x)
	stdev_y = std_deviation(y)
	if stdev_x > 0 and stdev_y > 0
		return covariance(x, y) / sstdev_x / stdev_y
	else:
		return 0
~~~

#### Simpson's Paradox

correlations can be misleading when confounding variables are ignored. Correlation is measuring the relationship between your two variables all else being equal.

remember. Causastion != correlattion

to feel assured about casualty, conduct randomized trials. Get two group sof similar semographics a different experience.

---

### Probability

if two events, E and F, are indepenedent. The probability of both events happening is

~~~
P(E, F) = P(E)P(F)
~~~

If E and F are not necessarily independent. Then we define the probability of E "conditional on F" as

~~~
P(E|F) = P(E,F)/P(F)

or

P(E,F) = P(E|F) P(F)
~~~

if they are independent

~~~
P(E|F) = P(E)
~~~


#### Bayes's Theorem

~~~
P(E|F) = P(E,F)/P(F) = P(F|E) P(E) / P(F)

P(F) = P(F,E) + P(F, !E)

so...

P(E|F) = P(F|E) P(E) / [P(F|E)P(E) + P(F|!E)P(!E)]
~~~

- ex.
	- disease affect 1 in every 10,000 people. correct result at 99%
	- given P(D) is you have a disease and P(T) is positive
	- P(D|T) = P(T|D) P(D) / (P(T|D)P(D) + P(T|!D)P(!D))
		- = .99 * .0001 / ( .99 * .0001 + .01 * .9999)
	- P(D|T) = .98 %
	- if you have one million people. You can expect 100 of them to have the disease, and 99 of those 100 to the test positive. 
	- 
