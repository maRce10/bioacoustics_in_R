---
title: "Creating song catalogs"
author: "Marcelo Araya-Salas"
date: "2017-03-17"
output: 
  md_document:
    variant: markdown_github
rmd_hash: c69d47a8588f3b61

---

When looking at geographic variation of songs we usually want to compare the spectrograms from different individuals and sites. This can be challenging when working with large numbers of signals, individuals and/or sites. The new \[warbleR\]((<https://cran.r-project.org/package=warbleR>) function `catalog` aims to simplify this task.

This is how it works:

-   The function plots a matrix of spectrograms from signals listed in a selection table (a data frame similar to the example data frame `selec.table` in [warbleR]((https://cran.r-project.org/package=warbleR))
-   The graphs are saved as image files in the working directory (or path provided)
-   Several images are generated if the number of signals don't fit in a single file
-   Spectrograms can be labeled or color tagged to facilitate exploring variation related to the parameter of interest (e.g. site, song type) A legend can be added to help match colors with tag levels and different color palettes can be used for each tag
-   The duration of the signals can be "fixed" so all the spectrograms have the same duration (and can be compared more easily)
-   Users can control the number of rows and columns as well as the width and height of the output image

Below is a short tutorial showing some of these features. To be able to run the code you need \[warbleR\]((<https://cran.r-project.org/package=warbleR>) 1.1.6 or higher, which has not been released on CRAN and it's only available in the most recent development version on github. It can be installed using the \[devtools\]((<https://cran.r-project.org/package=devtools>) package as follows

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#run it only if devtools isn't installed</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/install.packages.html'>install.packages</a></span><span class='o'>(</span><span class='s'>"devtools"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>devtools</span><span class='nf'>::</span><span class='nf'><a href='https://remotes.r-lib.org/reference/install_github.html'>install_github</a></span><span class='o'>(</span><span class='s'>"maRce10/warbleR"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

Load the package and save the example sound files as .wav in the working directory

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span> </span>
<span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='nv'><a href='https://marce10.github.io/warbleR/'>warbleR</a></span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/data.html'>data</a></span><span class='o'>(</span>list <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"Phae.long1"</span>, <span class='s'>"Phae.long2"</span>, <span class='s'>"Phae.long3"</span>, </span>
<span>              <span class='s'>"Phae.long4"</span>, <span class='s'>"selec.table"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long1</span>,<span class='s'>"Phae.long1.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long2</span>,<span class='s'>"Phae.long2.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long3</span>,<span class='s'>"Phae.long3.wav"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/writeWave.html'>writeWave</a></span><span class='o'>(</span><span class='nv'>Phae.long4</span>,<span class='s'>"Phae.long4.wav"</span><span class='o'>)</span></span></code></pre>

</div>

The basic catalog plots the spectrograms of the signals listed in 'X'. 'X' is simply a data frame with the name of sound files and the temporal position of the signals in those sound files (as in the `selec.table` example data). The following code creates a catalog with 2 columns and 5 rows labeled with the sound file name and selection number (default `labels` argument)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>5</span>, ncol <span class='o'>=</span> <span class='m'>2</span>, </span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>TRUE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, gr <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span></code></pre>

</div>

![catalog1](img/Catalog_p1-.png)

Spectrograms can be color-tagged using the `tags` argument. A legend is added when `legend > 0` (check documentation). The duration can also be allowed to vary between spectrograms setting `same.time.scale = TRUE`

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>5</span>, ncol <span class='o'>=</span> <span class='m'>2</span>, </span>
<span>        same.time.scale <span class='o'>=</span> <span class='kc'>FALSE</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, legend <span class='o'>=</span> <span class='m'>1</span>, </span>
<span>        tags <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"sound.files"</span><span class='o'>)</span>, leg.wd <span class='o'>=</span> <span class='m'>10</span><span class='o'>)</span></span></code></pre>

</div>

![catalog2](img/Catalog_p1-2.png)

Two color tags can be used at the same times (`tags` argument) using different color palettes for each tag (`tag.pal` argument). The code below also uses a different color palette for the spectrogram (`pal` argument) and set `orientation` of the output image file to horizontal

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span>  </span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>selec.table</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>,<span class='o'>]</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>5</span>, ncol <span class='o'>=</span> <span class='m'>2</span>, </span>
<span>       mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, orientation <span class='o'>=</span> <span class='s'>"h"</span>,  tags <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"sound.files"</span>, <span class='s'>"selec"</span><span class='o'>)</span>, </span>
<span>       leg.wd <span class='o'>=</span> <span class='m'>10</span><span class='o'>)</span></span></code></pre>

</div>

![catalog3](img/Catalog_p1-3.png)

Many more columns and rows can be displayed. In this example a bigger selection data frame is created by combining binding several times the data used in the previous examples. The code also creates some new (hypothetical) data for song type and site\> Note that the data is sorted by sites so songs from the same site are closer in the catalog

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'>#create bigger data</span></span>
<span><span class='nv'>Y</span> <span class='o'>&lt;-</span> <span class='nv'>selec.table</span></span>
<span></span>
<span><span class='kr'>for</span><span class='o'>(</span><span class='nv'>i</span> <span class='kr'>in</span> <span class='m'>1</span><span class='o'>:</span><span class='m'>4</span><span class='o'>)</span> <span class='nv'>Y</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/cbind.html'>rbind</a></span><span class='o'>(</span><span class='nv'>Y</span>, <span class='nv'>Y</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'>#simulated columns</span></span>
<span>  <span class='nv'>Y</span><span class='o'>$</span><span class='nv'>songtype</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/sample.html'>sample</a></span><span class='o'>(</span><span class='nv'>letters</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='m'>3</span><span class='o'>]</span>, <span class='nf'><a href='https://rdrr.io/r/base/nrow.html'>nrow</a></span><span class='o'>(</span><span class='nv'>Y</span><span class='o'>)</span>, replace <span class='o'>=</span> <span class='kc'>T</span><span class='o'>)</span></span>
<span>  <span class='nv'>Y</span><span class='o'>$</span><span class='nv'>site</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/rep.html'>rep</a></span><span class='o'>(</span><span class='nv'>letters</span><span class='o'>[</span><span class='m'>4</span><span class='o'>:</span><span class='m'>25</span><span class='o'>]</span>, <span class='nf'><a href='https://rdrr.io/r/base/nrow.html'>nrow</a></span><span class='o'>(</span><span class='nv'>Y</span><span class='o'>)</span><span class='o'>)</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='nf'><a href='https://rdrr.io/r/base/nrow.html'>nrow</a></span><span class='o'>(</span><span class='nv'>Y</span><span class='o'>)</span><span class='o'>]</span></span>
<span></span>
<span>  <span class='c'>#sort by site and the by song type </span></span>
<span><span class='nv'>Y</span> <span class='o'>&lt;-</span> <span class='nv'>Y</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/order.html'>order</a></span><span class='o'>(</span><span class='nv'>Y</span><span class='o'>$</span><span class='nv'>site</span>, <span class='nv'>Y</span><span class='o'>$</span><span class='nv'>songtype</span><span class='o'>)</span>, <span class='o'>]</span></span>
<span>  </span>
<span>  </span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>Y</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>12</span>, ncol <span class='o'>=</span> <span class='m'>5</span>, cex <span class='o'>=</span> <span class='m'>2</span>, leg.wd <span class='o'>=</span> <span class='m'>8</span>,</span>
<span>       mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, labels <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"sound.files"</span>,<span class='s'>"songtype"</span>, <span class='s'>"site"</span><span class='o'>)</span>, </span>
<span>       legend <span class='o'>=</span> <span class='m'>3</span>, width <span class='o'>=</span> <span class='m'>23</span>, height <span class='o'>=</span> <span class='m'>30</span>, </span>
<span>       tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>gray.colors</span>, <span class='nv'>temp.colors</span><span class='o'>)</span>, tags <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"songtype"</span>, <span class='s'>"site"</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span></span></code></pre>

