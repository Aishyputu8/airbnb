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

-------------------------------------------------------------------------------------------------

/***users with fewer than 5 sessions who made a booking to the destination "US". 
Sort the results by the number of sessions in descending order**.*/

with ab as (
select s.user_id , count(s.user_id) as session_count
from sessions_data s
join users u on s.user_id = u.id
where u.country_destination = 'US' and u.date_first_booking is not null
group by s.user_id 
)
select user_id , session_count
from ab
where session_count < 5
order by session_count desc;

-------------------------------------------------------------------------------------------------
/* **analyze the activity of organic users, defined as those with "direct" listed as their affiliate provider. Specifically, we are 
interested in the total number of clicks made by these users. Please write a query to calculate the total clicks made by organic users**.*/

select * from sessions_data;
select count(*) from
(select u.*, s.action_type
from users u  
join sessions_data s on u.id = s.user_id
where u.affiliate_provider = 'direct' ) as new
where action_type = 'click';

-------------------------------------------------------------------------------------------------
/* **to understand the most common actions performed by users who have made a booking, as well as the devices they use for these 
actions.Identify the top 5 most common actions performed by users who made a booking and the devices they use for these actions**.*/

select s.action, s.device_type, count(s.user_id) as action_count
from 
sessions_data s
join users u on s.user_id = u.id
where u.country_destination <> 'NDF' and u.date_first_booking is not null
group by s.action , s.device_type
order by action_count desc
limit 5;

-------------------------------------------------------------------------------------------------
/* **to determine the average time spent on actions by users who have made a booking (i.e., country_destination is not 'NDF'),
cross action type and device type. Sort the results by average time spent in descending order**.*/

select s.action_type, s.device_type, avg(s.secs_elapsed) as average_time_spend
from 
sessions_data s
join users u on s.user_id = u.id
where u.country_destination <> 'NDF' and u.date_first_booking is not null
group by s.action_type, s.device_type
order by average_time_spend desc;

-------------------------------------------------------------------------------------------------
/***find the most frequent combinations of two actions (performed by the same user on Windows Desktop devices) where the most time 
is spent, for users who have made a booking (i.e., country_destination is not 'NDF'). Consider the top 10 combinations from the 
resulting table which will be considered as most frequent**.*/
SELECT 
    s1.action AS first_action, 
    s2.action AS second_action, 
    COUNT(*) AS action_pair_count, 
    SUM(s1.secs_elapsed + s2.secs_elapsed) AS total_time_spent 
FROM sessions_data s1 
JOIN sessions_data s2 ON s1.user_id = s2.user_id AND s1.action <> s2.action 
JOIN users u ON s1.user_id = u.id 
WHERE u.country_destination <> 'NDF' and 
 s1.device_type = 'Windows Desktop' and s2.device_type = 'Windows Desktop' 
GROUP BY s1.action, s2.action 
ORDER BY total_time_spent DESC 
LIMIT 10;


-------------------------------------------------------------------------------------------------
/***find the number of bookings and the conversion rate for each first affiliate channel. Consider a booking as made if country_destination is not 'NDF'**.*/


with ab as (
select  first_affiliate_tracked, count(distinct id ) as total_users, count(date_first_booking) as bookings
from users
where country_destination <> 'NDF' and date_first_booking is not null
group by first_affiliate_tracked)

select  first_affiliate_tracked, total_users, bookings,
round(( bookings /total_users )*100, -3) as conversion_rate
from ab
 
----------------------------------------------------------------------------------------------------------------------------

SELECT 
    s1.action AS first_action, 
    s2.action AS second_action, 
    COUNT(*) AS action_pair_count, 
    SUM(s1.secs_elapsed + s2.secs_elapsed) AS total_time_spent 
FROM sessions_data s1 
JOIN sessions_data s2 ON s1.user_id = s2.user_id AND s1.action <> s2.action 
JOIN users u ON s1.user_id = u.id 
WHERE u.country_destination <> 'NDF' and 
 s1.device_type = 'Windows Desktop' and s2.device_type = 'Windows Desktop' 
GROUP BY s1.action, s2.action 
ORDER BY total_time_spent DESC 
LIMIT 10;


