#  Chrome Data Offline Downloader Utilities 
Purpose of the utilities is to make the frequently used data from the **Chrome Data Server** to be available in the local database for faster access. 
The project contains a collection of python script files. The scripts are divided into following processes

- Scraper : This will scrap the chromeData API wrapper and save the data in json files. 
- Convertor : Reads the JSON,CSV files and create SQL Statements.
- Ingestor : Reads the sql scripts and execute on the mySQL DB. 
- Tagger: Group data based on makes and models. Read Tags from the CSV files, generate tags, get media images and generate SQL statement. 
- ElasticSearch data Generator: Generate Elastic search data on the grouped table for model index. 
- Validate Media Images: Validate if the media image URL's are still valid, if not set them as empty. 
- Fetch Missing Media Images: Gets the list of empty media URL's and fetches the latest media image url from the Chrome Media server. 


## Setup Steps before running the tools

- create a virtual environment. [How to create virtual environment](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)
- install the required packages mentioned in the requirements.txt file with the following command
- `pip install -r requirements.txt`
- Set the DB Connection in dbConnection.py file to point to the local database.

> **Note**: Make sure you connect to a new DB and not the main OneRequest DB.

## Scraper
Scraper will download all the JSON files for Make,Model and Trims and save on local machine. 

### Setup: 
- Run the oneRequest API project `vehicle-data-api`. Open the terminal and navigate inside the api folder. Type command
```command
    serverless offline
``` 

### How to run: 
- To run the scraper use the command `python mainScraper.py`
- The file `mainScraper.py` contains the calls to scrap Year, Make, Model, Styles (Trims)
- Most of the individual scraper calls are commented and kept to prevent accidental scraping of data. Uncomment the required method calls based on requirements.
In the `mainScraper.py` file uncomment the call to execute scraper for each entity

    - **Make Years :** `scrapMakeYears.fetch_make_years()`
    - **Make :** `scrapMakes.fetch_makes()`
    - **Model :** `scrapModels.fetch_models()`
    - **Trim/Styles :** `scrapStyles.fetch_styles()`. Scraping the all trim files takes more than 1 hour. There is a delay introduced between consecutive calls to avoid getting making continuous api calls. 
    The file `scrapStyles.py` can be modified to skip scraping for certain years, line no 108. In the array `scraped_years` add the list of years you want to skip scraping. 
    You would want to make this change if you want to scrap trims only for a specific year. Default value for this array is empty, to scrap for all years.
    While running the scraper for Trims, the scraper might crash. The most probale re

> **Note**: Each of the above entity should be scraped in sequential order, as the next entity depends on the previous entities for fetching the data. 



## Convertor
Convertor reads the JSON, CSV files and generates SQL insert statements.

### Setup: 
- make sure the scraper is run before running this step and all the JSON files are saved in `data/chrome-data` folder.
- make sure the files from the FTP server for past years are downloaded any kept in the respective folders 
    - StyleMap Files: `data/chrome-data/past-years/stylemaps/zip`
    - JPG Links Files : `data/chrome-data/past-years/jpgs/zip`
    
### How to run: 
- To run the Convertor use the command `python mainConvertor.py`
- Past Data:
    - Unzips all the files and puts them in ../csv/ folder
    - converts the CSV files into SQL insert statements
- Current Data: Make,Model,Year
    - Reads the JSON files and converts into SQL insert statements
- All the generated SQL statements are saved in `sql/` folder


## Ingestor
Ingestor reads all the SQL statements in the `sql/` folder and executes on the database. 

### Setup: 
- validate that the database connection is set in `connectToDB.py` file. Make sure that you create a new Db everytime you scrap, if you reuse the same DB then all the tables will be truncated before new data is inserted. 
If you need to avoid this, comment the line of code which does in truncation. 
- validate that the SQL files are present in the 'sql/' folder.

### How to run: 
- To run the Ingestor use the command `python mainIngestor.py`
- The Ingestor will execute the statements. The SQL script file contains only insert statements, there is no validation to check if duplicate data is there. So if you run the ingestor two times it will truncate the previously inserted data, before putting in new data. 
- Once the ingestion process is complete all the sql files will be moved into `archive/sql/` folder


