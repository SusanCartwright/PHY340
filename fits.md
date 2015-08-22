#Fitting Functions

What is the value of the Hubble constant?  Does this dataset contain a Higgs boson signal, and if so what is the corresponding Higgs mass?  The answers to questions such as these are found by **fitting a function to data**, and extracting the best values of the function parameters together with their uncertainties.

A function such as _y_ = _a_<sub>0</sub> + _a_<sub>1</sub>_x_ + _a_<sub>2</sub>_x_<sup>2</sup> has three ingredients:

* the **independent variable** _x_ (or (_x,y,..._) in the case of functions of more than one variable), which is an _input_ to the function;

* the **dependent variable** _y_, which is the _output_ of the function;

* the **parameters** _a_<sub>k</sub>, which are part of the _definition_ of the function.

When we talk about fitting a function to data, we mean adjusting the parameters _a_<sub>k</sub> so that the output of the function, _y_<sub>fit</sub>, best matches the measured data points (_x_<sub>i</sub>, _y_<sub>i</sub>).

##The unweighted least-squares fit (see [this notebook](fits1.ipynb))

How do we define "best matches the measured points"?  When dealing with [distributions](pdfs.ipynb), we used the variance as a measure of the spread, so it seems logical to use the analogous measure, &lang;(_y_<sub>i</sub> - _y_<sub>fit</sub>)<sup>2</sup>&rang; as a measure of spread of the measured points about the fit.  The best fit would then be obtained by minimising this quantity (with respect to the set of parameters _a_<sub>k</sub>).  This is indeed a widely used fitting technique, known as a **least squares fit**.

###Example: fitting a straight line

One of the simplest cases to consider is the fit to a straight line, _y_ = _mx_ + _c_, where it is assumed that the measurements _x_<sub>i</sub> hsve negligible uncertainty and the _y_<sub>i</sub> all have the same uncertainty, i.e. all points are equally important.  Differentiating &sum;<sub>i</sub>(_y_<sub>i</sub> &minus; _y_<sub>fit</sub>)<sup>2</sup> with respect to _m_ and _c_ and equating the derivatives to zero gives two simultaneous equations that are straightforward to solve, such that a Python routine to perform the fit is only three lines long (six, if the uncertainties in the best-fit parameters are also calculated: see [the notebook](fits1.ipynb)).  

In this example, the estimated uncertainties on the measured _y_<sub>i</sub> do not enter into the calculation at all: the fit uses the spread of the points to, in effect, calculate the uncertainty.  If you do have an independent estimate of &sigma;<sub>y</sub> (recall that we are assuming that all the points have equal errors), this can be used to make a rough check of the plausibility of the straight line: approximately 2/3 of the points should have error bars that overlap the line, and 19/20 should be within two error bars of the line (based on Gaussian statistics, see [the notebook on the Gaussian distribution](gaussian.ipynb)).  If more than 1/3 of your points do not overlap the line, then either you have underestimated your uncertainty or the straight line is not a good description of your data (which of these possibilities is correct can be indicated by the _pattern_ of the disagreement: if it looks like a random scatter, you've probably underestimated your error; if there _is_ a pattern (e.g. points at the ends too low, points in the middle too high), then a straight line may be too simplistic a model).  Note that if _too many_ points have error bars overlapping the line, you _also_ have a problem: a straight line fit to 20 data points where every single data point is witin 1&sigma; of the fitted line is not a triumph, it's a sign that something is wrong with your error bars!  (The probability that this would happen by chance, assuming properly calculated statistical errors, is 0.05%.)  The likeliest explanation, assuming that you have not simply made a calculational error, is that the errors are not purely statistical: since systematic errors do not affect the scatter of the points, they would cause an incorrect gradient rather than a gradient with greater uncertainty.

###Notes on the straight line fit

* You can always fit a straight line, whether your data actually lie on one or not!  Always check that the function you are trying to fit is a reasonable description of your data (we will return to this later).

* Fitting routines are set up on the assumption that the scatter of the points is caused by the dependent variable, i.e. that the error bars are vertical only.  If the errors are in the _x_ coordinate, the fit might not be optimal.  Therefore, always set up your function so that the coordinate with the largest errors is the dependent variable, even if that isn't the natural way to do it (for example, Hubble's law is written _v_ = _Hd_, but in astronomy the errors on distance are nearly always larger than the errors on velocity, so you should really fit _d_ = _v_/_H_).

