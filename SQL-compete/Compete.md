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

### 4)(Sadid Q1) Find all actors who acted in more than one language. Output should contain actor_name, language. Sorted by languages, actor_name.

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
### 5)(Sadid Q2) Find the category which was rented the most for each month in year 2005, if there is a tie find all categories. Sorted by months and category names. Output should contain month, category, count. You can ignore the month if there is no data present for it.
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
### 6)(Nandini Q1)list out all films with highest rental rate  and classify the films labelled as "inappropriate for children under 13" with ratings PG-13 , "General" with ratings as G and " Restricted for Age below 18" with ratings as R
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
### 7)(Nandini Q2) List the top 3 actors having done maximum sci-fi movies and with maximum rental rates
        select concat(ac.first_name, ' ',ac.last_name) as names, c.name, f.rental_rate
            from film f
                inner join film_category fc
                        on f.film_id = fc.film_id
                inner join category c
                        on fc.category_id = c.category_id
                inner join film_actor fa
                        on f.film_id = fa.film_id
                inner join actor ac
                        on fa.actor_id = ac.actor_id
            where c.name = 'Sci-Fi'
        group by names, c.name, f.rental_rate
        order by f.rental_rate desc
        LIMIT 3;
### 8)(Tejas Q2) Query to Find Countries with the Longest Average Address Street Length.
        select c.country, address ,avg(char_length(address.address)) as avg from address
                  inner join city ct
                             on address.city_id = ct.city_id
                  inner join public.country c
                             on ct.country_id = c.country_id
        group by c.country, address
        order by avg
