 #                                                                    Spotify SQL Project
![spotify_logo](https://github.com/user-attachments/assets/c0d474d8-6950-4a79-8656-c0437d0ebbc5)
## Overview
This project involves analyzing a Spotify dataset with various attributes about tracks, albums, and artists using **SQL**. It covers an end-to-end process of normalizing a denormalized dataset, performing SQL queries of varying complexity (easy, medium, and advanced). The primary goals of the project are to practice advanced SQL skills and generate valuable insights from the dataset.

```sql
-- create table
DROP TABLE IF EXISTS spotify;
CREATE TABLE spotify (
    artist VARCHAR(255),
    track VARCHAR(255),
    album VARCHAR(255),
    album_type VARCHAR(50),
    danceability FLOAT,
    energy FLOAT,
    loudness FLOAT,
    speechiness FLOAT,
    acousticness FLOAT,
    instrumentalness FLOAT,
    liveness FLOAT,
    valence FLOAT,
    tempo FLOAT,
    duration_min FLOAT,
    title VARCHAR(255),
    channel VARCHAR(255),
    views FLOAT,
    likes BIGINT,
    comments BIGINT,
    licensed BOOLEAN,
    official_video BOOLEAN,
    stream BIGINT,
    energy_liveness FLOAT,
    most_played_on VARCHAR(50)
);
```
  
## Project Steps

### 1. Data Exploration
Before diving into SQL, it’s important to understand the dataset thoroughly. The dataset contains attributes such as:
- `Artist`: The performer of the track.
- `Track`: The name of the song.
- `Album`: The album to which the track belongs.
- `Album_type`: The type of album (e.g., single or album).
- Various metrics such as `danceability`, `energy`, `loudness`, `tempo`, and more.

### 2. Querying the Data
After the data is inserted, various SQL queries can be written to explore and analyze the data. Queries are categorized into **easy**, **medium**, and **advanced** levels to help progressively develop SQL proficiency.

#### Easy Queries
- Simple data retrieval, filtering, and basic aggregations.
  
#### Medium Queries
- More complex queries involving grouping, aggregation functions, and joins.
  
#### Advanced Queries
- Nested subqueries, window functions, CTEs, and performance optimization.


### Easy Level
**Q1: Retrieve the names of all tracks that have more than 1 billion streams.**  

```sql
SELECT track_name  
FROM spotify  
WHERE stream > 1000000000;  
```
**Q2: List all albums along with their respective artists.**  

```sql
SELECT DISTINCT album, artist  
FROM spotify  
ORDER BY album;
```
**Q3: Get the total number of comments for tracks where licensed = TRUE.**  

```sql  
SELECT SUM(comments) AS total_comments  
FROM spotify  
WHERE licensed = 'true';  
```
**Q4: Find all tracks that belong to the album type 'single'.**  

```sql  
SELECT *  
FROM spotify  
WHERE album_type = 'single';  
```
**Q5: Count the total number of tracks by each artist.**  

```sql  
SELECT artist, COUNT(track) AS total_tracks  
FROM spotify  
GROUP BY artist  
ORDER BY total_tracks DESC;  
```
### Medium Level
**Q1: Calculate the average danceability of tracks in each album.**  

```sql  
SELECT album,  
       AVG(danceability) AS avg_danceability  
FROM spotify  
GROUP BY album  
ORDER BY avg_danceability DESC;  
```
**Q2: Find the top 5 tracks with the highest energy values.**  

```sql  
SELECT track,  
       MAX(energy) AS max_energy  
FROM spotify  
GROUP BY track  
ORDER BY max_energy DESC  
LIMIT 5;  
```
**Q3: List all tracks along with their views and likes where official_video = TRUE.**  

```sql  
SELECT track,  
       SUM(views) AS total_views,  
       SUM(likes) AS total_likes  
FROM spotify  
WHERE official_video = 'true'  
GROUP BY track  
ORDER BY total_views DESC  
LIMIT 5;  
```
**Q4: For each album, calculate the total views of all associated tracks.**  

```sql  
SELECT album,  
       track,  
       SUM(views) AS total_views  
FROM spotify  
GROUP BY album, track  
ORDER BY total_views DESC;  
``` 
**Q5: Retrieve the track names that have been streamed on Spotify more than YouTube.**  

```sql  
SELECT *  
FROM (  
    SELECT  
        track,  
        COALESCE(SUM(CASE WHEN most_played_on = 'Youtube' THEN stream END), 0) AS streamed_on_youtube,  
        COALESCE(SUM(CASE WHEN most_played_on = 'Spotify' THEN stream END), 0) AS streamed_on_spotify  
    FROM spotify  
    GROUP BY track  
) AS t1  
WHERE streamed_on_spotify > streamed_on_youtube  
AND streamed_on_youtube <> 0;  
```  
### Advanced Level

**Q1: Find the top 3 most-viewed tracks for each artist using window functions.**  

```sql  
WITH ranking_artist AS (  
    SELECT  
        artist,  
        track,  
        SUM(views) AS total_views,  
        DENSE_RANK() OVER(PARTITION BY artist ORDER BY SUM(views) DESC) AS rank  
    FROM spotify  
    GROUP BY artist, track  
)  
SELECT *  
FROM ranking_artist  
WHERE rank <= 3  
ORDER BY artist, total_views DESC;  
```
**Q2: Write a query to find tracks where the liveness score is above the average.**  

```sql  
SELECT track, artist, liveness  
FROM spotify  
WHERE liveness > (SELECT AVG(liveness) FROM spotify);  
``` 
**Q3: Use a `WITH` clause to calculate the difference between the highest and lowest energy values for tracks in each album.**
```sql
WITH cte
AS
(SELECT 
	album,
	MAX(energy) as highest_energy,
	MIN(energy) as lowest_energery
FROM spotify
GROUP BY 1
)
SELECT 
	album,
	highest_energy - lowest_energery as energy_diff
FROM cte
ORDER BY 2 DESC
```

## Technology Used
- **Database**: PostgreSQL
- **SQL Queries**: DDL, DML, Aggregations, Joins, Subqueries, Window Functions
- **Tools**: pgAdmin 4, PostgreSQL
