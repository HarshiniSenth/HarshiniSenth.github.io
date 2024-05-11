---
permalink: /
title: "MetNet-2: A leap in Weather Prediction"
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---
Weather forecasting is essential for everything from organising outdoor activities to getting ready for extreme weather conditions. Existing weather forecasting models use Numerical weather prediction(NWP) models which are based on sophisticated  computer simulationss used to forecast future weather conditions based on current and past atmospheric data. However, these models face computational bottlenecks and time lags, limiting their effectiveness, especially for short-term forecasts. 

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
 

![GIF](https://blogger.googleusercontent.com/img/a/AVvXsEhpJxLN_5CuSyz_gt-xrDIoLyi1HQ0PdAYHQgomGhbABA-qbDAcevBYsq0XcgpozNP3e_UWvwmgoUOC6pxv5vjKnDM4Wdn8zy6GYv3jVe3iccXNKh_-x4V0RAFugPfroW3FHmFcuQmLTzaNZGDultf0a72BrweNciUhWKSzYcgl1buu0gyg7BrV7k--WA=s1600)
*Fig 2: Illustration of the computation through MetNet-2. As the computation progresses, the network processes an ever larger context from the input and makes a probabilistic forecast of the likely future weather conditions. (ADD REF)*

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

###### Exponentially dilated convolutions
+ MetNet-2 uses two-dimensional convolutional residual blocks with exponentially increasing dilation factors (1, 2, 4, ..., 128). This allows each position in the layer representing the encoded context to connect with every other position, capturing the full context necessary for forecasting

