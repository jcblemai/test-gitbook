# Model Description

(Roughly summarizing conceptual breakdown of model, modular nature, compartmental model vs outcomes vs seeding)

### Generalized compartmental infection model

At the core of our code is a dynamic mathematical model that categorizes individuals in the population into a discrete set of states ('compartments') and describes the rates at which transitions between states can occur. Our modeling approach was developed to describe classic infectious disease transmission models, like the SIR model, but is much more general. It can encode any compartmental model in which transitions between states are of the form

$$X \xrightarrow{b X Z^a} Y$$

where $X$, $Y$, and $Z$ are time-dependent variables describing the number of individuals in each state, $b$ is a rate parameter (units of time$^{-1}$) and $a$ is a scaling parameter (unitless). $Z$ may be $X$, $Y$, a different variable, or 1, and the rate may also be the sum of terms of this form. Rates that involve non-linear functions or more than two variables are currently not possible.

The model can be simulated as a continuous time, deterministic process (ie, an **ordinary differential equation**), which in this example would be of the form

$$
\frac{dX}{dt} = b X Z^a
$$

Details on the numerical integration procedure for simulating such an equation is given in the Advanced section.

Alternatively, the model can be simulated as a **discrete time stochastic process**, where the number of individuals transitioning between states $X$ and $Y$ at time $t$ is a binomial random variable

$$
N_{X\rightarrow Y}(t) = \textrm{Binom}(X,1-e^{-\Delta{t} \cdot bZ(t)^a})
$$

where the timestep $\Delta{t}$ is a chosen parameter that must be small for equivalence between continuous and discrete time versions of the model.

For example, a **SEIR model** -- which describes an infection for which susceptible individuals (S) who are infected first pass through a `latent' or` exposed' (E) phase before becoming infectious (I) and that confers perfect lifelong immunity after recovery (R) -- could be encoded as

$$
S \xrightarrow{\beta S I} E \xrightarrow{\sigma E} I \xrightarrow{\gamma I} R
$$

where $\beta$ is the transmission rate (rate of infectious contact per infectious individual), $\sigma$ is the rate of progression ($1/\sigma$ is the average latent/incubation period), and $\gamma$ is the recovery rate ($1/gamma$ is the average duration of the infectious period). In differential equation form, this model is

$$
\frac{dS}{dt} = - \beta S I
$$

$$
\frac{dE}{dt} = \beta S I - \sigma E
$$

$$
\frac{dI}{dt} = \sigma E - \gamma I
$$

$$
\frac{dR}{dt} = \gamma I
$$

and as a stochastic process, it is

$$
N_{S\rightarrow E}(t) = \textrm{Binom}(X,1-e^{-\Delta{t} \cdot \beta S(t) I(t)})
$$

$$
N_{E\rightarrow I}(t) = \textrm{Binom}(X,1-e^{-\Delta{t} \cdot \sigma E(t)})
$$

$$
N_{I\rightarrow R}(t) = \textrm{Binom}(X,1-e^{-\Delta{t} \cdot \gamma I(t)})
$$

