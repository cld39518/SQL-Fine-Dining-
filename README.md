# SQL-Fine-Dining-
SQL fine dining database project

# Members
Christian Myrie,Ben Satinoff, Josh Berman, Chandler Denton 

# About Us
We’re an upscale, fine dining restaurant that is reservation only. We occasionally lease out our space for guests to hosts events and entertainment, and we also offer catering services. We have six different restauraunt locations. Our data model displays the functions of our Fine Dining, reservation only, 6 restaurant franchise. To increase sales for our company and grow our customer demographic, our CEO decided to implement catering services with its own specific menu. Additionally, due to our CEO’s love for nonprofits, he allows one non-profit to use the restaurant to host a nonprofit event, free of charge. In the near future, we look to expand into takeout ordering for our customers as well as bringing our “classic sauce” to grocery stores by branding it.


# Data Model:
<img width="593" alt="Screenshot 2023-03-31 at 4 35 07 PM" src="https://user-images.githubusercontent.com/95833587/229224537-c4e03ffd-37c9-435e-a68d-d2b7a5f9ba32.png">

# Restaurant
We have six different restaurant locations across different regions, each having a name, address, phone number, and website. The Restaurant table has a one to many relationship with Customers and a many-to-many relationship with Tables. This forms a one-to-many relationship with the Employees table. To prevent long commutes to the restaurant, each restaurant requires a set number of employees to be staffed exclusively at one of the six restaurants. 


# Employees:
There is a one to many relationship between Restaurants and Employees as stated above. There is a one to many relationship between orders and employees. One order is handled by many employees, such as the waiter/waitress, the chef, and the host/hostess.
There is a one to many relationship between tables and employees. For each table there are several employees responsible for satisfying the needs of the specific table. The employees required to provide excellent service to a table include waiter, food runner, and table busser. 

# Menu: 
There is a many to many relationship between menu and customers forming a one to many relationship between menu and orders forming the associative entity Orders. There are different menus unique to each of the six restaurants, likewise there are different customers that will reap the benefits of the options listed on each restaurant's specific menu. Additionally, because each restaurant has their own menu, there can be many orders from each of the restaurants specific and individual menu.

# Tables:
Tables has a many-to-many with Restaurants, forming the Employees associated entity. One table has many employees operating on it such as the hosts/hostesses, waitor/waitresses, and the chefs making the meals being prepared for the table. Reservations have a one-to-many relationship with tables. There is only one unique reservation per table but that reservation can happen at any time, and some of our guests have repeat reservations where they can set the same reservation every week at the same table through our Loyalty Program.

# Reservations:
Customers have a one to many relationship with reservations, additionally reservations have a one to many with tables. One reservation is required for a group containing many customers such as a family or a company social event. Also, one table will be the home of a night of memories to several reservations because there are multiple time windows that a reservation can be created for a specific table. 

# Events: 
The relationship between events to customers is a one-to-many relationship. When a nonprofit reserves the restaurant space to host an event, there can be many customers, but only to that one specific event since customers unfortunately cannot be at two places at once. 

# Catering:
There is a many to many relationship between catering and orders creating the associative entity, catering menus. When an organization, for example, requests catering services, which many requests can be put in, there are many orders that can be put in to suffice the need. Consequently, a specific ordering menu is offered to give these catering orders a taste of what our restaurant chain is all about. A quick side note is that we do not offer the same dishes on the catering menu as we do on our regular in-house menu because of our marketing strategy. We want people to explore all of the flavorful possibilities our restaurant offers, so to encourage them to come into our restaurant and try the inhouse menu, we only give them a glimpse of what the in house menu offers.

# CateringMenu:
There is the associate entity between orders and catering, birthing Catering Menu. This offers a unique journey for the catered audience’s taste buds to experience. 

# Orders: 
Orders has a many-to-many relationship with catering, forming the CateringMenu associated entity. It is also the associated entity between customer and menu. Customers can order as many menu items and as many times as they please.

# Query 1
#This query is used to determine the bill total for a particular guess. The manager could use this query to find out how much certain guests are willing to spend when they come to the restaurant

SELECT CONCAT(("$"),orderQuantity*menuPrice), customerName
FROM Customers
JOIN Orders ON Orders.customerID = Customers.customerID
JOIN Menu ON Orders.menuID = Menu.menuID;

# Query 2
#This query allows us to know what ingredients go into each of the catering menu items. This is because the catering menu is created by the customer. Therefore, by being able to see what ingredients they have requested in the past, we can prepare for possible future catering needs. We did this by having the name of the meal and the ingredients in the select statement, and then ordered by 

SELECT cmIngredients, cmItemName
FROM CateringMenu
ORDER BY cmIngredients DESC;

