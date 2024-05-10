---
permalink: /
title: "MetNet-2: A leap in Weather Prediction"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---
In our daily lives, weather forecasting is essential for everything from organising outdoor activities to getting ready for extreme weather conditions. Existing weather forecasting models use Numerical weather prediction(NWP) models which are based on sophisticated  computer simulationss used to forecast future weather conditions based on current and past atmospheric data. However, these models face computational bottlenecks and time lags, limiting their effectiveness, especially for short-term forecasts. 

![Weather Forecast](/images/img1.jpeg "Fig 1:Weather forecast")
*Fig 1: Weather Forecast*

Efficient models based on deep neural networks
represent a promising alternative framework for weather modeling (ADD REFERENCE HERE 5,6). 
This blog post presents a neural network capable of predicting precipitation at a high resolution up to 12 h ahead.

## The Need for Improved Short-Term Forecasting: 
NWP models rely on complex mathematical equations and supercomputers to simulate the behavior of the atmosphere over time.These models require significant computational resources to simulate the complex interactions of the atmosphere accurately. Running high-resolution models over large geographic areas and long forecast periods can be computationally intensive and time-consuming. These models are sensitive to errors in the initial conditions. Small inaccuracies in the observed data can lead to significant errors in the forecast. 

Neural network-driven models present a hopeful option as they extract connections from data directly. They have the capability to produce forecasts more frequently and at finer resolutions, operating smoothly on parallel hardware within seconds. Moreover, neural networks inherently offer probabilistic forecasts, encompassing forecast fluctuations derived from the data.These properties can not only offer
improved forecasts, but also frequent and personalized forecasts(ADD REFERENCE 3)

## Introducing MetNet-2: 
MetNet-2 is a probabilistic weather model based on deep neural networks that is a successor to MetNet (ADD REFERENCE 7).Unlike physics-based models, MetNet-2 learns directly from data, bypassing the need for complex simulations.

![Types of Weather Models](/images/img2.png)
*Fig. 2: Types of Weather Models (WRITE THE DESCRIPTION IN DETAIL)*

### Key Features of MetNet-2

* **High Resolution and Frequency:** MetNet-2 has frequency of 2min and a spatial resolution of 1 km. Whereas weather forecasts models typically have a grid resolution of 3–12 km and a frequency of one or more hours.

* **Large Input Context:** It processes data from a 2048 km x 2048 km area to ensure comprehensive coverage and accuracy.

* **Avanced Neural Architecture:** The model incorporates novel neural network elements like a context-aggregating module, which doubles the receptive field with each layer, enhancing its ability to make precise predictions over larger areas, strong lead time conditioning scheme and a model parallel training setup utilizing multiple chips for increased memory and parallel computation (ADD REFERENCE OF THIS PAPER)
 

### MetNet-2 Architechture 
#### Framework
This framework is designed to leverage the capabilities of neural networks for weather forecasting.

##### Data Collection and Preparation
MetNet-2 model needs initial state of the atmosphere as the basis of forecast. The Data is obtained from sensors located in weather stations, sattelites and ground radars. Radars estimate the reflectivity which provide estimates of precipitation every few minutes with a spatial resolution of 1 km × 1 km.


**Precipitation Measures used:** Two main types of measures used are instantaneous precipitation, obtained from radar reflectivity every two minutes, and hourly cumulative precipitation, indicating the total precipitation over the past hour. The radar measurements are supplemented by rain gauges at weather stations to enhance the accuracy and reliability of the data.


**Assimilation Features:**The Radar does not provide information on pressure, temperature and wind velocity and direction. To incorporate these infomation in the model, available set of atmospheric
observations that result from the data assimilation process in the
NWPmodel HRRR is used.Furthermore, space-time coordinates encompassing longitude, latitude, elevation, and forecast time, along with optical satellite images os also provided.

##### Model Architecture and Training
MetNet- 2 uses a probabilistic formulation for precipitation forecasting

