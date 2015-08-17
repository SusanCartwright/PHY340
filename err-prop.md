#Errors, uncertainties and distributions

You were introduced to experimental uncertainties in the first and second year labs, but we will review the basics here anyway.

##Terminology (see  [this notebook](variance.ipynb))

Formally, the **error** in a measurement is the difference between the experimental result and the true value, and is unknown, whereas the **uncertainty** is a measure of the _expected precision_ of the experimental result, which is known, or at least estimated.  However, most physicists say "experimental error" when they mean "uncertainty", and nobody talks about "uncertainty bars" on a plot!  In this course, we will follow the normal usage and talk about errors and uncertainties more-or-less interchangeably.

There are two fundamentally different types of experimental uncertainty:

* **statistical** or **random** errors are caused by limitations in the precision of your equipment and/or inherent randomness in the measurement (e.g. number of radioactive decays per second, number of photons per second from a faint source), and affect each measurement independently &ndash; they cause point-to-point scatter in repeated measurements, but do not affect the mean;

* **systematic** errors are caused by some overall problem with the experiment, such as uncertainties in calibration, use of approximations in the theory, saturation of instruments at high signal rates, or non-zero dark currents at low rates, etc. &ndash; they cause *correlated* changes in different measurements, so typically they affect the mean but not the scatter.

In addition, if more than one variable is involved, the measurements of different variables may be **correlated** or **uncorrelated**.  If the measurements are uncorrelated, the difference between your measured value of _x_ and its true value does not have any relation to the difference between your measured _y_ and its true value.  In correlated variables, on the other hand, this is not the case: if you measure _x_ too high, your measurement of _y_ will also be systematically too high (*correlated*) or too low (*anticorrelated*).  The degree of correlation is measured by the **covariance** or **correlation coefficient** of _x_ and _y_.

##Correlation and covariance (see  [this notebook](variance.ipynb))

The key measure of the spread of a distribution about its mean is the **variance**, which is the average squared deviation from the mean: _V_<sub>xx</sub> = &sigma;<sub>x</sub><sup>2</sup> = (&sum;(x<sub>i</sub> - &lang;x&rang;)<sup>2</sup>)/(_N_ - 1), where _N_ is the number of measurements, &lang;x&rang; is the mean, and the reason for the factor of _N_ - 1 instead of _N_ is that we have used up one **degree of freedom** by using the data to determine the mean (if we knew the true value _a priori_, then one point would be enough to give a rough estimate of the spread, but if we have to use the data to estimate the true value, then we need at least two points to estimate the spread about the true value).

If we have two variables, the degree to which they are correlated is given by the **covariance**, defined as _V_<sub>xy</sub> = (&sum;(x<sub>i</sub> - &lang;x&rang;)(y<sub>i</sub> - &lang;y&rang;))/(_N_ - 1).  For uncorrelated variables, the signs and magnitudes of (x<sub>i</sub> - &lang;x&rang;) and (y<sub>i</sub> - &lang;y&rang;) are unrelated, so the product is as likely to be positive as negative and will average out to zero.  For correlated variables, the signs and magnitudes _are_ related, so the average, and hence _V_<sub>xy</sub>, will not be zero (it will be positive for correlated variables and negative for anticorrelated).

