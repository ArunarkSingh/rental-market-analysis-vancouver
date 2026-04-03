\# Vancouver Rental Market Analysis



Scraped and analyzed 342 live rental listings from Craigslist Vancouver to answer three questions:

1\. Which neighbourhoods offer the best value relative to their features?

2\. Does proximity to SkyTrain meaningfully affect rent?

3\. What factors drive rental prices most?



\*\*Data collected:\*\* April 2, 2026 · 342 listings across 8 Metro Vancouver neighbourhoods



\---



\## Key Findings



\- \*\*Neighbourhood matters more than transit.\*\* Coquitlam ($2,898/mo median) is 49% more expensive than North Vancouver ($1,950/mo) despite similar SkyTrain access — suggesting neighbourhood brand and demand drive prices more than infrastructure proximity.



\- \*\*SkyTrain proximity premium is surprisingly weak.\*\* Listings within 1km of a station averaged $2,611/mo vs $2,578/mo for listings over 2km away — a difference of just $33. The linear model estimated a slope of -$9/km, which is not economically meaningful at this sample size.



\- \*\*New Westminster and North Vancouver are the most underpriced\*\* relative to their features (bedroom count, transit distance, neighbourhood). New Westminster listings averaged $453 below model predictions — the largest underpricing gap in the dataset.



\- \*\*Port Moody is the most overpriced\*\* at +$241 above predicted, followed by Surrey (+$101). Both are farther from SkyTrain on average but command above-expected rents, likely driven by newer stock and amenities.



\- \*\*Bedroom count has a modest effect.\*\* 1BR averaged $2,476/mo, 2BR $2,595, 3BR $2,798 — a $322 step from 1BR to 3BR. The SHAP analysis confirmed skytrain\_km as the highest-importance feature in the model, followed by neighbourhood and bedrooms.



\---



\## Charts



\### Rent Distribution

!\[](outputs/00\_price\_distribution.png)

Median rent is $2,300/mo. The distribution is right-skewed with a long tail above $6,000 — mostly luxury listings. Log-transforming shows the bulk of listings cluster between $1,800–$3,500.



\### Median Rent by Neighbourhood

!\[](outputs/01\_rent\_by\_neighbourhood.png)



\### SkyTrain Proximity vs Rent

!\[](outputs/02\_skytrain\_vs\_rent.png)

The weak negative slope (-$9/km) and minimal bracket differences suggest SkyTrain proximity is not a primary price driver in this market at current sample size.



\### Rent by Bedroom Count

!\[](outputs/03\_rent\_by\_bedrooms.png)



\### Feature Importance (SHAP)

!\[](outputs/04\_shap\_importance.png)

SHAP values from a HistGradientBoosting model trained on bedrooms, skytrain\_km, and neighbourhood. Distance to SkyTrain contributed the most to model output magnitude, but overall model fit was weak (R² = -0.11) due to limited features — sqft was unavailable in the source data.



\### Over/Underpriced Neighbourhoods

!\[](outputs/05\_over\_underpriced.png)

Residuals (actual − predicted rent) per neighbourhood. Green = underpriced relative to features, orange = overpriced.



\### SQL Analysis

!\[](outputs/07\_sql\_analysis.png)



\### Feature Correlation Matrix

!\[](outputs/08\_correlation\_heatmap.png)

Bedrooms and bathrooms are strongly correlated (0.73), as expected. Price shows weak correlations with all individual features, consistent with the model's low R².



\---



\## SQL Analysis



Four analytical queries run on the cleaned dataset loaded into SQLite:



| Query | Finding |

|---|---|

| Avg rent by neighbourhood | Port Moody ($3,118) and Surrey ($2,908) top the list; North Vancouver ($2,206) is cheapest |

| Rent by bedroom count | 1BR: $2,476 · 2BR: $2,595 · 3BR: $2,798 |

| SkyTrain proximity premium | Within 1km: $2,611 · 1-2km: $2,512 · Over 2km: $2,578 |

| Best value neighbourhoods | North Vancouver and Burnaby offer lowest avg rent with reasonable transit access |



\---



\## Model



A `HistGradientBoostingRegressor` was trained on bedrooms, SkyTrain distance, and neighbourhood (label-encoded) to predict monthly rent. SHAP explainability was used to rank feature contributions.



| Metric | Value |

|---|---|

| MAE | $784 |

| R² | -0.11 |



The low R² reflects missing features (sqft unavailable from source), small sample size (342 listings), and high price variance within neighbourhoods. The model is included for feature importance analysis rather than production prediction.



\---



\## Data



\- \*\*Source:\*\* Craigslist Vancouver `/search/apa` — scraped via `requests` + `BeautifulSoup`, structured data extracted from JSON-LD (`ld\_searchpage\_results`)

\- \*\*Fields:\*\* title, price, bedrooms, bathrooms, lat, lon, neighbourhood

\- \*\*Cleaning:\*\* removed listings outside $500–$15,000/mo, deduplicated on title + coordinates

\- \*\*Limitation:\*\* sqft not available in JSON-LD; postal codes not populated by source



\---



\## Tech Stack



`Python` · `pandas` · `NumPy` · `scikit-learn` · `SHAP` · `Folium` · `Seaborn` · `SQLite` · `BeautifulSoup` · `Matplotlib`



\---



\## How to Run



1\. Open `VanRentMarketAnalysis.ipynb` in Google Colab

2\. Mount Google Drive and `cd` into your project folder

3\. Run cells top to bottom — scraping takes \~30 seconds, full notebook \~3 minutes



\---



\## Limitations \& Next Steps



\- Sample is a single-day snapshot — longitudinal scraping would reveal price trends over time

\- sqft unavailable from JSON-LD; adding it would likely improve model fit significantly

\- Craigslist listings skew toward individual landlords — purpose-built rental buildings are underrepresented

\- Next: automate weekly scraping to track price changes by neighbourhood

