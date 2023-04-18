// This source code is subject to the terms of the Mozilla Public License 2.0 at https://mozilla.org/MPL/2.0/
// © CryptoButgi_Bot
//@version=5


strategy("EMA_RSI_TUT_1M", shorttitle="EMA_RSI_TUT_1M", overlay=true)
Ema50_Input = input(50,title = "EMA FAST")
Ema150_Input = input(150,title = 'EMA SLOW')
Ema300_Input = input(300,title = 'EMA SuperSlow')
Ema50 = (ta.ema(close,Ema50_Input))
plot(Ema50, title="Ema50", color=color.rgb(62, 235, 99))
Ema150 = (ta.ema(close,Ema150_Input))
plot(Ema150, title="Ema150", color=#f60404)
Ema300 = ta.ema(close,Ema300_Input)
plot(Ema300, title="Ema300", color=#2f618d)
Ema50Upper = Ema50 * 1.002
Ema50Downer = Ema50 * 0.998
Ema150Upper = Ema150 * 1.002
Ema150Downer = Ema150 * 0.998
Ema300Upper = Ema300 * 1.002
Ema300Downer = Ema300 * 0.998


RSAa_Length = input(7,title = 'RSI')
RSAa = ta.rsi(close,RSAa_Length)
MRSIa = ta.sma(RSAa,5)


// var startUnitSize = 0.0
var unit_size = 0.0
var unit_Price = 0.0
var Martin_Count = 0.0
var Martin_Target = 0.0
var Martin_TargetTwo = 0.0
var Rande_Long = 0.0
var Rande_Long_Size = 0.0
var Rande_Price = 0.0

var unit_size_Short = 0.0
var unit_Price_Short = 0.0
var Martin_Count_Short = 0.0
var Rande_Short = 0.0
var Rande_Short_Size = 0.0
var Rande_Short_Price = 0.0


////////////////////
Risk_Percentage = math.abs((Ema150Downer-close)*100/close)
Short_Risk_Percentage = math.abs((Ema150Upper-close)*100/close)

Longiz = close >= Ema300Downer
LongCondition = (RSAa[1] < 30.0 and RSAa[0] > 30.0) and (Ema150[2] > Ema300[2]) and (Ema50[1] > Ema150[1]) and ((close < Ema50Upper and close > Ema50Downer) or (close < Ema150Upper and close > Ema150Downer) or (close < Ema300Upper and close > Ema300Downer)) and (strategy.position_size <= 0)
if LongCondition
    strategy.close_all()

    Quisk = ((5.0 / 100) * (strategy.initial_capital / close / Risk_Percentage) * 0.3) *  30
    strategy.entry("Long",strategy.long,qty = Quisk)
    unit_size := Quisk
    unit_Price:= close
    Martin_Count := 2.0
    unit_size_Short := 0.0
    unit_Price_Short:= 0.0
    Martin_Count_Short := 0.0
    Martin_Target := 1.0
    Martin_TargetTwo := 1.0
    Rande_Long := 1.0
if Martin_Count == 2.0 and ohlc4 <= (0.995 * unit_Price) and (strategy.position_size >= 0)
    strategy.entry("Long",strategy.long,qty = unit_size)

    unit_size := 2 * unit_size
    PosiAVg = unit_Price + close
    unit_Price:= PosiAVg / 2.0
    Martin_Count := 3.0
    Martin_Target := 1.0
    Rande_Long := 2.0

if Martin_Count == 3.0 and ohlc4 <= (0.99 * unit_Price) and (strategy.position_size >= 0)
    strategy.entry("Long",strategy.long,qty = unit_size * 2)
    PosiAVg = unit_Price + close
    unit_Price:= PosiAVg / 2.0

    Martin_Target := 1.0
    Rande_Long := 3.0


if ohlc4 >= (1.005 * unit_Price) and (strategy.position_size >= 0) and Martin_Target == 1.0 and Rande_Long == 1.0
    strategy.close('Long',qty = strategy.position_size * 0.5)
  
    Martin_Target := 0.0
    Rande_Long := 2.0

if ohlc4 >= (1.01 * unit_Price) and (strategy.position_size >= 0) and Rande_Long == 2.0
    strategy.close('Long')
  
    Martin_Target := 0.0
    Rande_Long := 0.0

if Ema50[1] < Ema150[1] and ohlc4 <= (0.985 * strategy.position_avg_price) and (strategy.position_size >= 0)
    strategy.close_all()
    unit_size := 0.0
    unit_Price:= 0.0
    Martin_Count := 0.0




















































Shortiz = close <= Ema300Downer
ShortCondition = (RSAa[1] > 70.0 and RSAa[0] < 70.0) and (Ema150[2] < Ema300[2]) and (Ema50[1] < Ema150[1]) and ((close < Ema50Upper and close > Ema50Downer) or (close < Ema150Upper and close > Ema150Downer) or (close < Ema300Upper and close > Ema300Downer)) and (strategy.position_size >= 0)
if ShortCondition
    strategy.close_all()
    SQuisk = ((5.0 / 100) * ((strategy.initial_capital / close) / (Short_Risk_Percentage * 30)) * 0.3)
    strategy.entry("Short",strategy.short,qty = SQuisk)
    unit_size_Short := SQuisk
    unit_Price_Short:= close
    Martin_Count_Short := 2.0
    unit_size := 0.0
    unit_Price:= 0.0
    Martin_Count := 0.0
    Rande_Short := 1.0
if Martin_Count_Short == 2.0 and ohlc4 >= (1.005 * unit_Price_Short) and (strategy.position_size <= 0)
    strategy.entry("Short",strategy.short,qty = unit_size_Short)

    unit_size_Short := 2 * unit_size_Short
    PosiAVg = unit_Price_Short + close
    unit_Price_Short:= PosiAVg / 2.0
    Martin_Count_Short := 3.0


if Martin_Count_Short == 3.0 and ohlc4 >= (1.01 * unit_Price_Short) and (strategy.position_size <= 0)
    strategy.entry("Short",strategy.short,qty = unit_size_Short * 2)
    PosiAVg = unit_Price_Short + close
    unit_Price_Short:= PosiAVg / 2.0
    Martin_Count_Short := 0.0


if close <= (0.995 * unit_Price_Short) and (strategy.position_size <= 0) and Rande_Short == 1.0
    strategy.close('Short',qty = strategy.position_size * 0.5)

    Rande_Short := 2.0

if close <= (0.985 * unit_Price_Short) and (strategy.position_size <= 0) and Rande_Short == 2.0
    strategy.close('Short')
 
    Rande_Short := 0.0


if Ema50[1] > Ema150[1] and ohlc4 >= (1.015 * unit_Price_Short) and (strategy.position_size <= 0)
    strategy.close('Short')
    unit_size_Short := 0.0
    unit_Price_Short:= 0.0
    Martin_Count_Short := 0.0
