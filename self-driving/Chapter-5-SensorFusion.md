# Sensors

## Radar (Ratio Detection and Ranging)
- **Radar uses Doppler effect to measure speed**

  Other sensors measure velocity by calculating the difference between two readings
  
- **Radar can be used for localization by generating radar maps of the environment**

  Radar waves bounce off hard-surfaces -> They can provide measurements to objects without direct line of slight(视线)
  
- **Radar is the least affected by rain or fog**
- **Radar can have a wide field of view(~150°) and a long range(200+ m)**
- Disadvantage: low resolution, campared to lidar and camera

  low resolution means: refelection from static objects can cause problems (e.g. ground manhole covers has strong radar reflections)
  
  Therefore, current automotive radars usually disregrard static objects.

### Doppler effect
- Doppler effect measure the change in frequence of the radar waves based on whether the object is moving away from you or toward you.
- The velocity  output can be used in sensor fusion, which makes fusion algorithms converge much faster.

## Lidar (Light Detection and ranging)
