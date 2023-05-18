---
layout: post
title: "Signal detection with cross-correlation using monitoR "
date: 15-12-2016
---




Here I show how to detect signals with cross-correlation using the very cool package [monitoR](https://cran.r-project.org/package=monitoR). This package aims to facilitate acoustic template detection. The code here is similar but much less detailed than the [quick start](https://cran.r-project.org/web/packages/monitoR/vignettes/monitoR_QuickStart.pdf) vignette of the [monitoR](https://cran.r-project.org/package=monitoR) package, so I encourage to look at the vignette if you want to learn more about it. 

The package [monitoR](https://cran.r-project.org/package=monitoR) runs cross-correlation across sound files to search for the signals using previously defined templates. Thus, templates should be examples of the signals we want to detect. It can search for several templates in the same run. I show how the package works using the sound files and data examples that come with the package [warbleR](https://cran.r-project.org/package=warbleR). These are recordings from long-billed hermits, each one singing a different song type.

First load the packages (the code will install the packages if missing)


```r
x<-c("warbleR", "monitoR")

A <- lapply(x, function(y) {
  if(!y %in% installed.packages()[,"Package"])  install.packages(y)
require(y, character.only = T)
  })
```

We need to create the templates. We just have to select and example from each sound file and provide the start and end as well as the frequency range of the signal. We will load the long-billed hermit sound


```r
#optional: write files in temporal file
# setwd(file.path(tempdir()))

# load sound files and data
data(list = c("Phae.long1", "Phae.long2", "Phae.long3", "Phae.long4", "selec.table"))

#write files to disk
writeWave(Phae.long1,"Phae.long1.wav")
writeWave(Phae.long2,"Phae.long2.wav")
writeWave(Phae.long3,"Phae.long3.wav")
writeWave(Phae.long4,"Phae.long4.wav")
```


and create the templates


```r
phae1T1<-makeCorTemplate("Phae.long1.wav", t.lim=c(selec.table$start[2],selec.table$end[2]),wl = 300,ovlp=90,
    frq.lim=c(1, 11), dens=1, name="phae11")
```

![plot of chunk unnamed-chunk-3](/assets/Rfig/unnamed-chunk-3-1.png)

{% highlight text %}

Automatic point selection.

Done.
```



```r
phae2T1<-makeCorTemplate("Phae.long2.wav", t.lim=c(selec.table$start[5],selec.table$end[5]),wl = 300,ovlp=90,
       frq.lim=c(1, 11), dens=1, name="phae21")
```

![plot of chunk unnamed-chunk-3](/assets/Rfig/unnamed-chunk-3-2.png)

{% highlight text %}

Automatic point selection.

Done.
```



```r
phae3T1<-makeCorTemplate("Phae.long3.wav", t.lim=c(selec.table$start[7],selec.table$end[7]),wl = 300,ovlp=90,
       frq.lim=c(1, 11), dens=1, name="phae31")
```

![plot of chunk unnamed-chunk-3](/assets/Rfig/unnamed-chunk-3-3.png)

{% highlight text %}

Automatic point selection.

Done.
```



```r
phae4T1<-makeCorTemplate("Phae.long4.wav", t.lim=c(selec.table$start[9],selec.table$end[9]),wl = 300,ovlp=90,
       frq.lim=c(1, 11), dens=1, name="phae41")
```

![plot of chunk unnamed-chunk-3](/assets/Rfig/unnamed-chunk-3-4.png)

{% highlight text %}

Automatic point selection.

Done.
```

Now that we have the templates we can search for those "acoustic patterns" in a sound file. As the function uses cross-correlation to determine the similarity to the templates we need to select a correlation method. In this case we use Pearson correlation. Lets do it first with the template from the Phae.long1.wav file


```r
cm<-"pearson"
cscoresPhae1<-corMatch(survey = "Phae.long1.wav",templates = phae1T1, parallel = T,show.prog = F, time.source = "fileinfo",
                  cor.method = cm,warn=F,write.wav = T)
```



{% highlight text %}

Starting  phae11 . . .
	Fourier transform on survey . . .
	Continuing. . .

	Done.
```

Now we can extract the detection peaks and generate a graphical output


```r
cdetectsPhae1<-findPeaks(cscoresPhae1, parallel = TRUE)
```



{% highlight text %}

Done with  phae11
Done
```



```r
# View results
plot(cdetectsPhae1, hit.marker="points")
```

![plot of chunk unnamed-chunk-5](/assets/Rfig/unnamed-chunk-5-1.png)

We can do the same for each sound file

```r
cscoresPhae2<-corMatch(survey = "Phae.long2.wav",templates = phae2T1, parallel = T,show.prog = F, time.source = "fileinfo",
                  cor.method = cm,warn=F,write.wav = T)
```



{% highlight text %}

Starting  phae21 . . .
	Fourier transform on survey . . .
	Continuing. . .

	Done.
```



```r
cdetectsPhae2<-findPeaks(cscoresPhae2, parallel = TRUE)
```



{% highlight text %}

Done with  phae21
Done
```



```r
# View results
plot(cdetectsPhae2, hit.marker="points")
```

![plot of chunk unnamed-chunk-6](/assets/Rfig/unnamed-chunk-6-1.png)


```r
cscoresPhae3<-corMatch(survey = "Phae.long3.wav",templates = phae3T1, parallel = T,show.prog = F, time.source = "fileinfo",
                  cor.method = cm,warn=F,write.wav = T)
```



{% highlight text %}

Starting  phae31 . . .
	Fourier transform on survey . . .
	Continuing. . .

	Done.
```



```r
cdetectsPhae3<-findPeaks(cscoresPhae3, parallel = TRUE)
```



{% highlight text %}

Done with  phae31
Done
```



```r
# View results
plot(cdetectsPhae3, hit.marker="points")
```

![plot of chunk unnamed-chunk-7](/assets/Rfig/unnamed-chunk-7-1.png)


```r
cscoresPhae4<-corMatch(survey = "Phae.long4.wav",templates = phae4T1, parallel = T,show.prog = F, time.source = "fileinfo",
                  cor.method = cm,warn=F,write.wav = T)
```



{% highlight text %}

Starting  phae41 . . .
	Fourier transform on survey . . .
	Continuing. . .

	Done.
```



```r
cdetectsPhae4<-findPeaks(cscoresPhae4, parallel = TRUE)
```



{% highlight text %}

Done with  phae41
Done
```



```r
# View results
plot(cdetectsPhae4, hit.marker="points")
```

![plot of chunk unnamed-chunk-8](/assets/Rfig/unnamed-chunk-8-1.png)

We can also run all templates on a single sound file. It requires putting together all templates in a single object


```r
#put templates together
ctemps<-combineCorTemplates(phae1T1, phae2T1, phae3T1, phae4T1)

cscoresPhae4all<-corMatch(survey = "Phae.long1.wav",templates = ctemps, parallel = T,show.prog = F, time.source = "fileinfo",
                  cor.method = cm,warn=F, write.wav = T)
```



{% highlight text %}

Starting  phae11 . . .
	Fourier transform on survey . . .
	Continuing. . .

	Done.

Starting  phae21 . . .
	Done.

Starting  phae31 . . .
	Done.

Starting  phae41 . . .
	Done.
```



```r
cdetectsPhae4all<-findPeaks(cscoresPhae4all, parallel = TRUE)
```



{% highlight text %}

Done
```



```r
# View results
plot(cdetectsPhae4all, hit.marker="points")
```

![plot of chunk unnamed-chunk-9](/assets/Rfig/unnamed-chunk-9-1.png)


