# Introduction

This page is a _un_-complete list of supported sensors by betaflight. To be sure that your sensor is supported check in _/src/main/sensors/_ gyro.c, barometer.c, rangefinder.c, compass.c 

## Compass
* AK8975
* AK8963
* HMC5883l
* QMC5883l
* HMC5983

## Gyroscope / Accelerometer
* MPU3050
* MPU6050
* MPU6500
* MPU6000
* MPU9250
* BMI160
* ICM20649
* ICM20689
* L3GD20
* ~L3G4200D~ Retiring (https://github.com/betaflight/betaflight/pull/5900)

## Barometer
* BMP085
* BMP280
* QMP6988
* MS5611

## Range Finder
* HCSR04
* Benewake LIDAR TF02
* Benewake LIDAR TFmini