# Query 3
#This query shows each of menu items and the maximum amount of each item that was purchased. This allows us to see which of the items are the most popular and which ones we should really focus on in the future. We did this by having a maximum function in the select statement with orderQuantity and also listed the item name. In order to get the query to work we had to join together the Orders table and the Menu table and grouped it by the item name.

SELECT MAX(orderQuantity), menuItemName
FROM Orders
JOIN Menu ON Orders.menuID = Menu.menuID
GROUP BY menuItemName; 

# Query 4
#These are all of the customers that ate at the restaurant. Not all of our customers are from just the restaurant so it is useful to know which customers are soley eating at the restaurant rather than through catering or events. We did that by selecting the customers name so we know the names of which specific customers are eating there. Then we joined customers to restaurant and did an exist statement for restaurantID. If restaurantID does exist then that means that they ate at the restaurant.

SELECT customerName
FROM Customers
JOIN Restaurant ON Customers.restaurantID = Restaurant.rId
WHERE EXISTS (SELECT restaurantID FROM Customers);

# Query 5
#This query tells us how many of each item is greater than the average price of the items on our menu. By doing this we can figure out which items are our more expensive items, and we can focus on trying to make those as high quality as we can. In order to find this out we counted how many total menu items there are on the menu. Then we did a subquery to find what the average menu price is and compared that to all of the menu items to find which items cost more than the average price.

SELECT COUNT(*), menuItemName
FROM Menu
WHERE menuPrice > (SELECT AVG(menuPrice) FROM Menu)
GROUP BY menuItemName;

# Query 6 
#This query allows us to tell the amount of food that each employee sold. This is useful because from this we can tell how useful each employee is and how much money they are bringing in. We did this by getting the sum of the menu price multiplied by the quantity of the items they ordered. We then joined the employees table, orders table, and the menu table together so we could get the menu item and the price, combined with the order that was place, and which employee was involved.

SELECT employeeName, SUM(menuPrice*orderQuantity) AS 'Bill Price'
FROM Employees
JOIN Orders ON Employees.orderID = Orders.orderID
JOIN Menu ON Orders.menuID = Menu.menuID
GROUP BY Employees.employeeID
ORDER BY SUM(menuPrice*orderQuantity) DESC;

# Query 7
#This query shows how many of each customer who catered had special requirements, and what those requirements were. This allows us to know what meals we should be ready to make in the future. Not all customers will necessarily have what we are serving and we need to be prepared to serve their needs. We were able to do this by using case when and regular expression statements.

SELECT 
	COUNT(CASE WHEN cateringSpecialRequirements REGEXP("Peanut Free") THEN cateringSpecialRequirements END) AS "Allergic", 
    COUNT(CASE WHEN cateringSpecialRequirements REGEXP("Gluten Free") THEN cateringSpecialRequirements END) AS "Allergic", 
    COUNT(CASE WHEN cateringSpecialRequirements REGEXP("Vegan") THEN cateringSpecialRequirements END) AS "Allergic",
	COUNT(CASE WHEN cateringSpecialRequirements REGEXP("Vegetarian") THEN cateringSpecialRequirements END) AS "Allergic",
    COUNT(CASE WHEN cateringSpecialRequirements REGEXP("Kosher") THEN cateringSpecialRequirements END) AS "Allergic",
	COUNT(CASE WHEN cateringSpecialRequirements NOT REGEXP("Peanut Free|Gluten Free|Vegan|Vegetarian|Kosher") THEN cateringSpecialRequirements END) AS "Not Allergic"
FROM Catering;

# Query 8
#This query is used to figure out which menu items are ordered the most. It is useful to know this first of all to know the popularity of each item but also for the sake of recommendations. We can tell the customers which items are the most popular. We did this by putting the quantity ordered by each person over the total count of all of the quantities ordered.

SELECT menuItemName, CONCAT(ROUND(100*(orderQuantity)/(SELECT COUNT(orderQuantity) FROM Orders),2),("%")) AS 'Percent Ordered'
FROM Customers 
JOIN Orders ON Customers.customerID = Orders.customerID
JOIN Menu ON Orders.menuID = Menu.menuID;

# Query 9
#This query is useful because it allows the manager to see how many reservations we had on a specific day, who it is for, and where they are sitting. By knowing this we can prepare for what we think the specific amount of guests we will have, how we could contact them if we need to, and information we could possibly need to give them such as what table they will be seated at. We did this by listing out the customer name, customer phone, number of guests on the reservations, and tableID. We then had to join customers, reservations, and tables together and say the specific date we were looking for.

SELECT resNumOfGuests, customerPhone, customerName, tableID
FROM Customers
JOIN Reservations ON Reservations.customerID = Customers.customerID
JOIN Tables ON Reservations.reservationID = Tables.reservationID
WHERE resDate = '2022-07-10';















