# Assignment

## Instructions

Paste the answer to each question in the answer code section below each question.

### Question 1

Construct an ERD in DBML for a social media company whose database includes information about users, their followers, and the posts that they make. Users can follow multiple users and create multiple posts.

Each entity has the following attributes:

- User: id, username, email, created_at
- Post: id, title, body, user_id, status, created_at
- Follows: following_user_id, followed_user_id, created_at

Answer:

```dbml

Table users {
  id integer [primary key]
  username varchar
  role varchar
  created_at timestamp
}

Table posts {
  id integer [pk,incremnet]
  title varchar
  body text [note: 'Content of the post']
  user_id integer [not null]
  status varchar
  created_at timestamp
}

Table follows {
  following_user_id integer
  followed_user_id integer
  created_at timestamp
}

Ref user_posts: posts.user_id > users.id // many-to-one
Ref: users.id < follows.following_user_id
Ref: users.id < follows.followed_user_id
```
### Question 2

Using the data provided in lession 1.3 ( https://github.com/su-ntu-ctp/5m-data-1.3-sql-basic-ddl/tree/solutions/data ), write the SQL statement to alter the teachers table in the lesson schema to add a new column subject of type VARCHAR.

Answer:

```sql
ALTER TABLE lesson.teachers
ADD COLUMN subject VARCHAR;
```

### Question 3

Using the data provided in lession 1.3 ( https://github.com/su-ntu-ctp/5m-data-1.3-sql-basic-ddl/tree/solutions/data ), write the SQL statement to update the `email` of the teacher with the name 'John Doe' to 'john.doe@school.com' in the teachers table of the `lesson` schema.

Answer:

```sql
UPDATE lesson.teachers
SET email = 'john.doe@school.com'
WHERE name = 'John Doe';
```
### Question 4

Using the data provided in lesson 1.4 ( https://github.com/su-ntu-ctp/5m-data-1.4-sql-basic-dml/tree/main/db ), categorize flats into price ranges and count how many flats fall into each category:

- Under $400,000: 'Budget'
- $400,000 to $700,000: 'Mid-Range'
- Above $700,000: 'Premium'
  Show the counts in descending order.

```sql
SELECT 
CASE
When resale_price < 400000 THEN 'BUDGET'
WHEN resale_price BETWEEN 400000 AND 700000 THEN 'Mid-Range'
WHEN resale_price > 700000 THEN 'Premium'
ELSE 'UNKNOWN'
END AS price_category,
Count(*) AS flat_count
FROM resale_flat_prices_2017
GROUP BY price_category
ORDER BY flat_count DESC;
```

### Question 5

Using the data provided in lesson 1.4 ( https://github.com/su-ntu-ctp/5m-data-1.4-sql-basic-dml/tree/main/db ),select the minimum and maximum price of flats sold in each town during the first quarter of 2017 (January to March).

```sql
SELECT 
  town,
  MIN(resale_price) AS min_price,
  MAX(resale_price) AS max_price
FROM resale_flat_prices_2017
WHERE month IN ('2017-01', '2017-02', '2017-03')
GROUP BY town
ORDER BY town;


```
### Question 6

Using the data provided in lesson 1.5 ( https://github.com/su-ntu-ctp/5m-data-1.5-sql-advanced/tree/main/db ), using the `claim` and `car` tables, write a SQL query to compute the running total of the `travel_time` column for each `car_id` in the `claim` table. The resulting table should contain `id, car_id, travel_time, running_total`.

Answer:

```sql
SELECT
  id,
  car_id,
  travel_time,
  SUM(travel_time) OVER (
    PARTITION BY car_id
    ORDER BY id DESC
  ) AS running_total
FROM claim;
```

### Question 7

Using the data provided in lesson 1.5 ( https://github.com/su-ntu-ctp/5m-data-1.5-sql-advanced/tree/main/db ), using a Common Table Expression (CTE), write a SQL query to return a table containing `id, resale_value, car_use` from `car`, where the car resale value is less than the average resale value for the car use.

Answer:

```sql
WITH avg_resale_value_by_car_use AS (
  SELECT car_use, AVG(resale_value) AS average_resale_value
  FROM car
  GROUP BY car_use
)
SELECT id, resale_value, c1.car_use
FROM car c1
INNER JOIN avg_resale_value_by_car_use c2 ON c1.car_use = c2.car_use
WHERE c1.resale_value < c2.average_resale_value
```

## Submission