$$
P(r_{x}, y, t \,|\, t_{0}) = f(c_{x}, y, t_{0}, L)
$$

where r are rates of precipitation <br>
x,y,t are the location and target time of the forecast <br>
t0 is the time at which the forecast is made <br>
c(x,y,t0) is the atmospheric context at time t0 relevant for location x,y <br>
L = t − t0 is the lead time of the forecast <br>

MetNet-2 aims to learn the function f that takes the atmospheric context (radar, satellite, weather model data etc.) at a given time t0 and the desired lead time L, and outputs the probability distribution P(rx,y,t|t0) over 512 categories of precipitation rates at each location (x, y) and future time t.

###### Input Encoder

+ MetNet-2 processes input data from a large context area of 2048 km × 2048 km around the target location. This includes radar reflectivity, satellite imagery, and weather state data from NWP models. a larger input context is required for longer lead time forecasts to capture enough atmospheric information for accurate precipitation predictions. This context size allows MetNet-2 to effectively forecast up to 12 hours ahead by providing sufficient spatial context around the target area.

+ The context size 2048 km x 2048 km is downsampled via averaging by a
factor of 4 in each spatial dimension, resulting in an input patch of
512 × 512 positions. This downsampling maintains a sufficient amount of information in the context while substantially reducing the computational resources required to encode the input. However, there is a tradeoff. 

![MetNet-2 context aggregation and architecture](/images/img3.png)
*Fig. 3: MetNet-2 gathers more context surrounding the target patch, gradually expanding its coverage. The illustration depicts how the orthographic projection influences both the context and target, as they are projected onto Earth using an equirectangular projection.*

+ Furthermore, the input patches also has spatial dimensions and a time dimension in the form of multiple time slices

+ After padding
and concatenation together along the depth axis, the input sets are
embedded using a convolutional recurrent network(REFERENCE 10) in the time
dimension(REFERENCE 7 ). (PARAHRASE)

