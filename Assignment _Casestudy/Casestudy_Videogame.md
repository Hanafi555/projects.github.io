# Video Game Sales Analysis

## Dataset

You will be working with the following dataset: [Video Game Sales](https://www.kaggle.com/datasets/gregorut/videogamesales?resource=download)

📦 **Dataset Download Instructions**
1. Download the dataset ZIP file from the above link.
2. After downloading: Unzip the file to access vgsales.csv. Note the full file path to vgsales.csv — you'll need it in the next step.

🔍 **Challenge: Load the Data into DuckDB**
Using DBeaver and your DuckDB connection, how would you load the vgsales.csv file into a table so you can begin querying it?

## Business Question
How can game developers and publishers optimize their strategy to maximize global sales by understanding the performance of different game genres, platforms, and publishers?

*To answer the above question, use the following SQL queries to explore the dataset and address the following questions:*

Which genres contribute the most to global sales?

SQL:
```sql
SELECT
   Genre,
SUM(Global_Sales) AS Total_Global_Sales
FROM VGSALES
GROUP BY Genre
ORDER BY Total_Global_Sales DESC;
```
| #   | Genre          | Total_Global_Sales |
|-|-|-|
| 1   | "Action"       | "1,751.18"         |
| 2   | "Sports"       | "1,330.93"         |
| 3   | "Shooter"      | "1,037.37"         |
| 4   | "Role-Playing" | "927.37"           |
| 5   | "Platform"     | "831.37"           |
| 6   | "Misc"         | "809.96"           |
| 7   | "Racing"       | "732.04"           |
| 8   | "Fighting"     | "448.91"           |
| 9   | "Simulation"   | "392.2"            |
| 10  | "Puzzle"       | "244.95"           |
| 11  | "Adventure"    | "239.04"           |
| 12  | "Strategy"     | "175.12"           |
```
Findings:
```findings
Action games dominate in global sales compared to other genres. From the table, Action games have the highest total global sales, amounting to 1,751.18B.
```
Which platforms generate the highest global sales?

SQL:
```sql
SELECT
   Platform,
SUM(Global_Sales) AS Total_Global_Sales
FROM VGSALES
GROUP BY Platform
ORDER BY Total_Global_Sales DESC;
```
| #   | Platform | Total_Global_Sales |
|-|-|-|
| 1   | "PS2"    | "1,255.64"         |
| 2   | "X360"   | "979.96"           |
| 3   | "PS3"    | "957.84"           |
| 4   | "Wii"    | "926.71"           |
| 5   | "DS"     | "822.49"           |
| 6   | "PS"     | "730.66"           |
| 7   | "GBA"    | "318.5"            |
| 8   | "PSP"    | "296.28"           |
| 9   | "PS4"    | "278.1"            |
| 10  | "PC"     | "258.82"           |
| 11  | "XB"     | "258.26"           |
| 12  | "GB"     | "255.45"           |
| 13  | "NES"    | "251.07"           |
| 14  | "3DS"    | "247.46"           |
| 15  | "N64"    | "218.88"           |
| 16  | "SNES"   | "200.05"           |
| 17  | "GC"     | "199.36"           |
| 18  | "XOne"   | "141.06"           |
| 19  | "2600"   | "97.08"            |
| 20  | "WiiU"   | "81.86"            |
| 21  | "PSV"    | "61.93"            |
| 22  | "SAT"    | "33.59"            |
| 23  | "GEN"    | "28.36"            |
| 24  | "DC"     | "15.97"            |
| 25  | "SCD"    | "1.87"             |
| 26  | "NG"     | "1.44"             |
| 27  | "WS"     | "1.42"             |
| 28  | "TG16"   | "0.16"             |
| 29  | "3DO"    | "0.1"              |
| 30  | "GG"     | "0.04"             |
| 31  | "PCFX"   | "0.03"             |
```
Findings:
```findings
From the table, PlayStation 2 (PS2) stands out as the top-performing platform, with total global sales reaching 1,255.64B.
```
Which publishers are the most successful in terms of global sales?

SELECT
   Publisher,
SUM(Global_Sales) AS Total_Global_Sales
FROM VGSALES
GROUP BY Publisher
ORDER BY Total_Global_Sales DESC
LIMIT 10;

| #   | Publisher                      | Total_Global_Sales |
| :-- | :----------------------------- | :----------------- |
| 1   | "Nintendo"                     | "1,786.56"         |
| 2   | "Electronic Arts"              | "1,110.32"         |
| 3   | "Activision"                   | "727.46"           |
| 4   | "Sony Computer Entertainment"  | "607.5"            |
| 5   | "Ubisoft"                      | "474.72"           |
| 6   | "Take-Two Interactive"         | "399.54"           |
| 7   | "THQ"                          | "340.77"           |
| 8   | "Konami Digital Entertainment" | "283.64"           |
| 9   | "Sega"                         | "272.99"           |
| 10  | "Namco Bandai Games"           | "254.09"           |

How does success vary across regions (North America, Europe, Japan, Others)?

SQL:
```sql
SELECT
SUM(NA_Sales) AS Total_NA_Sales,
SUM(EU_Sales) AS Total_EU_Sales,
SUM(JP_Sales) AS Total_JP_Sales,
SUM(Other_Sales) AS Total_Other_Sales
FROM VGSALES;
```
| #   | Total_NA_Sales     | Total_EU_Sales     | Total_JP_Sales     | Total_Other_Sales |
|-|-|-|-|-|
| 1   | "4,392.9500000003" | "2,434.1300000006" | "1,291.0199999999" | "797.7499999999"  |
```
Findings:
```findings
North America leads in game sales with 4,392.95B, making it the most lucrative market, while Europe follows with 2,434.13B, showing strong demand but lower than NA. Japan has 1,291.02B in sales, whereas Other regions contribute the least at 
797.75B in total sales. 
```
What are the trends over time in game sales by genre and platform?

SQL:
```sql
 WITH CTE AS (
    SELECT Year, Genre, SUM(Global_Sales) AS Total_Global_Sales
    FROM vgsales
    WHERE Year IS NOT NULL
    GROUP BY Genre, Year
)
SELECT
    CTE.Year,
    CTE.Genre,
    CTE.Total_Global_Sales
FROM CTE
JOIN (
    SELECT Year, MAX(Total_Global_Sales) AS Highest_Sales
    FROM CTE
    GROUP BY Year
) HIGHEST
    ON CTE.Year = HIGHEST.Year
    AND CTE.Total_Global_Sales = HIGHEST.Highest_Sales
ORDER BY CTE.Year
```
| #   | Year   | Genre          | Total_Global_Sales |
|-|-|-|-|
| 1   | "1980" | "Shooter"      | "7.07"             |
| 2   | "1981" | "Action"       | "14.84"            |
| 3   | "1982" | "Puzzle"       | "10.03"            |
| 4   | "1983" | "Platform"     | "6.93"             |
| 5   | "1984" | "Shooter"      | "31.1"             |
| 6   | "1985" | "Platform"     | "43.17"            |
| 7   | "1986" | "Action"       | "13.74"            |
| 8   | "1987" | "Fighting"     | "5.42"             |
| 9   | "1988" | "Platform"     | "27.73"            |
| 10  | "1989" | "Puzzle"       | "37.75"            |
| 11  | "1990" | "Platform"     | "22.97"            |
| 12  | "1991" | "Platform"     | "7.64"             |
| 13  | "1992" | "Fighting"     | "15.25"            |
| 14  | "1993" | "Platform"     | "18.67"            |
| 15  | "1994" | "Platform"     | "28.74"            |
| 16  | "1995" | "Platform"     | "16.69"            |
| 17  | "1996" | "Role-Playing" | "43.96"            |
| 18  | "1997" | "Racing"       | "31.91"            |
| 19  | "1998" | "Sports"       | "41.79"            |
| 20  | "1999" | "Role-Playing" | "49.09"            |
| 21  | "2000" | "Sports"       | "41.19"            |
| 22  | "2001" | "Action"       | "59.39"            |
| 23  | "2002" | "Action"       | "86.77"            |
| 24  | "2003" | "Action"       | "67.93"            |
| 25  | "2004" | "Action"       | "76.26"            |
| 26  | "2005" | "Action"       | "85.69"            |
| 27  | "2006" | "Sports"       | "136.16"           |
| 28  | "2007" | "Action"       | "106.5"            |
| 29  | "2008" | "Action"       | "136.39"           |
| 30  | "2009" | "Action"       | "139.36"           |
| 31  | "2010" | "Action"       | "117.64"           |
| 32  | "2011" | "Action"       | "118.96"           |
| 33  | "2012" | "Action"       | "122.04"           |
| 34  | "2013" | "Action"       | "125.22"           |
| 35  | "2014" | "Action"       | "99.02"            |
| 36  | "2015" | "Action"       | "70.7"             |
| 37  | "2016" | "Action"       | "19.91"            |
| 38  | "2017" | "Role-Playing" | "0.04"             |
| 39  | "2020" | "Simulation"   | "0.29"             |
| 40  | "N/A"  | "Action"       | "28.3"             |

WITH CTE AS (
    SELECT Year, Platform, SUM(Global_Sales) AS Total_Global_Sales
    FROM vgsales
    WHERE Year IS NOT NULL
    GROUP BY Platform, Year
)
SELECT
    C.Year,
    C.Platform,
    C.Total_Global_Sales
FROM CTE C
JOIN (
    SELECT Year, MAX(Total_Global_Sales) AS Highest_Sales
    FROM CTE C
    GROUP BY Year
) HIGHEST
    ON C.Year = HIGHEST.Year
    AND C.Total_Global_Sales = HIGHEST.Highest_Sales
ORDER BY C.Year

```
| #   | Year   | Platform | Total_Global_Sales |
|-|-|-|-|
| 1   | "1980" | "2600"   | "11.38"            |
| 2   | "1981" | "2600"   | "35.77"            |
| 3   | "1982" | "2600"   | "28.86"            |
| 4   | "1983" | "NES"    | "10.96"            |
| 5   | "1984" | "NES"    | "50.09"            |
| 6   | "1985" | "NES"    | "53.44"            |
| 7   | "1986" | "NES"    | "36.41"            |
| 8   | "1987" | "NES"    | "19.76"            |
| 9   | "1988" | "NES"    | "45.01"            |
| 10  | "1989" | "GB"     | "64.98"            |
| 11  | "1990" | "SNES"   | "26.16"            |
| 12  | "1991" | "SNES"   | "16.21"            |
| 13  | "1992" | "SNES"   | "32.98"            |
| 14  | "1993" | "SNES"   | "40.01"            |
| 15  | "1994" | "SNES"   | "35.08"            |
| 16  | "1995" | "PS"     | "35.92"            |
| 17  | "1996" | "PS"     | "94.68"            |
| 18  | "1997" | "PS"     | "136.08"           |
| 19  | "1998" | "PS"     | "169.58"           |
| 20  | "1999" | "PS"     | "144.57"           |
| 21  | "2000" | "PS"     | "96.28"            |
| 22  | "2001" | "PS2"    | "166.43"           |
| 23  | "2002" | "PS2"    | "205.4"            |
| 24  | "2003" | "PS2"    | "184.29"           |
| 25  | "2004" | "PS2"    | "211.78"           |
| 26  | "2005" | "PS2"    | "160.65"           |
| 27  | "2006" | "Wii"    | "137.91"           |
| 28  | "2007" | "Wii"    | "154.97"           |
| 29  | "2008" | "Wii"    | "174.16"           |
| 30  | "2009" | "Wii"    | "210.44"           |
| 31  | "2010" | "X360"   | "171.05"           |
| 32  | "2011" | "PS3"    | "159.37"           |
| 33  | "2012" | "PS3"    | "109.49"           |
| 34  | "2013" | "PS3"    | "117.39"           |
| 35  | "2014" | "PS4"    | "98.76"            |
| 36  | "2015" | "PS4"    | "115.3"            |
| 37  | "2016" | "PS4"    | "39.25"            |
| 38  | "2017" | "PS4"    | "0.03"             |
| 39  | "2020" | "DS"     | "0.29"             |
| 40  | "N/A"  | "PS2"    | "22.18"            |


Findings:
```findings
For Genre:

Action has been the dominant genre for over a decade, peaking in 2009 with 139.36M global sales, while Sports and Role-Playing had strong peaks in 2006 and 1999, respectively. Earlier genres like Puzzle and Platform were more popular in the 1980s and 1990s, but gradually declined over time.

For platform:

The PlayStation platform dominated multiple generations, with PS1, PS2, PS3, and PS4 consistently ranking among the highest-selling consoles, while the Wii saw a massive peak in 2009, nearly matching PS2’s best year. Early gaming was led by Atari 2600 and NES, but as the industry evolved, newer consoles like Xbox 360 and PlayStation 4 took over the market, maintaining high global sales into the 2010s.
```
Which platforms are most successful for specific genres?

SQL:
```sql
SELECT 
    Genre, 
    Platform, 
    Total_Global_Sales, 
    Rank
FROM ( 
    SELECT 
        Genre,
        Platform,
        SUM(Global_Sales) AS Total_Global_Sales,
        ROW_NUMBER() OVER (PARTITION BY Genre ORDER BY SUM(Global_Sales) DESC) AS Rank
    FROM vgsales
    GROUP BY Genre, Platform
) Ranked_Data
WHERE Rank <= 4
ORDER BY Genre, Rank
LIMIT 10;
```
| #   | Genre       | Platform | Total_Global_Sales | Rank |
|-|-|-|-|-|
| 1   | "Action"    | "PS3"    | "307.88"           | "1"  |
| 2   | "Action"    | "PS2"    | "272.76"           | "2"  |
| 3   | "Action"    | "X360"   | "242.67"           | "3"  |
| 4   | "Action"    | "PS"     | "127.05"           | "4"  |
| 5   | "Adventure" | "DS"     | "47.29"            | "1"  |
| 6   | "Adventure" | "PS3"    | "22.9"             | "2"  |
| 7   | "Adventure" | "PS2"    | "21.16"            | "3"  |
| 8   | "Adventure" | "PS"     | "20.97"            | "4"  |
| 9   | "Fighting"  | "PS2"    | "92.6"             | "1"  |
| 10  | "Fighting"  | "PS"     | "72.68"            | "2"  |
```
Findings:
```findings
The top-ranking platform for Action games is PlayStation 3, with the highest global sales, while Nintendo DS leads in Adventure, and PlayStation 2 dominates in Fighting. Each genre has unique platform preferences, with PlayStation consoles appearing most frequently among the highest sellers.

```
## Deliverables:
- SQL Queries: Provide all the SQL queries you used to answer the business questions.
- Summary of Findings: For each question, summarise your key findings and recommendations based on your analysis.

## Submission
