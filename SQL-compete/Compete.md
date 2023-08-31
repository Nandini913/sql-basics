### 1)(Shreyash Q1)select 25 the actors firstname and lastname in one column named as name and their film names who have released in recent year 2006 order by name
        
    select concat(ac.first_name,ac.last_name) as name ,f.title from actor ac
            inner join film_actor fa
                on ac.actor_id = fa.actor_id
            inner join film f
                on fa.film_id = f.film_id
        where release_year = 2006
    order by name
    limit 25

### 2)(Shreyash Q2)select the staffs firstname and lastname in one column and their whole payment

        select concat(s.first_name ,' ',s.last_name) ,
               sum(p.amount) as payment
               from staff s
                inner join payment p
                     on s.staff_id = p.staff_id
        group by concat(s.first_name ,' ',s.last_name)

### 3)(Vaishnav Q1)What are the top 10 films that have been rented by customers in the United States, and how many times have they been rented?

        select count(con.country) , f.title from country con
                inner join city c
                     on con.country_id = c.country_id
                inner join address ad
                     on c.city_id = ad.city_id
                inner join customer cus
                     on ad.address_id = cus.address_id
                inner join rental rent
                     on cus.customer_id = rent.customer_id
                inner join inventory i
                     on rent.inventory_id = i.inventory_id
                inner join film f
                     on i.film_id = f.film_id
        where con.country ='United States'
        group by f.title
        order by  count(con.country) desc limit 10

### 4)(Vaishnav Q2) What is the average rental duration for the top 5 films that have been rented by customers in each country?

        Select c.country as countries, film.title, avg(film.length) as duration
                from film
                inner join inventory i
                    on film.film_id = i.film_id
                inner join rental r
                    on i.inventory_id = r.inventory_id
                inner join customer cus
                    on r.customer_id = cus.customer_id
                inner join address ad
                    on cus.address_id = ad.address_id
                inner join city ct
                    on ad.city_id = ct.city_id
                inner join country c
                    on ct.country_id = c.country_id
        group by film.title, countries
        order by duration desc
        limit 5;

### 5)(Sadid Q1) Find all actors who acted in more than one language. Output should contain actor_name, language. Sorted by languages, actor_name.

        select concat(ac.first_name,' ',ac.last_name) as name, l.name
        from film f
            inner join language l
                on f.language_id = l.language_id
            inner join film_actor fa
                on f.film_id = fa.film_id
            inner join actor ac
                on fa.actor_id = ac.actor_id
        group by name, l.name
        having count(*) > 1
        order by l.name, name;

### 6)(Sadid Q2) Find the category which was rented the most for each month in year 2005, if there is a tie find all categories. Sorted by months and category names. Output should contain month, category, count. You can ignore the month if there is no data present for it.
        
        select c.name , count(*) from film f
                 inner join inventory i
                         on f.film_id = i.film_id
                 inner join rental
                         on i.inventory_id = rental.inventory_id
                 inner join film_category fc
                            on f.film_id = fc.film_id
                 inner join category c
                            on fc.category_id = c.category_id
        group by c.name
        order by count(*) desc;

### 7)(Nandini Q1)list out all films with highest rental rate  and classify the films labelled as "inappropriate for children under 13" with ratings PG-13 , "General" with ratings as G and " Restricted for Age below 18" with ratings as R
        
        with table2 as (select description,
                       case
                           when rating = 'PG-13' then 'inappropriate for children under 13'
                           when rating = 'G' then 'General'
                           when rating = 'R' then 'Restricted for Age below 18'
                           end as Category  from

                    (select description , rating ,max(rental_rate) from film group by description ,rating) as sub)

        select description , category
                from table2 tab
        where category is not null