This is the front page of a website that is powered by the [Academic Pages template](https://github.com/academicpages/academicpages.github.io) and hosted on GitHub pages. [GitHub pages](https://pages.github.com) is a free service in which websites are built and hosted from code and data stored in a GitHub repository, automatically updating when a new commit is made to the respository. This template was forked from the [Minimal Mistakes Jekyll Theme](https://mmistakes.github.io/minimal-mistakes/) created by Michael Rose, and then extended to support the kinds of content that academics have: publications, talks, teaching, a portfolio, blog posts, and a dynamically-generated CV. You can fork [this repository](https://github.com/academicpages/academicpages.github.io) right now, modify the configuration and markdown files, add your own PDFs and other content, and have your own site for free, with no ads! An older version of this template powers my own personal website at [stuartgeiger.com](http://stuartgeiger.com), which uses [this Github repository](https://github.com/staeiou/staeiou.github.io).

A data-driven personal website
======
Like many other Jekyll-based GitHub Pages templates, Academic Pages makes you separate the website's content from its form. The content & metadata of your website are in structured markdown files, while various other files constitute the theme, specifying how to transform that content & metadata into HTML pages. You keep these various markdown (.md), YAML (.yml), HTML, and CSS files in a public GitHub repository. Each time you commit and push an update to the repository, the [GitHub pages](https://pages.github.com/) service creates static HTML pages based on these files, which are hosted on GitHub's servers free of charge.

Many of the features of dynamic content management systems (like Wordpress) can be achieved in this fashion, using a fraction of the computational resources and with far less vulnerability to hacking and DDoSing. You can also modify the theme to your heart's content without touching the content of your site. If you get to a point where you've broken something in Jekyll/HTML/CSS beyond repair, your markdown files describing your talks, publications, etc. are safe. You can rollback the changes or even delete the repository and start over -- just be sure to save the markdown files! Finally, you can also write scripts that process the structured data on the site, such as [this one](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.ipynb) that analyzes metadata in pages about talks to display [a map of every location you've given a talk](https://academicpages.github.io/talkmap.html).

Getting started
======
1. Register a GitHub account if you don't have one and confirm your e-mail (required!)
1. Fork [this repository](https://github.com/academicpages/academicpages.github.io) by clicking the "fork" button in the top right. 
1. Go to the repository's settings (rightmost item in the tabs that start with "Code", should be below "Unwatch"). Rename the repository "[your GitHub username].github.io", which will also be your website's URL.
1. Set site-wide configuration and create content & metadata (see below -- also see [this set of diffs](http://archive.is/3TPas) showing what files were changed to set up [an example site](https://getorg-testacct.github.io) for a user with the username "getorg-testacct")
1. Upload any files (like PDFs, .zip files, etc.) to the files/ directory. They will appear at https://[your GitHub username].github.io/files/example.pdf.  
1. Check status by going to the repository settings, in the "GitHub pages" section

Site-wide configuration
------
The main configuration file for the site is in the base directory in [_config.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_config.yml), which defines the content in the sidebars and other site-wide features. You will need to replace the default variables with ones about yourself and your site's github repository. The configuration file for the top menu is in [_data/navigation.yml](https://github.com/academicpages/academicpages.github.io/blob/master/_data/navigation.yml). For example, if you don't have a portfolio or blog posts, you can remove those items from that navigation.yml file to remove them from the header. 

Create content & metadata
------
For site content, there is one markdown file for each type of content, which are stored in directories like _publications, _talks, _posts, _teaching, or _pages. For example, each talk is a markdown file in the [_talks directory](https://github.com/academicpages/academicpages.github.io/tree/master/_talks). At the top of each markdown file is structured data in YAML about the talk, which the theme will parse to do lots of cool stuff. The same structured data about a talk is used to generate the list of talks on the [Talks page](https://academicpages.github.io/talks), each [individual page](https://academicpages.github.io/talks/2012-03-01-talk-1) for specific talks, the talks section for the [CV page](https://academicpages.github.io/cv), and the [map of places you've given a talk](https://academicpages.github.io/talkmap.html) (if you run this [python file](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.py) or [Jupyter notebook](https://github.com/academicpages/academicpages.github.io/blob/master/talkmap.ipynb), which creates the HTML for the map based on the contents of the _talks directory).

**Markdown generator**

I have also created [a set of Jupyter notebooks](https://github.com/academicpages/academicpages.github.io/tree/master/markdown_generator
) that converts a CSV containing structured data about talks or presentations into individual markdown files that will be properly formatted for the Academic Pages template. The sample CSVs in that directory are the ones I used to create my own personal website at stuartgeiger.com. My usual workflow is that I keep a spreadsheet of my publications and talks, then run the code in these notebooks to generate the markdown files, then commit and push them to the GitHub repository.

How to edit your site's GitHub repository
------
Many people use a git client to create files on their local computer and then push them to GitHub's servers. If you are not familiar with git, you can directly edit these configuration and markdown files directly in the github.com interface. Navigate to a file (like [this one](https://github.com/academicpages/academicpages.github.io/blob/master/_talks/2012-03-01-talk-1.md) and click the pencil icon in the top right of the content preview (to the right of the "Raw | Blame | History" buttons). You can delete a file by clicking the trashcan icon to the right of the pencil icon. You can also create new files or upload files by navigating to a directory and clicking the "Create new file" or "Upload files" buttons. 

Example: editing a markdown file for a talk
![Editing a markdown file for a talk](/images/editing-talk.png)

For more info
------
More info about configuring Academic Pages can be found in [the guide](https://academicpages.github.io/markdown/). The [guides for the Minimal Mistakes theme](https://mmistakes.github.io/minimal-mistakes/docs/configuration/) (which this theme was forked from) might also be helpful.
