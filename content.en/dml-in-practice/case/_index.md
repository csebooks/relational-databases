---
title: 'Case'
weight: 1
--- 

We have localized system that stores movies in multiple languages. Below is the table structure.

```sql
CREATE TABLE movies (
  id SERIAL PRIMARY KEY,
  title TEXT,
  year_of_release  smallint 
);

CREATE TABLE movies_localized (
    movie_id UUID,
    locale VARCHAR(8) NOT NULL,
    title TEXT,
    FOREIGN KEY (movie_id) REFERENCES movies (id),
    PRIMARY KEY(movie_id, locale)
);
```

Please note that for a movie `title` can be in multiple languages here as `year_of_release` can not be localized. Lets create the data now


```sql
INSERT INTO movies(title, year_of_release)
	VALUES ('Avatar', 2009);
	
INSERT INTO movies_localized(movie_id, locale, title) 
	VALUES (currval('movies_id_seq'), 'ta', 'அவதார்');

INSERT INTO movies(title, year_of_release)
	VALUES ('Inception', 2010);

INSERT INTO movies(title, year_of_release)
	VALUES (NULL, 2000);    
```

Now we need to consider 3 scnerios when we read movie data for specific language for e.g `ta`

1. For the movie `Avatar` it should return `அவதார்`
2. For the movie `Inception` it should return `Inception` as fallback since there is no localized value
3. For the third it should return **NULL**

Lets get all the movies 

```sql
SELECT id, title, year_of_release FROM movies
```

Lets join the `movie localized`

```sql
SELECT id, 
        m.title, ml.title, 
        year_of_release 
    FROM movies m
	LEFT JOIN movies_localized ml ON m.ID = ml.movie_id
```

Time to solve our actual problem of fall back locale. here is where CASE comes to help us

```sql
SELECT id,
       CASE
         WHEN ml.locale = 'ta' THEN ml.title
         ELSE m.title
       END AS title,
       year_of_release
    FROM   movies m
    LEFT JOIN movies_localized ml
              ON m.id = ml.movie_id
    WHERE  ml.locale IS NULL
        OR ml.locale = 'ta'
```

Cool isn't it ? But wait . Is there any better way to it ?!

```sql
SELECT id,
       COALESCE(ml.title, m.title) AS title,
       year_of_release
    FROM   movies m
    LEFT JOIN movies_localized ml
              ON m.id = ml.movie_id
    WHERE  ml.locale IS NULL
        OR ml.locale = 'ta'
```

Which is better. You can EXPLAIN yourself 😊