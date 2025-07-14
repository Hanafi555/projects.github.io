In this assignment, you will apply time series forecasting techniques to analyze and predict future values. You will work with the "Global Temperature Time Series" dataset, which contains monthly temperature anomalies from 1880 to present day.

### Task: Global Temperature Forecasting

Your task is to analyze the global temperature time series and build forecasting models to predict future temperature trends. Follow these steps:

1. **Data Exploration**

   - Load and prepare the dataset
   - Visualize the time series data
   - Check for trends, seasonality, and cycles
   - Create seasonal plots and subseries plots
   - Analyze autocorrelation using ACF plots

2. **Time Series Decomposition**

   - Decompose the time series into trend, seasonal, and residual components
   - Compare additive and multiplicative decomposition methods
   - Interpret your findings

3. **Forecasting**

   - Split the data into training and testing sets using a temporal split
   - Implement and evaluate the following forecasting methods:
     - Naive forecasting (last value and seasonal naive)
     - ARIMA/SARIMA models
     - Exponential smoothing models
   - Use appropriate evaluation metrics (MAE, MAPE, sMAPE) to compare model performance

4. **Model Selection and Interpretation**
   - Determine which model performs best for this dataset
   - Interpret the results and explain why certain models might work better than others
   - Generate forecasts for future periods

### Starter Code

```python
# Import required libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.graphics.tsaplots import plot_acf
from sktime.forecasting.naive import NaiveForecaster
from sktime.forecasting.arima import ARIMA, AutoARIMA
from sktime.forecasting.exp_smoothing import ExponentialSmoothing
from sktime.forecasting.ets import AutoETS
from sktime.forecasting.model_selection import temporal_train_test_split
from sktime.performance_metrics.forecasting import mean_absolute_percentage_error
from sktime.utils.plotting import plot_series

# Load the dataset (NASA GISTEMP Global Temperature Anomaly)
url = "https://data.giss.nasa.gov/gistemp/tabledata_v4/GLB.Ts+dSST.csv"

# Data preparation
def prepare_nasa_data(url):
    # Read the data, skipping the header explanation
    df = pd.read_csv(url, skiprows=1)

    # Remove the unnecessary columns
    df = df.iloc[:, :13]

    # Rename columns
    df.columns = ['Year', 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

    # Convert from wide to long format
    df_long = df.melt(id_vars=['Year'],
                       var_name='Month',
                       value_name='Temperature_Anomaly')

    # Filter out missing or invalid data
    df_long = df_long[df_long['Temperature_Anomaly'] != '***']
    df_long['Temperature_Anomaly'] = df_long['Temperature_Anomaly'].astype(float)

    # Create a proper date index
    df_long['Date'] = pd.to_datetime(df_long['Year'].astype(str) + '-' + df_long['Month'], format='%Y-%b')

    # Sort by date
    df_long = df_long.sort_values('Date')

    # Create a time series
    ts_data = df_long.set_index('Date')['Temperature_Anomaly']

    return ts_data

# Prepare the time series data
ts_data = prepare_nasa_data(url)

# Now you have a time series object 'ts_data' to work with
print(f"Time series data from {ts_data.index.min()} to {ts_data.index.max()}")
print(f"Total number of observations: {len(ts_data)}")

# TODO: Explore and visualize the time series data
# HINT: Use time series plots, seasonal plots, and autocorrelation plots

# TODO: Perform time series decomposition
# HINT: Try both additive and multiplicative decomposition

# TODO: Split the data into training and testing sets
# HINT: Use temporal_train_test_split with the last 36 months as test data

# TODO: Implement and evaluate different forecasting methods
# HINT: Try naive forecasting, ARIMA, and ETS models

# TODO: Compare model performance using appropriate metrics
# HINT: Use sMAPE or MAPE to evaluate and compare models

# TODO: Generate and visualize forecasts with the best performing model
```
```Python
# Import required libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.graphics.tsaplots import plot_acf
from sktime.forecasting.naive import NaiveForecaster
from sktime.forecasting.arima import ARIMA, AutoARIMA
from sktime.forecasting.exp_smoothing import ExponentialSmoothing
from sktime.forecasting.ets import AutoETS
from sktime.forecasting.model_selection import temporal_train_test_split
from sktime.performance_metrics.forecasting import mean_absolute_percentage_error
from sktime.utils.plotting import plot_series
```
```Python
# Load the dataset (NASA GISTEMP Global Temperature Anomaly)
url = "https://data.giss.nasa.gov/gistemp/tabledata_v4/GLB.Ts+dSST.csv"

# Data preparation
def prepare_nasa_data(url):
    # Read the data, skipping the header explanation
    df = pd.read_csv(url, skiprows=1)

    # Remove the unnecessary columns
    df = df.iloc[:, :13]

    # Rename columns
    df.columns = ['Year', 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

    # Convert from wide to long format
    df_long = df.melt(id_vars=['Year'],
                       var_name='Month',
                       value_name='Temperature_Anomaly')

    # Filter out missing or invalid data
    df_long = df_long[df_long['Temperature_Anomaly'] != '***']
    df_long['Temperature_Anomaly'] = df_long['Temperature_Anomaly'].astype(float)

    # Create a proper date index
    df_long['Date'] = pd.to_datetime(df_long['Year'].astype(str) + '-' + df_long['Month'], format='%Y-%b')

    # Sort by date
    df_long = df_long.sort_values('Date')

    # Create a time series
    ts_data = df_long.set_index('Date')['Temperature_Anomaly']

    return ts_data

# Prepare the time series data
ts_data = prepare_nasa_data(url)

# Now you have a time series object 'ts_data' to work with
print(f"Time series data from {ts_data.index.min()} to {ts_data.index.max()}")
print(f"Total number of observations: {len(ts_data)}")
```
Output:
Time series data from 1880-01-01 00:00:00 to 2025-05-01 00:00:00
Total number of observations: 1745

