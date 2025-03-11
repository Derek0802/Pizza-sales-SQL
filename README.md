CREATE TABLE orders (
    order_id NUMBER PRIMARY KEY,
    order_date DATE NOT NULL,
    order_time TIMESTAMP NOT NULL
);
alter table orders rename column order_time to time1;
alter table orders_details modify order_details_id number primary key;

select*from order_details;
select*from pizza_type;
select*from pizza;
select*from orders;

---TOTAL NO.OF.ORDER PLACED
SELECT COUNT(ORDER_ID)AS Total_orders FROM ORDERS;

---Total Revenue from pizza sales
select round(sum(order_details.quantity * pizza.price),2) as Total_revenue from order_details 
join pizza 
on order_details.pizza_id =pizza.pizza_id;

---Highest price pizza
SELECT pizza_type.name, pizza.price
FROM pizza_type
JOIN pizza ON pizza_type.pizza_type_id = pizza.pizza_type_id
ORDER BY pizza.price DESC;
 

--MOST ORDER PIZZA SIZE
select quantity, count(order_id) from order_details group by quantity;

select pizza_size,count(order_details.order_details_id)as order_count from pizza
join order_details on pizza.pizza_id=order_details.pizza_id group by pizza_size order by order_count desc;

----Top  most order pizza along with their quantity
select pizza_type.name,sum(order_details.quantity) as quantity from pizza_type join pizza
on pizza_type.pizza_type_id = pizza.pizza_type_id
join order_details on order_details.pizza_id = pizza.pizza_id group by pizza_type.name order by quantity desc;

--Total quantity of each pizza category order
SELECT
    pizza_type.category,
    SUM(order_details.quantity) AS quantity
FROM
    pizza_type
    JOIN pizza ON pizza_type.pizza_type_id = pizza.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizza.pizza_id
GROUP BY
    pizza_type.category
ORDER BY
    quantity DESC;

---Category wise distribution of pizzas
SELECT
    category,
    COUNT(name)
FROM
    pizza_type
GROUP BY
    category;

----Grup the orders by date and calculate the average no.of.pizzas per day
SELECT round(AVG(quantity),0) as avarage_per_day
FROM (
    SELECT orders.date1, SUM(order_details.quantity) AS quantity
    FROM orders 
    JOIN order_details ON orders.order_id = order_details.order_id
    GROUP BY orders.date1
);
 
----TOP most pizza type based on revenue
SELECT
    pizza_type.name,
    SUM(order_details.quantity * pizza.price) AS revenue
FROM
    pizza_type
    JOIN pizza ON pizza.pizza_type_id = pizza_type.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizza.pizza_id
GROUP BY
    pizza_type.name
ORDER BY
    revenue DESC;

---calulate the percentage contribution of each pizza type of total revenue
SELECT
    pizza_type.category,
    round((SUM(order_details.quantity * pizza.price) /(
        SELECT
            SUM(order_details.quantity * pizza.price)
        FROM
            order_details
            JOIN pizza ON order_details.pizza_id = pizza.pizza_id
    )) * 100, 2) AS revenue_percentage
FROM
    pizza_type
    JOIN pizza ON pizza_type.pizza_type_id = pizza.pizza_type_id
    JOIN order_details ON order_details.pizza_id = pizza.pizza_id
GROUP BY
    pizza_type.category
ORDER BY
    revenue_percentage DESC;


