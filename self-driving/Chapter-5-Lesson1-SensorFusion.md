# Lesson 1: Sensors

## 1. Radar (Ratio Detection and Ranging)
### Strenths and weaknesses
- **Radar uses Doppler effect to measure speed --- get velocity directly and precise vertical velocity**

  Other sensors measure velocity by calculating the difference between two readings
  
- **Radar can be used for localization by generating radar maps of the environment**

  Radar waves bounce off hard-surfaces -> They can provide measurements to objects without direct line of sight(视线)
  
- **Radar is the least affected by rain or fog**
- **Radar can have a wide field of view(~150°) and a long range(200+ m)**
- Disadvantage: low resolution (especially in vertical direction), campared to lidar and camera

  low resolution means: refelection from static objects can cause problems (e.g. ground manhole covers has strong radar reflections)
  
  Therefore, current automotive radars usually disregrard static objects.

### Doppler effect
- Doppler effect measure the change in frequence of the radar waves based on whether the object is moving away from you or toward you.
- The velocity  output can be used in sensor fusion, which makes fusion algorithms converge much faster.


## 2. Lidar (Light Detection and Ranging)
### Strenths and weaknesses
- **Lidar uses infrared laser beam to determine the distance between the sensor and a nearby object**
  Lidar reflections returns a pointcloud to present objects
- **Lidar has a much highrt spatial resolution than radar**
  
  Beacuse of the more focused laser beam, the larger number of scan layers in the vertical direction 
  
- Disadvantage: cannot measure the velocity of objects directly (have to rely on the differing position between two or more scans)
- Lidar are more affected by weather conditions and by dirt on the sensor

# Lesson 2: Kalman Filter
- Requirement: Understanding where are the cars are, and make predictions on where they gonna move.

- Key idea: 
  1. KF estimates a **continuous** state
  2. KF gives us a **uni-modal(单峰)** distribution (vs: Particle filter: continous, multi-modal)
## Gausion distribution
In FK, distribution given here is Gaussion
- Gaussion is a continuous function over the space of locations. 
- Area underneath the gaussian function sums up to one
- mean: the center highest point, variance: the width of the Gaussian (half-width)
- 1D Gaussian: X ~ N($\mu$, $\sigma$^2)
-  $\sigma$ measures the uncertainty refering to real state.

**Our task in KF is:** to maintain $\mu$ and $\sigma$^2 as our best estimate of X. 
(保持对位置物体位置最佳估计的均值和方差)

**ans:** X ~  $\mu$， Gaussian function returns the maximum value

## Measurement and Motion
The Kalman Filter represents our distributions by Gaussians and iterates on two main cycles.
- The first cycle is the **Measurement Update** ---- update posterior (i.e., where we should be, based on the measurements)

**Uses Bayes rule + product**

<p align="center"> $P(B \mid A) = \frac{P(B) P(A \mid B)} {P(A)}$ </p>

if$B = { B_1, B_2, \dots, B_n}$

then,

<p align="center"> $P(A) = \sum_{i=1}^n P(B_i) P(A \mid B_i)$ </p>

we have 

<p align="center">$P(B_i \mid A) = \frac{P(B_i) P(A \mid B_i)}
{\sum_{i=1}^n P(B_i) P(A \mid B_i)}$</p>

We call $P(B_i \mid A)$ posterior probability. The bottom P(A) is used to normalize the probability.

- The second cycle is the **Motion Update** ---- do the prediction

