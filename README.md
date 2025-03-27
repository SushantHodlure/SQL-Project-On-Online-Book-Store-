# SQL-Project- On-Online-Book-Store, end to end project 
DROP TABLE IF EXISTS books;

create table books(book_id serial primary key ,
                   title varchar(100),
				    author varchar(100),
					genre varchar(100),
					published_year int,
					price numeric(10,2),
					stock int 
			      );
				  
select * from books;

drop table if exists customers;
create table customers (
    customer_id serial primary key,
    name varchar(100),
    email varchar(100),
    phone varchar(15),
    city varchar(50),
    country varchar(150)
);

select * from customers;

drop table if exists orders;
create table orders (
    order_id serial primary key,
    customer_id int references customers(customer_id),
    book_id int references books(book_id),
    order_date date,
    quantity int,
    total_amount numeric(10, 2)
);



-- Import Data into Books Table
COPY Books(book_id, title, author, genre, published_year, price, stock) 
FROM 'C:\Users\Dell\Downloads\Books.csv' 
CSV HEADER;

-- Import Data into Customers Table
copy customers(customer_id, name, email, phone, city, country) 
from 'C:\Users\Dell\Downloads\Customers.csv' 
csv header;

-- Import Data into Orders Table
copy orders(order_id, customer_id, book_id, order_date, quantity, total_amount) 
from 'C:\Users\Dell\Downloads\Orders.csv' 
csv header;

select * from books;
select * from customers;
select * from orders;

-- 1) Retrieve all books in the "Fiction" genre:

SELECT
	*
FROM
	BOOKS
WHERE
	GENRE = 'Fiction';

-- 2) Find books published after the year 1950:
SELECT
	*
FROM
	BOOKS
WHERE
	PUBLISHED_YEAR > 1950;

-- 3) List all customers from the Canada:

SELECT
	*
FROM
	CUSTOMERS
WHERE
	COUNTRY = 'Canada';

-- 4) Show orders placed in November 2023:
SELECT
	*
FROM
	ORDERS
WHERE
	ORDER_DATE BETWEEN '2023-11-01' AND '2023-11-30';


-- 5) Retrieve the total stock of books available:
SELECT
	SUM(STOCK) AS TOTAL_STOCK
FROM
	BOOKS;

-- 6) Find the details of the most expensive book:
SELECT
	*
FROM
	BOOKS
ORDER BY
	PRICE DESC
LIMIT
	1;

-- 7) Show all customers who ordered more than 1 quantity of a book:
SELECT
	*
FROM
	ORDERS
WHERE
	QUANTITY > 1;

-- 8) Retrieve all orders where the total amount exceeds $20:
SELECT
	*
FROM
	ORDERS
WHERE
	TOTAL_AMOUNT >20;

-- 9) List all genres available in the Books table:
SELECT DISTINCT
	(GENRE)
FROM
	BOOKS;

-- 10) Find the book with the lowest stock:
SELECT
	*
FROM
	BOOKS
ORDER BY
	STOCK
LIMIT
	1;

-- 11) Calculate the total revenue generated from all orders:
SELECT
	SUM(TOTAL_AMOUNT) AS REVENUE
FROM
	ORDERS;
    

-- ADVANCE QUESTIONS : 
 -- 1) Retrieve the total number of books sold for each genre:
SELECT
	B.GENRE,
	SUM(O.QUANTITY)
FROM
	ORDERS O
	JOIN BOOKS B ON O.BOOK_ID = B.BOOK_ID
GROUP BY
	B.GENRE;

-- 2) Find the average price of books in the "Fantasy" genre:
SELECT
	AVG(PRICE) AS AVERGE_PRICE
FROM
	BOOKS
WHERE
	GENRE = 'Fantasy';


-- 3) List customers who have placed at least 2 orders:
SELECT o.customer_id, c.name, COUNT(o.Order_id) AS ORDER_COUNT
FROM orders o
JOIN customers c ON o.customer_id=c.customer_id
GROUP BY o.customer_id, c.name
HAVING COUNT(Order_id) >=2;

-- 4) Find the most frequently ordered book:
select o.book_id,b.title,count(o.order_id) as order_count 
from orders o
join books b  on o.book_id=b.book_id
group by o.book_id,b.Title 
order by order_count desc  limit 1 ;

select * from books;
select * from customers;
select * from orders;

-- 5) Show the top 3 most expensive books of 'Fantasy' Genre :
SELECT
	*
FROM
	BOOKS
WHERE
	GENRE = 'Fantasy'
ORDER BY
	PRICE DESC LIMIT 3;

-- 6) Retrieve the total quantity of books sold by each author:
SELECT
	B.AUTHOR,
	SUM(O.QUANTITY) AS TOTAL_SOLD_BOOK
FROM
	BOOKS B
	JOIN ORDERS O ON O.BOOK_ID = B.BOOK_ID
GROUP BY
	B.AUTHOR;

-- 7) List the cities where customers who spent over $30 are located:
select distinct c.city,o.total_amount
from customers c
join orders o on c.customer_id=o.customer_id 
where o.total_amount >30;
-- 8) Find the customer who spent the most on orders:
SELECT
	C.CUSTOMER_ID,
	C.NAME,
	SUM(O.TOTAL_AMOUNT) AS TOTAL_SPENT
FROM
	CUSTOMERS C
	JOIN ORDERS O ON C.CUSTOMER_ID = O.CUSTOMER_ID
GROUP BY
	C.CUSTOMER_ID,
	C.NAME
ORDER BY
	TOTAL_SPENT DESC
LIMIT 1;

--9) Calculate the stock remaining after fulfilling all orders:
SELECT
	B.BOOK_ID,
	B.TITLE,
	B.STOCK,
	COALESCE(SUM(O.QUANTITY), 0) AS ORDER_QUANTITY,
	B.STOCK - COALESCE(SUM(O.QUANTITY), 0) AS REMAINING_STOCK
FROM
	BOOKS B
	LEFT JOIN ORDERS O ON B.BOOK_ID = O.BOOK_ID GROUP BY
	B.BOOK_ID;
