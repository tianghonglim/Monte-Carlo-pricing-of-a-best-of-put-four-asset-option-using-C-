# Monte-Carlo-pricing-of-a-best-of-put-four-asset-option-using-C-

Consider the issue of the Monte Carlo pricing of a best-of-put four-asset option, in a Black-Scholes setting. The returns of the four stocks are assumed to be correlated. You should implement the Monte Carlo pricing of this basket option in C++, and briefly explain in your report:
1. What method you used for simulating standard normal random variables.
2. How you handled correlation in simulating from an n-dimensional Gaussian distribution.
3. The efficiency and variance-reduction methods you have used in your simulation.

Overview

In this assignment, we are going to look into the issue of using Monte Carlo simulations to price a best-of-put four asset option, in a Black Scholes setting.  The returns of the four stocks are assumed to be correlated so we have to make adjustments to the standard normal random variables generated by Monte Carlo to make them correlated.  The methods we will be using here will be the Marsaglia Polar method and the Cholesky decomposition.  Lastly, we will have to use efficiency and variance-reduction methods for our simulation.  This will be done using the Antithetic Variate method.  The final best-of-put option price can then be calculated.











	Introduction to option pricing

One of the main aims of mathematical finance is the pricing of options, with the most famous derivation coming from the Black Scholes model.  According to Bayer (2010), the most basic model of a financial market includes a probability space (Ω, F, P), on which a random variable S is defined.  In our case S represents the collection of all stock prices St for t ∈[0,T].  Correspondingly, the payoff function of a European put option can be represented as f(S) for some functional f.

f(S)=Max(0, K-S_t^1)

Pricing the option can be reduced to E[f(S)].  Here we have assumed with the risk neutral probability P.  We assume that X := f(S) ∈ L(Ω, F, P) where L is a random number generator that produces random numbers according to the distribution of X, which are independent of each other.  We can use the Monte-Carlo simulation to calculate E[X] for an integrable random variable X.

	Introduction to Monte Carlo Simulation

Monte Carlo simulation is a method for assessing an integral as an expected value.  Gille-Genest (2012) explained that its basic principles are based on the Strong Law of Large Numbers and the Central limit Theorem.  The Monte Carlo simulation gives an unbiased estimator and the error on the estimate is within a controlled confidence interval.  We first look into the background of these two limit theorems and then describe the principle of the simulation.  

	Strong Law of Large Numbers

X denotes a n-dimensional random vector where X = (X 1,…,Xn).  ψ is some function on E ∈Rn over R.  If xi are i.i.d (independent and identically distributed) to X and if E[|ψ(X)|] < +∞ then 
1/N ∑_(i=1)^N▒ψ(x_i )  □(→┴(a.s) )  E[ψ(X)]

This theorem demonstrates that the mean of ψ(xi) for a large sample converges to the expected value of ψ under an integrability condition.


	Central Limit Theorem

Let σ2 = Var[ψ(X)].  If σ2 < +∞ then 
√N/σ(1/N ∑_(i=1)^N▒ψ(x_i ) -E[ψ(X) ])□(→┴L )  N(0,1)

The Central Limit Theorem demonstrates that the mean estimator converges in law to a Gaussian standard distribution. 

2.3 Principle of simulation

An unbiased estimator of I for N trials with the Standard Monte Carlo method is given by: 
 

Variance of estimator is as follows:
 
and estimated by: 
 
When N → +∞, variance decreases till 0, implying that the greater the number N, the more unbiased the estimator is.  The speed of convergence of θN to I is  . 

From the Central Limit Theorem, we can define a confidence interval IC as below:
 
where zα = Φ-1(1 -α) and Φ-1 is the inverse cumulative distribution function of the standard Gaussian law. 


	Simulation of random variables

To perform a Monte Carlo simulation, we need a sample of N random variables xi which are i.i.d according to a same density.  Simulation of most random variables require uniform variables over [0,1].  A uniform variable over [a,b] is also known as a + (b-a)u where u is uniform over [0, 1].  There are many methods to simulate these random methods but for this case, we will be looking at normally distributed random numbers.  
This is because according to the Black Scholes model:

St = S0 * exp(σWT + (μ - 1/2σ)T)

St is the stock price at time t and WT denotes a Brownian motion.  Thus St has a log normal distribution but WT has a normal distribution.  Thus we can either sample the stock from the log-normal or from the normal distribution.  We will be looking at pseudo-random number sampling method for generating independent, standard, normally distributed random numbers from sources of uniformly distributed random numbers.

	Inverse method for Gaussian standard variables 

