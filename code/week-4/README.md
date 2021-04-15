# update_weights
multi-variate Gaussian Distribution을 이용하여 각 파티클의 가중치를 업데이트 한다.
1. 센서 범위 이내로 인지할 수 있는 랜드마크들을 구한다(예측한다).(sensor_range보다 유클리디안 거리가 작으면 보이는 것으로 취급)(line 84~95)
2. 관측된 랜드마크의 좌표계를 로컬에서 전체 맵 좌표로 전환한다.(변환식 사용)(line 99~107)
3. 예측(1번)과 관측(2번)을 associate()함수를 이용하여 매칭하고 반환한다. (line 118)
4. 각각의 매칭과 파티클의 확률을 multi-variate Gaussian distribution 기반으로 계산하고 파티클의 weight를 업데이트 한다.(line 125~137)

# resample
가중치에 비례하는 확률로 대체된 입자들을 다시 샘플링한다.
1. 입자의 가중치를 기반으로 particle set을 재구성한다.(line 146~148)
2. 모든 입자의 가중치와 uniform distribution의 계산 값을 비교하여 다시 샘플링한다.(line 156~164) 

# Week 4 - Motion Model & Particle Filters

---

[//]: # (Image References)
[empty-update]: ./empty-update.gif
[example]: ./example.gif

## Assignment

You will complete the implementation of a simple particle filter by writing the following two methods of `class ParticleFilter` defined in `particle_filter.py`:

* `update_weights()`: For each particle in the sample set, calculate the probability of the set of observations based on a multi-variate Gaussian distribution.
* `resample()`: Reconstruct the set of particles that capture the posterior belief distribution by drawing samples according to the weights.

To run the program (which generates a 2D plot), execute the following command:

```
$ python run.py
```

Without any modification to the code, you will see a resulting plot like the one below:

![Particle Filter without Proper Update & Resample][empty-update]

while a reasonable implementation of the above mentioned methods (assignments) will give you something like

![Particle Filter Example][example]

Carefully read comments in the two method bodies and write Python code that does the job.
