# Pandas-Factorplots-for-Dataframes-and-Series
Matplotlib, Pandas factorplot functions

Functions to create factorplots in matplotlib for Pandas dataframes and series of real numbers.



```python
import math
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from matplotlib.ticker import FuncFormatter

%matplotlib inline
```


```python
data = [[0.967428, -1.884223,  1.330792,  0.607123],
        [0.044540, -2.298559,  0.662756,  0.616026],
        [0.397169,  0.714731,  0.709576, -0.018898]]
```


```python
df = pd.DataFrame(data, index = ['1-1-2010','1-1-2011', '1-1-2012'],columns = ['Asset 1', 'Asset 2', 'Asset 3', 'Asset 4'])
ser = pd.Series([-0.967428, 0.04454, 0.397169, 0.22], index=['Asset 1', 'Asset 2', 'Asset 3', 'Asset 4'])
```


```python
d1 = {'colors': {'Asset 1': '#d38330',
      'Asset 2': '#cdbc45',
      'Asset 3': '#c54a36',
      'Asset 4': '#ac7850'},
      'f_name': './myfile1',
      'f_typ': 'pdf',
      'pct': True}

d2 = {'colors': {'Asset 1': '#d38330',
      'Asset 2': '#cdbc45',
      'Asset 3': '#c54a36',
      'Asset 4': '#ac7850'},
      'f_name': './myfile2',
      'f_typ': 'pdf',
      'pct': True}

d3 = {'colors': {'Asset 1': '#d38330',
      'Asset 2': '#cdbc45',
      'Asset 3': '#c54a36',
      'Asset 4': '#ac7850'},
      'f_name': './myfile3',
      'f_typ': 'pdf',
      'pct': True}
```


```python
def column_fun(df, d):
    fig, ax = plt.subplots(figsize=(10, 8))
    
    if df.get_ftype_counts()[0] > 1:
        n_groups = df.shape[1]
    else:
        n_groups = len(df)
    
    index = np.arange(1.2, n_groups + 1)
    bar_width = 0.3
    rows = float(len(df.index))
    saturation = [(x/100.0 - 0.1) for x in range(100,1,-int(100./(rows)))][::-1]
    
    # Plot factorplot for pandas dataframe
    if df.get_ftype_counts()[0] > 1:
        for i, a in zip(range(0,n_groups + 1), saturation):
            if i == 0:
                plt.bar(index, df.loc[df.index[i]], bar_width - 0.05,
                        alpha = a,
                        color = d['colors'].values(),
                        edgecolor = d['colors'].values(),
                        linewidth = 1,
                        zorder = 10)

                plt.bar(index, df.loc[df.index[i]], bar_width - 0.05,
                        color = 'none',
                        edgecolor = d['colors'].values(),
                        linewidth = 1,
                        zorder = 10)
            else: 
                plt.bar(index + bar_width * i, df.loc[df.index[i]], bar_width - 0.05,
                        alpha = a,
                        color = d['colors'].values(),
                        edgecolor = d['colors'].values(),
                        linewidth = 1,
                        zorder = 10)

                plt.bar(index + bar_width * i, df.loc[df.index[i]], bar_width - 0.05,
                        color = 'none',
                        edgecolor = d['colors'].values(),
                        linewidth = 1,
                        zorder = 10)

        plt.xticks(index + bar_width * 1.5, list(df.columns),
                   fontsize=12)
        ax.set_xticks(index[:-1] + bar_width * 3, minor = True)
        ax.xaxis.grid(True, which = 'minor', linestyle = "-", color = '0.75', zorder = 1)
        ax.yaxis.grid(True, linestyle = "-", color = '0.75', zorder = 1)
        
    else:   # Plot factorplot for pandas series
        plt.bar(index + bar_width + 0.5, df, bar_width * 2.5 - 0.05,
                alpha=0.9,
                color=d['colors'].values(),
                edgecolor=d['colors'].values(),
                linewidth=1)
        plt.xticks(index + bar_width * 4, df.index,
                   fontsize=12)
        ax.set_xticks(index + bar_width * 2.25, minor = True)
        ax.xaxis.grid(True, which = 'minor', linestyle = "-", color = '0.8', zorder = 1)
        ax.yaxis.grid(True, linestyle = "-", color = '0.8', zorder = 1)
        
    def to_percent(y, position):
        # Ignore the passed in position. This has the effect of scaling the default
        # tick locations.
        s = str(y)

        # The percent symbol needs escaping in latex
        if plt.rcParams['text.usetex'] == True:
            return s + r'$\%$'
        else:
            return s + '%'
        
    # Check if output 'y' scale should be percentage
    if d['pct']:
        # Create the formatter using the function to_percent.
        formatter = FuncFormatter(to_percent)

        # Set the formatter
        plt.gca().yaxis.set_major_formatter(formatter)
    
    # Save fig given config dictionary
    plt.savefig(d['f_name'] + "." + d['f_typ'], format = d['f_typ'])
```


```python
def row_fun(df, d):
    fig, ax = plt.subplots(figsize=(10, 8))

    n_groups = len(df)
    index = np.arange(1.2, n_groups + 1)
    bar_width = 0.3
    rows = float(len(df.index))
    names = list(df.index)
    saturation = [(x/100.0 - 0.1) for x in range(100,1,-int(100./(rows)))][::-1]
    
    # Plot barh plot for pandas series
    plt.barh(index, df.values, 
             align = 'center', 
             alpha = 0.9, 
             color = d['colors'].values(), 
             edgecolor = d['colors'].values(), 
             label = names,
             zorder = 10)
    
    plt.yticks(visible=False)
    for i in range(n_groups):
        if df.loc[df.index[i]] > 0:
            ax.text(-len(names[i])/50.0, 1.2 + i, 
                    names[i],
                    fontsize = 12)
        else:
            ax.text(len(names[i])/100.0, 1.2 + i,
                    names[i],
                    fontsize = 12)

    def to_percent(y, position):
        # Ignore the passed in position. This has the effect of scaling the default
        # tick locations.
        s = str(y)

        # The percent symbol needs escaping in latex
        if plt.rcParams['text.usetex'] == True:
            return s + r'$\%$'
        else:
            return s + '%'
    
    # Check if output 'y' scale should be percentage
    if d['pct']:
        # Create the formatter using the function to_percent.
        formatter = FuncFormatter(to_percent)

        # Set the formatter
        plt.gca().xaxis.set_major_formatter(formatter)
    else:
        m = int(math.ceil(max(abs(ser))))
        plt.xticks(np.arange(-m, m, 0.5))
    plt.gca().yaxis.set_major_locator(plt.NullLocator())
    ax.set_yticks(index[:-1] + bar_width * 1.5 + 0.05, minor = True)
    ax.yaxis.grid(True, which = 'minor', linestyle = "-", color = '0.8', zorder = 1)
    ax.xaxis.grid(True, linestyle = "-", color = '0.8', zorder = 1)

    # Save fig given config dictionary
    plt.savefig(d['f_name'] + "." + d['f_typ'], format = d['f_typ'])
```


```python
column_fun(df, d1)
```


![pdf](https://github.com/mastraut/Pandas-Factorplots-for-Dataframes-and-Series/blob/master/myfile1.pdf)



```python
column_fun(ser, d2)
```


![pdf](https://github.com/mastraut/Pandas-Factorplots-for-Dataframes-and-Series/blob/master/myfile2.pdf)



```python
row_fun(ser, d3)
```


![pdf](https://github.com/mastraut/Pandas-Factorplots-for-Dataframes-and-Series/blob/master/myfile3.pdf)



```python

```
