# **Exploring Airbnb Market Trends**

## **Project Description**

Welcome to New York City, one of the most-visited cities in the world. There are many Airbnb listings in New York City to meet the high demand for temporary lodging for travelers, which can be anywhere between a few nights to many months. In this project, we will take a closer look at the New York Airbnb market by combining data from multiple file types like `.csv`, `.tsv`, and `.xlsx`.

Recall that **CSV**, **TSV**, and **Excel** files are three common formats for storing data. 
Three files containing data on 2019 Airbnb listings are available to you:

**data/airbnb_price.csv**
This is a CSV file containing data on Airbnb listing prices and locations.
- **`listing_id`**: unique identifier of listing
- **`price`**: nightly listing price in USD
- **`nbhood_full`**: name of borough and neighborhood where listing is located

**data/airbnb_room_type.xlsx**
This is an Excel file containing data on Airbnb listing descriptions and room types.
- **`listing_id`**: unique identifier of listing
- **`description`**: listing description
- **`room_type`**: Airbnb has three types of rooms: shared rooms, private rooms, and entire homes/apartments

**data/airbnb_last_review.tsv**
This is a TSV file containing data on Airbnb host names and review dates.
- **`listing_id`**: unique identifier of listing
- **`host_name`**: name of listing host
- **`last_review`**: date when the listing was last reviewed

```py
# Import necessary packages
import pandas as pd
import numpy as np
```

```py
# Load data into dataframes.
airbnb_price = pd.read_csv("../data/airbnb_price.csv", sep=",")
airbnb_room_type = pd.read_excel("../data/airbnb_room_type.xlsx")
airbnb_last_review = pd.read_csv("../data/airbnb_last_review.tsv", sep="\t")

print("Airbnb Price \n", airbnb_price.head())
print("Airbnb Room Type \n", airbnb_room_type.head())
print("Airbnb Last Review \n", airbnb_last_review.head())
```

## Merging the DataFrames to one single DataFrame

We merge the cells so that the listing can be analyzed together, thus making it more efficient and easier to process any analysis for these datasets.

```py
airbnb_listings = pd.merge(airbnb_price, airbnb_room_type, on="listing_id")
airbnb_listings = pd.merge(
    airbnb_listings, airbnb_last_review, on="listing_id")

airbnb_listings.head()
```

## Exploratory Data Analysis

```py
# Doing an EDA to understand about the dataset.
airbnb_listings.info()
```

Based on our DataFrame information, we notice that we need to change some datatypes of some columns. 

We're changing the following: 

* `price` as integer datatype.
* `room_type` to be lowercase to be consistent
* `last_review` as datetime datatype.

```py
# Update price to decimal format. Eg. 22.00
airbnb_listings["price"] = airbnb_listings["price"].str.replace(" dollars", "").astype(int)

# Update room_type to be lowercase.
airbnb_listings["room_type"] = airbnb_listings["room_type"].str.lower()

# Update last_review to date format.
airbnb_listings["last_review"] = pd.to_datetime(
    airbnb_listings['last_review'], format='%B %d %Y')
```
```py
# Find the top 10 listing with smallest listing price.
lowest_price_listing = airbnb_listings.nsmallest(
    10, "price")[["listing_id", "price"]]

# Find the top 10 listing with highest listing price.
highest_price_listing = airbnb_listings.nlargest(
    10, "price")[["listing_id", "price"]]

print("Top 10 Lowest Prices: \n", lowest_price_listing)
print("\nTop 10 Highest Prices: \n", highest_price_listing)
```

As per this listing, we can see there are some listing with 0.0 price, depicting that those are free or the price wasn't captured correctly. As for the highest price listing, we have to confirm, whether those are outliers or not.

But for this analysis, since those are not the criteria, we're going to skip those. But on an actual analysis, we would dig deeper into those.

```py
# View the dataset information again.
airbnb_listings.info()
```

```py
# View the statistical analysis for price
airbnb_listings.describe()
```

```py
# View the updated DataFrame.
airbnb_listings.head()
```

### **1. Finding the Earliest and Most Recent Reviews Dates**

Task: What are the dates of the earliest and most recent reviews? Store these values as two separate variables with your preferred names.

```py
first_reviewed = airbnb_listings["last_review"].min()
last_reviewed = airbnb_listings["last_review"].max()

print(f"The earliest and most recent reviews were done on {first_reviewed.strftime('%b %d %Y')} and {last_reviewed.strftime('%b %d %Y')}.")
```

The earliest and most recent reviews were done on Jan 01 2019 and Jul 09 2019.

### **2. Determining the count of private rooms**

Task: How many of the listings are private rooms? Save this into any variable.

```py
nb_private_rooms = (
    airbnb_listings["room_type"].str.lower() == "private room").sum()

print(f"Out of all the listings, {nb_private_rooms} are private rooms.")
```

Out of all the listings, 11356 are private rooms.

### **3. Determining the average listing price**

Task: What is the average listing price? Round to the nearest two decimal places and save into a variable.

```py
avg_price = airbnb_listings["price"].mean()

print(f"The average listing price is {'{:.2f}'.format(avg_price)}")
```

The average listing price is 141.78

### **4. Creating a new DataFrame with combined data**
    
Task: Combine the new variables into one DataFrame called `review_dates` with four columns in the following order: `first_reviewed`, `last_reviewed`, `nb_private_rooms`, and `avg_price`. The DataFrame should only contain one row of values.

```py
review_dates = pd.DataFrame(
    {
        "first_reviewed": [first_reviewed],
        "last_reviewed": [last_reviewed],
        "nb_private_rooms": [nb_private_rooms],
        "avg_price": [round(avg_price, 2)]
    }
)

review_dates
```

| first_reviewed | last_reviewed | nb_private_rooms | avg_price |
|----------------|---------------|------------------|-----------|
| 2019-01-01     | 2019-07-09    | 11356            | 141.78    |
