# HullShiftFX 📈💡
**A Precision Trend-Following TradingView Strategy for Forex**

HullShiftFX is a Pine Script strategy for TradingView that combines the power of the **Hull Moving Average (HMA)** and a **shifted Exponential Moving Average (EMA)** with multi-layered momentum filters including **RSI** and **dual Stochastic Oscillators**.

It’s designed for traders looking to catch high-probability breakouts with tight risk management and visual clarity.

---

## 🔧 Strategy Logic

### ✅ Entry Conditions
**Long Position:**
- Price closes above the 12-period Hull Moving Average.
- Price closes above the 5-period EMA shifted forward by 2 bars.
- RSI is above 50.
- Stochastic Oscillator (12,3,3) %K is above 50.
- Stochastic Oscillator (5,3,3) %K is above 50.
- Hull MA crosses above the shifted EMA.

**Short Position:**
- Price closes below the 12-period Hull Moving Average.
- Price closes below the 5-period EMA shifted forward by 2 bars.
- RSI is below 50.
- Stochastic Oscillator (12,3,3) %K is below 50.
- Stochastic Oscillator (5,3,3) %K is below 50.
- Hull MA crosses below the shifted EMA.

---

## 📉 Risk Management

- **Stop Loss:** Set at the low (for long) or high (for short) of the previous 2 candles.
- **Take Profit:** Calculated at a risk/reward ratio of **1.65x** the stop loss distance.

---

## 📊 Indicators Used

- **Hull Moving Average (12)**
- **Exponential Moving Average (5) [Shifted by +2 bars]**
- **Relative Strength Index (14)**
- **Stochastic Oscillators:** 
  - %K (12,3,3)
  - %K (5,3,3)

---

## 🔍 Visual Features

- Buy and Sell signal arrows on chart
- Real-time plotting of HMA and EMA
- Combined indicator panel (optional) showing RSI and both Stochastic oscillators
- Midline at 50 for quick momentum zone recognition

---

## 📷 Demo Screenshot

Here’s a demo of the strategy in action on TradingView:

![TrendTwisterV1.5 Strategy Demo]https://www.tradingview.com/x/kUS2yWmC/

---

## ⚙️ Compatibility

- Optimized for **Forex Pairs**
- Built with **Pine Script v5**
- Works in all timeframes (recommended: 15m to 1H)

---

## 📥 How to Use

1. Open [TradingView](https://tradingview.com/)
2. Open the Pine Editor
3. Copy and paste the `HullShiftFX` strategy script
4. Click "Add to Chart"
5. Adjust settings if needed

---

## 💡 Ideas for Extension

- Add volume filters or ATR-based trailing stops
- Integrate with alerts for automation
- Enable custom timeframe overrides for filters

---

## 📜 License

MIT License – free to use, modify, and share. Attribution appreciated but not required.

---

## 💬 Feedback / Contributions

Open an issue or pull request if you have improvements, bug fixes, or want to collaborate!

---

## 🖤 Dark Code Snippet Example:

```pinescript
//@version=5
strategy("Trend TwisterV1.5 (Forex Ready + Indicators)", overlay=true, default_qty_type=strategy.percent_of_equity, default_qty_value=10, commission_type=strategy.commission.percent, commission_value=0.01)

// === Parameters ===
hmaLength = 12
emaLength = 5
rsiLength = 14
profitFactor = 1.65

// === Indicators ===
hma = ta.hma(close, hmaLength)
ema = ta.ema(close, emaLength)
emaShifted = ema[2]
rsi = ta.rsi(close, rsiLength)

// === Stochastic Oscillators ===
k1 = ta.stoch(close, high, low, 12)
k1Smooth = ta.sma(k1, 3)

k2 = ta.stoch(close, high, low, 5)
k2Smooth = ta.sma(k2, 3)

// === Plots: Main Strategy Indicators ===
plot(hma, color=color.orange, title="HMA 12")
plot(emaShifted, color=color.blue, title="Shifted EMA 5 (+2)")

// === Stop Loss & Take Profit ===
longStop = ta.lowest(low[1], 2)
shortStop = ta.highest(high[1], 2)

longSL_pips = close - longStop
shortSL_pips = shortStop - close

pip = syminfo.mintick
longTP = close + (longSL_pips * profitFactor)
shortTP = close - (shortSL_pips * profitFactor)

// === Crossover Conditions ===
hmaCrossesAbove = ta.crossover(hma, emaShifted)
hmaCrossesBelow = ta.crossunder(hma, emaShifted)

// === Entry Conditions ===
longCondition = close > hma and close > emaShifted and rsi > 50 and k1Smooth > 50 and k2Smooth > 50 and hmaCrossesAbove
shortCondition = close < hma and close < emaShifted and rsi < 50 and k1Smooth < 50 and k2Smooth < 50 and hmaCrossesBelow

// === Entries & Exits ===
if (longCondition and strategy.position_size == 0)  // Allow long trades only if no position is open
    strategy.entry("Long", strategy.long)
    strategy.exit("Long Exit", from_entry="Long", stop=longStop, limit=longTP)

if (shortCondition and strategy.position_size == 0)  // Allow short trades only if no position is open
    strategy.entry("Short", strategy.short)
    strategy.exit("Short Exit", from_entry="Short", stop=shortStop, limit=shortTP)

// === Signal Arrows ===
plotshape(longCondition, title="Buy Signal", location=location.belowbar, color=color.green, style=shape.arrowup, size=size.small)
plotshape(shortCondition, title="Sell Signal", location=location.abovebar, color=color.red, style=shape.arrowdown, size=size.small)

// === Overlay RSI + Stochs in strategy panel ===
rsiPlot = plot(rsi, title="RSI", color=color.purple, linewidth=1, offset=-10, display=display.data_window)
k1Plot = plot(k1Smooth, title="Stoch %K (12,3,3)", color=color.green, linewidth=1, offset=-10, display=display.data_window)
k2Plot = plot(k2Smooth, title="Stoch %K (5,3,3)", color=color.fuchsia, linewidth=1, offset=-10, display=display.data_window)
hline(50, "Midline", color=color.gray, linestyle=hline.style_dashed)