</div>

The first image would look like this (several image are produced in the signals don't fit in a single one)

![catalog4](img/Catalog_p1-4.png)

When several levels are displayed in a tag colors could become very similar, as in the example above. In that case cross-hatched labels can be used using the `hatching` argument. Either one or both color tags can be cross-hatched. The following applies cross-hatching on the second tag

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>Y</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>12</span>, ncol <span class='o'>=</span> <span class='m'>5</span>, cex <span class='o'>=</span> <span class='m'>2</span>, leg.wd <span class='o'>=</span> <span class='m'>8</span>,</span>
<span>        mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, legend <span class='o'>=</span> <span class='m'>3</span>, width <span class='o'>=</span> <span class='m'>23</span>, height <span class='o'>=</span> <span class='m'>30</span>,</span>
<span>        tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>topo.colors</span>, <span class='nv'>temp.colors</span><span class='o'>)</span>,tags <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"songtype"</span>, <span class='s'>"site"</span><span class='o'>)</span>, </span>
<span>        hatching <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span></span>
<span></span></code></pre>

</div>

![catalog5](img/Catalog_p1-5.png)

Continuous variables can be used for color tagging as well. The following code calculates the signal-to-noise ratio for each selection and then use it to tag them in the catalog. The breaks argument specifies that the signal-to-noise ratio range should be split in 3 intervals. It also sets the first color tag twice the size of the second one

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#get signal-to-noise ratio </span></span>
<span><span class='nv'>Ysnr</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/sig2noise.html'>sig2noise</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>Y</span>, mar <span class='o'>=</span> <span class='m'>0.04</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'>#shuffle rows (just for not having snr values unsorted)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/Random.html'>set.seed</a></span><span class='o'>(</span><span class='m'>27</span><span class='o'>)</span></span>
<span><span class='nv'>Ysnr</span> <span class='o'>&lt;-</span> <span class='nv'>Ysnr</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/sample.html'>sample</a></span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='nf'><a href='https://rdrr.io/r/base/nrow.html'>nrow</a></span><span class='o'>(</span><span class='nv'>Ysnr</span><span class='o'>)</span><span class='o'>)</span>,<span class='o'>]</span> </span>
<span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>Ysnr</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>12</span>, ncol <span class='o'>=</span> <span class='m'>5</span>, cex <span class='o'>=</span> <span class='m'>2</span>, leg.wd <span class='o'>=</span> <span class='m'>8</span>,</span>
<span>       mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, tag.widths <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>2</span>, <span class='m'>1</span><span class='o'>)</span>,legend <span class='o'>=</span> <span class='m'>3</span>, </span>
<span> width <span class='o'>=</span> <span class='m'>23</span>, height <span class='o'>=</span> <span class='m'>30</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>temp.colors</span>, <span class='nv'>heat.colors</span><span class='o'>)</span>,</span>
<span>  tags <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"songtype"</span>, <span class='s'>"SNR"</span><span class='o'>)</span>, hatching <span class='o'>=</span> <span class='m'>1</span>, breaks <span class='o'>=</span> <span class='m'>3</span><span class='o'>)</span></span>
<span></span>
<span></span></code></pre>

