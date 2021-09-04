# SQL CRUD

## Part 1: Restaurant finder

I created two tables one for the restaurants and one for the reviews for the restaurants. 

**Restaurant Table**

```mysql
CREATE TABLE restaurants(
    id INTEGER PRIMARY KEY,
    name TEXT, 
    category TEXT,
    price_tier TEXT,
    neighbourhood TEXT,
    average_rating REAL,
    good_for_kids TEXT,
    opening_time TEXT,
    closing_time TEXT
);
```
**Reviews Table (where the restaurants_id is the foreign key)**

```mysql
CREATE TABLE reviews(
    id INTEGER PRIMARY KEY,
    reviews TEXT, 
    restaurant_id INTEGER
);
```

I used the following scripts to import my data 
```
.mode csv
.headers on
.import data/restaurants.csv restaurants 
```
[Link to Restaurants data](https://github.com/database-design-assignments/sql-crud-tazasahar/blob/main/data/restaurants.csv)

### Queries

1) Find all cheap restaurants in a particular neighborhood (pick any neighborhood as an example).
```mysql
SELECT name FROM restaurants WHERE price_tier = "cheap" AND neighbourhood = "Willowbrook - Staten Island";
```
2) Find all restaurants in a particular genre (pick any genre as an example) with 3 stars or more, ordered by the number of stars in descending order.
```mysql
SELECT name, average_rating FROM restaurants WHERE category = "indian" AND average_rating>= 3.0 ORDER BY average_rating DESC;
```
3) Find all restaurants that are open now.
```mysql
SELECT name, opening_time, closing_time FROM restaurants WHERE opening_time <= strftime('%H:%M', 'now') AND closing_time >=strftime('%H:%M', 'now');
```
4)Leave a review for a restaurant (pick any restaurant as an example).
```mysql 
INSERT INTO reviews (reviews, restaurant_id) VALUES ("very good food", 6);
```
5) Delete all restaurants that are not good for kids.
```mysql
DELETE FROM restaurants WHERE good_for_kids = "false";
```
6) Find the number of restaurants in each NYC neighborhood.
```mysql
SELECT neighbourhood, count(id) FROM restaurants GROUP BY neighbourhood;
```



## Part 2

I created two tables one for users and one for posts. 

**User Table**
```mysql
CREATE table users(
    user_id INTEGER PRIMARY KEY,
    email TEXT NOT NULL,
    password TEXT NOT NULL, 
    username TEXT NOT NULL
);
```
**Posts Table (sender_id and reciever_id are foreign keys)**
```mysql
CREATE table posts(
    posts_id INTEGER PRIMARY KEY,
    type TEXT,  
    post_time TEXT,
    content TEXT,
    sender_id INTEGER, 
    reciever_id INTEGER, 
    visible TEXT
);
```
I used the following script to import the date the tables 

```
.mode csv
.headers on
.import data/users.csv users
.import data/posts.csv posts

```
[Link to Users data](https://github.com/database-design-assignments/sql-crud-tazasahar/blob/main/data/users.csv)

[Link to Posts data](https://github.com/database-design-assignments/sql-crud-tazasahar/blob/main/data/posts.csv)

### Queries

1) Register a new User.
```mysql
INSERT INTO users (email, password, username) VALUES ("ms11831@nyu.edu", "NYU1234", "taza123");
```
2) Create a new Message sent by a particular User to a particular User (pick any two Users for example).
```mysql
INSERT INTO posts (type, post_time, content, sender_id, reciever_id, visible) values ("message",  "2022-03-11 16:05:54", "hiiiiiiiii", 23, 30, "true");
```
3) Create a new Story by a particular User (pick any User for example).
```mysql
 INSERT INTO posts (type, post_time, content, sender_id, reciever_id, visible) VALUES ("story",  "2022-03-11 17:05:54", "Loooooooooooool", 23,NULL, "true");
```
4) Show the 10 most recent visible Messages and Stories, in order of recency.
```mysql
SELECT posts_id, type, post_time, content, sender_id, reciever_id FROM posts order by post_time desc limit 10;
```
5) Show the 10 most recent visible Messages sent by a particular User to a particular User (pick any two Users for example), in order of recency.
```mysql
 SELECT posts_id,content FROM posts WHERE sender_id = 432 AND reciever_id = 98 AND type = "message" AND visible ="true" ORDER BY post_time DESC LIMIT 10;
```
6)  Make all Stories that are more than 24 hours old invisible.
```mysql
UPDATE posts SET visible = "false" WHERE ROUND((JULIANDAY('now') - JULIANDAY(post_time)) * 60) >= 24;
```
7) Show all invisible Messages and Stories, in order of recency.
```mysql
SELECT * FROM posts WHERE visible = "false" ORDER BY post_time DESC;
```
8) Show the number of posts by each User.
```mysql
SELECT users.user_id, users.username, count(posts.posts_id) FROM users LEFT JOIN posts ON users.user_id = posts.sender_id GROUP BY users.user_id;
```
9) Show the post text and email address of all posts and the User who made them within the last 24 hours.
```mysql
SELECT users.email, posts.content FROM users inner join posts ON users.user_id = posts.sender_id WHERE ROUND((JULIANDAY('now') - JULIANDAY(posts.post_time)) * 60) <= 24;
```
10) Show the email addresses of all Users who have not posted anything yet.
```mysql
SELECT users.email FROM users LEFT JOIN posts ON users.user_id = posts.sender_id WHERE posts.posts_id = NULL;
```