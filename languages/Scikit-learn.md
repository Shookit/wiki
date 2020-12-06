Scikit-learn
============

Normalization
-------------

```python
import sklearn.linear_model
import sklearn.preprocessing
import numpy

#A single instance is x[0], y[0]
x = numpy.array([[/0.0,_1.0,_1000.0],_[0.0,_2.0,_2000.0|0.0, 1.0, 1000.0], [0.0, 2.0, 2000.0]])
y = numpy.array([4.0, 5.0])

#axis=0 normalizes so that each 'column' (feature) has a max of ~1
#axis=1 normalizes so that each 'row' (instance) has a max of ~1
x = sklearn.preprocessing.normalize(x, axis=0)

classifier = sklearn.linear_model.LogisticRegression()
classifier.fit(x, y)
```