### 8)(Nandini Q2) List the top 3 actors having done maximum sci-fi movies and with maximum rental rates

        Select concat(a.first_name,' ', a.last_name) as names,
            ct.name, count(*) as count
                    from actor a
                    inner join film_actor fa
                        on a.actor_id = fa.actor_id
                    inner join film f
                        on fa.film_id = f.film_id
                    inner join film_category fc
                        on f.film_id = fc.film_id
                    inner join category ct
                        on fc.category_id = ct.category_id
        where ct.name = 'Sci-Fi'
        group by names, a.actor_id,ct.name,f.rental_rate
        order by f.rental_rate,count desc
        limit 3;

### 9)(Tejas Q1)Query to Find Actors Who Have Collaborated the Most.

        select a1.actor_id as actor1_id,
               a1.first_name as actor1_first_name,
               a2.actor_id as actor2_id, 
               a2.first_name as actor2_first_name,
               COUNT(DISTINCT fa1.film_id) as total_count
        from actor a1
                inner join film_actor fa1
                    on a1.actor_id = fa1.actor_id
                inner join film_actor fa2
                    on fa1.film_id = fa2.film_id AND fa1.actor_id != fa2.actor_id
                inner join actor a2
                    on fa2.actor_id = a2.actor_id
        group by a1.actor_id, a1.first_name, a2.actor_id, a2.first_name
        order by total_count DESC

### 10)(Tejas Q2)Query to Find Countries with the Longest Average Address Street Length.

        select  co.country, avg(length(a.address)) as avgStreetLength
            from country co
                    inner join city c
                        on co.country_id = c.country_id
                    inner join address a
                        on c.city_id = a.city_id
        group by co.country_id, co.country
        order by avgStreetLength desc
        limit 5;

### 11) (pulkit 1)Find the Indian Staff from whom customer rented film language id 1.

        Select count(*) from country c
                 inner join city ct
                     on c.country_id = ct.country_id
                 inner join public.address a
                     on ct.city_id = a.city_id
                 inner join staff s
                     on a.address_id = s.address_id
                 inner join rental r
                     on s.staff_id = r.staff_id
                 inner join inventory i
                     on r.inventory_id = i.inventory_id
                 inner join film f
                     on i.film_id = f.film_id
        where c.country = 'India'
        and f.language_id = 1

### 12)(pulkit 2)Find the Location of store where film was rented whose release year is 2006.

        Select a.district, f.film_id
        from address a
                inner join store  st
                     on a.address_id = st.address_id
                inner join staff stf
                     on st.address_id = stf.address_id
                inner join rental r
                     on stf.staff_id = r.staff_id
                inner join inventory i
                     on r.inventory_id = i.inventory_id
                inner join film f
                     on f.film_id = i.film_id
        where f.release_year = 2006
        group by a.district, f.film_id;

### 13)(harshit 1)Q1: Determine which customer watch how many movies of particular actor.

        select c.customer_id, concat(c.first_name ,' ' ,c.last_name) as Customer,a.actor_id, a.first_name, COUNT(*) as count
                from customer c
                inner join rental r
                    on c.customer_id = r.customer_id
                inner join inventory i
                    on r.inventory_id = i.inventory_id
                inner join film f
                    on i.film_id = f.film_id
                inner join film_actor fa
                    on f.film_id = fa.film_id
                inner join actor a
                    on fa.actor_id = a.actor_id
        group by c.customer_id, Customer, a.actor_id, a.first_name, a.actor_id
        order by count desc;

### 14)(harshit 2)Q2 : List out top 10 most revenue generated district to release animated film with average rental rent on that district

        select address.district, round(avg(rental_rate), 2)
        from film
                inner join film_category
                    on film.film_id = film_category.film_id
                inner join category
                    on film_category.category_id = category.category_id
                inner join inventory
                    on film.film_id = inventory.film_id
                inner join rental
                    on inventory.inventory_id = rental.inventory_id
                inner join payment
                    on rental.rental_id = payment.rental_id
                inner join customer
                    on payment.customer_id = customer.customer_id
                inner join address
                    on customer.address_id = address.address_id
        where category.name = 'Animation'
        group by address.district
        order by sum(amount) desc
        LIMIT 10;