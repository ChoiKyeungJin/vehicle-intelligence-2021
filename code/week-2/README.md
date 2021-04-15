
# motion_model

이전에 있을 수 있는 모든 위치에서 차량이 다음에 어느 위치로 이동할 확률을 구하는 함수이다.
norm_pdf를 이용하여 prediction과정을 수행하며, 직전에 어디에 있었는지에 대한 확률을 가진 priors를 곱해줌
으로써 다음 위치에 대한 확률을 구한다.

<pre>
<code>
for i in range(map_size):
    tmp = norm_pdf(position-i, mov, stdev)*priors[i]
    position_prob += tmp
</code>
</pre>

# observation_model

주어진 observation 정확도(신뢰도)의 확률을 구하는 함수이다.
observation 범위가 pseudo(지도) 범위 이내라면 norm_pdf를 이용하여 관측 신뢰도의 확률을 구하며
관측이 없거나 관측 범위가 지도를 넘어가 버리면 확률을 0으로 취급한다. 

<pre>
<code>
if(len(observations) <= len(pseudo_ranges)):
        for p, o in zip(pseudo_ranges, observations):
                tmp = norm_pdf(o,p,stdev)
                distance_prob *= tmp
    else:
        distance_prob = 0

</code>
</pre>


# Week 2 - Markov Localization

---

[//]: # (Image References)
[plot]: ./markov.gif

## Assignment

You will complete the implementation of a simple Markov localizer by writing the following two functions in `markov_localizer.py`:

* `motion_model()`: For each possible prior positions, calculate the probability that the vehicle will move to the position specified by `position` given as input.
* `observation_model()`: Given the `observations`, calculate the probability of this measurement being observed using `pseudo_ranges`.

The algorithm is presented and explained in class.

All the other source files (`main.py` and `helper.py`) should be left as they are.

If you correctly implement the above functions, you expect to see a plot similar to the following:

![Expected Result of Markov Localization][plot]

If you run the program (`main.py`) without any modification to the code, it will generate only the frame of the above plot because all probabilities returned by `motion_model()` are zero by default.
