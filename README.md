# Dual Moving Average Crossover Option (DMACO)

<img width="362" height="193" alt="image" src="https://github.com/user-attachments/assets/e0f1f589-7699-4f6c-98ce-2851c908d48f" />

Dual Moving Average Crossover Option: an exotic option with a binary payoff that is triggered when two specified simple moving averages (SMAs) of the underlying asset cross each other. The option pays a fixed amount ($1) if the crossover event occurs in the time horizon, and nothing otherwise. 

# Motivation
Dual-SMA trading systems often suffer from signal noise, where short-term and long-term moving averages generate false or “whipsaw” crossovers shortly after an initial trade signal. These rapid re-crossings can lead to premature trades and short-term losses, undermining the reliability of the strategy. To protect against such false signals, one can introduce a **Dual Moving Average Crossover Option (DMACO)** as a noise hedge. The DMACO is structured with a time horizon of *T* days and pays a fixed amount (e.g., $1) if the short-term and long-term SMAs cross again within that period. In effect, it acts as an insurance payout against noisy reversal signals. By purchasing a DMACO alongside each trade signal, traders can offset a portion of losses caused by false crossovers, thereby smoothing overall strategy performance. This approach monetizes signal noise and transforms what would otherwise be “bad luck” reversals into quantifiable, hedgeable events.

# Inspired by
A **One-Touch Option** is a type of binary exotic option that pays a fixed amount as soon as the underlying asset’s price touches or surpasses a predetermined barrier level before the option’s expiration date. Unlike standard options, which depend on the final price at maturity, the one-touch option triggers a payout the moment the barrier is reached, making it particularly useful for traders looking to speculate on or hedge against sharp price movements.

A **Moving Average Barrier Option** is an exotic option whose activation (knock-in) or deactivation (knock-out) depends on whether the underlying asset’s price crosses a moving average barrier during the observation period. This structure blends traditional barrier option mechanics with technical analysis concepts, linking the option’s existence or expiry to the dynamic behavior of the asset’s moving average rather than a fixed price level.

A **Moving Average Look-Back Option** determines its payoff by comparing the final price of the underlying asset—or another benchmark—to the moving average of its past prices over a specified time window. This design smooths out short-term volatility and ties the payout to a more stable representation of price behavior, offering a way to hedge or speculate based on the underlying’s average performance rather than transient fluctuations.

A **Moving Average Reset Option** is an exotic derivative whose strike price or reference level is periodically adjusted based on the moving average of the underlying asset’s price. By resetting in line with market trends, this option maintains relevance across changing conditions and can reduce path dependency, making it a flexible tool for managing exposure in volatile or trending markets.

The **Dual Moving Average Crossover (DMAC) Trading Strategy** is a systematic approach that generates trading signals based on the relationship between two moving averages—typically a short-term and a long-term one. A buy signal occurs when the short-term moving average crosses above the long-term average, indicating upward momentum, while a sell signal occurs when the short-term average crosses below, signaling potential downward movement. This strategy is widely used to identify trend reversals and capture medium- to long-term price momentum.

<!--
- One-touch Option: a binary exotic option that pays a fixed amount as soon as the underlying price hits a predetermined barrier before expiry.
- Moving Average Barrier Option: an option activates (knock-in) or deactivates (knock-out) when the underlying price crosses its moving average barrier during the observation period.
- Moving Average Look-Back Option: an option which the payoff is determined by comparing the final price (or another benchmark) to the moving average of past prices over a given window.
- Moving Average Reset Option: an exotic option whose strike price (or reference level) is periodically reset based on the moving average of the underlying asset’s price.
- Dual Moving Average Crossover (DMAC) Trading Strategy: a strategy that signals a buy when the short-term moving average crosses above the long-term one and a sell when it crosses below.
-->

