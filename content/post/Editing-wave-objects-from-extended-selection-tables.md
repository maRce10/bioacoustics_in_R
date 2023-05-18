---
title: "Editing wave objects from extended selection tables"
author: "Marcelo Araya-Salas"
date: "2020-05-26"
output: 
  md_document:
    variant: markdown_github
editor_options: 
  chunk_output_type: console
rmd_hash: 01e15783284989a5

---

> “Is there a simple way to remove noise from the clips in an extended
> table – I can do this by directly manipulating the attributes of the
> table but it seems a bit kludgy … so again, am I missing something
> simple?”

Manipulating clips from *extended selection tables* can be pretty
straightforward. It can be done by using [`lapply()`](https://rdrr.io/r/base/lapply.html) to go over each
clip. Things should be fine as long as you don’t mess with any time
related feature (i.e. the time position of the signals in the clip
remains unchanged). For example filtering out low frequencies on the
clips from the example *extended selection table* ‘lbh.est’ from the
package [NatureSounds](https://marce10.github.io/NatureSounds). This is
how the original clips look like:

``` r
# load packages
library(NatureSounds)
library(warbleR)
library(seewave)

# load data
data("lbh.est")

# extract 2 clips
w1 <- read_wave(lbh.est, 12, from = 0, to = Inf)
w2 <- read_wave(lbh.est, 20, from = 0, to = Inf)

# split graphic device
par(mfrow = c(1, 2))

# plot spectros
spectro(w1, wl = 300, ovlp = 95, flim = c(1, 10), scale = FALSE, 
        grid = FALSE, palette = reverse.gray.colors.1, 
        collevels = seq(-70, 0, 5))

spectro(w2, wl = 300, ovlp = 95, flim = c(1, 10), scale = FALSE, 
        grid = FALSE, palette = reverse.gray.colors.1, 
        collevels = seq(-70, 0, 5))
```

<img src="Editing-wave-objects-from-extended-selection-tables_files/figure-markdown_github/unnamed-chunk-2-1.png" width="900px" />

Clips are stored in an attribute call ‘wave.objects’. In this particular
example the list of clips can be called like this:
`attributes(lbh.est)$wave.objects`.

We can apply a bandpass filter from 4 to 10 kHz over each element of
this list using [`lapply()`](https://rdrr.io/r/base/lapply.html) and [`ffilter()`](https://rdrr.io/pkg/seewave/man/ffilter.html) (from
[seewave](http://rug.mnhn.fr/seewave)):

``` r
# filter out freqs below 4 kHz
attributes(lbh.est)$wave.objects <- lapply(attributes(lbh.est)$wave.objects, 
    FUN = ffilter, from = 4000, to = 10000, output = "Wave")
```

We can double-check that the bandpass actually worked by looking again
at the spectrograms:

``` r
# extract the same 2 clips again
w1 <- read_wave(lbh.est, 12, from = 0, to = Inf)
w2 <- read_wave(lbh.est, 20, from = 0, to = Inf)

# split graphic device
par(mfrow = c(1, 2))

# plot spectros
spectro(w1, wl = 300, ovlp = 95, flim = c(1, 10), scale = FALSE, 
        grid = FALSE, palette = reverse.gray.colors.1, 
        collevels = seq(-70, 0, 5))

spectro(w2, wl = 300, ovlp = 95, flim = c(1, 10), scale = FALSE, 
        grid = FALSE, palette = reverse.gray.colors.1, 
        collevels = seq(-70, 0, 5))
```

<img src="Editing-wave-objects-from-extended-selection-tables_files/figure-markdown_github/unnamed-chunk-4-1.png" width="900px" />

So any [seewave](http://rug.mnhn.fr/seewave) function that works on
‘wave’ objects and returns ‘wave’ objects as well, can be used. For
instance, applying a linear frequency shift to increase pitch in 2 kHz:

``` r
# load data
data("lbh.est")

# filter out freqs below 4 kHz
attributes(lbh.est)$wave.objects <- lapply(attributes(lbh.est)$wave.objects, 
    FUN = lfs, shift = 2000, output = "Wave")

# extract the same 2 clips again
w1 <- read_wave(lbh.est, 12, from = 0, to = Inf)
w2 <- read_wave(lbh.est, 20, from = 0, to = Inf)

# split graphic device
par(mfrow = c(1, 2))

# plot spectros
spectro(w1, wl = 300, ovlp = 95, scale = FALSE, 
        grid = FALSE, palette = reverse.gray.colors.1, 
        collevels = seq(-70, 0, 5))

spectro(w2, wl = 300, ovlp = 95, scale = FALSE, 
        grid = FALSE, palette = reverse.gray.colors.1, 
        collevels = seq(-70, 0, 5))
```

<img src="Editing-wave-objects-from-extended-selection-tables_files/figure-markdown_github/unnamed-chunk-5-1.png" width="900px" />

Again, this should be OK as long as you don’t modify any time related
feature. If temporal features change then the time annotations are no
longer useful.

------------------------------------------------------------------------

<font size="4">Session information</font>

    ## R version 4.2.2 Patched (2022-11-10 r83330)
    ## Platform: x86_64-pc-linux-gnu (64-bit)
    ## Running under: Ubuntu 20.04.5 LTS
    ## 
    ## Matrix products: default
    ## BLAS:   /usr/lib/x86_64-linux-gnu/blas/libblas.so.3.9.0
    ## LAPACK: /usr/lib/x86_64-linux-gnu/lapack/liblapack.so.3.9.0
    ## 
    ## locale:
    ##  [1] LC_CTYPE=es_ES.UTF-8       LC_NUMERIC=C              
    ##  [3] LC_TIME=es_CR.UTF-8        LC_COLLATE=es_ES.UTF-8    
    ##  [5] LC_MONETARY=es_CR.UTF-8    LC_MESSAGES=es_ES.UTF-8   
    ##  [7] LC_PAPER=es_CR.UTF-8       LC_NAME=C                 
    ##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
    ## [11] LC_MEASUREMENT=es_CR.UTF-8 LC_IDENTIFICATION=C       
    ## 
    ## attached base packages:
    ## [1] stats     graphics  grDevices utils     datasets  methods   base     
    ## 
    ## other attached packages:
    ## [1] warbleR_1.1.28     seewave_2.2.0      tuneR_1.4.4        NatureSounds_1.0.4
    ## [5] knitr_1.42        
    ## 
    ## loaded via a namespace (and not attached):
    ##  [1] Rcpp_1.0.10     rstudioapi_0.14 magrittr_2.0.3  MASS_7.3-58.2  
    ##  [5] rjson_0.2.21    R6_2.5.1        rlang_1.1.1     fastmap_1.1.1  
    ##  [9] pbapply_1.7-0   highr_0.10      tools_4.2.2     parallel_4.2.2 
    ## [13] xfun_0.39       dtw_1.23-1      cli_3.6.1       htmltools_0.5.5
    ## [17] yaml_2.3.7      digest_0.6.31   brio_1.1.3      bitops_1.0-7   
    ## [21] RCurl_1.98-1.12 testthat_3.1.8  signal_0.7-7    evaluate_0.21  
    ## [25] rmarkdown_2.21  proxy_0.4-27    compiler_4.2.2  fftw_1.0-7

