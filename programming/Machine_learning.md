Machine learning
================

Common algorithms
-----------------

- PCA - creates a "line of best fit" in all dimensions (normal regression minimizes the error in X or Y; PCA minimizes the error in X _and_ Y simultaneously (perpendicular to the fit line) using eigenvectors. For a high-dimensional set of data, the least descriptive components can be optionally discarded to reduce the dimensionality and required information to describe the data set.
- SVM - uses a line (plane in higher dimensions) to split a set of data into two pieces. Simplest method (on "separable" data) can use a single line to do this. On harder datasets, the data can't be cleanly separated with an element of dimensionality D-1 (e.g., a plane in a 3D data set). Using a kernel, data is transformed in a way which allows data to be fit with a single D-1 element.
- HMM - Goal is to recover a data sequence that is not immediately observable. Uses a state machine with transition probabilities.
- Adaboost
    - Take original data set, run a classifier
    - Increase weights on incorrectly classified data
    - Re-run classifier w/ weighted data
    - Repeat some number of times
    - Each of these weak classifiers are then assigned weights, which are used in conjunction with one another to get an overall prediction

Time series analysis
--------------------

- Euclidean distance preprocessing - these techniques can remove data which is similar in time, but has distortions in the dependent axis
    - Remove offsets
        - Q=Q-mean(Q)
        - C=C-mean(C)
    - Amplitude scaling
        - Q=(Q-mean(Q))/std(Q)
        - C=(C-mean(C))/std(C)
    - Linear trend
        - Find line of best fit
        - Subtract that line from the time series
    - Remove noise
- Dynamic time warping - finds distance between two signals, but allows for expanding/compressing certain regions in time; is able to find similarities between signals which have similar peaks and valleys (or whatever), but don't perfectly line up in time
    - Looks for similarity between two signals by mapping across a matrix and finding the minimum path between the two
- Also consider as features:
    - autocorrelation
    - zero crossings
    - ARIMA

Under/oversampling
------------------

- If doing classification on a very unbalanced data set (e.g., 100 True, 5 False) and using an unweighted algorithm, use under/oversampling. Randomly include repeats (or exclude) from the smaller (or larger) data set to make data categories even.
