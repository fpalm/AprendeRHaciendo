******************
Reportes dinámicos
******************

This post examines the features of R Markdown using knitr in Rstudio 0.96. This
combination of tools provides an exciting improvement in usability for
reproducible analysis. Specifically, this post

#. discusses getting started with R Markdown and knitr in Rstudio 0.96;
#. provides a basic example of producing console output and plots using R
Markdown;
#. highlights several code chunk options such as caching and controlling how
input and output is displayed;
#. demonstrates use of standard Markdown notation as well as the extended
features of formulas and tables; and
#. discusses the implications of R Markdown.

This post was produced with R Markdown. The source code is available here as a
gist. The post may be most useful if the source code and displayed post are
viewed side by side. In some instances, I include a copy of the R Markdown in
the displayed HTML, but most of the time I assume you are reading the source
and post side by side.

Getting started
---------------

To work with R Markdown, if necessary:

    Install R
    Install the lastest version of RStudio (at time of posting, this is 0.96)
    Install the latest version of the knitr package: install.packages("knitr")

To run the basic working example that produced this blog post:

    Open R Studio, and go to File - New - R Markdown

    If necessary install ggplot2 and lattice packages:
    install.packages("ggplot2"); install.packages("lattice")

    Paste in the contents of this gist (which contains the R Markdown file used
    to produce this post) and save the file with an .rmd extension

    Click Knit HTML


Prepare for analyses
--------------------

```{r }
set.seed(1234)
library(ggplot2)
library(lattice)
```


## Basic console output
To insert an R code chunk, you can type it manually or just press `Chunks - Insert chunks` or use the shortcut key. This will produce the following code chunk:

   ```{r}

   ```

Pressing tab when inside the braces will bring up code chunk options.

The following R code chunk labelled `basicconsole` is as follows:

   ```{r }
   x <- 1:10
   y <- round(rnorm(10, x, 1), 2)
   df <- data.frame(x, y)
   df
   ```

The code chunk input and output is then displayed as follows:

   ```{r basicconsole}
   x <- 1:10
   y <- round(rnorm(10, x, 1), 2)
   df <- data.frame(x, y)
   df
   ```

## Plots
Images generated by `knitr` are saved in a figures folder. However, they also appear to be represented in the HTML output using a [data URI scheme]( http://en.wikipedia.org/wiki/Data_URI_scheme). This means that you can paste the HTML into a blog post or discussion forum and you don't have to worry about finding a place to store the images; they're embedded in the HTML.

### Simple plot
Here is a basic plot using base graphics:

   ```{r }
   plot(x)
   ```

   ```{r simpleplot}
   plot(x)
   ```

Note that unlike traditional Sweave, there is no need to write `fig=TRUE`.


### Multiple plots
Also, unlike traditional Sweave, you can include multiple plots in one code chunk:

    ```{r }
    boxplot(1:10~rep(1:2,5))
    plot(x, y)
    ```

   ```{r multipleplots}
   boxplot(1:10~rep(1:2,5))
   plot(x, y)
   ```

### `ggplot2` plot
Ggplot2 plots work well:

   ```{r ggplot2ex}
   qplot(x, y, data=df)
   ```

### `lattice` plot
As do lattice plots:

   ```{r latticeex}
   xyplot(y~x)
   ```

Note that unlike traditional Sweave, there is no need to print lattice plots directly.

## R Code chunk features
### Create Markdown code from R
The following code hides the command input (i.e., `echo=FALSE`), and outputs the content directly as code (i.e., `results=asis`, which is similar to `results=tex` in Sweave).


    ```{r , results='asis', echo=FALSE}
    cat("Here are some dot points\n\n")
    cat(paste("* The value of y[", 1:3, "] is ", y[1:3], sep="", collapse="\n"))
    ```

   ```{r dotpointprint, results='asis', echo=FALSE}
   cat("Here are some dot points\n\n")
   cat(paste("* The value of y[", 1:3, "] is ", y[1:3], sep="", collapse="\n"))
   ```

### Create Markdown table code from R
   ```{r , results='asis', echo=FALSE}
   cat("x | y", "--- | ---", sep="\n")
   cat(apply(df, 1, function(X) paste(X, collapse=" | ")), sep = "\n")
   ```

   ```{r createtable, results='asis', echo=FALSE}
   cat("x | y", "--- | ---", sep="\n")
   cat(apply(df, 1, function(X) paste(X, collapse=" | ")), sep = "\n")
   ```

### Control output display
The folllowing code supresses display of R input commands (i.e., `echo=FALSE`)
and removes any preceding text from console output (`comment=""`; the default is `comment="##"`).

   ```{r echo=FALSE, comment="", echo=FALSE}
   head(df)
   ```

   ```{r echo=FALSE, comment="", echo=FALSE}
   head(df)
   ```



### Control figure size
The following is an example of a smaller figure using `fig.width` and `fig.height` options.

   ```{r , fig.width=3, fig.height=3}
   plot(x)
   ```

   ```{r smallplot, fig.width=3, fig.height=3}
   plot(x)
   ```

### Cache analysis
Caching analyses is straightforward.
Here's example code.
On the first run on my computer, this took about 10 seconds.
On subsequent runs, this code was not run.

If you want to rerun cached code chunks, just [delete the contents of the `cache` folder](http://stackoverflow.com/a/10629121/180892)

   ```{r , cache=TRUE}
   for (i in 1:5000) {
       lm((i+1)~i)
   }
   ```

## Basic markdown functionality
For those not familiar with standard [Markdown](http://daringfireball.net/projects/markdown/), the following may be useful.
See the source code for how to produce such points. However, RStudio does include a Markdown quick reference button that adequatly covers this material.

### Dot Points
Simple dot points:

* Point 1
* Point 2
* Point 3

and numeric dot points:

1. Number 1
2. Number 2
3. Number 3

and nested dot points:

* A
    * A.1
    * A.2
* B
    * B.1
    * B.2


### Equations
Equations are included by using LaTeX notation and including them either between single dollar signs (inline equations) or double dollar signs (displayed equations).
If you hang around the Q&A site [CrossValidated](http://stats.stackexchange.com) you'll be familiar with this idea.

There are inline equations such as $y_i = \alpha + \beta x_i + e_i$.

And displayed formulas:

$$\frac{1}{1+\exp(-x)}$$


knitr provides self-contained HTML code that calls a Mathjax script to display formulas.
However, in order to include the script in my blog posts I [took the script](https://gist.github.com/2716053) and incorporated it into my blogger template.
If you are viewing this post through syndication or an RSS reader, this may not work.
You may need to view this post on my website.

### Tables
Tables can be included using the following notation

A  | B | C
--- | --- | ---
1  | Male | Blue
2  | Female | Pink

### Hyperlinks

* If you like this post, you may wish to subscribe to [my RSS feed](http://feeds.feedburner.com/jeromyanglim).

### Images
Here's an example image:

![image from redmond barry building unimelb](http://i.imgur.com/RVNmr.jpg)


### Code
Here is Markdown R code chunk displayed as code:

   ```{r}
   x <- 1:10
   x
   ```


And then there's inline code such as `x <- 1:10`.