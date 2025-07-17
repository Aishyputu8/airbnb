# airbnb
Analyze Airbnb user data to uncover behavior, engagement trends, and location patterns, enabling strategic enhancements to user experience, service personalization, and demographic targeting.

select * from countries ;
select * from sessions_data ;
select * from users ;
------------------------------------- 
select count(*) from sessions_data ;
select count(*), id from users 
group by id
having count(*)> 1 ; -- no duplicate id 

/***Identify the top 5 most active users who have spent more than 10,000 seconds on at least one session.** */

select 
user_id from sessions_data
where user_id in (
select distinct user_id 
from sessions_data
where secs_elapsed > 10000
)
group by user_id
order by count(*) desc
limit 5;

----------------------------------------------------------------------------------------------------------------------------------
****/* determine which country_destination appears most frequently. Identify and report the country code that occurs the highest number 
of times in the country_destination column.*/****

select country_destination, count(country_destination) as cn
from users
where country_destination <> 'NDF'
group by country_destination
order by cn desc ;
 
-------------------------------------------------------------------------------------------------------

/***determine the most frequently used signup method for each Gender category, considering only users who have made a booking 
(as indicated by a non-null value in the Date_first_booking column)**.*/

select gender, signup_method,count(date_first_booking)
from users
where date_first_booking is not null and country_destination <> 'NDF'
group by gender, signup_method;

---------------------------------------------------------------------------------------------------------------------

/* **identify the most frequently used signup method among users categorized as female who have made a booking. Determine which signup 
method has the highest count for this group.***/

select signup_method,count(*)
from users
where gender = 'FEMALE' and country_destination <> 'NDF' and date_first_booking is not null
group by signup_method;

-------------------------------------------------------------------------------------------------

/***Determine the average age of users by destination country, considering only those with a booking and available age data. 
Sort the results from the youngest to the oldest users**.*/

select country_destination, avg(age) average_age
from users
where country_destination <> 'NDF' and date_first_booking is not null and age is not null
group by country_destination
order by average_age ;

 
