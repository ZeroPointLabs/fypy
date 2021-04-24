
# FyPy

Vanilla and exotic option pricing library to support quantitative R&D. Focus on pricing interesting/useful models 
and contracts (including and beyond Black-Scholes), as well as calibration of financial models to market data.

This library is under active development. Many of the exotic pricing algorithms will be translated into python from:
https://github.com/jkirkby3/PROJ_Option_Pricing_Matlab

## Currently Supported
### Models

- Black-Scholes
- Levy: Variance Gamma, Normal Inverse Gaussian (NIG)

### Pricing Methods
- Fourier: PROJ, Lewis, Gil-Peleaz


## Coming Soon !

- Exotic Option Pricing - Asian, Barrier, American, Parisian, Cliquet, Variance Swaps, etc.
- Models: Stochastic Volatility, Regime Switching, Stochastic Local Vol
- Levy Model Calibration (VG, NIG, CGMY, MJD, Kou, etc)
- Regime Switching Calibration
- Stochastic Volatility Calibration

## User installation

Coming soon, for now use git clone.

## Dependencies


fypy requires:

- Python (>= 3.7)
- NumPy (tested with 1.20.2)


## Source code


You can check the latest sources with the command

    git clone https://github.com/jkirkby3/fypy.git
    
    

## Example: Price Variance Gamma / Black-Scholes Models with PROJ (Fourier) Method

```python

"""
This example shows how to price using a Fourier pricing method (PROJ)
We include two examples:  1) Black Scholes 2) Variance Gamma
"""
from fypy.pricing.fourier.ProjEuropeanPricer import ProjEuropeanPricer
from fypy.model.levy.BlackScholes import *
from fypy.model.levy.VarianceGamma import *
from fypy.termstructures.DiscountCurve import DiscountCurve_ConstRate
import matplotlib.pyplot as plt

# ============================
# Set Common Parameters
# ============================
S0 = 100  # Initial stock price
r = 0.01  # Interest rate
q = 0.03  # Dividend yield
T = 1  # Time to maturity of option

# ============================
# Set Term Structures
# ============================
disc_curve = DiscountCurve_ConstRate(rate=r)
div_disc = DiscountCurve_ConstRate(rate=q)
fwd = EquityForward(S0=S0, discount=disc_curve, divDiscount=div_disc)

# ============================
# Create Black-Scholes Model
# ============================
model = BlackScholes(sigma=0.2, forwardCurve=fwd)
pricer = ProjEuropeanPricer(model=model, N=2 ** 10)

# Price a set of strikes
strikes = np.arange(50, 150, 1)
prices = pricer.price_strikes(T=T, K=strikes, is_calls=np.ones(len(strikes), dtype=bool))

# Plot
plt.plot(strikes, prices, label='Black Scholes')

# ============================
# Create Variance Gamma Model
# ============================
model = VarianceGamma(sigma=0.2, theta=0.1, nu=0.8, forwardCurve=fwd, discountCurve=fwd.discountCurve)
pricer = ProjEuropeanPricer(model=model, N=2 ** 10)

# Price a set of strikes
strikes = np.arange(50, 150, 1)
prices = pricer.price_strikes(T=T, K=strikes, is_calls=np.ones(len(strikes), dtype=bool))

# Plot
plt.plot(strikes, prices, label='Variance Gamma')
plt.legend()
plt.xlabel(r'strike, $K$')
plt.ylabel('price')
plt.show()

```
