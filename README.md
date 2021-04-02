# Extract, Transform, Load -- Movie Data

## Overview

### Purpose

The purpose of this project is to demonstrate the ETL cycle for data using the medium of movies to do so. ETL, which stands for Extract, Transform, Load, refers to how the data gets dealt with: It's extracted from a file, usually either a JSON or CSV, transformed in order to better read the data and analyze the important info within, and loaded into a database where it can be compared with other data. This project in particular focuses on data pulled from Wikipedia and Kaggle, cleaned up to be legible and without bad data, merged into a couple of tables, and loaded into a SQL database.

## Process

### Extracting the Data

The first part of the ETL process is Extraction. As in, we have preliminary data we need to move from files into our development environmen so that we can perform transformation on it. To do this, it's simple enough to load the data from CSVs into pandas DataFrames using `pd.read_csv()`, and the process for extracting the information from the JSON is only slightly more convoluted:
```
# 3. Open the read the Wikipedia data JSON file.
    with open(wiki_data, mode='r') as file:
        wiki_movies_raw = json.load(file)
    # 4. Read in the raw wiki movie data as a Pandas DataFrame.
    wiki_movies_df = pd.DataFrame(wiki_movies_raw)
```

All of this is basic knowledge, however, and stuff we've already done before. If, for example, we wanted to make this code work for any three pieces of information, we can define a function that takes in three path strings that will lead to our inputs: one JSON and two CSVs. This is helpful if, for example, we want to perform the same analysis on similar data from a different source, and want DataFrames as output. However, there's more we could do with this code; we've only covered the E of ETL, after all.

### Cleaning Data

After the extraction process is complete, next comes Transformation, the step where we change the form of the data to what we need it to be.

![image](https://raw.githubusercontent.com/SirNancyTheNegative/ETL-Movies/main/Resources/Kaggle_data_gone_wrong.png)

...Okay, we can't do the Transformation step next, as we have to clean our data. Bad data can come from all kinds of errors, but the most likely is that some piece of data somewhere went missing in the retrieval process, and it tried to continue anyways. In order to fix this, we need to remove any rows affected by this. Thankfully, our easiest bet is to make use of the "adult" column of the data. Since, for the purposes of this example, we don't need any adult movies, we can remove any movies in the list that don't come up as "False" under the "adult" column. We also don't need the "adult" column beyond this action, so in the same line we can remove both the "adult" column and the bad data in a single line:
```
kaggle_metadata = kaggle_metadata[kaggle_metadata['adult'] == 'False'].drop('adult',axis='columns')
```

Similarly, we can format and drop columns to whittle down what we don't need in order to focus on what we want, and have it be able to be used in calculations. For example, the 'video', 'budget', 'id', 'popularity' and 'release date' columns all have incorrect data types. To remedy this, we write the following code:
```
    kaggle_metadata['video'] = kaggle_metadata['video'] == 'True'
    kaggle_metadata['budget'] = kaggle_metadata['budget'].astype(int)
    kaggle_metadata['id'] = pd.to_numeric(kaggle_metadata['id'], errors='raise')
    kaggle_metadata['popularity'] = pd.to_numeric(kaggle_metadata['popularity'], errors='raise')
    kaggle_metadata['release_date'] = pd.to_datetime(kaggle_metadata['release_date'])
```
Since just about everything was read in as a string, it's important to make sure that any kind of analysis we could do to this data can be done. Even though we're only looking to compile movie data, whatever this data will be used for in the future

### Transforming the Data



### Loading the Data

