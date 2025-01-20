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

<img width="1417" alt="Screenshot 2025-01-20 at 5 05 11 PM" src="https://github.com/user-attachments/assets/277333a2-7281-45a1-adc1-9cc0effeb68a" />

**Column Data**
- *Period:* (Primary Key) The date the price of oil was fetched at
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