# Stochastic Processes
The **stock price process** $S_t$ follows the **Merton Jump-Diffusion Model**, which extends the standard geometric Brownian motion by incorporating sudden, discontinuous jumps in price. This model captures both the continuous fluctuations driven by Brownian motion and the occasional large movements caused by unexpected market events, offering a more realistic description of asset price dynamics in financial markets. Mathematically, the evolution of $S_t$ can be expressed as a stochastic differential equation that includes both diffusion and jump components.

The stochastic differential equation (SDE) of $S_t$ under risk-neutral measure is:

$\frac{dS_t}{S_{t-}}=(r-\lambda_{\kappa})dt+\sigma dW_t+(J-1)dN_t$

where $r$ is the risk-free interest rate, $W_t$ is the standard Brownian motion, $\sigma$ is the volatility, $N_t$ is a Poisson process with intensity $\lambda$, $J$ is a log-normal with parameters $\mu_J$ and $\sigma_J$ to represent jump size, $\lambda_{\kappa}$ is the expected total movement contributed by jumps (expected jump size times expected number of jumps per year).

The **underlying stochastic process** for the Dual Moving Average Crossover Option (DMACO) is defined as $Y_t = MA^{(\text{m})}_t - MA^{(\text{M})}_t$, where $M^{(\text{m})}_t$ and $M^{(\text{M})}_t$ represent the short-term and long-term moving _arithmetic_ averages of the asset price, respectively. This process captures the difference between the two moving averages and serves as the key variable determining when crossover events occur.

Suppose the short window is 5-day, long window is 30-day:

$MA_t^{(5)}=\frac{S_t+S_{t-1}+S_{t-2}+S_{t-3}+S_{t-4}}{5}$

$MA_t^{(30)}=\frac{S_t+S_{t-1}+S_{t-2}+\dots+S_{t-28}+S_{t-29}}{30}$

$Y_t = MA_t^{(5)} - MA_t^{(30)} = \frac{6(S_t + S_{t-1} + S_{t-2} + S_{t-3} + S_{t-4}) - (S_t + S_{t-1} + \dots + S_{t-29})}{30}$

The corresponding SDE (or Stochastic Delay Differential Equation (SDDE) to be precise) of $Y_t$ can be obtained directly using the SDE of $S_t$, which the right-hand-side of the SDDE is tedious to represent here. It does not usually has exact analytical solutions, so numerical simulation is used throughout this project.

For the DMACO to produce a **payoff**, the process $Y_t$ must reach a **barrier** defined as $B = 0$. This occurs when the short-term and long-term moving averages intersect, i.e. when a crossover happens. Conceptually, this is analogous to a one-touch option, except that the underlying process is replaced by $Y_t$, which is a linear combination of past stock prices.

The **payoff process** is therefore modeled as $1_{{Y_t = 0 \text{ for some }t\in[0,T]}}$ which pays a fixed amount (normalized to $1) if $Y_t$ reaches zero at any time within the observation window $[0,T]$. This formulation captures the event-driven nature of the DMACO, where the option’s value is tied directly to the occurrence of a moving average crossover within the specified time horizon.

# Option pricing method - Monte Carlo Simulation

Let $V$ be the DMACO price, then it can be written as a function of parameters of the Merton jump-diffusion, the short moving average window ($m$), the long moving average window ($M$) and the time horizon (T):

$V:=\text{DMACO Price}=f(S_0,r,\sigma,\lambda_j,\mu_j,\sigma_j,m,M,T)$

Since the DMACO is a path-dependent exotic option, it is well understood that a closed-form pricing function $f$ rarely exists. A practical approach to price DMACO is the following Monte Carlo approach:

1. **Initialize historical prices**  
   - Generate or provide a single path of historical stock prices to seed the moving averages.  
   - This ensures that both short-term and long-term moving averages are defined for small $t$.

2. **Simulate future paths**  
   - Generate $N$ independent Monte Carlo paths of the underlying stock price according to the Merton jump-diffusion.