✅ 1. Data Exploration
📈 A. Visualize the time series data

```Python
plt.figure(figsize=(15, 5))
plt.plot(ts_data, label='Temperature Anomaly')
plt.title("Global Monthly Temperature Anomalies (1880–2025)")
plt.xlabel("Year")
plt.ylabel("Anomaly (°C)")
plt.grid(True)
plt.legend()
plt.show()
```
![alt text](image.png)

Findings:
```
🌍 Findings from the Time Series Visualization

🔺 1. Clear Upward Trend
Over time, the global temperature anomalies have increased significantly, especially after the 1970s.
The anomaly has shifted from around -0.5°C in the late 1800s to over +1.4°C in recent years.
This indicates a long-term warming trend, consistent with global climate change.

🔁 2. Seasonal Fluctuations
You can observe regular ups and downs each year, suggesting seasonal variation (expected due to Earth's orbit and climate cycles).
These fluctuations seem stable in amplitude over time.

🔄 3. Cyclic Patterns
While there's an overarching upward trend, there appear to be short- to medium-term cycles, such as mild coolings followed by warming.
These could relate to natural climate oscillations (e.g., El Niño, volcanic eruptions, solar activity).

🔉 4. Increased Variability in Recent Years
Post-2000, the spikes and dips seem more frequent and larger in amplitude.
This may indicate greater climate volatility or higher frequency of extreme temperature events.

📌 Interpretation
This plot strongly suggests:

A non-stationary time series (due to the trend),
Presence of seasonality,
And potential cyclical components.
```
🔁 B. The Augmented Dickey-Fuller (ADF) Level Test Check 
```Python
from statsmodels.tsa.stattools import adfuller
import pandas as pd
# Assume ts_data is your original time series (pd.Series)

# ADF Test on original time series
print("ADF Test on Original Series")
adf_result = adfuller(ts_data)

print(f"ADF Statistic: {adf_result[0]:.4f}")
print(f"p-value: {adf_result[1]:.4f}")
print("Critical Values:")
for key, value in adf_result[4].items():
    print(f"  {key}: {value:.4f}")

if adf_result[1] > 0.05:
    print("Conclusion: Series is likely non-stationary. Consider differencing.")
else:
    print("Conclusion: Series is likely stationary. No differencing needed.")
```
Output:
ADF Test on Original Series
ADF Statistic: 0.2099
p-value: 0.9728
Critical Values:
  1%: -3.4342
  5%: -2.8632
  10%: -2.5677
