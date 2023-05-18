---
title: "Fixing selections manually"
author: "Marcelo Araya-Salas"
date: "2017-08-03"
output: 
  md_document:
    variant: markdown_github
rmd_hash: dbc7686431eba87b

---

This short post shows how to use the `seltailor` function to adjust selection frequency and time 'coordinates' in an interactive and iterative manner.

To be able to run the code you need [warbleR](https://cran.r-project.org/package=warbleR) 1.1.9 or higher, which hasn't been released on CRAN and it's only available on github. It can be installed using the [devtools](https://cran.r-project.org/package=devtools) package as follows

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># install devtools and monitor if are not yet installed</span></span>
<span><span class='c'># install devtools if is not yet installed</span></span>
<span><span class='kr'>if</span><span class='o'>(</span><span class='o'>!</span><span class='s'>"devtools"</span> <span class='o'><a href='https://rdrr.io/r/base/match.html'>%in%</a></span> <span class='nf'><a href='https://rdrr.io/r/utils/installed.packages.html'>installed.packages</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>[</span>,<span class='s'>"Package"</span><span class='o'>]</span><span class='o'>)</span> <span class='nf'><a href='https://rdrr.io/r/utils/install.packages.html'>install.packages</a></span><span class='o'>(</span><span class='s'>"devtools"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>devtools</span><span class='nf'>::</span><span class='nf'><a href='https://remotes.r-lib.org/reference/install_github.html'>install_github</a></span><span class='o'>(</span><span class='s'>"maRce10/warbleR"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

<br>

## *seltailor* function

The function aims to provide an easy way to manually fix imperfect selections, as those that in some cases are obtain from automatic detection (e.g. from the `autodetec` function).

This function produces an interactive spectrographic view in which users can select new time/frequency coordinates the selections. 4 "buttons" are provided at the upper right side of the spectrogram that allow to stop the analysis ("stop"), go to the next sound file ("next"), return to the previous selection ("previous") or delete the current selection ("delete"). When a unit has been selected, the function plots dotted lines in the start and end of the selection in the spectrogram (or a box if *frange = TRUE*). Only the last selection is kept for each selection that is adjusted.

The function produces a .csv file (*seltailor_output.csv*) with the same information than the input data frame, except for the new time coordinates, plus a new column (*X\$tailored*) indicating if the selection has been tailored. The file is saved in the working directory and is updated every time the user moves into the next sound file (next sel "button") or stop the process (Stop "button"). It also return the same data frame as and object in the R environment. If no selection is made (by clicking on the 'next' button) the original time/frequency coordinates are kept. When resuming the process (after "stop" and re-running the function in the same working directory), the function will continue working on the selections that have not been analyzed. The function also displays a progress bar right on top of the sepctrogram.

First load the example data and recordings

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span> </span>
<span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='nv'><a href='https://marce10.github.io/warbleR/'>warbleR</a></span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/getwd.html'>setwd</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/tempfile.html'>tempdir</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/data.html'>data</a></span><span class='o'>(</span>list <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"Phae.long1"</span>, <span class='s'>"Phae.long2"</span>, <span class='s'>"Phae.long3"</span>, </span>
<span>              <span class='s'>"Phae.long4"</span>, <span class='s'>"selec.table"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long1</span>,<span class='s'>"Phae.long1.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long2</span>,<span class='s'>"Phae.long2.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long3</span>,<span class='s'>"Phae.long3.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long4</span>,<span class='s'>"Phae.long4.wav"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

Add some 'noise' to the selections so they are a bit off the actual song position

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nv'>selec.table</span><span class='o'>$</span><span class='nv'>start</span> <span class='o'>&lt;-</span> <span class='nv'>selec.table</span><span class='o'>$</span><span class='nv'>start</span> <span class='o'>+</span> <span class='nf'><a href='https://rdrr.io/r/stats/Normal.html'>rnorm</a></span><span class='o'>(</span>n <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/nrow.html'>nrow</a></span><span class='o'>(</span><span class='nv'>selec.table</span><span class='o'>)</span>, sd <span class='o'>=</span> <span class='m'>0.03</span><span class='o'>)</span></span>
<span><span class='nv'>selec.table</span><span class='o'>$</span><span class='nv'>end</span> <span class='o'>&lt;-</span> <span class='nv'>selec.table</span><span class='o'>$</span><span class='nv'>end</span> <span class='o'>+</span> <span class='nf'><a href='https://rdrr.io/r/stats/Normal.html'>rnorm</a></span><span class='o'>(</span>n <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/nrow.html'>nrow</a></span><span class='o'>(</span><span class='nv'>selec.table</span><span class='o'>)</span>, sd <span class='o'>=</span> <span class='m'>0.03</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

Now run the function on the selection table. For simplicity, it is run only on the first 4 selections

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'>
      flim = c(1,12), wl = 120, auto.next = FALSE, 
st <- seltailor(X =  selec.table[1:4,], 
      frange = TRUE)

View(st)
</code></pre>

</div>

And this is how it works

![gif1](/img/seltailor.noautonext.gif)

The `auto.next` argument can help speed up the process once users feel confortable with the function. The `pause` argument (not shown) controls how long the function waits until it moves into the next selection

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/unlink.html'>unlink</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/list.files.html'>list.files</a></span><span class='o'>(</span>pattern <span class='o'>=</span> <span class='s'>"csv$"</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>st2</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/seltailor.html'>seltailor</a></span><span class='o'>(</span>X <span class='o'>=</span>  <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>4</span>,<span class='o'>]</span>, </span>
<span>       flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>,<span class='m'>12</span><span class='o'>)</span>, wl <span class='o'>=</span> <span class='m'>120</span>, auto.next <span class='o'>=</span> <span class='kc'>TRUE</span>, </span>
<span>       frange <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/View.html'>View</a></span><span class='o'>(</span><span class='nv'>st2</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![gif1](/img/seltailor.autonext.gif)

The function has many more arguments to customize spectrograms. The `index` argument can be used to only fix a subset of the signals (while returning the whole data set). Check the function documentation for a full description of the additional arguments.