**Uses total probability + convolution**
![image](https://user-images.githubusercontent.com/47606318/123800759-35cef780-d91c-11eb-8e07-c8bf725d7add.png)

## Shift of mean

**Q:** Assume we have prior (black Gaussian function) and measurement (blue), where will the new mean of new Gaussian function?
![image](https://user-images.githubusercontent.com/47606318/123800534-fb655a80-d91b-11eb-8681-7c44f4ce82c2.png)

**ans:** middle, between the two old means. It should be slightly near the measurement side, because the measurement was more certain than the proir according to the diagram above.

**Q:** Referring to the diagram above, what will be the width (variance) of new gaussian after multiplying these two gaussians?

**ans:** The resulting Gaussian is more certain than two component gaussians. (Variance is smaller than either of the two gaussians).
The two Gaussians together have a higher information content. (**Remember this conclusion**)

**Prove:** do the multiplying between two gaussian function, the new mean is weighted by two variances, the variance see below:  
(可通过两高斯函数相乘求解出来， 高斯函数相乘还是高斯函数)
 ![image](https://user-images.githubusercontent.com/47606318/123811127-8eef5900-d925-11eb-88d4-f0463ed1feb3.png)

This is the Kalman filter **measurment update step**, the resulting gaussian represents the posterior.
       
### Separated Gaussians
**Q:** Considering a prior distribution and a far away measurement distribution. These two distribution has the same variance. Where will be the mean&variance of resulting Gaussian?
![image](https://user-images.githubusercontent.com/47606318/123811918-43897a80-d926-11eb-8e96-26e00084e694.png)

**ans:** 
- Mean is in the middle, because the two variance are same, which averaging the means 
- Vaiance is half of the old one(can use the formula above)
-（注意图中y值临近0， 但不等于0， 两个高斯函数一个表示先验pior， 一个表示似然 likelihood）

### Gaussian motion update
蓝色：current location

绿色：motion

红色：predicted location in the next step (X+motion = X')

(可参照：两相互独立，分别关于X,Y的高斯分布相加(X+Y)，所得结果就是mean 和 variance相加的定理 --- 两高斯分布做卷积)

reference: https://blog.csdn.net/chaosir1991/article/details/106960408/

![image](https://user-images.githubusercontent.com/47606318/123816329-ce1fa900-d929-11eb-9eb7-fb2396990845.png)

### Python implementation of measurement update and prediction

**1. measurement update**
![image](https://user-images.githubusercontent.com/47606318/123821141-ba764180-d92d-11eb-9df0-3db6eff1225a.png)

**2. motion prediction**

![image](https://user-images.githubusercontent.com/47606318/123821229-ccf07b00-d92d-11eb-9841-8e7fee6de4af.png)

**3. initialize values**

![image](https://user-images.githubusercontent.com/47606318/123821559-16d96100-d92e-11eb-91f6-74af717343db.png)

**4. kalman filter 1d** (start with initial value, and then use the resulting value)
![image](https://user-images.githubusercontent.com/47606318/123821498-06c18180-d92e-11eb-8cc2-7fc5ac9f23ad.png)

### Kalman filter in higher dimensions 
- KF in higher dimension not just allow you to just go into different dimension spaces (e.g. x, y), but also also allows you to **figure out the velocity of the object**. Use of the **velocity estimate for predicition** allow you to make a **good prediction of location**.
- Notice: sensor itself only sees position. Velocity is inferred from multiple seen positions

#### High dimensional Gaussians (multivariate Gaussians)
- n dimensional vector of mean
- nxn matrix of **covariance**
- diagram of two dimensional gaussian (mean: center (x0,y0), covariance defiens the spread of the Gaussian)

(you may have certain uncertainty in one dimension and huge uncertainty in another)

![image](https://user-images.githubusercontent.com/47606318/124127978-e836c400-daae-11eb-8237-df8e64bc5831.png)

### Kalman filter (e.g. two dimension example)
- Build two dimensional estimate e.g. 1 for location, 1 for velocity
- Initially know my location and velocity -> represent it with 2D gaussian that elevated around the location & board in velocity (shown in diagram)
- Prediction step: dont know the velocity -> so cant know current location.

  But there will be some correlation: use the location to calculate velocity and to predict the location in next time.
    **That is, location is correlated with volocity**
       x' = x + Δtẋ'
 ![image](https://user-images.githubusercontent.com/47606318/124130054-f38aef00-dab0-11eb-9dc7-9e7061173ffe.png)

Summary: In Kalman Filter, we can figure out hidden variables (e.g. velocity) from observed measurements (e.g. velocity), which helps us to do the prediction.

### Design of Kalman Filter
We need:
- for the state, we need transition function. (KF: uses a matrix, because we assumes linear motion)
- for the measurement, we need a measuremtn function.
- prediction & measurement update (below)
![image](https://user-images.githubusercontent.com/47606318/124137151-fe954d80-dab7-11eb-8a9c-2935f9528e8c.png)

**Note: KF精髓在于Kalman gain. Kalman gain本质上是weighted average 的 weight。你越相信你的prediction，uncertainty越小，observation的weight就越小；反之，你越不相信你的prediction，uncertainty越大，observation的weight越大。**