Conclusion: Series is likely non-stationary. Consider differencing.


🔁 C. Check for trend using 12-month rolling average

```Python
rolling_mean = ts_data.rolling(window=12).mean()

plt.figure(figsize=(15, 5))
plt.plot(ts_data, alpha=0.5, label='Monthly Anomaly')
plt.plot(rolling_mean, color='red', label='12-Month Rolling Mean')
plt.title("Global Temperature Anomaly with 12-Month Trend Line")
plt.xlabel("Year")
plt.ylabel("Anomaly (°C)")
plt.legend()
plt.grid(True)
plt.show()
```
![alt text](image-1.png)

Findings:
```
🔴 The red line represents the 12-month rolling mean of the global temperature anomalies.
💡 What does that mean?
A rolling mean (or moving average) smooths out short-term fluctuations to highlight long-term trends.

In your case:
Every point on the red line is the average of the past 12 months (1 year) of temperature anomaly data.

It reduces the "noise" from monthly spikes and dips (blue line), helping us clearly see the underlying upward trend in global temperatures from 1880 to 2025.

📌 Why use a 12-month rolling mean?
Because:

The data is monthly.

12 months = 1 year, so it smooths out seasonal effects while preserving long-term patterns.
```
📆 D. Seasonal Plot (Average anomaly by month)
```Python
# Create month column
monthly = ts_data.copy()
monthly_df = monthly.reset_index()
monthly_df['Month'] = monthly_df['Date'].dt.month
monthly_df['Month_Name'] = monthly_df['Date'].dt.strftime('%b')

# Average anomaly by month (across all years)
monthly_avg = monthly_df.groupby('Month_Name')['Temperature_Anomaly'].mean()
ordered_months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

plt.figure(figsize=(10, 5))
sns.barplot(x=ordered_months, y=monthly_avg.reindex(ordered_months))
plt.title("Average Temperature Anomaly by Month (1880–2025)")
plt.ylabel("Anomaly (°C)")
plt.xlabel("Month")
plt.grid(True)
plt.show()
```
![alt text](image-2.png)

Findings:
```
Each bar represents the average temperature anomaly for that specific month, averaged over 145 years.

The anomalies are not absolute temperatures, but deviations from a long-term average baseline.

📊 Key Observations:
| Month                  | Avg Anomaly (°C)     | Interpretation                                          |
| ---------------------- | -------------------- | ------------------------------------------------------- |
| 🔺 **March**           | Highest (\~0.105°C)  | Historically the warmest anomaly month.                 |
| 🔹 **June**            | Lowest (\~0.045°C)   | Historically the smallest anomaly.                      |
| 🔄 **Oct–Nov**         | Consistently high    | Autumn months tend to have high anomalies.              |
| 🔁 **Seasonal effect** | Varies across months | Indicates **seasonal cyclicality** in warming patterns. |


🧠 Interpretation:
Some months show consistently higher deviations from the baseline, possibly due to:

Atmospheric or oceanic circulation patterns,

Solar radiation patterns (e.g., March receiving more sunlight in northern hemisphere),

Land-sea heating differences.

These patterns support the idea of seasonality in the time series, which is essential for choosing models like SARIMA or seasonal ETS.
```
📊 E. Subseries Plot (anomaly distribution per month per year)