This method involves applying the inverse cumulative distribution Φ-1  to the uniform [0,1] sample.  The independent uniform [0,1] sample is transformed into independent normal sample.  This method is suitable for generating variables with any distribution (discrete or continuous).  It also needs only one uniform random variable to produce one normal random variable.

	Box-Muller transformation

If (u,v) is uniformly distributed on [0, 1] then x and y are: 
 x=√(-2logu)  sin⁡(2πv)
y=√(-2logu)  cos⁡(2πv)
are distributed as independent standard gaussians.  From two uniform random numbers, we obtain two simulations of a standard gaussian variable. 

	Marsaglia Polar Method

The Marsaglia Polar method is a similar transform to Box-Muller, which uses cartesian coordinates, instead of polar coordinates.  It works by choosing random points (x, y) in the square −1 < x < 1, −1 < y < 1 until
s = x2 + y2 < 1,
and then returning the required pair of normal random variables as
x√((-2ln⁡(s))/s),y√((-2ln⁡(s))/s)

The Marsaglia Polar method is different from the Box-Muller transformation in that it is a type of rejection sampling.    It rejects some generated random numbers, is simpler to compute and is more numerically robust.  It also rejects the use of trigonometric functions, which can be expensive in various computing environments.  For this exercise, we will be using the Marsaglia Polar method and the C++ code can be found in the Appendix.

	Brownian motion and Correlations

After looking at the generation of normal random numbers, we are going to look into Brownian motion in more detail because if we want to simulate the payoff of a path dependent option in the Black-Scholes model, we will need to calculate the payoff from the sample by using interpolation of the sample path of the underlying Brownian motion.  In this report, we are going to look at pricing the best of put four asset option.  Options with many underlyings are known as rainbow options or basket options.  

Suppose that: 
dSi= μiSidt + σiSidWi
where Si is the price of the ith stock, i=1,2,…,4 and μi and σi are the drift and volatility of the stock respectively.  dWi is the increment of the Brownian motion and can be in the form of a random number drawn from the normal distribution with mean zero and standard deviation 
dt 1/2.  

E(dWi) = 0 and E(dX_i^2)=dt

And the random numbers dWi and dWj are correlated:

E[dWi dWj] = ρij dt

Where ρij is the correlation coefficient between the ith and jth random walks.  

	Cholesky decomposition of 4 X 4 correlation matrix

The correlation matrix is the symmetric matrix with ρij as the value on the ith row and jth column.  In our case since we have four assets, the correlation matrix is as follows:

■(1&p_21&p_31&p_41@p_12&1&p_32&p_42@p_13&p_23&1&p_43@p_14&p_24&p_34&1)

The correlation matrix is positive definite yt Dy ≥0 and ρij = ρji. We now need a way to make the four random variables correlated via the correlation matrix.  This can be done using the Cholesky decomposition.  We let mij be the value in the matrix on the ith row and jith column after the Cholesky decomposition.

m22 = √(1 - p_12^2 )
m32 = (p_23-p_12*p_13)/m22
m33 = √(1-p_13^2-m_32^2 )
m42 = (p_24-p_12*p_14)/m22
m43 = (p_34-p_13*p_14-m_32*m_42)/m33
m44 = √(1 - p_14^2  -m_42^2-m_43^2 )

We let four normal random numbers be z1, z2, z3, z4.  Hence we are finding the solution to the multiplication of these 2 matrices.


■(〖    z〗_1&z_2&z_3&z_4 )    ■(1&p_12&p_13&p_14@0&m_22&m_32&m_42@0&0&m_33&m_43@0&0&0&m_44 )         = ■(〖    x〗_1&x_2&x_3&x_4 )    

Where:  
x1 = z1
x2 = p12*z1 + m22*z2
x3 = p13*z1 + m32*z2 + m33*z3
x4 = p14*z1 + m42*z2 + m43*z3 + m44*z4

	Handling correlation in simulating from n-dimensional Gaussian distribution

We can expand this approach for n-dimensional Gaussian distribution for the Cholesky decomposition.  According to Gille-Genest (2012), simulation of a n-dimensional Gaussian vector Γ with zero mean and a covariance matrix C is as follows:
- We find the lower triangulary matrix A obtained with the Cholesky decomposition of C, where C = AAt. 
A_ii=√(C_ii-∑_(k=1)^(i-1)▒〖(A_ik 〗) )^2

A_ji=(C_ii-∑_(k=1)^(i-1)▒A_ik  A_jk)/A_ii   for j=i+1, …, n
- Then we generate n independent gaussian standard variables gi with one of the methods previously described in section 3. We note G = (g1,…,gn). 
- Finally we compute Γ = AG. Γ is distributed as N(0,C).

	Efficiency and Variance Reduction 