The **correlation coefficient** of x and y, &rho;<sub>xy</sub>, is given by &rho;<sub>xy</sub> = _V_<sub>xy</sub>/(&sigma;<sub>x</sub>&sigma;<sub>y</sub>, and lies in the range -1 to +1, where -1 represents perfect anticorrelation and +1 perfect correlation.  Note that the covariance and correlation coefficient describe _linear_ correlations between x and y: if the correlation is not linear, it will not be well described by &rho;<sub>xy</sub>: see example in the notebook, and figure in the [Wikipedia article](https://en.wikipedia.org/wiki/Pearson_product-moment_correlation_coefficient).


##The Gaussian distribution (see [this notebook](gaussian.ipynb))

In the absence of information to the contrary, it is usual to assume that the result of repeating a given measurement many times would be a **Gaussian** or **normal** distribution, which is the typical "bell-shaped curve".  This assumption is the basis for many standard statistical results, such as the percentage of measurements that will lie within 1&sigma; of the mean.

The reason for assuming the Gaussian distribution is a powerful result called the **central limit theorem**, which states that the distribution of the **mean** of a set of _N_ measurements tends towards Gaussian as _N_ becomes large, _regardless_ of the distribution of the individual measurements.  

The main points to bear in mind when assuming normally distributed uncertainties are:

* the best estimate of the true value of the variable is the **mean** of the set of measurements, &lang;x&rang;;

* the best estimate of the uncertainty _of a single measurement_ is the **standard deviation**, &sigma; of the Gaussian, and the best estimate of this is given by &sigma;<sup>2</sup> = (&sum;(x<sub>i</sub> - &lang;x&rang;)<sup>2</sup>)/(_N_ - 1);

* the best estimate of the uncertainty _of the mean_ is given by &sigma;/_N_<sup>1/2</sup>;

* the probability that a given measurement will lie _outside_ the range x&plusmn;&sigma; is 31.8%, about 1 in 3.

##Other probability density functions (see [this notebook](pdfs.ipynb))

The Gaussian is an example of a **probability density function**: the probability that a measurement will lie in the range x to x+&delta;x is given by _P_(x)&delta;x, where in this case _P_(x) is the Gaussian.  To be a valid probability density function, _P_(x) should be normalised so that its total area is equal to one.  In the case of a continuous distribution like the Gaussian, the probability that x lies between, say, x<sub>1</sub> and x<sub>2</sub> is obtained by integrating _P_(x) from x<sub>1</sub> to x<sub>2</sub>: the Gaussian is non-trivial to integrate, but there are online tools (and a Python function) that will do it for you.

Other probability density functions commonly met with in physics and astrophysics are

* the **Poisson distribution**, a discrete (defined for non-negative integers only) function that describes the statistics of counting experiments (e.g. the number of radioactive decays per second, the number of entries in a histogram bin, the number of photons from a faint source reaching your detector);

* the **binomial distribution**, a discrete (again, defined for non-negative integers) function that describes the statistics of yes/no trials (e.g. the number of heads in _N_ coin tosses, the number of sixes in _N_ dice throws, the number of patients that recover after being treated with drug X).

Both the Poisson and the binomial distribution can be approximated by Gaussians if the number of events is large.  For the Poisson, the variance of the distribution &sigma;<sup>2</sup> is equal to the mean; for the binomial, the mean is _np_ (where _n_ is the number of trials and _p_ is the probability of success) and the variance is &sigma;<sup>2</sup> = _np_(1 - _p_).  Note that, although the Poisson and binomial distributions are only defined for non-negative integers, both can have non-integral means and variances.

#Propagating errors (see [this pdf](ErrorPropagation.pdf) and [this notebook](errors.pdf))

The required physics result is often not the experimentally measured quantity, but is derived from the measured quantity by mathematical manipulation, by combining with another measured quantity, or by fitting a function to the relationship between measured quantities (e.g. _y_ = _mx_ + _c_, where the physics result is _m_ and/or _c_).

Fitting functions we'll tackle later.  For functions of one variable, _f_(_x_), where the measured value of _x_ has uncertainty _\sigma_<sub>_x_</sub>, it is entirely legitimate to derive the uncertainty from the upper and lower limits _f_(_x_\plusmn;_\sigma_<sub>_x_</sub>).  Indeed, this is the best way to do it if the error is asymmetric (i.e. the lower error bar &sigma;<sub>&minus;</sub> is different from the upper error bar &sigma;<sub>+</sub>), if the error is not small, or if the function looks difficult to differentiate.  However, it is convenient from a theoretical perspective to use the approximation

<p align="center"> \sigma;<sub>_f_</sub>/\sigma;<sub>_x_</sub> &asympt; |d_f_/d_x_|, </p>

where the modulus signs are there because &sigma; is conventionally defined as positive.  

For functions of more than one variable, the key result is 

<p align="center"> _V_<sub>_x_+_y_</sub> = _V_<sub>_x_</sub> + _V_<sub>_y_</sub> + 2_V_<sub>_xy_</sub>, </p>

where _V_<sub>_x_</sub> = &sigma;<sub>_x_</sub><sup>2</sup>, and similarly for _y_ and _f_, and _V_<sub>_xy</sub> is the covariance of _x_ and _y_.  This is easy to prove (see [the pdf](ErrorPropagation.pdf) and by using \sigma;<sub>_f_</sub> = \sigma;<sub>_x_</sub> |d_f_/d_x_| can be extended to more general functions.

For details, see [the accompanying pdf file](ErrorPropagation.pdf); for some examples, see [the notebook](errors.pdf).