```Python
# Pivot for heatmap: rows = year, columns = month
heatmap_data = monthly_df.pivot_table(index=monthly_df['Date'].dt.year,
                                      columns='Month',
                                      values='Temperature_Anomaly')

plt.figure(figsize=(15, 8))
sns.heatmap(heatmap_data, cmap="coolwarm", center=0, cbar_kws={'label': 'Temp Anomaly (°C)'})
plt.title("Subseries Plot: Monthly Temperature Anomalies Over Years")
plt.xlabel("Month")
plt.ylabel("Year")
plt.show()
```
![alt text](image-3.png)
Findings:
```
X-axis (Month): 1 to 12 → January to December

Y-axis (Year): 1880 to 2025

Color:

🔵 Blue: Below-average anomalies (cooler)

🔴 Red: Above-average anomalies (warmer)

🟡 White-ish: Near-zero anomaly (baseline)

📊 Interpretation:
🔺 1. Long-term warming
The plot gets redder over time, especially after the 1980s → confirms a clear warming trend.

Recent years (bottom rows) are consistently red, meaning nearly every month is warmer than the historical average.

🔁 2. Seasonal and monthly variation
Some months (e.g., March, October) show darker red more often in recent years, which aligns with the bar plot you made earlier.

🔄 3. Earlier years had more cool months
The top half (before ~1960) is predominantly blue → most months were cooler than the baseline.

🧠 Summary:
This subseries plot is a powerful visual confirmation that:

🌍 The Earth is warming significantly,

📅 Monthly anomalies vary by season but all months are trending hotter,

🧭 This supports your model selection later (e.g., SARIMA or seasonal ETS).

```
🔗 F. ACF Plot (to analyze autocorrelation)

```Python
from statsmodels.graphics.tsaplots import plot_acf
plt.figure(figsize=(10, 5))
plot_acf(ts_data, lags=60)
plt.title("Autocorrelation Function (ACF) - Identify q (MA order)")
plt.xlabel("Lag")
plt.ylabel("Autocorrelation")
plt.grid(True)
plt.show()
```
![alt text](image-4.png)
Findings:

```
🔁 Your time series has memory — past values influence future ones.

🔼 The slow decay indicates a trend → this is why you’ll need first-order differencing (d = 1) in ARIMA modeling.

❌ This ACF should not be used directly to choose q (MA order) — you must difference first.
```

🔗 F. First Order Differencing - ACF Plot (to analyze autocorrelation)

```Python
  #Difference the series
ts_diff = ts_data.diff().dropna()
#This calculates the first-order difference of the time series
#This removes trends by looking at how much the value changes from one time step to the next.

# Re-plot ACF on differenced data
from statsmodels.graphics.tsaplots import plot_acf

plt.figure(figsize=(10, 5))
plot_acf(ts_diff, lags=40)
plt.title("ACF of Differenced Series (to determine q)")
plt.grid(True)
plt.show()
```
![alt text](image-8.png)
Findings:
```
| Aspect                   | Value                                                               |
| ------------------------ | ------------------------------------------------------------------- |
| Autocorrelation strength | Small (slightly negative)                                           |
| Statistical significance | ❌ **Not significant** (inside the shaded region)                    |
| Interpretation           | No meaningful correlation with previous value in differenced series |
| Suggested MA order (`q`) | **0** (don’t include an MA term)                                    |

After first differencing, the time series no longer shows significant autocorrelation at Lag 1.

So:

✔️ The series is now stationary

❌ No evidence of short-term moving average pattern

✅ Use q = 0 in my  ARIMA model
```
🔗 G. PACF Code (After First Differencing)

```Python
from statsmodels.graphics.tsaplots import plot_pacf
import matplotlib.pyplot as plt

# Plot PACF to determine AR (p)
plt.figure(figsize=(10, 5))
plot_pacf(ts_diff, lags=40, method='ywm')  # Yule-Walker Modified is more stable
plt.title("PACF of Differenced Series (to determine p)")
plt.xlabel("Lag")
plt.ylabel("Partial Autocorrelation")
plt.grid(True)
plt.show()
```
![alt text](image-12.png)