## Tagger
Tagger will read the csv files from `data/tags`  and generate tags and associate them with appropriate make and model. 
It reads the contents of the database tables `chromeData_model` and `pastData_styles` groups them based on make and model, 
add the associated tags, add the list of all years the model is available as a comma seperated tags in model_years column. 
Links the media image url and stock photo url of the random trim from the model. It will then generate the SQL insert statements for the new table.


### Setup: 
- make sure the Ingestor Process is run and validate that the tables `chromeData_model` and `pastData_styles` have records. 

### How to run:
- To run the Tagger use the command `python mainTagger.py`
- The final SQL statements will be grouped based on its starting letter and saved in a file. Example `grouped_models-A.sql`
- The generated SQL files will be saved in `sql/tagged' folder
- The tagger process will automatically ingest the SQL statement once all the tags are generated.
- The tagger will then generate the elastic search script. 

> **Note:** The entire Tagging process can take a long time to run, hence it would be advisable to run the grouping separately for each alphabet. For tagger process to complete it takes more than 3hrs
 
 ### Running Tagger for each Alphabet or multiple to avoid scraper timeout errors
 - open the file `tagger.py`. In the function `generateSQLStmtsForAllModels()` add the alphabets you want to skip scraping for current session in the `scraped_make_characters` array.
 - Example: if i want to run the scraper only for alphabet "A" then make the below change in the code
 ```python
    scraped_make_characters = []#"A"
    scraped_make_characters.extend(["B","C","D","E","F","G","H","I","J","K","L","M", "N", "O","P", "R","S","T","V" ])
```
 - To run just the tagger use the command `python tagger.py`
 - Next if you want to run the scraper for only "B" and "C". and assuming that "A" is already scraped
  ```python
    scraped_make_characters = ["A"]#"B","C",
    scraped_make_characters.extend(["D","E","F","G","H","I","J","K","L","M", "N", "O","P", "R","S","T","V" ])
```
- Above steps are kind of hack, but can  avoid interruptions due to timeout error.
- open the `mainTagger.py` file, comment the code to invoke the tagger. i.e `tagger.main()` 
- run the command `python mainTagger.py`, this will ingest and create elastic search index files

## ElasticSearch Script Generator
This step is run automatically at the end of Tagger process. But can be also executed manually if any changes were in the `groupeddata_models` table.

### Setup: 
- make sure the table `groupeddata_models` has the records ingested, after the Tagger process. 

### How to run:
- open the `mainTagger.py` file, comment the code to invoke the tagger i.e `tagger.main()` 
- comment the code to ingest the tagged files
```python
    print('')
    print('--------START :: TAGGING DATA-------------')
    #tagger.main()
    print('--------END :: TAGGING DATA-------------')

    print('')
    print('--------START :: INSERTING DATA INTO DB-------------')
    # Execute the SQL Statements in the DB
    #mainIngestor.create_tables()
    #mainIngestor.createBaseFolders()
    #mainIngestor.ingest_tagged_models()
    print('--------END :: INSERTING DATA INTO DB-------------')
    print('')
    
    print('--------START :: GENERATING ELASTIC SEARCH FILES-------------')
    # Generate Elastic Search Files
    condensedDataToJson.main()
    print('--------END :: GENERATING ELASTIC SEARCH FILES-------------')
```

## validateMediaImageURL's 
This step will check if all the media URL's stored in the table `groupeddata_models` are still valid URL's. If they are invalid it will generate SQL statements to update the column to empty value. 

>**Note**: Execute the Generated script files manually on the database. There is no ingestion process for this step. 

## Fetch Missing Media Images
This step will check for any records with media url, which are empty or null. It will try to get the media URL from the media query. This will generate SQL update statements to update the column to the valid media URL.

>**Note**: Execute the Generated script files manually on the database. There is no ingestion process for this step. 

Next: Execute the step `ElasticSearch Script Generator` to get the latest Elastic Search index files. 


