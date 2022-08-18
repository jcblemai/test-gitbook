# Model Inference

### What has been written in the vignette about inference

(joseph: not sure how true this is at the moment) We build an inference framework to be able to match observed epidemic trajectories so as to constraint model outcome uncertainty when simulating policy-relevant scenarios. Uncertain parameters of our modeling framework, which are all potentially location-specific, include epidemic seeding dates and amounts, values of the basic reproduction number and generation times, and the effectiveness of different types of NPIs. Inference on model parameters for each spatial location are to be drawn jointly given the spatial coupling of COVID-19 transmission dynamics.

Inference is drawn through Markov Chain Monte Carlo (MCMC) sampling using the Metropolis algorithm. We draw inference both on location-specific parameters, denoted by vector $\mathbf\theta$ and parameters that apply to transmission dynamics in all locations, vector $\mathbf \Theta$. Parameter which differ by location include the seeding date and amount (number of initial cases that spark the epidemic), and the efficiency of distinct NPI policies that have been implemented in the location, if any. The efficiency of NPIs is expressed as a percent reduction of the baseline $R\_0$ in each location. For a given location multiple NPIs may have been implemented during the period for which observations are available. Location-independent parameters including the initial value of the basic reproduction number $R\_0$, the length of the generation interval. We note that all parameters related to hospitalization processes are considered known and based on published data. Given a set locations $i \in \[1,\dots,N]$, epidemiological variables $k \in \[1, \dots, K]$, and time period $t\_j, j \in \[1, \dots, J]$, the full likelihood of the data $D$ given the parameters reads:

$$
L(D |\theta, \Theta) = \prod_{k=1}^K \prod_{i=1}^N \prod_{j=1}^J f_k(Y_{i,k,t_j} | X_{i,k,t_j}, \mathbf{\theta_i}, \mathbf{\Theta}),
$$

where $Y\_{i,k,t\_j}$ and $X\_{i,k,t\_j}$ are the observed and simulated variables respectively, $\mathbf{\theta\_i}$ is the vector of location-specific parameters, and $\mathbf{\Theta}$ location-independent parameters.

The framework allows for the use of aggregates of the observation and simulated variables over time periods longer than the ones used for simulation. In this report we use the following epidemiological data

We use independent normal distributions for all parameter proposals with standard deviations of 1 for seeding dates, 1 for seeding amounts and .1 for NPI efficiencies. Initial values of the seeding dates are set to five days prior to the first confirmed case each location and seeding amounts drawn randomly from a poisson distribution with mean equal to five times the first number of observed cases. We implement a blockwise MCMC scheme where we apply independently the Metropolis accept-reject rule at each location to update the seeding date and amount as well as NPI efficiencies. We run chains with a number of iterations specified in \[a configuration file]\(Not sure what to link to yet ). % JK : Fix MEt

### Likelihood

(Mathematical formula for the likelihood, different function options, single vs multiple observed variables, single vs multiple subpopulations, etc)

### Fitting algorithm

(MCMC, likelihood, priors, multiple parallel chains, method for global vs chimeric likelihood when there are multiple subpopulations)

### Initial values

(Starting values for MCMC chosen from specified distribution)

### Hierarchical parameters

(Math behind this option)