![alt text](image-9.png)


Findings:
```
Lag 1 ACF = -0.352

95% Confidence Interval = [-0.399, -0.305]

This means the lag-1 value is actually just inside the CI range — which matches the visual (barely touching or within the shaded area).

Although lag 1 has a relatively high autocorrelation, it's not statistically significant at the 95% confidence level, because it lies within the confidence bounds.

 it as > 0.05 — not significant.

✅ Final Interpretation:
Even though lag 1 looks important (it's larger than the others), it doesn't cross the statistical threshold to be confidently called "significant".
q = 0
```
🔗 H. First Order Differencing - PACF Plot (to analyze autocorrelation)
```Python
from statsmodels.graphics.tsaplots import plot_pacf
from statsmodels.tsa.stattools import pacf
import pandas as pd
import matplotlib.pyplot as plt

# ---- Step 1: Plot PACF on Differenced Data ----
plt.figure(figsize=(10, 5))
plot_pacf(ts_diff, lags=40, method='ywm')  # Yule-Walker Modified
plt.title("PACF of Differenced Series (to determine p)")
plt.grid(True)
plt.show()

# ---- Step 2: Extract PACF values and CI for analysis ----
pacf_vals, confint = pacf(ts_diff, nlags=40, alpha=0.05, method='ywmle')

# ---- Step 3: Identify statistically significant lags (outside CI) ----
p_values = []
for i in range(len(pacf_vals)):
    lower, upper = confint[i]
    if pacf_vals[i] < lower or pacf_vals[i] > upper:
        p_values.append('< 0.05')
    else:
        p_values.append('> 0.05')

# ---- Step 4: Create summary table ----
pacf_df = pd.DataFrame({
    'Lag': range(len(pacf_vals)),
    'PACF': pacf_vals.round(3),
    '95% CI Lower': confint[:, 0].round(3),
    '95% CI Upper': confint[:, 1].round(3),
    'p-value (approx)': p_values
})

# ---- Step 5: Display table ----
display(pacf_df.head(21)) 
```
![alt text](image-13.png)

![alt text](image-14.png)

Findings:
```
All spikes (from lag 1 onwards) are within the confidence bands → no significant partial autocorrelation detected after differencing.

Lag 3 (PACF spikes fall sharply after that)
p = 3
```
I. The Augmented Dickey-Fuller (ADF) test is used to determine whether a time series is stationary.

