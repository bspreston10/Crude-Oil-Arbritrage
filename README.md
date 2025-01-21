# Crude-Oil-Arbritrage
## Table of Contents
- [Introduction](#introduction)
- [Methodology](#methodology)
- [Findings](#findings)
- [Conclusion](#conclusion)

## Introduction
Crude oil is one of the most traded commodities globally, valued for its relative security and fundamental role in modern society. However, despite its perceived stability, the oil market has experienced significant price drops during events such as the 2008 financial crisis and the 2020 oil crash. While many market participants faced substantial losses during these periods, firms like Gunvor Group and Trafigura capitalized on the opportunity by leveraging a contango market structure.
This project explores the trends and key metrics leading up to these market crashes and assesses the performance of a random forest machine learning model designed to predict the type of market structure—such as contango or backwardation—in the oil market.

## Methodology
The data in this model was collected from the U.S Energy Information Administration (EIA) focusing on the spot price FOB of WTI crude oil price in $/BBL, as well as NYMEX future contracts for crude oil. Below is a screenshot of the excel sheet of the data I worked with.

<img width="1000" alt="Screenshot 2025-01-20 at 5 05 11 PM" src="https://github.com/user-attachments/assets/277333a2-7281-45a1-adc1-9cc0effeb68a" />

**Column Data**
- *Period:* (01/01/2000 - 4/5/2024) The date the price of oil was fetched at
- *Year:* Year of the period column
- *Month:* Month of the period column
- *product-name:* Name of the commodity (Crude Oil)
- *process-name:* The type of contract being assesed
- *Future Price:* The price of the future contract determined by NYMEX
- *Expiration Date:* The date the future contract expires
- *Days to Expiration:* Number of days until the future contract expires
- *Spot Prices:* The spot price FOB of WTI crude oil
- *Time to Expiration:*: Calculated column from Days to Expiration/365
- *Cost of Carry:* Total cost of holding the crude oil
- *Convience Yield:* The non-monetary benefit of holding the crude oil, rather than holding a futures contract
- *Theoretical Future Price:* The future price calculated for their respective future contract
- *1, 2, 3 Month Rates:* The U.S treasury yield rate, values are backfilled from July 31st 2000 - January 1st 2000 due to insufficient data


## Arbritrage Model
### Setting up the model
Before running any backtesting on the arbritrage strategy, I needed to compute the theoretical future price to compare it to the actual future price. The future theoretical future price equation requires the spot price, the risk free rate, storage costs, convience yield, and time to maturity:
- Spot price was gathered from the EIA dataset
- For the risk-free rates, I collected the 1 month, 2 month, 3 month U.S treasury yield rates as proxies for the futures contract risk free rates
- For simplicity, the storage cost is assumed to be zero, although any real world application of this arbritrage model would need to account for storage cost
- The convience yield is calculated from the exponential cost-of-carry model where the convience yield = risk free rate + CoC - (Log(future price/spot price)) / time to expire
- The Cost of Carry (CoC) is calcualted from the exponential cost-of-carry model where CoC = spot price * risk free rate * time to expire
- Time to expire is calcualted by dividing the days to expire by 365 to get the years left to expire
- As a constant for transaction costs, slippage, and hedging I subtracted $1000 from any trade made to simulate real world scenarios

After computing the theoretical future price, the arbritrage model would initiate either a "Buy futures, short spot" when the theoretical future price was greater than the actual future price, a "Short futures, buy spot" if the theoretical future price was less than the acutal future price, and a "No arbritrage opprotunity" if the prices were equal to eachother. The total arbritrage profit was calculated by the absolute difference in theoretical and future price, multiplied by 1000 (constant for contract size), and then subtracted by $1000 (proxy for transaction costs)

### Model Performance
The strategy was backtested over the period from January 1, 2000, to April 5, 2024, yielding a cumulative profit of $122,512. Notably, the entirety of the profit was derived from shorting futures while buying spot prices. A particularly profitable period was observed during the 2020 oil crash, when spot prices turned negative for the first time in history. Despite the impressive returns, the model does not fully account for certain variables that significantly impact the profitability of trades, such as storage costs and convenience yield. Due to the lack of direct access to comprehensive data on these variables, proxies were employed to estimate their effects. While these approximations provide a general insight into the strategy’s performance, the absence of precise data introduces a level of uncertainty that warrants further investigation.

## Market Analysis
### 2020 Oil Crash
The spring of 2020 marked an unprecedented event in the global oil markets, driven by a combination of collapsing demand and oversupply. The onset of the COVID-19 pandemic resulted in widespread lockdowns and travel restrictions, leading to a dramatic decline in global energy consumption. At the same time, a price war between major oil producers, particularly Saudi Arabia and Russia, exacerbated the oversupply issue, flooding the market with excess crude oil. These factors culminated in a historic moment on April 20, 2020, when the price of West Texas Intermediate (WTI) crude oil futures plunged below zero for the first time in history, reaching -$37.63 per barrel. This anomaly was largely attributed to a shortage of storage capacity, forcing sellers to pay buyers to take delivery of oil. Lets look at this phenomon graphically to better understand what happened, and if there was any quantitative warning that may have been overlooked by many.
#### January 2020 - February 2020:

<img width="600" alt="Screenshot 2025-01-21 at 6 43 10 PM" src="https://github.com/user-attachments/assets/8ee5c662-4d30-4744-8308-821cc9a1059e" />    <img width="600" alt="Screenshot 2025-01-21 at 6 43 48 PM" src="https://github.com/user-attachments/assets/5ca7446e-73d4-4cd8-ac64-bda81ebc454f" />

Despite a catastrophe soon to approach for the oil market, the months preceeding the oil crash we see minimal price discrepencies between the future contracts, as well as between the spot and future price of crude oil. This tight spread is often associated with market stability, as the COVID-19 pandemic hadn't been declared a pandemic yet and the OPEC production cuts were agreed upon a month earlier preventing a chance for oversupply. 




