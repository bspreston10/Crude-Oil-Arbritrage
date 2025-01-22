# Crude-Oil-Arbritrage
## Table of Contents
- [Introduction](#introduction)
- [Methodology](#methodology)
- [Arbritrage Model](#arbritrage-model)
- [2020 Market Analysis](#2020-oil-crash-analysis)
- [Market Strucutre Detection](#market-strucutre-detection)

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

## 2020 Oil Crash Analysis
The spring of 2020 marked an unprecedented event in the global oil markets, driven by a combination of collapsing demand and oversupply. The onset of the COVID-19 pandemic resulted in widespread lockdowns and travel restrictions, leading to a dramatic decline in global energy consumption. At the same time, a price war between major oil producers, particularly Saudi Arabia and Russia, exacerbated the oversupply issue, flooding the market with excess crude oil. These factors culminated in a historic moment on April 20, 2020, when the price of West Texas Intermediate (WTI) crude oil futures plunged below zero for the first time in history, reaching -$37.63 per barrel. This anomaly was largely attributed to a shortage of storage capacity, forcing sellers to pay buyers to take delivery of oil. Lets look at this phenomon graphically to better understand what happened, and if there was any quantitative warning that may have been overlooked by many.
#### December 2019 - February 2020:
December 2019 saw a constant period of backwardation, where the spot price was worth more than the future price, and the future contract 2 was worth more than the future contract 3 or 4. This is likely due to the OPEC decision to cut production, stabilizing markets, and the winter demand of crude oil casuing a short term increase in the demand of crude oil. This period of backwardation was short lived however, when in January and Febraury 2020 we see the spread of both the future and spot price and the future contracts tighten significantly, likely due to the uncertainty of the upcoming COVID-19 pandemic and disagreements between Saudi Arabia and Russia, two leading producers of crude oil. 

<img width="850" alt="Screenshot 2025-01-22 at 11 46 19 AM" src="https://github.com/user-attachments/assets/64326d95-9ead-4c7c-8187-96e98812f996" />

<img width="850" alt="Screenshot 2025-01-22 at 11 47 50 AM" src="https://github.com/user-attachments/assets/9d41c802-6ed5-48c7-88d4-741eea3464b7" />


Looking at this quantitatively, its not until March 2020 where we see any negative effect on the market. Before March the standard deviation for the future and spot price varied between 2 and 3, the spread between the future and spot price was only ~$0.13, and the calculated convience yield was ~0.12%. In March, we a STD of 10.71 for the spot price and 7.91 for the future price. Coupled with that, the calculated convience yield was -5.61% and the average price of the spot price was $29.21 per barrel and the future price $32.75. This $3.54 difference shows a moment of contango in the market, where ideally one would short the futures and buy the spot price expecting that both would revert back to their mean price. The large spread in both the future and spot price and the future contracts occurs in the begining of March, likely due to the immediate shocks of COVID-19 and the price war. We see a tightening in the later months however likely a small correction from earlier in the month. This correction was short lived however, because in April the convience yield dropped to -6.87%, the standard deviation of the spot price rose to 13, and the difference in the price between the future and spot rose to $10.36. Despite the challenging market conditions, the standard deviation of the future price dropped to 5, and Future Contract 4 was trading at a $3-4 premium over Future Contract 2. This could indicate a positive investment outlook for the future. This period of contango in the market presents intriguing opportunities for arbitrage trading, as demonstrated by the model, which identified April 2020 as the most profitable month.

<img width="850" alt="Screenshot 2025-01-22 at 11 49 30 AM" src="https://github.com/user-attachments/assets/b7eacbed-6c10-483f-881c-f81629c757ed" />


<img width="850" alt="Screenshot 2025-01-22 at 11 45 12 AM" src="https://github.com/user-attachments/assets/fb9144c6-2256-4435-a6e1-fb037d0ded47" />



Overall this event was largely due to two main reasons:
1. The growing concern over COVID-19 and the CDC officially declaring it a pandemic in March. With no positive outlook for the future any time soon after that, people tried to rapidly sell their oil due to demand concerns.
2. March 2020 also saw the start of the 2020 oil price war between Saudi Arabia and Russia due to disagreements in the OPEC production cuts. Saudi Arabia flooded the market with oil and decreased their export prices to try and gain more control in the oil market, particularly in Europe and Asia.

These events caused the oil market to go into panic mode, leading to a sharp and unprecedented decline in oil prices. As demand plummeted due to global lockdowns and travel restrictions, and supply surged from Saudi Arabia’s aggressive production and pricing strategy, the market became severely imbalanced. Storage facilities quickly filled up, creating logistical challenges that further exacerbated the crisis.

I invite you to explore the Excel sheet containing the project data and an interactive dashboard that tracks the future and spot price as well as the spread between the different future contracts.