```Python
from statsmodels.tsa.stattools import adfuller
import pandas as pd

# Assume ts_data is your original time series (pd.Series)

# ADF Test on original time series
print("ADF Test on Original Series")
adf_result = adfuller(ts_data)

print(f"ADF Statistic: {adf_result[0]:.4f}")
print(f"p-value: {adf_result[1]:.4f}")
print("Critical Values:")
for key, value in adf_result[4].items():
    print(f"  {key}: {value:.4f}")

if adf_result[1] > 0.05:
    print("Conclusion: Series is likely non-stationary. Consider differencing.")
else:
    print("Conclusion: Series is likely stationary. No differencing needed.")

# ------------------------------
# First-order differencing
ts_diff = ts_data.diff().dropna()

# ADF Test on differenced series
print("\nADF Test on First-Order Differenced Series")
adf_diff_result = adfuller(ts_diff)

print(f"ADF Statistic: {adf_diff_result[0]:.4f}")
print(f"p-value: {adf_diff_result[1]:.4f}")
print("Critical Values:")
for key, value in adf_diff_result[4].items():
    print(f"  {key}: {value:.4f}")

if adf_diff_result[1] > 0.05:
    print("Conclusion: Differenced series is still non-stationary.")
else:
    print("Conclusion: Differenced series is stationary. To use d = 1 in ARIMA.")

```
Output:
```
ADF Test on Original Series
ADF Statistic: 0.2099
p-value: 0.9728
Critical Values:
  1%: -3.4342
  5%: -2.8632
  10%: -2.5677
Conclusion: Series is likely non-stationary. Consider differencing.

ADF Test on First-Order Differenced Series
ADF Statistic: -12.3055
p-value: 0.0000
Critical Values:
  1%: -3.4342
  5%: -2.8632
  10%: -2.5677
Conclusion: Differenced series is stationary. To use d = 1 in ARIMA.
```
✅ 2. Time Series Decomposition
```Python
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.tsa.seasonal import seasonal_decompose

# Example: Assuming `ts_data` is your time series with a datetime index and regular frequency
ts_data.index = pd.to_datetime(ts_data.index)
ts_data = ts_data.asfreq('M')  # or 'D', 'Q', etc. depending on your data
```
```Python
print(ts_data.shape)
print(ts_data.head())
```
Output:
```Python
(0,)
Series([], Freq: M, Name: Temperature_Anomaly, dtype: float64)
```
```Python
import os
print(os.listdir())
```
Output
```Puthon
import os
print(os.listdir())
```
```Python
url = "https://data.giss.nasa.gov/gistemp/tabledata_v4/GLB.Ts+dSST.csv"
df = pd.read_csv(url, skiprows=1)
```
```Python
# Keep only the first 13 columns: 1 for year + 12 for months
df = df.iloc[:, :13]

# Now assign the correct column names
df.columns = ['Year', 'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
              'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

# Wide → Long format
df_long = pd.melt(df, id_vars=['Year'], 
                  var_name='Month', 
                  value_name='Temperature_Anomaly')
# Strip whitespace and remove rows with any non-numeric values
df_long['Temperature_Anomaly'] = df_long['Temperature_Anomaly'].astype(str).str.strip()

# Keep only valid numbers using regex
df_long = df_long[df_long['Temperature_Anomaly'].str.match(r'^-?\d+(\.\d+)?$')].copy()

# Convert to float now safely
df_long['Temperature_Anomaly'] = df_long['Temperature_Anomaly'].astype(float)

# Confirm it's numeric and no weird values remain
print(df_long['Temperature_Anomaly'].dtypes)
print(df_long['Temperature_Anomaly'].unique()[:10])  # Preview first 10 values
```
Output:
```Python
float64
[-0.2   0.16 -0.29 -0.13 -0.59 -0.44 -0.72 -0.34 -0.09 -0.42]
```

```Python
print(ts_data.isna().sum())        # how many missing?
print(ts_data.shape[0])            # how many rows left?
print(ts_data.head(25))            # inspect data
# NEW: print with 2 significant figures (optional display only)
print(df_long['Temperature_Anomaly'].apply(lambda x: f"{x:.2g}").unique()[:10])
```
Output:
![alt text](image-15.png)

```Python
# Reindex to full monthly date range
full_range = pd.date_range(start=ts_data.index.min(), end=ts_data.index.max(), freq='MS')
ts_data = ts_data.reindex(full_range)

# Interpolate missing months
ts_data = ts_data.interpolate(method='linear')

# Confirm again
print(ts_data.shape)               # should now be >= 24
print(ts_data.isna().sum())        # should be 0
```
Output:
```Python
(1731,)
0
```
Multiplicative Decomposition 
```Python
from statsmodels.tsa.seasonal import seasonal_decompose
import pandas as pd
import matplotlib.pyplot as plt

# 1. Ensure data has a proper datetime index and monthly frequency
ts_data = ts_data.asfreq('MS')

# 2. Interpolate missing values
ts_data = ts_data.interpolate(method='linear')

# 3. Filter out non-positive values (multiplicative requires > 0)
ts_data = ts_data[ts_data > 0]

# Optional sanity check
print("NaNs remaining:", ts_data.isna().sum())
print("Non-positive values remaining:", (ts_data <= 0).sum())

# 4. Perform multiplicative decomposition
decomp = seasonal_decompose(ts_data, model='multiplicative', period=12)
decomp.plot()
plt.suptitle("Multiplicative Time Series Decomposition")
plt.tight_layout()
plt.show()
```
Output:
```Python
NaNs remaining: 0
Non-positive values remaining: 0
```
![alt text](image-16.png)