* Because the unweighted fit treats all points equally, and because it relies on the _square_ of the distance between the point and the line, it can be badly affected by one or two "rogue" points.  Only use an unweighted fit if it is really true that all your points have comparable error bars: if most of them have comparable error bars, but there are one or two with much larger errors, don't include the latter in the fit (or, better, use a weighted fit as described below).

* The fitted values of the gradient and intercept are _highly anticorrelated_.  If your subsequent calculation uses both of them, rather than just one or the other, you should take this into account.

##Weighted fits (see [this notebook](fits2.ipynb))

What if the different meaasurements do _not_ all have the same uncertainty?  If some of our points have larger error bars than others, we do not want the fit to treat them all equally: the line of best fit needs to run close to the measurements with small error bars, but can be quite a long way from the points with large error bars.

It turns out that the best response to this is quite simple: we should measure the distance between the point and the fitted line _in units of the point's error bar_, i.e. we should minimise &sum;((_y_<sub>i</sub> &minus; _y_<sub>fit</sub>)/&sigma;<sub>i</sub>)<sup>2</sup>.  This quantity is called **&chi;<sup>2</sup>** ("chi squared", with the 'ch' pronounced as in Scottish 'loch'), and fits which minimise it are called &chi;<sup>2</sup> fits.  Effectively, we are weighting each point by 1/&sigma;<sup>2</sup>.  (The same applies if we want to take the average of several measurements of the same quantity with different error bars: multiply each number by a weight 1/&sigma;<sup>2</sup>, and divide the sum by the sum of the weights.  This is called a **weighted mean**.

##Linear and non-linear fits (see [this notebook](fits2.ipynb))

The straight line is an example of a **linear fit**.  So is the quadratic, _y_ = _a_<sub>0</sub> + _a_<sub>1</sub>_x_ + _a_<sub>2</sub>_x_<sup>2</sup>.  Linear fits are not linear in the independent variable, they are _linear in the parameters_.  This is an important distinction because when we differentiate (_y_ &minus; _y_<sub>fit</sub>)<sup>2</sup> for a function that is linear in the parameters, we get simultaneous equations that are also linear in the parameters.  These can be solved analytically, and therefore there is a well-defined, unique best fit.

**Non-linear fits** involve functions that are _not_ linear in the parameters, such as _y_ = _a_<sub>0</sub> sin(_a_<sub>1</sub>_x_ + _a_<sub>2</sub>).  We can differentiate them and set up simultaneous equations, but the equations are not linear in the _a_<sub>k</sub> and will not necessarily have neat analytic solutions.  The multidimensional surface described by its &chi;<sup>2</sup> may have a complicated shape with local minima.

When fitting a non-linear function, it is usually necessary to make a starting guess at the parameter values, and this guess may have to be quite close to the best fit if the fitting routine is not to get trapped in a local minimum, or indeed fail to converge at all.  Some fitting algorithms are better at avoiding these problems than others, and some non-linear functions are much more fussy than others &ndash; for example, the Cauchy distribution (aka the Lorentzian or Breit-Wigner; see [this notebook](pdfs.ipynb)) is particularly pathological and nearly impossible to fit unless your starting parameters are _very_ close to the best values.

If a function you wish to fit to your data appears to be non-linear, it is always worth looking to see if it can be made linear by a suitable transformation.  For example, _y_ = _Ax_<sup>_b_</sup> is non-linear, but taking logs produces log _y_ = _b_ log _x_ + log _A_, which is perfectly linear in its parameters _b_ and log _A_.  Fitting this will give a much more reliable result than fitting the power law directly.  (On the other hand, _y_ = _Ax_<sup>_b_</sup> + _c_ is incurably non-linear if you have to fit _c_ as well as _A_ and _b_: there's nothing you can do to this function that will make it linear in all its parameters.)

In Python, the routine [**scipy.optimize.curve_fit**](http://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.curve_fit.html) works for both linear and non-linear fits.  The argument **p0**, which is an array containing the start values of the parameters, is optional (if it is not present, all the start values default to 1), but is likely to turn out to be necessary to make the fit converge if your function is non-linear.