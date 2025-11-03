# Dual Moving Average Crossover Option (DMACO)
Dual Moving Average Crossover Option: an exotic option with a binary payoff that is triggered when two specified simple moving averages (SMAs) of the underlying asset cross each other. The option pays a fixed amount ($1) if the crossover event occurs during the observation period, and nothing otherwise.

# Inspired by
- One-touch Option
- Moving Average Barrier Option
- Moving Average Look-Back Option
- Moving Average Reset Option
- Dual Moving Average Crossover (DMAC) Trading Strategy

# Motivation
- Tackle SMA signal noise: Dual-SMA trading signals often generate false or “whipsaw” crossovers — where short- and long-term moving averages cross again soon after execution.
- Protect against false signals: These rapid re-crossings can trigger premature trades and short-term losses.
- DMACO as a noise hedge: Buy a Dual Moving Average Crossover Option (DMACO) alongside each trade signal.
- Mechanism: Time horizon T days; pays a fixed amount ($1) if the short-SMA and long-SMA cross again within T days; acts as an insurance payout against a noisy reversal signal.
- Outcome: Offsets part of the loss from false crossovers; smooths strategy performance by monetizing signal noise; transforms “bad luck” reversals into quantifiable, hedgeable events.

# Stochastic Process
Stock price (S_t) stochastic process:
- Merton Jump-Diffusion Model
Underlying stochastic process:
- Y_t = M^(short)_t - M^(long)_t
Underlying "barrier" for Y_t to hit:
- B = 0
Payoff stochastic process:
- 1_{Y_t = 0}, for some t in [0,T]

# Option pricing method - Monte Carlo Simulation

# Implied Volatility

# Conclusion and further research