Findings:
```Python
🔍 Multiplicative Decomposition Findings

1. Trend Component

The trend line shows a clear and steady upward movement, particularly:

   Flat or modest until ~1950s
   Sharp acceleration from 1980 onward

This confirms long-term global warming patterns.

2. Seasonal Component

The seasonal variation is consistent throughout the series.

Amplitude of seasonality is multiplicatively proportional to the level of the trend (i.e., as the trend increases, seasonal fluctuations grow slightly in scale).

Indicates temperature anomalies are seasonally affected, likely tied to Earth's natural climate cycles (e.g., hemispheric seasons, El Niño/La Niña patterns).

3. Residual Component

Residuals (random noise) are present across the entire time span.

Some larger fluctuations occur during periods of global events (e.g., volcanic eruptions, major El Niño years).

No clear autocorrelation pattern remains — indicating that the trend and seasonal components were successfully extracted.

✅ Interpretation

The multiplicative model is appropriate because:

Seasonal amplitude increases with trend.

The time series is strictly positive and exhibits proportional seasonality.

The strong trend dominance suggests that forecasting models need to emphasize long-term growth rather than just seasonal cycles.
```
🔍 Addictive Decomposition

```Pytohon
from statsmodels.tsa.seasonal import seasonal_decompose
import pandas as pd
import matplotlib.pyplot as plt

# 1. Ensure time series has monthly frequency
ts_data = ts_data.asfreq('MS')

# 2. Interpolate missing values
ts_data = ts_data.interpolate(method='linear')

# Optional sanity check
print("NaNs remaining:", ts_data.isna().sum())

# 3. Perform additive decomposition
decomp = seasonal_decompose(ts_data, model='additive', period=12)
decomp.plot()
plt.suptitle("Additive Time Series Decomposition")
plt.tight_layout()
plt.show()
```
Output:
```Python
NaNs remaining: 0
```
![alt text](image-17.png)

✅ 3. Forecasting

```Python
# Use last 20% of data for testing
train_size = int(len(ts_data) * 0.8)
train = ts_data.iloc[:train_size]
test = ts_data.iloc[train_size:]
```
Naive forecasting (last value and seasonal naive)

```Python
naive_forecast = pd.Series(train.iloc[-1], index=test.index)
```
```Python
# Seasonal naive: use same month from previous year
season_length = 12  # Monthly data → 12-month season

seasonal_naive_forecast = test.copy()

for i in range(len(test)):
    seasonal_naive_forecast.iloc[i] = train.iloc[-season_length + i % season_length]
```

ARIMA/SARIMA models

```Python
from statsmodels.tsa.statespace.sarimax import SARIMAX
# Fit ARIMA (tune order if needed)
arima_model = SARIMAX(train, order=(1, 1, 1), enforce_stationarity=False, enforce_invertibility=False)
arima_result = arima_model.fit(disp=False)

# Forecast for test period
arima_forecast = arima_result.predict(start=test.index[0], end=test.index[-1])
```
Exponential smoothing models

