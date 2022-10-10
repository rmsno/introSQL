/* query 1*/
/* query to capture the customer name, month and year of payment, and total payment amount for each month by these top 10 paying customers? */
SELECT CONCAT(c.first_name ,' ',c.last_name) AS fullname , COUNT(p.amount) AS pay_countpermon , SUM(p.amount) AS pay_amount ,
DATE_TRUNC('month', p.payment_date) pay_month
FROM customer c
JOIN payment p
/*connnect two tables*/
ON p.customer_id = c.customer_id
/* concat firstname & lastname */
WHERE CONCAT(c.first_name ,' ',c.last_name) IN
/*get fullname in query */
(SELECT t1.fullname
FROM
(SELECT CONCAT(c.first_name ,' ',c.last_name) AS fullname , SUM(p.amount) as amount_total
FROM customer c
JOIN payment p
ON p.customer_id = c.customer_id
 /* condition only in 2007 year */
 WHERE p.payment_date BETWEEN '2007-01-01' AND '2007-12-30'
GROUP BY 1
ORDER BY 2 DESC
 /* top 10 customer */
LIMIT 10)AS t1 )
/*group by names then date */
GROUP BY 1,4
/* order by name a-z then month */
ORDER BY 1,4


RESULT :
TOP 10 customers payment in 2007:
fullname	Sum of pay_amount
Ana Bradley	167.67
Clara Shaw	189.6
Curtis Irby	167.62
Eleanor Hunt	211.55
Karl Seal	208.58
Marcia Dean	166.61
Marion Snyder	194.61
Mike Way	162.67
Rhonda Kennedy	191.62
Tommy Collazo	183.63

34 results
TOP TOTAL PAYMENT IN 2007 WAS (Eleanor Hunt)



/* query 2*/
/*What is the highest number of rentals for a film category?*/
SELECT f.title , c.name , COUNT(r.rental_id)
/*using COUNT aggregation for  all rental_id to calculate all rental for films */ 
FROM film_category fc
/* JOIN and ON to connect all 5 tables togather 
within PK and FK*/
JOIN category c
ON fc.category_id=c.category_id
JOIN film f
ON fc.film_id=f.film_id
JOIN inventory n
ON f.film_id=n.film_id
JOIN rental r
ON n.inventory_id=r.inventory_id
WHERE c.name IN ('Family','Children','Animation', 
                 'Comedy','Music','Classics')
/* GROUP for film title */
GROUP BY 1,2 
/*ORDER 2 THEN 1 because we want order the title name from a-z , and start with 'Alter Victory'  then 2 which is the film category from a-z , start within 'animation' */
ORDER BY 2,1 

RESULT :
the HIGHEST is family category within 67 rental. 
The LOWEST is Music category within 51 rental.


/* query 3*/
/* how the length of rental duration of these family-friendly movies compares to the duration that all movies are rented for? */
SELECT  f.title , c.name , f.rental_duration  ,
/*  percentiles ,window function 4 levels using NTILE(4)*/
NTILE(4) OVER (PARTITION BY f.rental_duration ORDER BY f.rental_duration) AS standard_quartile
FROM film f
JOIN film_category fc
ON f.film_id=fc.film_id
JOIN category c
ON fc.category_id=c.category_id
/* order by standard_quartile  1 to 4*/
/* rating film type G = general audiences*/
WHERE f.rating='G'
/*order by standard quartile */
ORDER BY 3


/* query 4*/
/* a query of top20 count of actors in the DVD film in the store */

SELECT f.title, COUNT(*) number_of_actors
FROM film f
JOIN film_actor fa
ON f.film_id=fa.film_id
JOIN actor a
ON a.actor_id=fa.actor_id
GROUP BY 1 
ORDER BY 2 DESC
LIMIT 20 