3. **Compute the payoff for each path**  
   - At each time step, calculate $Y_t = M_t^{(\text{short})} - M_t^{(\text{long})}$.
   - Define the **payoff** as 1 if $Y_t$ reaches 0 at any time (the first crossover), otherwise 0.  
   - Record the **stopping time** $\tau$ corresponding to the first crossover.

4. **Discount the payoff**  
   - For each path, discount the payoff to time 0 using the risk-free rate $r$.

5. **Average across all paths**  
   - The Monte Carlo estimate of the DMACO price is the mean of the discounted payoffs:
   $\text{DMACO Price} \approx \frac{1}{N} \sum_{i=1}^{N} e^{-r \tau_i} \cdot 1_{\{Y_t^{(i)} = 0 \text{ for some }t\in[0,T]\}}$
   - $1_{\{Y_t^{(i)} = 0 \text{ for some }t\in[0,T]\}}$ is needed to indicate whether there is a payoff within the time horizon $[0,T]$.

This procedure captures the **first-touch feature** of the DMACO payoff while accounting for the stochastic behavior of the underlying asset and the memory effects introduced by moving averages.

Below are some DMACO prices for different parameters, assuming the stochastic process of the underlying stock has known and fixed parameters:

| Desciption     | S0  | r (%) | Volatility (%) | Jump Intensity λ | $μ_j$ | $σ_j$ | Short MA (days) | Long MA (days) | Time Horizon T (days) | DMACO Price |
|----------------|-----|-------|----------------|------------------|-------|-------|-----------------|----------------|-----------------------|-------------|
|Base            | 100 | 2     | 15             | 1                | -0.1  | 0.25  | 5               | 18             | 30                    | 0.876428    |
|Longer T        | 100 | 2     | 15             | 1                | -0.1  | 0.25  | 5               | 18             | 35                    | 0.917654    |
|Longer Long MA  | 100 | 2     | 15             | 1                | -0.1  | 0.25  | 5               | 26             | 30                    | 0.824179    |
|Longer Short MA | 100 | 2     | 15             | 1                | -0.1  | 0.25  | 7               | 18             | 30                    | 0.828989    |

In the Base scenario, with a short MA window of 5, long MA window of 18, and a time horizon of 30 days, the DMACO price is approximately 0.876. When the time horizon is extended to 35 days while keeping the same MA windows (Longer T scenario), the price rises to 0.918, reflecting the higher probability of a crossover over the longer horizon. In the Longer Long MA scenario, increasing the long MA window from 18 to 26 days while maintaining a 30-day horizon reduces the price to 0.824, as the longer moving average is smoother and early crossovers become less likely. Finally, in the Longer Short MA scenario, increasing the short MA window from 5 to 7 days with the long MA window stays at 18 and T = 30 lowers the price to 0.829, because the short MA responds more slowly, delaying potential crossovers. This analysis essentially brings us to the next section on the sensitivity of parameters, i.e. Greeks.

# Numerical Greeks

<img width="634" height="195" alt="image" src="https://github.com/user-attachments/assets/06b46d34-a935-4ea9-8f83-221bdada0e81" />

<img width="4767" height="4190" alt="greeks_summary" src="https://github.com/user-attachments/assets/b012e9ee-0971-4ee7-8374-357398e118db" />

All plots use the x-axis for the time horizon and the y-axis for the option price, with sensitivity shown by curves in different colors. 

**Delta**: The upper-left plot Delta shows the overlapping of all curves, indicates that the option price is largely insensitive to the underlying stock price, which is expected since the event depends on the timing of a moving average crossover, not the initial price levels. 

**Vega**: The Vega plot shows that option prices are generally higher when the volatility of the underlying asset is greater. This is expected, as higher volatility increases the likelihood that the short-term moving average will cross the longer (smoother) moving average, and vice versa. 

**Rho**: The Rho plot shows that the option price is somewhat sensitive but not strongly to the risk-free interest rate, with most of the sensitivity arising from discounting. 

