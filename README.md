# Analysis of NYC Public School Test Scores

## BY: Tahsin Jahin Khalid

## PostgreSQL has been used to explore the [NYC Public School Test Scores Dataset](https://www.kaggle.com/datasets/nycopendata/high-schools)

## Project Summary

### Data Card

| Column      | Description |
| ----------- | ----------  |
| school_name | Name of the school|
| borough | borough where school is located |
| building_code | indentifier Code for the building |
| avg_math_score | average math score for SATs |
| avg_reading_score | average reading score for SATs |
| avg_writing_score | average writing score for SATs |
| percentage_tested | percentage of students completing SATs |


### Show a preview of the preprocessed dataset

#### Query

```sql
SELECT *
FROM
public.nyc_schools
LIMIT 10;
```

#### Result

|"school_name"|"borough"|"building_code"|"avg_math_score"|"avg_reading_score"|"avg_writing_score"|"percentage_tested"|
|---|---|---|---|---|---|---|
|"New Explorations into Science Technology and Math High School"|"Manhattan"|"M022"|657|601|601|NULL|
|"Essex Street Academy"|"Manhattan"|"M445"|395|411|387|"78.9"|
|"Lower Manhattan Arts Academy"|"Manhattan"|"M445"|418|428|415|"65.1"|
|"High School for Dual Language and Asian Studies"|"Manhattan"|"M445"|613|453|463|"95.9"|
|"Henry Street School for International Studies"|"Manhattan"|"M056"|410|406|381|"59.7"|
|"Bard High School Early College"|"Manhattan"|"M097"|634|641|639|"70.8"|
|"Urban Assembly Academy of Government and Law"|"Manhattan"|"M445"|389|395|381|"80.8"|
|"Marta Valle High School"|"Manhattan"|"M025"|438|413|394|"35.6"|
|"University Neighborhood High School"|"Manhattan"|"M446"|437|355|352|"69.9"|
|"New Design High School"|"Manhattan"|"M445"|381|396|372|"73.7"|


### How many schools are in this dataset?

#### Query

```sql
SELECT 
COUNT(DISTINCT building_code) AS num_school_buildings
FROM public.nyc_schools;
```

#### Result

|"num_school_buildings"|
|---|
|"233"|

### What are the best Schools for Math?

#### Query

```sql
SELECT 
school_name, avg_math_score
FROM public.nyc_schools
-- 640 or higher
WHERE avg_math_score >= 640
ORDER BY avg_math_score DESC
LIMIT 10;
```

#### Result

|"school_name"|"avg_math_score"|
|---|---|
|"Stuyvesant High School"|754|
|"Bronx High School of Science"|714|
|"Staten Island Technical High School"|711|
|"Queens High School for the Sciences at York College"|701|
|"High School for Mathematics Science and Engineering at City College"|683|
|"Brooklyn Technical High School"|682|
|"Townsend Harris High School"|680|
|"High School of American Studies at Lehman College"|669|
|"New Explorations into Science Technology and Math High School"|657|
|"Eleanor Roosevelt High School"|641|

### What is the Lowest Reading Score?

#### Query

```sql
SELECT 
MIN(avg_reading_score) AS min_reading_score
FROM public.nyc_schools;
```

#### Result

|"min_reading_score"|
|---|
|302|

### Which school has the best average writing score?

#### Query

```sql
SELECT 
school_name, avg_writing_score
FROM public.nyc_schools
WHERE avg_writing_score = (
   SELECT 
   MAX(avg_writing_score)
   FROM public.nyc_schools
);
```

#### Result

|"school_name"|"avg_writing_score"|
|---|---|
|"Stuyvesant High School"|693|

### Which are the Top 10 schools?

#### Query

```sql
-- Top 10 Schools
SELECT
school_name,
SUM(avg_math_score + avg_reading_score + avg_writing_score)
AS average_SAT
FROM public.nyc_schools
GROUP BY school_name
ORDER BY average_SAT DESC
LIMIT 10;
```

#### Result

|"school_name"|"average_sat"|
|---|---|
|"Stuyvesant High School"|"2144"|
|"Staten Island Technical High School"|"2041"|
|"Bronx High School of Science"|"2041"|
|"High School of American Studies at Lehman College"|"2013"|
|"Townsend Harris High School"|"1981"|
|"Queens High School for the Sciences at York College"|"1947"|
|"Bard High School Early College"|"1914"|
|"Brooklyn Technical High School"|"1896"|
|"High School for Mathematics Science and Engineering at City College"|"1889"|
|"Eleanor Roosevelt High School"|"1889"|

### How are the NYC boroughs ranked?

#### Query

```sql
SELECT
borough,
COUNT(*) AS num_schools,
SUM(avg_math_score + avg_reading_score + avg_writing_score) / COUNT(*)
AS avg_borough_sat
FROM public.nyc_schools
GROUP BY borough
ORDER BY avg_borough_sat DESC;
```

#### Result

|"borough"|"num_schools"|"avg_borough_sat"|
|---|---|---|
|"Staten Island"|"10"|"1439"|
|"Queens"|"69"|"1345"|
|"Manhattan"|"89"|"1340"|
|"Brooklyn"|"109"|"1230"|
|"Bronx"|"98"|"1202"|

**Note**:
The score for Staten Island has the highest average SAT scores among all boroughs. However, it's misleading since there are only ten schools in this borough.

### Which are top 5 schools in Brooklyn which have the best math performance?

#### Query

```sql
SELECT
school_name, avg_math_score
FROM public.nyc_schools
WHERE borough = 'Brooklyn'
ORDER BY avg_math_score DESC
LIMIT 5;
```

#### Result

|"school_name"|"avg_math_score"|
|---|---|
|"Brooklyn Technical High School"|682|
|"Brooklyn Latin School"|625|
|"Leon M. Goldstein High School for the Sciences"|563|
|"Millennium Brooklyn High School"|553|
|"Midwood High School"|550|