One disadvantage of Monte Carlo simulations is that the amount of computing time increases with the number of simulations.  Variance reduction techniques are thus an important component in reducing computing time.  We need the smallest number of simulations to get the same level of accuracy on the estimate.  In this section, we will be discussing about the main variance reduction techniques for Monte Carlo option pricing.    




	Importance Sampling

Importance sampling involves changing the probability measure from which paths are generated to reduce the variance.  According to Glasserman (2004), importance sampling adjusts the probability to reflect the importance of the paths or events concerned for the purpose at hand.  For example, in simulating the value of an out of the money option, most paths will lead to a zero payoff.  Hence these paths are a waste of computational effort as the various paths do little to calculate the option value.  To overcome this, we can sample only from the paths where the option ends up in the money.  The simulated payoffs are then multiplied by the likelihood ratio between the modified probability distribution and the original one.

	Control Variate Method

Assume we want to find θ = E(Y) where Y=g(X).  Assume we can find another random variable Z with known mean E(Z).  Then according to Zhang (2009), we can construct many unbiased estimators of θ:
 
 
where c is a real number.
 
Thus we can apply the Monte Carlo simulation to the new estimator.  The random variate Z is now called the control variate.
 
We need to find a value of c that will minimise the quadratic.
 





By substituting the value of c that will minimise the quadratic, we get: 
 
As long as Cov(Y,Z) is not zero, we will achieve variance reduction.  Cov(Y,Z) can be estimated using a Monte Carlo simulation but the problem arises when it may be difficult to find a suitable control variate.  In practice, financial institutions may price the option product without variance reduction, calculate the deltas and vegas of the product, and then use a combination of calls and puts that have the same deltas and vegas as control variate.

	Antithetic Variate Method

This method is the easiest to implement and has been built in my C++ program as part of this project.  The code for this can be found in the Appendix.  Assume again θ = E(Y) where Y=g(X) and we have two generated samples, Y1 and Y2.   Then an unbiased estimator of θ is:
 
We then have:
 
If the two samples are negatively correlated then we will get a variance reduction.  Assume that: 
 
The crude Monte Carlo estimate is:
 
And the estimate by the antithetic variate method is:
 
where Xi and –Xi are the so-called anti antithetic variates.

6.1 Calculating price of best of put option for four assets

With four normal random numbers that are correlated in section 4.1, we can now find the prices of the four stocks at maturity:
Sn=1*exp(σn*xn*√T + (r-0.5*σn2*T))
where n=1,2,3,4, r = interest rate, σ of the stock, and T = time to maturity.
In reality, different stocks will have different initial prices, hence the strike of the best of put option of four assets will be in percentage terms, e.g. the strike is 90% or 0.9.  Thus for simplicity, it is alright for us to assume that all stocks are priced at one in the beginning.  

The best of put payoff for four assets at maturity will thus be:

	Payoff at maturity using normal random variables 
= Max(0, Min(k-S1, k-S2, k-S3, k-S4)) where k = strike

Running the simulation by N times for the Monte Carlo simulation, we have:

	Average payoff at maturity = (∑▒〖all payoffs at maturity〗)/N

	We run steps 2 and 3 again, using normal random variables from the antithetic variate method this time to find the average payoff at maturity with the antithetic variate method.

	Payoff after adjusting for antithetic variate 

= (Average payoff at maturity+Average payoff at maturity using antithetic variate)/2

	Discounting this value back to the present, the best of put option price is as follows:
Best of put option price = payoff after adjusting for antithetic variate * exp(-r*T)



6.2 Example

Assume we want to calculate best of put option for four assets with the following parameters:
	Strike percentage = 150%
	Time to maturity = 1 year
	Annualised interest rate = 5%
	Number of simulations = 100,000
	Volatility at first stock= 10% 
	Volatility at second stock= 20% 
	Volatility at third stock= 30% 
	Volatility at fourth stock= 40% 
	Correlation between first stock and second stock = 0.1
	Correlation between first stock and third stock= 0.1
	Correlation between first stock and fourth stock = 0.1
	Correlation between second stock and third stock = 0.1
	Correlation between second stock and fourth stock = 0.1
	Correlation between third stock and fourth stock = 0.1

 
The best of put option value is 0.215 or 21.5% of the notional value.  Thus for example, if someone wanted a $1million exposure, they will have to pay about $215,000 for this option.  The payoff on the option expiry date will be calculated on the notional of $1 million.

