# Lecture 3


# HW : [hw3.ipynb](https://colab.research.google.com/drive/1k-TMPsBGngVXukrg61-SMqccBASI3Jtu)
# Python Data Cleaning: The MyAnimeList Challenge 📉
# 1\. The First Steps: Why Data Cleaning Matters
In finance, imagine receiving a spreadsheet of stock prices where some entries say "Pending" or "N/A" instead of a number. If you try to calculate the average price of your portfolio, your software will crash or give you a nonsensical result.
In this lesson, we are tackling a **MyAnimeList** dataset. Our goal is to find the **average (mean) score** for different types of anime (TV shows, Movies, etc.). However, the data is "dirty"—it contains text values where numbers should be. We need:
* to clean it, 
* to convert it, and then 
* to aggregate it.

# 2\. The Simile: The Music Festival Playlist 🎸 
Imagine you are organizing a massive music festival. You have a list of all performers, but the "Ticket Price" column is a mess:
* Some rows say $50.
* Some rows say Free.
* Some rows say TBD (To Be Determined).

⠀If you want to find the **average ticket price per genre** (Rock, Jazz, Pop):
1. **Filter:** You toss out the "TBD" rows because you can't do math with them.
2. **Convert:** You turn the text "$50" into the actual number 50.
3. **Group:** You put all the "Rock" bands in one pile and all the "Jazz" bands in another.
4. **Mean:** You calculate the average price for each pile.
5. **Sort:** You list the genres from the most expensive to the cheapest.

⠀
# 3\. Python Solution (Pandas Edition) 🐍
This is how we solve the homework using Python's pandas library VS Scala spark 
## Python
```python
import pandas as pd

def homework(anime_data_extracted: pd.DataFrame) -> pd.Series:
    # 1. Group by 'Type' and select the 'Score' column
    # 2. Calculate the mean for each group
    # 3. Sort the resulting values in descending order
    result = anime_data_extracted.groupby('Type')['Score'].mean().sort_values(ascending=False)
    
    return result
```


```python

import pandas as pd 

def homework(anime_data_extracted: pd.DataFrame) -> pd.Series:
    # 0. Convert 'Score' to numeric (Unknown -> NaN) base on the real results from the anime input data
    numeric_scores = pd.to_numeric(anime_data_extracted['Score'], errors='coerce') 
    
    # 1. Group by 'Type' column and calculate the mean
    # 2. Drop any NaN results using .dropna(), because in the hw instruction the display does not contain "NAN" row 
    # 3. Sort descending
    result = numeric_scores.groupby(anime_data_extracted['Type']).mean().dropna().sort_values(ascending=False)
    
    return result

```


```python
def homework(anime_data_extracted):
    # Group by 'Type', calculate the mean of 'Score', and sort descending
    result = anime_data_extracted.groupby('Type')['Score'].mean().sort_values(ascending=False)
    
    return result
```


# 4\. The "Rules": Python Basics 📖
To solve this, we used three core Python/Pandas concepts:
1. **groupby('Column')**: This acts like an "Accounting Ledger." It separates your data into groups based on unique values in a specific column (e.g., all "TV" entries together).
2. **.mean()**: A mathematical function that sums the values and divides by the count. ==🟣It ignores NaN (null) values automatically.==
3. **sort_values(ascending=False)**: This is your "Leaderboard" function. Setting ascending=False ensures **the highest scores appear at the top**.

⠀
# 5\. The Deep Dive: Aggregation Logic 🧠
The most complex part for beginners is understanding how data "shrinks" during aggregation.

### Python (Pandas)

```python
# The logic: Split -> Apply -> Combine
result = df.groupby('Type')['Score'].mean().sort_values(ascending=False)
```


### Scala (Spark SQL)
In Scala, we use the agg (aggregate) function. It is more explicit, which is helpful for complex financial reporting.

```scala
import org.apache.spark.sql.functions.{avg, col}

// Logic: Group by 'Type', calculate average of 'Score', then sort
val resultDF = anime_data_extracted
  .groupBy("Type")
  .agg(avg(col("Score")).as("AverageScore"))
  .orderBy(col("AverageScore").desc)

display(resultDF)
```

**Key Difference:** - 
* **Python** lets you call .mean() directly on the grouped object.
* **Scala (Spark)** prefers the .agg() pattern, which allows you to calculate multiple things at once (like Average and Total Volume) in a single pass.

# 6\. Smart Ways to Work (Best Practices) 💡
* ==🟣**Cast Early**==: Always ensure your "Score" column is a float/numeric type before grouping. You can't calculate the average of a string!
* **Handle Nulls**: Use coalesce in Scala or fillna in Python if you want to replace missing values with a default (like 0) instead of ignoring them.
* **Naming**: When aggregating in Scala, always use .as("new_name") so your final report has clear headers like "Average_Rating" instead of avg(Score).

⠀
# 7\. Real Life & Games 🕹️
* **Real Life (Banking):** A bank uses this exact logic to calculate the **Average Transaction Value** per **Merchant Category** (e.g., how much does the average customer spend at Grocery Stores vs. Gas Stations?).
* **RPG Games:** Calculating the **Average Damage** per **Weapon Type** (Swords vs. Bows) to balance the game's difficulty.

⠀
# 8\. When to Think Twice ⚠️
* **Small Sample Sizes:** If a "Type" only has one anime entry, the mean might be misleadingly high. In finance, we call this "Outlier Risk."
* **Performance:** On a Databricks cluster with billions of rows, sorting (orderBy) is an "expensive" operation because it requires moving data across different servers. Only sort at the very end of your pipeline!

⠀**Note:** For your homework submission, ensure you remove the !!WRITE ME!! and only include the logic inside the def homework() function!
































































#04 Study/BigData/Matsuo-Iwasawa-lab#