**dstack is an open-source framework for Python and R for managing data science artifacts, and for building data science applications.**

![Action Status](https://github.com/dstackai/dstack-server/workflows/Build/badge.svg)  [![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) [![PyPI version](https://badge.fury.io/py/dstack.svg)](https://badge.fury.io/py/dstack) [![Discord Chat](https://img.shields.io/discord/687649691688501294.svg)](https://discord.gg/)  

How is dstack different from other frameworks:
 - It is designed for data scientists and doesn't require development skills to build applications.   
 - It simplifies the process of creating applications by leveraging a) a declarative approach to defining application components; b) a tight integration with data science frameworks and tools.

The key features of dstack:
- Managing data science artifacts (e.g. tracking revisions of datasets, pushing and pulling datasets)
- Building data science applications (this currently includes dashboards and reports; the support for ML models is coming soon) 

## How dstack works

The framework consists of the following parts:
- Client packages for Python ([dstack-py](https://github.com/dstackai/dstack-py)) an R ([dstack-r](https://github.com/dstackai/dstack-r)). These packages can be used from either notebooks or scripts to push data to dstack.
- A server application ([dstack-server](https://github.com/dstackai/dstack-server)). It handles the requests from the Client packages, and serve data applications. The application can run locally or in Docker.  

There are two general use-cases for dstack:

### Use case #1: Managing data science artifacts

The results of a data science process normally may include all kind of datasets, visualizations, ML models, text and binary files, etc. 

The `dstack-server` application serves as a storage engine to store these artifacts and track their revisions and meta-data.
The `dstack-py` and `dstack-r` packages provides the capabilities to push and pull these artifacts to and from the corresponding instance of `dstack-server`.

The dstack framework uses the following abstractions to represent artifacts:

- A *Stack* has a human-readable name (*Stack Name*), the ID of its latest frame (*Frame* or *Stack Head*), and the name of the user it belongs to (*User*) 
- A *Frame* represents a unique revision of a *Stack*. It has a unique ID (*Frame ID*), a human-readable description (*Frame Description*),and a set of *Attachments*.  
- An *Attachment* has a data (*Attachment Data*) which can be either plain or binary, a MIME type of the data (*Attachment Content Type*),
   an application name which can interpret this type of data (*Attachment Application*), 
   and most importantly a key/value dictionary with the metadata associated with the *Attachment* (*Attachment Params*).
- A *User* has a human-readable name (*Username*), and a secret token for authorization (*User Token*).
- A *User Profile* has a human readable name (*Profile Name*), a *Username*, a *User Token*, and the URL of the server API (*Server URL*). 
    User profiles are configured locally to authorize the requests from the `dstack` packages.
   
The workflow in this case is the following:

- Install the `dstack` package (e.g. via `pip` or `conda`)
- Runs `dstack-server` (e.g. using the `dstack server` dstack CLI command)
- Configure a dstack profile (e.g. using the `dstack config` dstack CLI command) 
- Push or pull *Stack Frames* from Jupyter notebooks, Rmarkdown files or any Python or R scripts. Pushing and pulling is done via the `dstack` packages for Python or R.

The information on how to push artifacts to a dstack server, can be found in the [dstack-py](https://github.com/dstackai/dstack-py) an [dstack-r](https://github.com/dstackai/dstack-r) repositories correspondingly. 

### Use case #2: Building data science applications

A data science application is a specific kind of applications that solves domain-specific problems using data and data-science methods.
 These data science methods may include data-wrangling, data visualizations, statistical modeling, machine learning, etc.  

There are several general use-cases for such data science applications:

1. *Interactive reports* – a set data visualizations and interactive widgets, combined using a certain layout 
2. *Live dashboards* – applications that fetch data from various data sources, turn it into visualizations and combine using a certain layout (not supported yet)
3. *Machine learning applications* – applications that let users to interact with ML models (not supported yet)

Currently, dstack supports only *Interactive reports*. The support for *Live dashboards* and *Machine learning applications* is coming soon.
    
#### Interactive reports

An interactive report can be currently built via the user interface of the `dstack-server` application.
    In order to create a report, one must first create *Stacks* by pushing data via the `dstack` packages from Python or R.
    The data can be dataframes (pandas, tidyverse, etc) or plots (matplotlib, plotly, ggplot, etc). 
    Once the *Stacks* are pushed, the user must open the `dstack-server` application in a browser, go to *Dashboards*,
    click *New dashboard*, and then select the *Stacks*. The `dstack-server` will automatically generate a dashboard
    out of the chosen *Stacks*.
    
It's important, that if any of the *Stacks* has multiple *Attachments* with parameters, the `dstack-server` application
    will automatically generate interactive widgets to select these parameters and update the dashboard accordingly.
    
The information on how to push artifacts to a dstack server, can be found in the [dstack-py](https://github.com/dstackai/dstack-py) an [dstack-r](https://github.com/dstackai/dstack-r) repositories correspondingly.
    
An example of such an interactive report can be seen [here](https://dstack.ai/cheptsov/d/505d2087-9adc-49e3-88de-6b46079c394f).

## Installation

The `dstack` package can be easily installed via either pip or conda:

```bash
pip install dstack
```

```bash
conda install dstack -c dstack.ai
```

The package comes with a command line tool called `dstack`. This command line tool can be used to configure local profiles, credentials, and to run a local server. 

If you're using R and don't need the command line tool, you can install the `dstack` package for R via the following command:

```R
install.packages("dstack")
```

## Quick start

### Run a server

In order to run a server locally, one must run this command line:

```bash
dstack server --start
```

You'll see the following output:

```
To access the dstack server, open one of these URLs in the browser:
		http://localhost:8080/auth/verify?user=dstack&code=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&next=/
	or	http://127.0.0.1:8080/auth/verify?user=dstack&code=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&next=/

If you're using Python, use the following command line command to configure your dstack profile:
	pip install dstack
	dstack config --token xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --user dstack --server http://localhost:8080/api

If you're using R, use the following R command to configure your dstack profile:
	install.packages("dstack")
	dstack::configure(user = "dstack", token = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", persist = "global", server = "http://localhost:8080/api")
```

Note, in your case instead of `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` you'll see your personal code.

### Configure a user profile

In order to send requests to the locally running server, one must run the command suggested in the output:

```bash
dstack config --token xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --user dstack --server http://localhost:8080/api
```

### Push artifacts

Uploading datasets and visualization to the server is done via the `dstack` packages available for both Python and R. 
    These packages can be used from Jupyter notebooks, RMarkdown, Python and R scripts and applications.
    
Once data is pushed to the server, it can be accessed via the URL returned in the response, 
    for example `http://localhost:8080/<username>/<stackname>` or via the web application's interface.
    
The pushed *Stacks* can be combined into interactive *Dashboards* via the web application's interface.
    
The `dstack` packages can be used with `pandas`, `tidyverse`, `matplotlib`, `ggplot2`, `bokeh` and `plotly`.  
    The `commit` and `push_frame` methods accept `pandas.core.frame.DataFrame`, `data.frame`, `data.table`, `tibble`, 
    `plotly.graph_objs._figure.Figure`, `bokeh.plotting.figure.Figure`, etc.

#### Push a static visualization 

Here's a simple example of the code that pushes a static visualization:

**Python**
```python
import matplotlib.pyplot as plt
from dstack import push_frame

fig = plt.figure()
plt.plot([1, 2, 3, 4], [1, 4, 9, 16])

push_frame("simple", fig, "My first plot")
```

**R**
```R
library(ggplot2)
library(dstack)

df <- data.frame(x = c(1, 2, 3, 4), y = c(1, 4, 9, 16))
image <- ggplot(data = df, aes(x = x, y = y)) + geom_line()

push_frame("simple", image, "My first plot")
```

#### Push an interactive visualization 

In some cases, you want to have plots that are interactive and that can change when the user change its parameters. 
    Suppose you want to publish a line plot that depends on the value of the parameter `Coefficient`

**Python**
```python
import matplotlib.pyplot as plt
from dstack import create_frame

def line_plot(a):
    xs = range(0, 21)
    ys = [a * x for x in xs]
    fig = plt.figure()
    plt.axis([0, 20, 0, 20])
    plt.plot(xs, ys)
    return fig


frame = create_frame("line_plot")
coeff = [0.5, 1.0, 1.5, 2.0]

for c in coeff:
    frame.commit(line_plot(c), 
    f"Line plot with the coefficient of {c}", {"Coefficient": c})

frame.push()
```

**R**
```R
library(ggplot2)
library(dstack)

line_plot <- function(a) {
    x <- c(0:20)
    y <- sapply(x, function(x) { return(a * x) })
  
    df <- data.frame(x = x, y = y)
    plot <- ggplot(data = df, aes(x = x, y = y)) + 
        geom_line() + xlim(0, 20) + ylim(0, 20)
    return(plot)

}

coeff <- c(0.5, 1.0, 1.5, 2.0)
frame <- create_frame(stack = "line_plot")

for(c in coeff) {
    frame <- commit(frame, line_plot(c), 
        paste0("Line plot with the coefficient of ", c), 
        Coefficient = c)
}

push(frame)
```

#### Push a single dataset

Here's an example of the code that pushes a single dataset:
 
**Python**
```python
import pandas as pd
import numpy as np
from dstack import push_frame

dates = pd.date_range('20130101', periods=6)
df = pd.DataFrame(np.random.randn(6, 4), index=dates, columns=list('ABCD'))

push_frame("static_dataset_example", df, "static dataset")
```

**R**
```R
library(ggplot2)
library(dstack)

data("midwest", package = "ggplot2")
push_frame("simple", midwest, "My first dataset")
```

### Pull artifacts

#### Pull a single dataset

Here's an example of the code that pulls a dataset from the server:

**Python**
```python
import pandas as pd
from dstack import pull

df = pull("/<username>/<stackname>")
df.head()
```

**R**
```R
library(dstack)

df <- read.csv(pull("/<username>/<stackname>"))
head(df)
```

Currently, the `dstack` packages are compatible with `pandas.core.frame.DataFrame`, `data.frame`, `data.table`, and `tibble`.

## Roadmap

Here's a list of things not implemented yet but considered for the nearest time:

- Push and pull for multiple large files (aka Git for data)
- Stacks that can run user code (aka Callbacks) – using these stacks it will be possible to implement Live Dashboards
- User interfaces for published Machine Learning models (so users may interace with ML models from the web application)