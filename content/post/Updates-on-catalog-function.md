---
title: "Updates on catalog function"
author: "Marcelo Araya-Salas"
date: "2017-07-31"
output: 
  md_document:
    variant: markdown_github
rmd_hash: f6167f176315654a

---

A [previous post](https://marce10.github.io/bioacoustics_in_R/2017/03/17/Creating_song_catalogs.html) described the new function `catalog`. Here are a few updates on `catalog` based on suggestions from [warbleR](https://cran.r-project.org/package=warbleR) users.

To be able to run the code you need [warbleR](https://cran.r-project.org/package=warbleR) 1.1.9 or higher, which hasn't been released on CRAN and it's only available in the most recent development version on github. It can be installed using the [devtools](https://cran.r-project.org/package=devtools) package as follows

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># install devtools if is not yet installed</span></span>
<span><span class='kr'>if</span><span class='o'>(</span><span class='o'>!</span><span class='s'>"devtools"</span> <span class='o'><a href='https://rdrr.io/r/base/match.html'>%in%</a></span> <span class='nf'><a href='https://rdrr.io/r/utils/installed.packages.html'>installed.packages</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>[</span>,<span class='s'>"Package"</span><span class='o'>]</span><span class='o'>)</span> <span class='nf'><a href='https://rdrr.io/r/utils/install.packages.html'>install.packages</a></span><span class='o'>(</span><span class='s'>"devtools"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>devtools</span><span class='nf'>::</span><span class='nf'><a href='https://remotes.r-lib.org/reference/install_github.html'>install_github</a></span><span class='o'>(</span><span class='s'>"maRce10/warbleR"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

And load the package and save the example sound files as .wav in the working directory

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span> </span>
<span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='nv'><a href='https://marce10.github.io/warbleR/'>warbleR</a></span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/data.html'>data</a></span><span class='o'>(</span>list <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"Phae.long1"</span>, <span class='s'>"Phae.long2"</span>, <span class='s'>"Phae.long3"</span>, </span>
<span>              <span class='s'>"Phae.long4"</span>, <span class='s'>"selec.table"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long1</span>,<span class='s'>"Phae.long1.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long2</span>,<span class='s'>"Phae.long2.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long3</span>,<span class='s'>"Phae.long3.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long4</span>,<span class='s'>"Phae.long4.wav"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

------------------------------------------------------------------------

### 1) Arguments "spec.mar", "lab.mar", "max.group.colors" and "group.tag" to color background of selection groups

The following code creates a catalog with 3 columns and 3 rows labeled with the sound file name and selection number (default `labels` argument)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>11</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, </span>
<span>        wl <span class='o'>=</span> <span class='m'>150</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog1](/img/Updates_Catalog_p1.png)

The new arguments can be used to add a background color that is shared by selections belonging to the same grouping variable level. The following code uses the "sound.files" column to color selection groups using "group.tag". It also makes use of the "spec.mar" to increase the colored areas around the spectrograms and "lab.mar" to shrink the area allocated for selection labels and tags

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># modify palette to have softer colors</span></span>
<span><span class='nv'>cmc</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>n</span><span class='o'>)</span> <span class='nf'><a href='https://rdrr.io/r/grDevices/palettes.html'>cm.colors</a></span><span class='o'>(</span><span class='nv'>n</span>, alpha <span class='o'>=</span> <span class='m'>0.5</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>11</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>150</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"sound.files"</span>, spec.mar <span class='o'>=</span> <span class='m'>0.4</span>, lab.mar <span class='o'>=</span> <span class='m'>0.8</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog2](/img/Updates_Catalog_p2.png)

Note that the selection tables are sorted to ensure that selection sharing the same grouping factor level are clumped together in the catalog.

In the example some sound files are highlighted with very similar colors, which makes the visual identification of groups difficult. Using a different palette might solve the problem. Alternatively, the "max.group.cols" argument can be used to set a maximum number of different colors (independent of the levels of the grouping variable) that will be recycled when the number of colors is smaller than the number of levels. This works as follows

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"sound.files"</span>, spec.mar <span class='o'>=</span> <span class='m'>0.4</span>, lab.mar <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>3</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog3](/img/Updates_Catalog_p3.png)

------------------------------------------------------------------------

### 2) Arguments "title", "by.row", "prop.mar", "box" and "rm.axes" to further customize catalog setup

As their names suggest, the arguments "title" and "rm.axes" allow users to add a title at the top of catalogs and remove the *x* and *y* axes. respectively:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"sound.files"</span>, spec.mar <span class='o'>=</span> <span class='m'>0.4</span>, lab.mar <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>3</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span>, </span>
<span>        title <span class='o'>=</span> <span class='s'>"This one has a title and no axes"</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog4](/img/Updates_Catalog_p4.png)

The argument "by.row" allows to fill catalogs either by rows (if TRUE) or by columns (if FALSE).

By column

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"sound.files"</span>, spec.mar <span class='o'>=</span> <span class='m'>0.4</span>, lab.mar <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>3</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span>, title <span class='o'>=</span> <span class='s'>"By column"</span>, </span>
<span>        by.row <span class='o'>=</span> <span class='kc'>FALSE</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog5](/img/Updates_Catalog_p5.png)

By row

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"sound.files"</span>, spec.mar <span class='o'>=</span> <span class='m'>0.4</span>, lab.mar <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>3</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span>, title <span class='o'>=</span> <span class='s'>"By row"</span>, </span>
<span>        by.row <span class='o'>=</span> <span class='kc'>TRUE</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog6](/img/Updates_Catalog_p6.png)

The argument "box" allows users to draw a rectangle around the spectrogram and corresponding labels and tags

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"sound.files"</span>, spec.mar <span class='o'>=</span> <span class='m'>0.4</span>, lab.mar <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>3</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span>, title <span class='o'>=</span> <span class='s'>"By row"</span>, </span>
<span>        by.row <span class='o'>=</span> <span class='kc'>TRUE</span>, tags <span class='o'>=</span> <span class='s'>"sel.comment"</span>, box <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog7](/img/Updates_Catalog_p7.png)

Finally, the argument "prop.mar" allows to add margins at both sides of the signals (when creating the spectrogram) that is proportional to the duration of the signal. For instance a value of 0.1 in a signal of 1s will add 0.1 s at the beginning and end of the signal. This can be particularly useful when the duration of signals varies a lot. In this example a margin equals to a third of signal duration is used:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>9</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>3</span>, ncol <span class='o'>=</span> <span class='m'>3</span>, </span>
<span>        height <span class='o'>=</span> <span class='m'>10</span>, width <span class='o'>=</span> <span class='m'>10</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"sound.files"</span>, spec.mar <span class='o'>=</span> <span class='m'>0.4</span>, lab.mar <span class='o'>=</span> <span class='m'>0.8</span>,</span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>3</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span>, title <span class='o'>=</span> <span class='s'>"By row"</span>, </span>
<span>        by.row <span class='o'>=</span> <span class='kc'>TRUE</span>, prop.mar <span class='o'>=</span> <span class='m'>1</span><span class='o'>/</span><span class='m'>3</span>, tags <span class='o'>=</span> <span class='s'>"sel.comment"</span>, box <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![upd.catalog8](/img/Updates_Catalog_p8.png)

