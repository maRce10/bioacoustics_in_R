---
title: "Potential issues of the 'spectral parameters/PCA' approach"
author: "Marcelo Araya-Salas"
date: "2018-07-04"
output: 
  md_document:
    variant: markdown_github
rmd_hash: 3008b32d62a8733d

---

Somehow measuring a bunch of spectral/temporal parameters and then reducing its dimensionality using principal component analysis has become the standard procedure when looking at variation in signal structure (i.e. measuring acoustic space), particularly in behavioral ecology and comparative bioacoustics. In most cases the approach is used without any kind of ground-truthing that can help validate the analysis. Given the complexity of animal acoustic signals, the approach could miss key signal features. Here I share a quick-and-dirty comparison of this 'standard approach' to a potentially better suited alternative.

But first load/install [warbleR](https://cran.r-project.org/package=warbleR), set warbleR options and create a fancy color palette for catalogs:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'># vector with packages needed</span></span>
<span><span class='nv'>w</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"Rtsne"</span>, <span class='s'>"githubinstall"</span>, <span class='s'>"mclust"</span>, <span class='s'>"RColorBrewer"</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># load/install each with a loop</span></span>
<span><span class='kr'>for</span><span class='o'>(</span><span class='nv'>i</span> <span class='kr'>in</span> <span class='nv'>w</span><span class='o'>)</span></span>
<span><span class='kr'>if</span> <span class='o'>(</span><span class='o'>!</span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>require</a></span><span class='o'>(</span><span class='o'>(</span><span class='nv'>i</span><span class='o'>)</span>, character.only <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span><span class='o'>)</span> <span class='nf'><a href='https://rdrr.io/r/utils/install.packages.html'>install.packages</a></span><span class='o'>(</span><span class='nv'>i</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># install/load warbleR from github (remove it first if already installed)</span></span>
<span><span class='nf'>githubinstall</span><span class='o'>(</span><span class='s'>"warbleR"</span>, ask <span class='o'>=</span> <span class='kc'>FALSE</span>, force <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span>
<span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='nv'><a href='https://marce10.github.io/warbleR/'>warbleR</a></span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># set warbleR options</span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/warbleR_options.html'>warbleR_options</a></span><span class='o'>(</span>bp <span class='o'>=</span>  <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>2</span>, <span class='m'>10</span><span class='o'>)</span>, flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>3.5</span>, <span class='m'>10.5</span><span class='o'>)</span>, pb <span class='o'>=</span> <span class='kc'>FALSE</span>, wl <span class='o'>=</span> <span class='m'>200</span>, </span>
<span>                ovlp <span class='o'>=</span> <span class='m'>90</span>, parallel <span class='o'>=</span> <span class='m'>3</span>, pal <span class='o'>=</span> <span class='nv'>reverse.heat.colors</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># create nice color pallete</span></span>
<span><span class='nv'>cmc</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>n</span><span class='o'>)</span> <span class='nf'><a href='https://rdrr.io/r/base/rep.html'>rep</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/grDevices/adjustcolor.html'>adjustcolor</a></span><span class='o'>(</span><span class='nf'>brewer.pal</span><span class='o'>(</span><span class='m'>5</span>, <span class='s'>"Spectral"</span><span class='o'>)</span>, </span>
<span>                                   alpha.f <span class='o'>=</span> <span class='m'>0.6</span><span class='o'>)</span>, </span>
<span>                       <span class='nf'><a href='https://rdrr.io/r/base/Round.html'>ceiling</a></span><span class='o'>(</span><span class='nv'>n</span><span class='o'>/</span><span class='m'>4</span><span class='o'>)</span><span class='o'>)</span><span class='o'>[</span><span class='m'>1</span><span class='o'>:</span><span class='nv'>n</span><span class='o'>]</span></span>
<span></span></code></pre>

</div>

<div class="highlight">

</div>

As in the [previous post](https://marce10.github.io/bioacoustics_in_R/2018/06/29/Frequency_range_detection_from_spectrum.html), we will run the comparison on signals detected on a recording from a male [Striped-throated Hermit (*Phaethornis striigularis*)](https://neotropical.birds.cornell.edu/Species-Account/nb/species/stther2/overview) from [Xeno-Canto](http://xeno-canto.org). We can download the sound file and convert it into wave format as follows:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'># set temporary working directory</span></span>
<span> <span class='nf'><a href='https://rdrr.io/r/base/getwd.html'>setwd</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/tempfile.html'>tempdir</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># Query and download  Xeno-Canto for metadata using genus </span></span>
<span><span class='c'># and species as keywords</span></span>
<span><span class='nv'>Phae.stri</span> <span class='o'>&lt;-</span> <span class='nf'>quer_xc</span><span class='o'>(</span>qword <span class='o'>=</span> <span class='s'>"nr:154074"</span>, download <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># Convert mp3 to wav format</span></span>
<span><span class='c'># Simultaneously lower sampling rate to speed up down stream analyses</span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/mp32wav.html'>mp32wav</a></span><span class='o'>(</span>samp.rate <span class='o'>=</span> <span class='m'>22.05</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

A long spectrogram would help to get a sense of the song structure in this species:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/lspec.html'>lspec</a></span><span class='o'>(</span>ovlp <span class='o'>=</span> <span class='m'>50</span>, sxrow <span class='o'>=</span> <span class='m'>3</span>, rows <span class='o'>=</span> <span class='m'>12</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![frange_gif](/img/lspec-spec.pca.png)

We can also listen to it from [Xeno-Canto](http://xeno-canto.org):

<iframe src="https://www.xeno-canto.org/154074/embed?simple=1" scrolling="no" frameborder="0" width="900" height="150">
</iframe>

The elements of this song are pure tone, highly modulated sounds that are recycled along the sequence. Overall, the structure of the element types seems to be consistent across renditions and the background noise level of the recording looks fine.

To run any analysis we need to detect the time 'coordinates' of the signals in the sound file using `auto_detec`:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nv'>ad</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/auto_detec.html'>auto_detec</a></span><span class='o'>(</span>wl <span class='o'>=</span> <span class='m'>200</span>, threshold <span class='o'>=</span> <span class='m'>3.5</span>, ssmooth <span class='o'>=</span> <span class='m'>1200</span>, </span>
<span>                 bp <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>4</span>, <span class='m'>9.6</span><span class='o'>)</span>, mindur <span class='o'>=</span> <span class='m'>0.1</span>, </span>
<span>                 maxdur <span class='o'>=</span> <span class='m'>0.25</span>, img <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

Lets' select the 100 highest signal-to-noise ratio signals, just for the sake of the example:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'># measure SNR</span></span>
<span><span class='nv'>snr</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/sig2noise.html'>sig2noise</a></span><span class='o'>(</span><span class='nv'>ad</span>, mar <span class='o'>=</span> <span class='m'>0.05</span>, type <span class='o'>=</span> <span class='m'>3</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># selec the 100 highest SNR</span></span>
<span><span class='nv'>ad</span> <span class='o'>&lt;-</span> <span class='nv'>snr</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/rank.html'>rank</a></span><span class='o'>(</span><span class='o'>-</span><span class='nv'>snr</span><span class='o'>$</span><span class='nv'>SNR</span><span class='o'>)</span> <span class='o'>&lt;=</span> <span class='m'>100</span>, <span class='o'>]</span></span>
<span></span></code></pre>

</div>

... and measure the frequency range (as in the [previous post](https://marce10.github.io/bioacoustics_in_R/2018/06/29/Frequency_range_detection_from_spectrum.html)):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nv'>fr_ad</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/freq_range.html'>freq_range</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>ad</span>, bp <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>2</span>, <span class='m'>12</span><span class='o'>)</span>, fsmooth <span class='o'>=</span> <span class='m'>0.001</span>, </span>
<span>                    ovlp <span class='o'>=</span> <span class='m'>95</span>, wl <span class='o'>=</span> <span class='m'>200</span>, threshold <span class='o'>=</span> <span class='m'>10</span>, </span>
<span>                    img <span class='o'>=</span> <span class='kc'>FALSE</span>, impute <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

Finally, let's pack the acoustic data and metadata together as a 'extended_selection_table' ([check this post to learn more about these objects](https://marce10.github.io/bioacoustics_in_R/2018/05/15/Extended_selection_tables.html)):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nv'>est</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/selection_table.html'>selection_table</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>fr_ad</span>, extended <span class='o'>=</span> <span class='kc'>TRUE</span>, </span>
<span>                       confirm.extended <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

We can take a look at the selected signals (or elements, subunits or whatever you want to call them) by creating a catalog:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span><span class='nv'>est</span>, nrow <span class='o'>=</span> <span class='m'>10</span>, ncol <span class='o'>=</span> <span class='m'>10</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, labels <span class='o'>=</span> <span class='s'>"selec"</span>, </span>
<span>        flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>3.5</span>, <span class='m'>10.5</span><span class='o'>)</span>, ovlp <span class='o'>=</span> <span class='m'>30</span>, pal <span class='o'>=</span> <span class='nv'>reverse.heat.colors</span>, </span>
<span>        width <span class='o'>=</span> <span class='m'>15</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span>, spec.mar <span class='o'>=</span> <span class='m'>0.5</span>, </span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>4</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>2</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span></code></pre>

</div>

![frange_gif](/img/catalog-spec.pca.png)

Some are too noisy, but still good enough for the example.

------------------------------------------------------------------------

## Element classification using the 'standard' approach

So let's use the *spectro-temporal parameters + PCA* recipe. First acoustic parameters are measured using `spec_an` and then a PCA is run over those parameters:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nv'>sp</span> <span class='o'>&lt;-</span> <span class='nf'>spec_an</span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>est</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>pca</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/stats/prcomp.html'>prcomp</a></span><span class='o'>(</span><span class='nv'>sp</span><span class='o'>[</span> , <span class='o'>-</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>,<span class='m'>2</span><span class='o'>)</span><span class='o'>]</span>, scale. <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>pca_sp</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/summary.html'>summary</a></span><span class='o'>(</span><span class='nv'>pca</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/graphics/barplot.html'>barplot</a></span><span class='o'>(</span><span class='nv'>pca_sp</span><span class='o'>$</span><span class='nv'>importance</span><span class='o'>[</span><span class='m'>3</span>, <span class='m'>1</span><span class='o'>:</span><span class='m'>10</span><span class='o'>]</span>, col <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/rev.html'>rev</a></span><span class='o'>(</span><span class='nv'>.Options</span><span class='o'>$</span><span class='nv'>warbleR</span><span class='o'>$</span><span class='nf'>pal</span><span class='o'>(</span><span class='m'>10</span><span class='o'>)</span><span class='o'>)</span>, </span>
<span>        ylab <span class='o'>=</span> <span class='s'>"Cumulative variance explained"</span>, xlab <span class='o'>=</span> <span class='s'>"PCs"</span>, </span>
<span>        ylim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='m'>1</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/graphics/abline.html'>abline</a></span><span class='o'>(</span>h <span class='o'>=</span> <span class='m'>0.8</span>, lty <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![frange_gif](/img/hist.pca.png)

The first 5 components explain almost %80 of the variance.

Now let's look and how good is the classification of elements based on the first 5 PCs. To do this we can use the `Mclust` function from the [mclust](https://cran.r-project.org/package=mclust) package to choose the most likely number of clusters and assign each element to one of those clusters:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'># run mclust</span></span>
<span><span class='nv'>sp_clust</span> <span class='o'>&lt;-</span> <span class='nf'>Mclust</span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/matrix.html'>as.matrix</a></span><span class='o'>(</span><span class='nv'>pca_sp</span><span class='o'>$</span><span class='nv'>x</span><span class='o'>[</span>, <span class='m'>1</span><span class='o'>:</span><span class='m'>5</span><span class='o'>]</span><span class='o'>)</span>, G<span class='o'>=</span><span class='m'>1</span><span class='o'>:</span><span class='m'>15</span>, </span>
<span>                   modelNames <span class='o'>=</span> <span class='nf'>mclust.options</span><span class='o'>(</span><span class='s'>"emModelNames"</span><span class='o'>)</span>, </span>
<span>                   verbose <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span>  </span>
<span></span>
<span><span class='c'># add cluster label to each element (row)</span></span>
<span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.sp</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/character.html'>as.character</a></span><span class='o'>(</span><span class='nv'>sp_clust</span><span class='o'>$</span><span class='nv'>classification</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># add a 0 to each value so they are displayed in order </span></span>
<span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.sp</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nchar</a></span><span class='o'>(</span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.sp</span><span class='o'>)</span> <span class='o'>==</span> <span class='m'>1</span>,  </span>
<span>                             <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste0</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.sp</span><span class='o'>)</span>, <span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.sp</span><span class='o'>)</span></span></code></pre>

</div>

The classification can be visually assessed using a 'group-tagged' catalog. In the catalog, elements belonging to the same cluster are located next to each other. Elements are also labeled with the cluster number and colors highlight groups of elements from the same clusters (note that colors are recycled):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span><span class='nv'>est</span>, nrow <span class='o'>=</span> <span class='m'>10</span>, ncol <span class='o'>=</span><span class='m'>10</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, </span>
<span>        flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>3.5</span>, <span class='m'>10.5</span><span class='o'>)</span>, ovlp <span class='o'>=</span> <span class='m'>30</span>, labels <span class='o'>=</span> <span class='s'>"class.sp"</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"class.sp"</span>, pal <span class='o'>=</span> <span class='nv'>reverse.heat.colors</span>, </span>
<span>        width <span class='o'>=</span> <span class='m'>15</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span>, spec.mar <span class='o'>=</span> <span class='m'>0.5</span>, </span>
<span>        title <span class='o'>=</span> <span class='s'>"sp/PCA"</span>, img.suffix <span class='o'>=</span> <span class='s'>"sp-PCA"</span>, max.group.cols <span class='o'>=</span> <span class='m'>4</span>, </span>
<span>        tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>2</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span></code></pre>

</div>

![frange_gif](/img/Catalog_p1-sp-PCA.png)

A better way to look at this is by plotting the first 2 PC's:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://rdrr.io/r/graphics/plot.default.html'>plot</a></span><span class='o'>(</span><span class='nv'>pca_sp</span><span class='o'>$</span><span class='nv'>x</span><span class='o'>[</span>, <span class='m'>1</span><span class='o'>]</span>, <span class='nv'>pca_sp</span><span class='o'>$</span><span class='nv'>x</span><span class='o'>[</span>, <span class='m'>2</span><span class='o'>]</span>, col <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.sp</span><span class='o'>)</span>, </span>
<span>     pch <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.sp</span><span class='o'>)</span>, cex <span class='o'>=</span> <span class='m'>1.5</span>, xlab <span class='o'>=</span> <span class='s'>"PC1"</span>, </span>
<span>     ylab <span class='o'>=</span> <span class='s'>"PC2"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![tsne_plot](/img/pca.plot.png)

Most clusters include several different element types and the same element type can be found on several categories. In this example the performance of the 'standard approach' is not ideal.

## An alternative

When working with pure tone modulated whistles, the best approach is likely measuring [dynamic time warping](https://marce10.github.io/bioacoustics_in_R/2016/09/12/Similarity_of_acoustic_signals_with_dynamic_time_warping_(DTW).html) distances on dominant frequency contours. We can do all that at once using `df_DTW`:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nv'>df</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://marce10.github.io/warbleR/reference/df_DTW.html'>df_DTW</a></span><span class='o'>(</span>X <span class='o'>=</span> <span class='nv'>est</span>, wl <span class='o'>=</span> <span class='m'>200</span>, threshold <span class='o'>=</span> <span class='m'>15</span>, img <span class='o'>=</span> <span class='kc'>FALSE</span>, </span>
<span>             clip.edges <span class='o'>=</span> <span class='kc'>TRUE</span>, bp <span class='o'>=</span>  <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>2</span>, <span class='m'>10</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

To convert this distance matrix to a rectangular data frame we can use TSNE ([check out this awesome post about it](https://marce10.github.io/bioacoustics_in_R/2018/05/15/Extended_selection_tables.html)). The name stands for *T-distributed Stochastic Neighbor Embedding* and is regarded as a more powerful way to find data structure than PCA (and yes, it can also be applied to non-distance matrices). The method can be easily run in **R** using the `Rtsne` function from the package of the same name. The following code does the clustering and cataloging as we did above:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'># set seed so we all get the same results</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/Random.html'>set.seed</a></span><span class='o'>(</span><span class='m'>10</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># run TSNE</span></span>
<span><span class='nv'>tsne.df</span> <span class='o'>&lt;-</span> <span class='nf'>Rtsne</span><span class='o'>(</span>X <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/stats/dist.html'>as.dist</a></span><span class='o'>(</span><span class='nv'>df</span><span class='o'>)</span>, theta <span class='o'>=</span> <span class='m'>0.01</span>, dims <span class='o'>=</span> <span class='m'>5</span>, </span>
<span>                 is_distance <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># clustering</span></span>
<span><span class='nv'>df_clust</span> <span class='o'>&lt;-</span> <span class='nf'>Mclust</span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/matrix.html'>as.matrix</a></span><span class='o'>(</span><span class='nv'>tsne.df</span><span class='o'>$</span><span class='nv'>Y</span><span class='o'>)</span>, G<span class='o'>=</span><span class='m'>1</span><span class='o'>:</span><span class='m'>15</span>, </span>
<span>              modelNames <span class='o'>=</span> <span class='nf'>mclust.options</span><span class='o'>(</span><span class='s'>"emModelNames"</span><span class='o'>)</span>, </span>
<span>              verbose <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span>  </span>
<span></span>
<span><span class='c'># label elements (rows)</span></span>
<span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.df</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/character.html'>as.character</a></span><span class='o'>(</span><span class='nv'>df_clust</span><span class='o'>$</span><span class='nv'>classification</span><span class='o'>)</span></span>
<span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.df</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/ifelse.html'>ifelse</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nchar</a></span><span class='o'>(</span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.df</span><span class='o'>)</span> <span class='o'>==</span> <span class='m'>1</span>,  <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste0</a></span><span class='o'>(</span><span class='m'>0</span>, <span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.df</span><span class='o'>)</span>,</span>
<span>                       <span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.df</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># make catalog</span></span>
<span><span class='nf'><a href='https://marce10.github.io/warbleR/reference/catalog.html'>catalog</a></span><span class='o'>(</span><span class='nv'>est</span>, nrow <span class='o'>=</span> <span class='m'>10</span>, ncol <span class='o'>=</span> <span class='m'>10</span>, mar <span class='o'>=</span> <span class='m'>0.01</span>, </span>
<span>        flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>3.5</span>, <span class='m'>10.5</span><span class='o'>)</span>, ovlp <span class='o'>=</span> <span class='m'>30</span>, labels <span class='o'>=</span> <span class='s'>"class.df"</span>, </span>
<span>        group.tag <span class='o'>=</span> <span class='s'>"class.df"</span>, pal <span class='o'>=</span> <span class='nv'>reverse.heat.colors</span>, </span>
<span>        width <span class='o'>=</span> <span class='m'>15</span>, box <span class='o'>=</span> <span class='kc'>FALSE</span>, spec.mar <span class='o'>=</span> <span class='m'>0.5</span>, </span>
<span>        title <span class='o'>=</span> <span class='s'>"df_DTW/TSNE"</span>, img.suffix <span class='o'>=</span> <span class='s'>"df_DTW-TSNE"</span>, </span>
<span>        max.group.cols <span class='o'>=</span> <span class='m'>4</span>, tag.pal <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span><span class='nv'>cmc</span><span class='o'>)</span>, </span>
<span>        cex <span class='o'>=</span> <span class='m'>2</span>, rm.axes <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span></code></pre>

</div>

![frange_gif](/img/Catalog_p1-df_DTW-TSNE.png)

We can obtain 2 dimensions using TSNE so it fits better in a bi-dimensional plot (grouping is likely to improve when adding more dimensions, so this plot gives a conservative estimate):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'># set seed so we all get the same results</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/Random.html'>set.seed</a></span><span class='o'>(</span><span class='m'>10</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>tsne.df</span> <span class='o'>&lt;-</span> <span class='nf'>Rtsne</span><span class='o'>(</span>X <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/stats/dist.html'>as.dist</a></span><span class='o'>(</span><span class='nv'>df</span><span class='o'>)</span>, theta <span class='o'>=</span> <span class='m'>0.01</span>, dims <span class='o'>=</span> <span class='m'>2</span>, </span>
<span>                 is_distance <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/graphics/plot.default.html'>plot</a></span><span class='o'>(</span><span class='nv'>tsne.df</span><span class='o'>$</span><span class='nv'>Y</span><span class='o'>[</span>, <span class='m'>1</span><span class='o'>]</span>, <span class='nv'>tsne.df</span><span class='o'>$</span><span class='nv'>Y</span><span class='o'>[</span>, <span class='m'>2</span><span class='o'>]</span>, col <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.df</span><span class='o'>)</span>, </span>
<span>     pch <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nv'>est</span><span class='o'>$</span><span class='nv'>class.df</span><span class='o'>)</span> <span class='o'>+</span> <span class='m'>5</span>, cex <span class='o'>=</span> <span class='m'>1.5</span>, xlab <span class='o'>=</span> <span class='s'>"Dim 1"</span>, </span>
<span>     ylab <span class='o'>=</span> <span class='s'>"Dim 2"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

![tsne_plot](/img/tsne.plot.png)

The classification seems OK. Most clusters contain a single element type, and most types are found in a single cluster. Nonetheless, the classification was not perfect. For instance, clusters 5 and 6 share some element types. However, it's much better compared to the 'standard approach'. In a more formal analysis I will make sure the frequency contours are tracking the signals (using `sel_tailor()`). This will likely improve the analysis.

This quick-and-dirty comparison suggests that we (behavioral ecologists) might actually be missing important signal features when using the *spectral/temporal parameters + PCA* recipe as the silver bullet in bioacoustic analysis. It also stresses the importance of validating our analyses in some way. Otherwise, there is no way to tell whether the results are simply an artifact of our measuring tools, particularly when no differences are found.

<div class="highlight">

</div>

