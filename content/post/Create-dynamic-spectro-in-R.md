---
title: "Creating dynamic spectrograms (videos)"
author: "Marcelo Araya-Salas"
date: "2016-12-12"
rmd_hash: 93f785dfebf4d38e

---

<div class="alert alert-info">

<center>
Note that the code below is now available in the new R package <a href="https://marce10.github.io/dynaSpec/">dynaSpec</a>
</center>

</div>

This code creates a video with a spectrogram scrolling from right to left. The spectrogram is synchronized with the audio. This is done by creating single image files for each of the movie frames and then putting them together in .mp4 video format. You will need the ffmpeg UNIX application to be able to run the code (only works for OSX and Linux).

First load the [warbleR](https://cran.r-project.org/package=warbleR) package

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>require</a></span><span class='o'>(</span><span class='s'><a href='https://marce10.github.io/warbleR/'>"warbleR"</a></span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

Download and read the example sound file ([long-billed hermit](http://neotropical.birds.cornell.edu/portal/species/overview?p_p_spp=231771) song)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/download.file.html'>download.file</a></span><span class='o'>(</span>url <span class='o'>=</span> <span class='s'>"http://marceloarayasalas.weebly.com/uploads/2/5/5/2/25524573/0.sur.2014.7.3.8.31.wav"</span>, </span>
<span>    destfile <span class='o'>=</span> <span class='s'>"example.wav"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>wav1</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/readWave.html'>readWave</a></span><span class='o'>(</span><span class='s'>"example.wav"</span>, from <span class='o'>=</span> <span class='m'>0</span>, to <span class='o'>=</span> <span class='m'>19</span>, units <span class='o'>=</span> <span class='s'>"seconds"</span><span class='o'>)</span></span></code></pre>

</div>

Set the frequency limit, frames per second (video) and a margin of silence to add at the start and end of the sound file (so the sound starts playing at 0)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='nv'>tlimsize</span> <span class='o'>&lt;-</span> <span class='m'>1.5</span></span>
<span></span>
<span></span>
<span><span class='c'># frames per second</span></span>
<span><span class='nv'>fps</span> <span class='o'>&lt;-</span> <span class='m'>50</span></span>
<span></span>
<span><span class='c'>#margin</span></span>
<span><span class='nv'>marg</span> <span class='o'>&lt;-</span> <span class='nv'>tlimsize</span> <span class='o'>/</span> <span class='m'>2</span></span>
<span></span>
<span><span class='c'>#add silence</span></span>
<span><span class='nv'>wav</span> <span class='o'>&lt;-</span><span class='nf'><a href='https://rdrr.io/pkg/seewave/man/pastew.html'>pastew</a></span><span class='o'>(</span>wave2 <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/Waveforms.html'>silence</a></span><span class='o'>(</span>duration <span class='o'>=</span> <span class='nv'>marg</span>, samp.rate <span class='o'>=</span> <span class='nv'>wav1</span><span class='o'>@</span><span class='nv'>samp.rate</span>, </span>
<span>            xunit <span class='o'>=</span> <span class='s'>"time"</span><span class='o'>)</span>, wave1 <span class='o'>=</span> <span class='nv'>wav1</span>, f <span class='o'>=</span> <span class='nv'>wav1</span><span class='o'>@</span><span class='nv'>samp.rate</span>, </span>
<span>            output <span class='o'>=</span> <span class='s'>"Wave"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>wav</span> <span class='o'>&lt;-</span><span class='nf'><a href='https://rdrr.io/pkg/seewave/man/pastew.html'>pastew</a></span><span class='o'>(</span>wave1 <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/pkg/tuneR/man/Waveforms.html'>silence</a></span><span class='o'>(</span>duration <span class='o'>=</span> <span class='nv'>marg</span>, samp.rate <span class='o'>=</span> <span class='nv'>wav</span><span class='o'>@</span><span class='nv'>samp.rate</span>, </span>
<span>            xunit <span class='o'>=</span> <span class='s'>"time"</span><span class='o'>)</span>, wave2 <span class='o'>=</span> <span class='nv'>wav</span>, f <span class='o'>=</span> <span class='nv'>wav</span><span class='o'>@</span><span class='nv'>samp.rate</span>,</span>
<span>            output <span class='o'>=</span> <span class='s'>"Wave"</span><span class='o'>)</span></span></code></pre>

</div>

and create the individual images that will be put together in a video (this might take a while...)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#start graphic device to create image files</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/grDevices/png.html'>tiff</a></span><span class='o'>(</span><span class='s'>"fee%04d.tiff"</span>,res <span class='o'>=</span> <span class='m'>120</span>, width <span class='o'>=</span> <span class='m'>1100</span>, height <span class='o'>=</span> <span class='m'>700</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='m'>0</span></span>
<span></span>
<span><span class='c'>#loop to create image files </span></span>
<span><span class='kr'>repeat</span><span class='o'>&#123;</span></span>
<span></span>
<span>  <span class='nv'>tlim</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='nv'>x</span>, <span class='nv'>x</span> <span class='o'>+</span> <span class='nv'>tlimsize</span><span class='o'>)</span></span>
<span></span>
<span>  <span class='nf'><a href='https://rdrr.io/pkg/seewave/man/spectro.html'>spectro</a></span><span class='o'>(</span>wave <span class='o'>=</span> <span class='nv'>wav</span>, f <span class='o'>=</span> <span class='nv'>wav</span><span class='o'>@</span><span class='nv'>samp.rate</span>, wl <span class='o'>=</span> <span class='m'>300</span>, ovlp <span class='o'>=</span> <span class='m'>90</span>, </span>
<span>          flim <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>2</span>, <span class='m'>10.5</span><span class='o'>)</span>, tlim <span class='o'>=</span> <span class='nv'>tlim</span>, scale <span class='o'>=</span> <span class='kc'>F</span>, grid <span class='o'>=</span> <span class='kc'>F</span>, </span>
<span>          palette <span class='o'>=</span> <span class='nv'>gray.colors</span>,  norm <span class='o'>=</span> <span class='kc'>F</span>, dBref <span class='o'>=</span> <span class='m'>2</span><span class='o'>*</span><span class='m'>10e-5</span>, </span>
<span>          osc <span class='o'>=</span> <span class='kc'>T</span>, colgrid<span class='o'>=</span><span class='s'>"white"</span>, colwave<span class='o'>=</span><span class='s'>"chocolate2"</span>, </span>
<span>          colaxis<span class='o'>=</span><span class='s'>"white"</span>, collab<span class='o'>=</span><span class='s'>"white"</span>, colbg<span class='o'>=</span><span class='s'>"black"</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nf'><a href='https://rdrr.io/r/graphics/abline.html'>abline</a></span><span class='o'>(</span>v <span class='o'>=</span> <span class='nv'>tlim</span><span class='o'>[</span><span class='m'>1</span><span class='o'>]</span><span class='o'>+</span><span class='nv'>marg</span>, lty <span class='o'>=</span> <span class='m'>2</span>, col <span class='o'>=</span> <span class='s'>"skyblue"</span>, lwd <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nv'>x</span> <span class='o'>+</span> <span class='m'>1</span><span class='o'>/</span><span class='nv'>fps</span></span>
<span>  </span>
<span>  <span class='c'># stop when the end is reached</span></span>
<span>  <span class='kr'>if</span><span class='o'>(</span><span class='nv'>x</span> <span class='o'>&gt;=</span> <span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/length.html'>length</a></span><span class='o'>(</span><span class='nv'>wav</span><span class='o'>@</span><span class='nv'>left</span><span class='o'>)</span><span class='o'>/</span><span class='nv'>wav</span><span class='o'>@</span><span class='nv'>samp.rate</span><span class='o'>)</span> <span class='o'>-</span> <span class='nv'>tlimsize</span><span class='o'>)</span> <span class='kr'>break</span></span>
<span>  </span>
<span>  <span class='o'>&#125;</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/grDevices/dev.html'>dev.off</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

The last step is to run the ffmeg application in UNIX (only works for OSX and Linux, ffmpeg needs to be installed)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span></span>
<span><span class='c'>#Make video</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/system.html'>system</a></span><span class='o'>(</span><span class='s'>"ffmpeg -framerate 50 -i fee%04d.tiff -c:v libx264 -profile:v high -crf 2 -pix_fmt yuv420p spectro_movie.mp4"</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># save audio file</span></span>
<span><span class='nf'><a href='https://rdrr.io/pkg/seewave/man/savewav.html'>savewav</a></span><span class='o'>(</span>wave <span class='o'>=</span> <span class='nv'>wav1</span>,filename <span class='o'>=</span>  <span class='s'>"audio1.wav"</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'>#Add audio</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/system.html'>system</a></span><span class='o'>(</span><span class='s'>"ffmpeg -i spectro_movie.mp4 -i audio1.wav -vcodec libx264 -acodec libmp3lame -shortest spectro_movie_audio.mp4"</span><span class='o'>)</span></span>
<span></span></code></pre>

</div>

At the end you should get something like [the video in this link](https://www.youtube.com/embed/McAQaIXeuUQ)

<center>
<iframe allowtransparency="true" style="background: #FFFFFF;" style="border:0px solid lightgrey;" width="854/2" height="480/2" src="https://www.youtube.com/embed/McAQaIXeuUQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen>
</iframe>
</center>

