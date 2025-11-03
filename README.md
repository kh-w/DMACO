# Dual Moving Average Crossover Option (DMACO)
Dual Moving Average Crossover Option: an exotic option with a binary payoff that is triggered when two specified simple moving averages (SMAs) of the underlying asset cross each other. The option pays a fixed amount ($1) if the crossover event occurs during the observation period, and nothing otherwise.

# Motivation
- Tackle SMA signal noise: Dual-SMA trading signals often generate false or “whipsaw” crossovers, where short-term and long-term moving averages cross again soon after execution.
- Protect against false signals: These rapid re-crossings can trigger premature trades and short-term losses.
- DMACO as a noise hedge: Buy a Dual Moving Average Crossover Option (DMACO) alongside each trade signal.
- Mechanism: Time horizon T days; pays a fixed amount ($1) if the short-SMA and long-SMA cross again within T days; acts as an insurance payout against a noisy reversal signal.
- Outcome: Offsets part of the loss from false crossovers; smooths strategy performance by monetizing signal noise; transforms “bad luck” reversals into quantifiable, hedgeable events.

# Inspired by
- One-touch Option: a binary exotic option that pays a fixed amount as soon as the underlying price hits a predetermined barrier before expiry.
- Moving Average Barrier Option: an option activates (knock-in) or deactivates (knock-out) when the underlying price crosses its moving average barrier during the observation period.
- Moving Average Look-Back Option: an option which the payoff is determined by comparing the final price (or another benchmark) to the moving average of past prices over a given window.
- Moving Average Reset Option: an exotic option whose strike price (or reference level) is periodically reset based on the moving average of the underlying asset’s price.
- Dual Moving Average Crossover (DMAC) Trading Strategy: a strategy that signals a buy when the short-term moving average crosses above the long-term one and a sell when it crosses below.
- 
# Stochastic Process
Stock price ($S_t$) stochastic process:
- Merton Jump-Diffusion Model
- (Stochastic differential equation here)

The underlying stochastic process of DMACO:
- $Y_t=M^{(short)}_t-M^{(long)}_t$

For the option to payoff, we have a "barrier" for $Y_t$ to hit:
- $B=0$

Payoff stochastic process:
- $1_{\{Y_t=0\}}$, for some $t \in [0,T]$

# Option pricing method - Monte Carlo Simulation

# Implied Volatility

# Supply and demand

# Conclusion and further research