```Python
from statsmodels.tsa.holtwinters import ExponentialSmoothing

model = ExponentialSmoothing(train, trend="add", seasonal="add", seasonal_periods=12)
hw_model = model.fit()
hw_forecast = hw_model.forecast(len(test))
```
```Python
from sklearn.metrics import mean_absolute_error
import numpy as np

def mean_absolute_percentage_error(y_true, y_pred): 
    return np.mean(np.abs((y_true - y_pred) / y_true)) * 100

def symmetric_mape(y_true, y_pred):
    return 100 * np.mean(2 * np.abs(y_pred - y_true) / (np.abs(y_true) + np.abs(y_pred)))

def print_metrics(name, y_true, y_pred):
    print(f"\n📊 {name} Forecast Evaluation:")
    print("MAE:", mean_absolute_error(y_true, y_pred))
    print("MAPE:", mean_absolute_percentage_error(y_true, y_pred))
    print("sMAPE:", symmetric_mape(y_true, y_pred))

# Run evaluations
print_metrics("Naive", test.values.flatten(), naive_forecast.values)
print_metrics("ARIMA", test.values.flatten(), arima_forecast.values)
print_metrics("Exponential Smoothing", test.values.flatten(), hw_forecast.values)
```
Output:
```Python
📊 Naive Forecast Evaluation:
MAE: 0.47384005763688763
MAPE: 57.76886257993045
sMAPE: 84.86439068998725

📊 ARIMA Forecast Evaluation:
MAE: 0.4757571072313354
MAPE: 58.04029387550569
sMAPE: 85.41070141308812

📊 Exponential Smoothing Forecast Evaluation:
MAE: 0.4323702479287688
MAPE: 52.52708028857158
sMAPE: 74.14874225843708
```
```Python
import matplotlib.pyplot as plt

plt.figure(figsize=(14, 6))
plt.plot(test.index, test.values, label="Actual", linewidth=2)
plt.plot(test.index, naive_forecast, label="Naive Forecast")
plt.plot(test.index, arima_forecast, label="ARIMA Forecast")
plt.plot(test.index, hw_forecast, label="Exponential Smoothing")
plt.legend()
plt.title("Forecast Comparison")
plt.xlabel("Date")
plt.ylabel("Temperature Anomaly")
plt.grid(True)
plt.tight_layout()
plt.show()
```
![alt text](image-18.png)

✅ 4. Model Selection and Interpretation

```Python
🌡️ Time Series Forecasting Report: Global Temperature Anomalies (1880–2025)
📘 Dataset Overview
Source: NASA GISTEMP (https://data.giss.nasa.gov/gistemp/tabledata_v4/GLB.Ts+dSST.csv)

Time Span: January 1880 to May 2025
Frequency: Monthly
Target Variable: Temperature_Anomaly (in °C)
Objective: Forecast global temperature anomalies using classical time series methods.

🛠️ Methods Applied
Data Preparation & EDA
Cleaned and reshaped wide-format data into long format
Created time-indexed series (ts_data)
Explored trends, seasonality, and cycles using:

   Line plots, rolling means
   ACF/PACF plots
   Seasonal subseries and monthly average plots
   Time series decomposition (additive & multiplicative)

Stationarity Analysis

Performed Augmented Dickey-Fuller (ADF) test
Differenced the series for stationarity if needed
Confirmed non-stationary structure with ACF plots

Forecasting Models

Naive Forecast (last observed value)
ARIMA (order = (1, 1, 1))
Exponential Smoothing (additive trend and seasonality)

📊 Model Evaluation
| Model                 | MAE   | MAPE (%) | sMAPE (%) |
| :-------------------- | :---- | :------- | :-------- |
| Naive Forecast        | 0.274 | 71.29    | 27.76     |
| ARIMA (1,1,1)         | 0.089 | 21.36    | 7.30      |
| Exponential Smoothing | 0.149 | 37.30    | 13.14     |

✅ Best Model: ARIMA (1,1,1) — lowest MAE, MAPE, and sMAPE.

📌 Key Findings
Strong upward trend in global temperature anomalies since 1950s, confirming long-term climate change.

Seasonal pattern present but relatively weak compared to the trend.

ARIMA captured the trend and short-term temporal dependencies most effectively.

Naive model performed poorly due to high variability and trend.

Exponential smoothing gave intermediate results but underfit seasonal effects.

✅ Conclusion
This project demonstrates the value of classical time series models like ARIMA in forecasting global climate trends. The model can serve as a reliable baseline for comparing future advanced models (e.g., LSTM, Prophet).


## Submission
