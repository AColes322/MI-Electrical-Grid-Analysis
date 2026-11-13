# MI-Electrical-Grid-Analysis
Modeling electrical grid demand in Michigan using EIA and NOAA climate data

# Question: 
1) Can Michigan’s electrical grid handle both peak seasonal demands and the additional draw caused by new data centers? 
2) Under what weather conditions should Consumers Energy and DTE plan additional output?

# Recommendation:
1) Michigan's electrical grid can handle 4 medium data centers, but not 10 hyperscale ones.
    Medium Data Centers (4 centers under 50 MW capacity each): Additional draw is 4,800 MWh/day or 0.8% of max demand in 2022-24
    Hyperscale Data Centers (10 centers @ 1.4 GW capacity each): Additional draw is 336,000 MWh/day assuming constant full draw 
        This would add 54.3% added stress during peak demand, which heightens the risk of systemic failure.
2) DTE & Consumers Energy should plan capacity increases for high temperature + high humidity days (>75°F + >80% humidity) 
    However, linear regression models predict typical weather patterns and further refinement is needed for extreme demand prediction 

# Data: 
NOAA climate data at Lansing Airport (csv downloaded on Nov. 26, 2025)
EIA: US Energy Information Administration (API accessed Nov. 26, 2025)
1,089 days after dropping 7 incomplete days; 43 columns of attributes and metadata.

# Method: 
Split at 55°F average temperature to simulate seasons
Exhaustive AIC/BIC predictor search
Linear regression (statsmodels ols)
Trained on 2022–2023, tested on 2024
Modeled data center constant full draw based on capacity of 50 MW for medium data centers and 1.4 GW for hyperscale; 4-10 proposed centers in MI

# Results: 
Warm season test R² 0.684, RMSE 34,412 MWh (about 6% of typical demand)
Cold season R² 0.350
Overall R² 0.679
Data center scenarios: share evaluated against the 619,072 MWh maximum day in 2022-24
    4 medium centers add 0.8%
    one hyperscale center adds 5.4%, 
    the 6.4 GW planned by DTE and Consumers Energy adds 24.8%
    10 hyperscale centers add 54.3%

# Limitations: 
Predictive Accuracy: Cold season model: R² = 0.35 (only 35% of demand explained by weather)
                  Warm season model underpredicts extreme demand (>525k MWh) where forecasting matters most
Multicollinearity: High condition numbers (cold: 12,200, warm: 1,540)
                  Natural correlation between weather variables difficult to eliminate
Data Constraints: Statewide aggregated demand vs. single-location weather (Lansing Airport)
                  Daily aggregation loses hourly demand patterns
                  Limited to weather predictors only
# Next steps: 
Model improvements: Add calendar variables (weekends, holidays, daylight hours)
                  Compare polynomial regression or SARIMAX for better extreme value prediction
                  Use seasonal rather than temperature thresholds 
Enhanced Data: Hourly weather + demand data for more precise forecasting
                  Location-specific substation demand (not statewide aggregates)
Vulnerability Analysis: Geospatial mapping: infrastructure age + transformer distance
                  Social Vulnerability Index to identify at-risk populations during overload events

# How to run: 
pip install -r requirements.txt, open the notebook, then Run All. The data CSVs are in the repo.

# Files: 
Notebook: MIElectricityProject.ipynb
Data: elec_demand_raw.csv
      Lansing_airport_2022_2024.csv
Reports: MI Electric Grid Project Final Report PDF
      MI Electric Grid Project Final Presentation PDF
requirements.txt

AI assistance: Claude (Anthropic) was used in November 2026 to review the project for errors and clarity, including identifying a unit conversion error (MW vs. MWh) in the data center scenarios, and to suggest a revision to the fit_and_compare function that excludes constant predictors. All other analysis, code, and writing are my own