</div>

![catalog6](img/Catalog_p1-6.png)

Both tags could be numeric. The following code calculates the spectral entropy with `specan` and the use it as a tag along with the signal-to-noise ratio

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#get signal-to-noise ratio </span></span>
<span><span class='nv'>Ysnr</span><span class='o'>$</span><span class='nv'>sp.ent</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/specan.html'>specan</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>Ysnr</span><span class='o'>)</span><span class='o'>[</span>,<span class='s'>"sp.ent"</span><span class='o'>]</span></span>
<span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>Ysnr</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>12</span>, ncol <span class='o'>=</span> <span class='m'>5</span>, cex <span class='o'>=</span> <span class='m'>2</span>, leg.wd <span class='o'>=</span> <span class='m'>8</span>,</span>
<span>        mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, legend <span class='o'>=</span> <span class='m'>3</span>,  width <span class='o'>=</span> <span class='m'>23</span>, height <span class='o'>=</span> <span class='m'>30</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>temp.colors</span>, <span class='nv'>gray.colors</span><span class='o'>)</span>, tags <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"sp.ent"</span>, <span class='s'>"SNR"</span><span class='o'>)</span>, </span>
<span>        hatching <span class='o'>=</span> <span class='m'>2</span>, breaks <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>3</span>, <span class='m'>2</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span></span></code></pre>

</div>

![catalog7](img/Catalog_p1-7.png)

The legend can be removed for one of both tags. The argument `legend = 2` plots the legend only for the second tag

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>Ysnr</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>10</span><span class='o'>)</span>, nrow <span class='o'>=</span> <span class='m'>12</span>, ncol <span class='o'>=</span> <span class='m'>5</span>, cex <span class='o'>=</span> <span class='m'>2</span>, leg.wd <span class='o'>=</span> <span class='m'>8</span>,</span>
<span>        mar <span class='o'>=</span> <span class='m'>0.01</span>, wl <span class='o'>=</span> <span class='m'>200</span>, legend <span class='o'>=</span> <span class='m'>1</span>,  width <span class='o'>=</span> <span class='m'>23</span>, height <span class='o'>=</span> <span class='m'>30</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>temp.colors</span>, <span class='nv'>gray.colors</span><span class='o'>)</span>, tags <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"sp.ent"</span>, <span class='s'>"SNR"</span><span class='o'>)</span>, hatching <span class='o'>=</span> <span class='m'>2</span>, breaks <span class='o'>=</span> <span class='m'>4</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![catalog7](img/Catalog_p1-8.png)

The following plots show a nice example of song geographic variation of \[Northern Cardinals (Cardinalis cardinalis)\]((<https://www.allaboutbirds.org/guide/Northern_Cardinal/id>) in Mexico (from my collaborator Marco Ortiz).

![catalog6](img/Catalog_p1-Cardinalis.png) ![catalog7](img/Catalog_p2-Cardinalis.png)

A single pdf containing all catalog images can be generated using the `catalog2pdf` function.

The `catalog` function has other arguments to specify spectrogram settings (e.g. margin, grid, frequency limits) and image parameters (e.g. image type, resolution). Check out the function help document.

