# Predicting Neighborhood Affluence Using Yelp's Price Estimates

By: Vanessa Alvarado, Leland Roberts, Danielle Mizrachi, Jessica Ertel

## Problem Statement
The organization [New Light Technologies](https://www.linkedin.com/company/new-light-technologies/) (NLT) provides solutions to government, commercial, and non-profit clients. NLT is a team of dedicated technologists, scientists, engineers, designers, and strategists working on some of the most interesting, challenging, and important assignments in the world, ranging from disaster response to enabling growing telecommunications networks to providing healthcare to Americans.  

New Light Technologies was contacted by The Wildflower Cafe, a high end restaurant looking to strategically expand their business to new areas of Connecticut. In order to identify the best location, they're interested in obtaining an estimate of affluence based on the existing number and type of restaurants across different towns and cities. To tackle this request, NLT is utilizing big data related to commercial activity and cost of product and services as an indicator for affluence.  

[Yelp](https://www.yelp.com/) is a business directory and crowd-sourced review platform that ranks businesses on a 4-point pricing scale ($, $$, $$$, $$$$). The NLT team decided to adopt a unique approach and use Yelp cost estimates to determine the affluence of particular cities and towns in Connecticut. Our success can be validated if we are able to answer the following:  
1. Can Yelp price estimates for restaurants in Connecticut accurately predict median household income, mean household income, per capita income or adjusted gross income?
2. Can we confidently advise The Wildflower Cafe which city to expand their business?

## Table of Contents
1. Data Gathering
    1. [Yelp](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/code/01_Yelp_Restaurant_Data_Gathering.ipynb)
2. Data Cleaning
    1. [Yelp](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/code/02_Yelp_Restaurant_Data_Cleaning.ipynb)
    2. [IRS](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/code/03_IRS_Data_Cleaning.ipynb)
    3. [CT Gov](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/code/04_CT_Income_Data_Cleaning.ipynb)
3. [Combine Data](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/code/05_Combining_Data.ipynb)
4. [Exploratory Data Analysis](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/code/06_EDA.ipynb)
5. [Modeling](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/code/07_Modeling.ipynb)

### Data Dictionary
|Feature|Type|Description|
|---|---|---|
|zipcode|object|5-digit zip code for cities and towns in Connecticut|
|income_bucket|int|Size of adjusted gross income, binned into six categories*|
|n_returns|int|Number of returns filed during the 12-month period of January 1, 2018 to December 31, 2018|
|agi|int|Adjusted gross income|
|total_income|int|Total income amount|
|city|object|City in Connecticut|
|price|float|Average price category of restaurants in each city|
|num_restaurants|float|The number of restaurants in each city (from Yelp data)|
|y|float|Weighted average of each IRS income category weighted by the number of returns in each city|
|1|float|Percent of restaurants in a given city that fall into the \$ price category|
|2|float|Percent of restaurants in a given city that fall into the \$\$ price category|
|3|float|Percent of restaurants in a given city that fall into the \$\$\$ price category|
|4|float|Percent of restaurants in a given city that fall into the \$\$\$\$ price category|
|Median Household Income|int|Median household income as defined by CT gov|
|Mean Household Income|int|Mean household income as defined by CT gov|
|Per Capita Income|int|Per capita income as defined by CT gov|
 
*Binned adjusted income categories (per IRS determinations) <br>
1 = $1 under $25,000 <br> 
2 = $25,000 under $50,000  
3 = $50,000 under $75,000 <br>
4 = $75,000 under $100,000 <br>
5 = $100,000 under $200,000 <br> 
6 = $200,000 or more <br>  

### Data Files
- [combined_1.csv - first set of features with IRS data](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/data/combined_1.csv) 
- [combined_2.csv - second set of features with IRS data](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/data/combined_2.csv) 
- [combined_3.csv - first set of features with CT Gov data](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/data/combined_3.csv)
- [combined_4.csv - second set of features with CT Gov data](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/data/combined_4.csv)

## Executive Summary
We cleaned and analyzed the three datasets then modeled using the following process:

### Data Acquisition
**The Internal Revenue Service (IRS)** produces [state income tax statistics](https://www.irs.gov/statistics/soi-tax-stats-individual-income-tax-statistics-2017-zip-code-data-soi) by zip code. We used the most recent data available from Connecticut from the year 2017. See the [IRS documentation](https://git.generalassemb.ly/danielle-mizrachi/yelp/blob/master/irs_data_dictionary.docx) for more details.  

**The Connecticut Department of Economic and Community Development** produces [housing and income data](https://portal.ct.gov/DECD/Content/About_DECD/Research-and-Publications/01_Access-Research/Exports-and-Housing-and-Income-Data) on Connecticut, including per capita income, median household income and median family income at the state, county and town level. The most recent data available is from the year 2015.  

**Yelp** data was gathered using the [Yelp API](https://www.yelp.com/developers). We developed a function that allowed us to hit the daily limit of 5,000 API calls per 24 hours and gathered a data on restaurants within each of Connecticut's zipcodes. This did result in the gathering of duplicate locales, but after dropping duplicates we were left with a total of 3,729 rows.

### Data Cleaning
**The Internal Revenue Service (IRS)** 
- Convert numerical columns from string to float datatype
- Convert zip codes to string and ensure all have 6 digits
- Map numerical values to gross income bins as designated by IRS
- Drop rows for zip code totals
- Add a column that identifies city based on zip code
- Drop rows with zip code 00000 and 99999

**The Connecticut Department of Economic and Community Development**   
- Remove commas from numeric values
- Bucket median and mean household income (using same categories as IRS dataset)

**Yelp** 
- Drop duplicates
- Map price values to numeric values (1-4)
- Update incorrectly formatted city names to match IRS/CT Gov data
- Aggregated restaurants by city

### Feature Engineering
 We came up with two separate sets of features to use in our model:
1. Average price category of restaurants in each city
2. We calculated a normalized count per category per city.
We combined the engineered features with the IRS and CT Gov data, resulting in four datasets for modeling. We also had to engineer our predicted value, which involved creating a weighted average of each IRS income category weighted by the number of returns in each city.

### Modeling
Our baseline model had a high score of 78% with very imbalanced classes. When the weighted averages were calculated, the highest (over \$200,000) and lowest (under \$25,000) income classes were not represented. We Gridsearched and fit a Logistic Regression model. Our best estimator had a train score of 77.7% and test score of 79.5%. Our model predicted income class 3 (the majority class) for every city.

Our next approach involved using both regression and classification models on the CT Gov dataset. This dataset also had imbalanced classes and therefore did not result in better scores. As a result we decided to use linear regression models using per capita income and achieved our best performing model with a train r-squared score of .19 and test score of .16. 

## Conclusions and Limitations
The biggest limitation we faced was the size of our dataset. We were unable to pull additional data from Yelp because of their API limit and we chose to aggregate restaurants by city, leaving us with only 160 observations to work with. This is not nearly enough data! 

The number of zip codes we gathered from each source varied. Yelp collects restuarants from more zip codes than the IRS or CT Gov sources contained because it sorts towns into subsections. We were forced to drop this added level of specificity from the analysis when modeling with the CT Gov data, which did not provide data on the same number of zipcodes. Additionally, when scraping data from Yelp by zipcode, the API pulled restaurants within a certain radius of that zipcode. This resulted in gathering many duplicates and uncertainty around completeness, especially since the API capped at 50. The reason for the lack of data was not due to the API limit, but rather due to the limited amount of cities in Connecticut that we could include in our analysis. Therefore, we cannot confidently advise on where The Wildflower Cafe should expand their business. 

_How does Yelp define their pricing scale and is this consistent across different parts of the U.S.?_    
Evaluating the distribution of median household income in light of the poor modeling results led us to some fundamental questions: Is the price range for each category consistent across different regions? How and who actually classifies restaurant prices? In other words, does $$$ mean the same in a low and high income city? The [answer is astounding](https://www.quora.com/How-are-dollar-signs-calculated-on-Yelp-and-who-calculates-them/answer/Terry-Lambert)! The dollar sign classifications are defined by customers themselves, as part of a survey that is collected when a customer posts a review of the restaurant. The fact that this information is crowd sourced and somewhat subjective is interesting, yet problematic for our investigation. Using Yelp pricing scales might be a better indication of how residents of a certain city rate the cost of product and services in terms of their personal affluence. From this analysis, it is not an accurate predictor of median household income, mean household income, per capita income or adjusted gross income.

## Future Work
For future work, we would gather additional data from different sources and further investigate the data that is available by the IRS. We would expand our search radius to inlcude the North East region, East Coast, or potentially include the U.S. in its entirety. To gather that data we would utilize the U.S Census API to aggregate median, mean and per-capita income. Once we have that data in hand we would have to increase our radius to attain a larger dataset of Yelp data. Having a more robust dataset would allow us to do more complex modelling.

*This project was completed through General Assembly's Data Science Immersive Program.*