**Lambda**: The Lambda plot shows that the option price is somewhat sensitive to the jump intensity (average number of jumps per unit time) but not significantly, mainly because the moving average windows are not large enough to contain frequent jumps. 

**Width of short and long MA**: The last two plots show the sensitivity of the option price to the short and long moving average (MA) windows. The first plot indicates that, because the short MA is more volatile than the long MA, a narrower short MA window (with a fixed long MA) is more likely to cross the long MA. Note that the option price approaches zero when both the short and long MA windows are wide and the time horizon is short—for example, the yellow curve remains near zero at shorter horizons. Similarly, the second plot shows that, with a fixed short MA window, a shorter long MA window is more likely to be reached by the short MA. 

Overall, the plots indicate that DMACO prices tend to be higher for longer time horizons, which makes sense: the more time we allow, the greater the likelihood that the short-term and long-term SMAs will cross. For a DMACO to be attractive and practical, the time horizon should be short—aligning with this project’s goal of protecting against the whipsaw effect in a dual-SMA trading strategy. 

# Supply and demand

The DMACO was primarily designed to provide protection for dual-SMA trading strategies, meeting demand from hedgers. At the same time, it can serve as a trend-following investment vehicle, appealing to traders who seek to capitalize directly on moving average crossovers. The option also functions as a tool for volatility speculation, allowing investors to take long positions if they expect high volatility or short positions if they anticipate low volatility. For the same reason, DMACOs can be used to vega hedge existing portfolios. On the supply side, these options are generally offered by market makers or traded over-the-counter between institutional participants, with transactions occurring only when buyers and sellers are matched.

# Implied Volatility

Traditionally, when the parameter space is low-dimensional, a dense grid (i.e., a uniform grid across all parameters) can be used to approximate the implied volatility surface. However, for DMACO, the parameter space is high-dimensional, making the computational cost of a dense grid prohibitively high. To address this, we employ the sparse grid method. In this approach, option prices are precomputed across the multidimensional parameter space using a reduced set of strategically selected grid points, i.e. allocating more points to parameters with higher sensitivity and fewer to those with lower sensitivity. Once this efficient grid is constructed, observed market prices and known parameters can be used to interpolate within the sparse grid to infer the remaining unknown parameter.

# Conclusion

The Dual Moving Average Crossover Option (DMACO) provides a clear link between technical trading concepts and quantitative option pricing. By tying the payoff to the occurrence of a crossover between short-term and long-term moving averages, the DMACO converts signal noise in trading strategies into a measurable and tradable risk event. This allows traders to hedge against losses from false crossover signals while also offering a tool for speculation on trend dynamics and volatility.

Pricing DMACO using the Merton jump-diffusion model and Monte Carlo simulation captures both continuous price fluctuations and sudden jumps, reflecting realistic market behavior. Simulation results show that the option price increases with volatility and the length of the time horizon and decreases when moving averages are longer and smoother. These results highlight the option’s sensitivity to market movements and the likelihood of crossovers.

Numerical Greeks confirm that the DMACO is largely insensitive to the initial stock price, while being more sensitive to volatility. Interest rate and jump intensity have a moderate impact on price. This reflects the event-driven nature of the option, as its value depends on the timing of crossovers rather than price levels.

From a market perspective, the DMACO can serve as a hedge for trend-following strategies, providing protection against short-term losses caused by noisy signals. The sensitivity analysis with respect to volatility indicates that the DMACO can be used for vega hedging in existing portfolios. It also offers opportunities for speculation, either on volatility or on the expected timing of trend changes. The option can be structured and traded over the counter, allowing market participants to tailor exposure to their specific needs.

Finally, the use of sparse grid interpolation to estimate implied volatility makes the DMACO practical for high-dimensional parameter spaces and real-world applications. Overall, the DMACO represents a useful addition to the set of event-driven derivatives, providing both hedging and speculative opportunities linked directly to technical analysis signals.
