# Implied Volatility Surface Builder

A browser-based tool that works backwards from option prices to the volatility the market is pricing in, then plots it as an interactive 3D surface.

**Live demo:** https://YOUR-USERNAME.github.io/iv-surface-builder

## What it does

Black-Scholes turns a volatility into an option price. This tool runs it in reverse: given a quoted price, it finds the volatility that reproduces it (the *implied volatility*). Doing this across every strike and expiry produces a surface. If Black-Scholes held, that surface would be flat. In real markets it is not, and the shape is the point:

- **Skew:** implied volatility is typically higher for low strikes, reflecting demand for downside protection.
- **Smile:** in some markets it also rises at high strikes.
- **Term structure:** short-dated smiles are steeper, and long-dated ones flatten.

## How it works

1. **Parse** an options chain from CSV (`expiry,strike,type,price`).
2. **Select** the out-of-the-money option at each strike (puts below the forward, calls above), since these are the most liquid quotes.
3. **Filter** quotes below a minimum price and prices that violate no-arbitrage bounds.
4. **Invert** Black-Scholes (with continuous dividend yield) using bisection to get each implied volatility.
5. **Interpolate** each expiry onto a common moneyness grid and plot the surface, with the raw quotes overlaid, plus a per-expiry smile chart.

## Usage

Open `index.html` in a browser. It loads with a synthetic sample chain.

To use real data, paste a chain into the text box in this format:

```
expiry,strike,type,price
2026-12-18,450,P,3.20
2026-12-18,500,C,2.85
```

Set spot, risk-free rate, dividend yield and valuation date on the left, then click **Build surface**. Use the mid of bid and ask as the price. With Python, `yfinance` can export a chain: `Ticker("SPY").option_chain("2026-12-18")`.

## Limitations

- The built-in sample is **synthetic**: prices are generated from a skewed volatility model and then inverted, so it demonstrates the shape rather than reflecting a real market.
- Black-Scholes is strictly valid for European options. Most US single-stock options are American, so their implied volatilities here are approximate.
- No live data fetching: chains are pasted in.
- Constant rate and dividend yield across expiries.

## Tech

JavaScript, Plotly.js. Single self-contained HTML file.
