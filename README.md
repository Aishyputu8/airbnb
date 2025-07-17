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

select count(distinct id) from users 
where first_device_type = 'iphone' ; 

select count(distinct id), gender from users 
group by gender ;

select device_type, count(user_id) from sessions_data
group by device_type
order by count(user_id) desc;

/*Identify the top 5 most active users who have spent more than 10,000 seconds on at least one session. */

 