We commonly code a variant of this SEIR model that incorporates a) multiple identical stages of the infectious period, which allows us to model gamma-distributed durations of infectiousness, and b) an infection rate modified by a \`mixing coefficient', $\alpha \in \[0,1]$, which is rough a heuristic for the slow down in disease spread that occurs in realistically heterogeneous populations where more well-connected individuals are infected first.

$$
S \xrightarrow{\beta S (I_1+I_2+I_3)^\alpha} E \xrightarrow{\sigma E} I_1 \xrightarrow{3\gamma I_1} I_2 \xrightarrow{3\gamma I_2} I_3 \xrightarrow{3\gamma I_3} R
$$

The CSP model structure is specifically designed to make it simple to encode the type of more complex \`\`stratified'' models that often arise in infectious disease dynamics. For example, to describe an SEIR-type disease that spreads and progresses differently among children versus adults, one may want to repeat each compartment of the model for each of the two age groups (C - Children, A - Adults), creating an **age-stratified model**:

$$
S_C \xrightarrow{S_C (\beta_{CC} I_C + \beta{AC} I_A} E_C \xrightarrow{\sigma_C E_C} I_C \xrightarrow{\gamma_C I_C} R_C
$$

$$
S_A \xrightarrow{S_A (\beta_{AA} I_C + \beta{CA} I_C} E_A \xrightarrow{\sigma_A E_A} I_ A \xrightarrow{\gamma_A I_A} R_A
$$

where $\beta\_{XY}$ is the transmission rate between age X and Y, and we have assumed individuals do not age on the timescale relevant to the model.

Another possible type of stratification could be **vaccination status**, which could influence disease progression and infectiousness and could change over time as individuals choose to get the vaccine (V-vaccinated, U-unvaccinated):

$$
S_U \xrightarrow{\beta S_U (I_U + I_V)} E_U \xrightarrow{\sigma_U E_U} I_U \xrightarrow{\gamma_U I_U} R_U
$$

$$
S_V \xrightarrow{\beta (1-\theta) S_V (I_U + I_V)} E_V \xrightarrow{\sigma_V E_V} I_V \xrightarrow{\gamma_V I_V} R_V
$$

$$
S_U \xrightarrow{\nu S_U} S_V
$$

$$
R_U \xrightarrow{\nu R_U} R_V
$$

where $\nu$ is the vaccination rate (we assume that individuals do not receive vaccine while they are exposed or infectious) and $\theta$ is the vaccine efficacy against infection. Similar structures could be used for other sources of prior immunity or other dynamic risk groups.

Yet another common stratification would be **pathogen strain**: individuals may be infected with one of several variants/strains/serotypes. Our framework can easily create multistrain models, for example

$$
S_A \xrightarrow{\beta_A S_A I_A} E_A \xrightarrow{\sigma_A E_A} I_ A \xrightarrow{\gamma_A I_A} R_A
$$

$$
S_A \xrightarrow{\beta_B S_B I_B} E_B \xrightarrow{\sigma_B E_B} I_B \xrightarrow{\gamma_B I_B} R_B
$$

$$
R_{A} \xrightarrow{\beta_B(1-\phi_{AB}) R_A I_B} E_{AB} \xrightarrow{\sigma_{AB} E_{AB}} I_{AB} \xrightarrow{\gamma_{AB} I_{AB}} R_{AB}
$$

$$
R_{B} \xrightarrow{\beta_A (1-\phi_{BA}) R_B I_A} E_{AB} \xrightarrow{\sigma_{AB} E_{AB}} I_{AB} \xrightarrow{\gamma_{AB} I_{AB}} R_{AB}
$$

where $\phi\_{AB}$ is the cross-protection in immunity conferred from infection with strain A to subsequent infection with strain B. Co-infection is ignored. All individuals are assumed to be initially susceptible equally to both infections and are just categorized in $S\_A$ (vs $S\_B$) for convenience.

All of these situations can be quickly coded in CSP. Details on how to encode these models is provided in the Data and Parameter Inputs section, with examples given in the Tutorials section.

### Clinical outcomes and observations model

The pipeline allows for an additional type of dynamic state variable beyond those included in the mathematical model. We refer to these extra variables as "Outcomes" or "Observations". Outcome variables can be functions of model variables, but do not feed back into the model by influencing other state variables. Typically, we use outcome variables to describe the process through which some subset of individuals in a compartment are \`\`observed'' and become part of the data to which models are compared and attempt to predict. For example, in the context of a model for an infectious disease like COVID-19, outcomes variables include reported cases, hospitalizations, and deaths.

An outcome variable $H(t)$ can be generated from a state variable of the mathematical model $X(t)$ using the following properties

* The proportion of all individuals in $X$ who will be observed as $H$, $p$
* The delay between when an individual enters state $X$ and when they are observed as $H$, which can follow a class of probability distributions $f(\Delta t;\theta)$ where $\theta$ is the parameters of the distribution (e.g. the mean and standard deviation of a normal distribution)

The number of individuals in $X$ at time $t\_1$ who become part of the outcome variable $H(t\_2)$ is a random variable, and individuals who are observed in $H$ at time $t$ could have entered $X$ at different times in the past. Formally,

$$
H(t) = \sum_{\tau=0}^{t} \textrm{Binom}(X(\tau),p \cdot f(t-\tau,\theta)
$$

Outcomes can also be constructed as functions of other outcomes - for example, a fraction of hospitalized patients may end up in the intensive care unit (ICU).

There are several benefits to separating outcome variables from the mathematical model. Firstly, these variables can be calculated AFTER the model is run, and only at the timepoints of interest, which can dramatically reduce the memory needed during model simulation. Secondly, outcome variables can be fully stochastic even when the mathematical model is simulated deterministically. This becomes useful when an infection might be at high enough prevalence that a deterministic simulation is appropriate, but when there is a rare and therefore quite stochastic outcome reported in the data (e.g., severe cases) that the model is tasked with predicting. Thirdly, outcome variables can have arbitrary delay distributions, to take into account the complexities of health-reporting practices, whereas our mathematical modeling framework is designed mainly for exponentially distributed delays and only easily permits extensions to gamma-distributed delays. Finally, this separation keeps the pipeline modular and allow for easy editing of one component of the model without disrupting the other.

Details on how to specify these outcomes in the model configuration files is provided in the Data and Parameter Inputs section, with examples given in the Tutorials section.

### Population structure

(Metapopulation structure which allows for any parameter to vary by location. Well-mixed within each location, matrix describing transmission between locations)

### Initial conditions

(Different types of initial conditions aka “seeding”)

### Time-dependent interventions

(aka “NPIs” but these can also be “PIs” so should use more general terminology) Single time period Multiple time periods
