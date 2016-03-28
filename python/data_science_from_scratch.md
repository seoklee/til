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
