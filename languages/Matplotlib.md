Matplotlib
==========

Setup
-----

```python
# Get a new figure
fig = matplotlib.pyplot.figure(figsize=(6.5, 4))

# Get a new plot (use 1, 1 if only 1 plot)
ax=fig.add_subplot(width, height, plotnum)

# Auto-arrange layout
fig.tight_layout()

# Save figure
fig.savefig("motivating1.pdf")

# Close figure (neglecting this may lead to memory leaks)
pyplot.close()
```

Labels and legends
------------------

```python
# Set label
ax.set_xlabel("Xlab")
ax.set_ylabel("Ylab")

# Label size
fontsize=4

# Padding size
labelpad=1

# Set title
ax.set_title("Title")

# Size
size=3
axis.title.set_fontsize(6)

# Set subplot legend
ax.legend([options])

# Location
loc="center left"

# Offset
bbox_to_anchor=(1,.5)

# Subplot axis limits
axis.set_xlim([0, 100])

# Ticks
axis.tick_params(labelsize=6)

# Set figure legend
fig.legend(ax.lines + ax2.lines, ['label1', 'label2', 'label3', [options]]

# Tick label options
ax.tick_params(axis='both', which='major', labelsize=3)
ax.tick_params(axis='both', which='minor', labelsize=3)

# Percentages on axis
def to_percent(y, position):

   s = str(100 * y)
   if matplotlib.rcParams['text.usetex']:
       return s + r'$\%$'
   else:
       return s + '%'

axis.get_xaxis().set_major_formatter(matplotlib.ticker.FuncFormatter(to_percent))
```

Plots
-----

### New x/y plot

```python
# Line plot
line = ax.plot(x, y, [options]) #Return value is a list of lines that were added.

# Scatter plot
line = ax.scatter(x, y, [options])
line = ax.scatter(x, y, marker="o", facecolor="blue", s=1, linewidth=0) #markers can be +, ., o; s=size; s can also be array of sizes, one for each point

# Color
c="black"

# Label
label="label"

# Transparent lines
alpha=.5

# Second y axis
ax2 = ax.twinx()
```

### Boxplot

```python
fig = matplotlib.pyplot.figure()
ax = fig.add_subplot(1, 1, 1)

data_labels = [1,2,3,4]
data = [[/1,2,3],_[2,5,7],_[9,10,13],_[2,5,17|1,2,3], [2,5,7], [9,10,13], [2,5,17]]

axis_rate.boxplot(data)
axis_rate.set_xticklabels(data_labels)

Use seaborn-specific boxplot for better looking plots
```

Seaborn
-------

Library built on top of matplotlib. Generally prettier looking, and has some interesting color palette support, but has some strange (nasty global) behavior.

```python
# Using a palette w/ more than 6 colors
# Need to explicitly state the number of colors being used:
seaborn.set_palette(seaborn.hls_palette(7), n_colors=7)

# Saturation/lightness
seaborn.set_palette(seaborn.hls_palette(l=.4, s=.9))

# If colors look different than you selected, it's likely because seaborn sometimes has defaults for lightness and saturation:
seaborn.boxplot(plot_data, axis, saturation=1)

# General
seaborn.set_palette(default_palette)
seaborn.set_style("whitegrid")
```
