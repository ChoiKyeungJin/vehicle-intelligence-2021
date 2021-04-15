# update_ekf

기존의 KF는 이산적인 데이터에 대해서만 사용이 가능하기 때문에 연속적인 데이터를 다루기위해
EKF를 사용한다.
전체적인 흐름은 baysfilter를 기반으로 한 KF와 유사하지만 Kalman Gain을 구해주는 과정이 추가된다.

<pre>
<code>
# state variable의 쟈코비안 행렬을 구한다.
H_j = Jacobian(self.x)

# Kalman gain을 구하기 위한 중간 계산을 한다.
S = np.dot(np.dot(H_j, self.P), H_j.T) + self.R

# Kalman gian을 계산한다.
K = np.dot(np.dot(self.P, H_j.T), np.linalg.inv(S)) 

# 관측된 값과 예측된 값의 오차를 계산한다.
px, py, vx, vy = self.x
r = sqrt(px*px+py*py)
p = atan2(py,px)
r_dot = (px*vx+py*vy)/r
h = np.array([r,p,r_dot], dtype=np.float32)
y = z - h

# 라디안 각도의 범위를 -PI에서 PI까지로 보정한다.
if (z[1] > pi): z[1] -=2*pi
elif (z[1] < -pi): z[1] +=2*pi

# K, y, x를 이용하여 예측을 갱신하고, P, K, H_j를 이용하여 새로운 예측 공분산 P를 계산한다.
self.x = self.x + np.dot(K,y)
self.P = np.dot(np.eye(4)-np.dot(K,H_j), self.P)
</code>
</pre>



# Week 3 - Kalman Filters, EKF and Sensor Fusion

---

[//]: # (Image References)
[kalman-result]: ./kalman_filter/graph.png
[EKF-results]: ./EKF/plot.png

## Kalman Filter Example

In directory [`./kalman_filter`](./kalman_filter), a sample program for a small-scale demonstration of a Kalman filter is provided. Run the following command to test:

```
$ python testKalman.py
```

This program consists of four modules:

* `testKalman.py` is the module you want to run; it initializes a simple Kalman filter and estimates the position and velocity of an object that is assumed to move at a constant speed (but with measurement error).
* `kalman.py` implements a basic Kalman fitler as described in class.
* `plot.py` generates a plot in the format shown below.
* `data.py` provides measurement and ground truth data used in the example.

The result of running this program with test input data is illustrated below:

![Testing of Kalman Filter Example][kalman-result]

Interpretation of the above results is given in the lecture.

In addition, you can run `inputgen.py` to generate your own sample data. It will be interesting to experiment with a number of data sets with different characteristics (mainly in terms of variance, i.e., noise, involved in control and measurement).

---

## Assignment - EFK & Sensor Fusion Example

In directory [`./EKF`](./EKF), template code is provided for a simple implementation of EKF (extended Kalman filter) with sensor fusion. Run the following command to test:

```
$ python run.py
```

The program consists of five modules:

* `run.py` is the modele you want to run. It reads the input data from a text file ([data.txt](./EKF/data.txt)) and feed them to the filter; after execution summarizes the result using a 2D plot.
* `sensor_fusion.py` processees measurements by (1) adjusting the state transition matrix according to the time elapsed since the last measuremenet, and (2) setting up the process noise covariance matrix for prediction; selectively calls updated based on the measurement type (lidar or radar).
* `kalman_filter.py` implements prediction and update algorithm for EKF. All the other parts are already written, while completing `update_ekf()` is left for assignment. See below.
* `tools.py` provides a function `Jacobian()` to calculate the Jacobian matrix needed in our filter's update algorithm.
*  `plot.py` creates a 2D plot comparing the ground truth against our estimation. The following figure illustrates an example:

![Testing of EKF with Sensor Fusion][EKF-results]

### Assignment

Complete the implementation of EKF with sensor fusion by writing the function `update_ekf()` in the module `kalman_filter`. Details are given in class and instructions are included in comments.
