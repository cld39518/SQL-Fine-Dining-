# SQL Fine Dining Project
SQL fine dining database project

# Members
Christian Myrie,Ben Satinoff, Josh Berman, Chandler Denton 

# About Us
We’re an upscale, fine dining restaurant that is reservation only. We occasionally lease out our space for guests to hosts events and entertainment, and we also offer catering services. We have six different restauraunt locations. Our data model displays the functions of our Fine Dining, reservation only, 6 restaurant franchise. To increase sales for our company and grow our customer demographic, our CEO decided to implement catering services with its own specific menu. Additionally, due to our CEO’s love for nonprofits, he allows one non-profit to use the restaurant to host a nonprofit event, free of charge. In the near future, we look to expand into takeout ordering for our customers as well as bringing our “classic sauce” to grocery stores by branding it.


# Data Model:
<img width="593" alt="Screenshot 2023-03-31 at 4 35 07 PM" src="https://user-images.githubusercontent.com/95833587/229224537-c4e03ffd-37c9-435e-a68d-d2b7a5f9ba32.png">

# Restaurant:
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

<img width="1033" alt="Screenshot 2023-03-31 at 6 44 03 PM" src="https://user-images.githubusercontent.com/129543675/229245540-2df1275f-e18a-4086-bb9c-b55273d232ed.png">

# Query 1
#This query is used to determine the bill total for a particular guess. The manager could use this query to find out how much certain guests are willing to spend when they come to the restaurant

CREATE PROCEDURE TP_Q0()
SELECT CONCAT(("$"),orderQuantity*menuPrice), customerName
FROM Customers
JOIN Orders ON Orders.customerID = Customers.customerID
JOIN Menu ON Orders.menuID = Menu.menuID;

# Query 2
#This query allows us to know what ingredients go into each of the catering menu items. This is because the catering menu is created by the customer. Therefore, by being able to see what ingredients they have requested in the past, we can prepare for possible future catering needs. We did this by having the name of the meal and the ingredients in the select statement, and then ordered by 

CREATE PROCEDURE TP_Q2()
SELECT cmIngredients, cmItemName
FROM CateringMenu
ORDER BY cmIngredients DESC;

# Query 3
#This query shows each of menu items and the maximum amount of each item that was purchased. This allows us to see which of the items are the most popular and which ones we should really focus on in the future. We did this by having a maximum function in the select statement with orderQuantity and also listed the item name. In order to get the query to work we had to join together the Orders table and the Menu table and grouped it by the item name.

CREATE PROCEDURE TP_Q3()
SELECT MAX(orderQuantity), menuItemName
FROM Orders
JOIN Menu ON Orders.menuID = Menu.menuID
GROUP BY menuItemName; 

# Query 4
#These are all of the customers that ate at the restaurant. Not all of our customers are from just the restaurant so it is useful to know which customers are soley eating at the restaurant rather than through catering or events. We did that by selecting the customers name so we know the names of which specific customers are eating there. Then we joined customers to restaurant and did an exist statement for restaurantID. If restaurantID does exist then that means that they ate at the restaurant.

CREATE PROCEDURE TP_Q4()
SELECT customerName
FROM Customers
JOIN Restaurant ON Customers.restaurantID = Restaurant.rId
WHERE EXISTS (SELECT restaurantID FROM Customers);

# Query 5
#This query tells us how many of each item is greater than the average price of the items on our menu. By doing this we can figure out which items are our more expensive items, and we can focus on trying to make those as high quality as we can. In order to find this out we counted how many total menu items there are on the menu. Then we did a subquery to find what the average menu price is and compared that to all of the menu items to find which items cost more than the average price.

CREATE PROCEDURE TP_Q5()
SELECT COUNT(*), menuItemName
FROM Menu
WHERE menuPrice > (SELECT AVG(menuPrice) FROM Menu)
GROUP BY menuItemName;

# Query 6 
#This query allows us to tell the amount of food that each employee sold. This is useful because from this we can tell how useful each employee is and how much money they are bringing in. We did this by getting the sum of the menu price multiplied by the quantity of the items they ordered. We then joined the employees table, orders table, and the menu table together so we could get the menu item and the price, combined with the order that was place, and which employee was involved.

CREATE PROCEDURE TP_Q6()
SELECT employeeName, SUM(menuPrice*orderQuantity) AS 'Bill Price'
FROM Employees
JOIN Orders ON Employees.orderID = Orders.orderID
JOIN Menu ON Orders.menuID = Menu.menuID
GROUP BY Employees.employeeID
ORDER BY SUM(menuPrice*orderQuantity) DESC;

# Query 7
#This query shows how many of each customer who catered had special requirements, and what those requirements were. This allows us to know what meals we should be ready to make in the future. Not all customers will necessarily have what we are serving and we need to be prepared to serve their needs. We were able to do this by using case when and regular expression statements.

CREATE PROCEDURE TP_Q7()
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

CREATE PROCEDURE TP_Q8()
SELECT menuItemName, CONCAT(ROUND(100*(orderQuantity)/(SELECT COUNT(orderQuantity) FROM Orders),2),("%")) AS 'Percent Ordered'
FROM Customers 
JOIN Orders ON Customers.customerID = Orders.customerID
JOIN Menu ON Orders.menuID = Menu.menuID;

# Query 9
#This query is useful because it allows the manager to see how many reservations we had on a specific day, who it is for, and where they are sitting. By knowing this we can prepare for what we think the specific amount of guests we will have, how we could contact them if we need to, and information we could possibly need to give them such as what table they will be seated at. We did this by listing out the customer name, customer phone, number of guests on the reservations, and tableID. We then had to join customers, reservations, and tables together and say the specific date we were looking for.

CREATE PROCEDURE TP_Q9()
SELECT resNumOfGuests, customerPhone, customerName, tableID
FROM Customers
JOIN Reservations ON Reservations.customerID = Customers.customerID
JOIN Tables ON Reservations.reservationID = Tables.reservationID
WHERE resDate = '2022-07-10';

# Query 10
#This query allows us to figure out how many employees we have in each position. By knowing this we can figure out how well we operate with a specific amount of employees. If we are not operating as well as we should and see that one group does not have enough employees and hire more. We did this by using case when statements for each of the different employee types.

CREATE PROCEDURE TP_Q10()
SELECT 
COUNT(CASE WHEN employeeTitle REGEXP("Chef") THEN employeeTitle END) AS "Chef", 
COUNT(CASE WHEN employeeTitle REGEXP("Waiter") THEN employeeTitle END) AS "Waiter", 
COUNT(CASE WHEN employeeTitle REGEXP("Host") THEN employeeTitle END) AS "Host",
COUNT(CASE WHEN employeeTitle NOT REGEXP("Chef|Waiter|Host") THEN employeeTitle END) AS "Unknown"
FROM Employees;

CALL TP_Q0;
CALL TP_Q2;
CALL TP_Q3;
CALL TP_Q4;
CALL TP_Q5;
CALL TP_Q6;
CALL TP_Q7;
CALL TP_Q8;
CALL TP_Q9;
CALL TP_Q10;


# Total Database Code

USE ns_21482_5;



insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('90895', 'Merrielle Mucci', '899-598-0268', '5048377171302941', '76005', NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('86256', 'Laney Ashenhurst', '320-430-9877', '5108756335734239', '41699', NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('84418', 'Georgie Gorick', '815-487-8704', '5048376972509050', '69636', NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('76111', 'Maggie Pogg', '119-351-0981', '5048373079848945', '42057', NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('64598', 'Carmelle Pietrasik', '599-369-1210', '5108751100581303', '34694', NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID,cateringID, eventID) values ('40889', 'Haskell Scheu', '986-409-8349', '5108758330119440', '16548', NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('22823', 'Maddie Solano', '703-213-0087', '5048370190420752', NULL, '12374', NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('59997', 'Trude Gidman', '508-514-7047', '5048377146810747', NULL, '37917', NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('37725', 'Amalle Jain', '847-993-3985', '5048376854429625', NULL, '30700', NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('00053', 'Dani Bale', '252-384-0341', '5108755941397654', NULL, '94656', NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('57158', 'Fredericka Horning', '687-990-0108', '5048376437087858', NULL, '42154', NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('97796', 'Rose Mallindine', '339-404-1902', '5048372630415889', NULL, '46785', NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('89445', 'Anestassia Gregh', '661-204-4381', '5108750825853559', NULL, NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('19701', 'Florrie Hise', '796-971-3625', '5108754441638261', NULL, NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('84757', 'Shanon Osmond', '599-954-0498', '5108754713761874',NULL, NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('52727', 'Barb Oliveto', '417-545-8660', '5108752883884534', NULL, NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('09940', 'Avril Suttie', '324-170-5944', '5108751283513305', NULL, NULL, NULL);
insert into Customers (customerID, customerName, customerPhone, customerPaymentInfo, restaurantID, cateringID, eventID) values ('91039', 'Maggi Zannetti', '981-417-2346', '5048372473991236', NULL, NULL, NULL);



insert into Catering (cateringID, cateringLocation, cateringSpecialRequirements, cateringPrice) values ('12374', '4 Westport Plaza', 'Kosher', '4500');
insert into Catering (cateringID, cateringLocation, cateringSpecialRequirements, cateringPrice) values ('37917', '380 Sauthoff Parkway', 'Vegetarian', '5250');
insert into Catering (cateringID, cateringLocation, cateringSpecialRequirements, cateringPrice) values ('30700', '9 Hazelcrest Drive', 'Vegan', '5100');
insert into Catering (cateringID, cateringLocation, cateringSpecialRequirements, cateringPrice) values ('94656', '57 Merry Court', 'Gluten Free', '5750');
insert into Catering (cateringID, cateringLocation, cateringSpecialRequirements, cateringPrice) values ('42154', '1450 Helena Park', 'Peanut Free', '4250');
insert into Catering (cateringID, cateringLocation, cateringSpecialRequirements, cateringPrice) values ('46785', '7 Northfield Point', 'Gluten Free', '4750');


insert into Reservations (reservationID, customerID, resDate, resNumOfGuests) values ('13488', '22823', '2023-02-13', 4);
insert into Reservations (reservationID, customerID, resDate, resNumOfGuests) values ('62177', '59997', '2022-08-07', 3);
insert into Reservations (reservationID, customerID, resDate, resNumOfGuests) values ('80850', '37725', '2022-12-23', 3);
insert into Reservations (reservationID, customerID, resDate, resNumOfGuests) values ('79981', '00053', '2022-07-10', 8);
insert into Reservations (reservationID, customerID, resDate, resNumOfGuests) values ('27135', '57158', '2022-05-06', 5);
insert into Reservations (reservationID, customerID, resDate, resNumOfGuests) values ('04676', '97796', '2022-04-09', 6);



insert into Tables (tableID, reservationID, tableNumber, tableSeatCapacity, tableStatus) values ('94855', '13488', 1, 6, true);
insert into Tables (tableID,  reservationID, tableNumber, tableSeatCapacity, tableStatus) values ('68404', '62177', 2, 10, false);
insert into Tables (tableID,  reservationID, tableNumber, tableSeatCapacity, tableStatus) values ('70859', '80850', 3, 8, false);
insert into Tables (tableID,  reservationID, tableNumber, tableSeatCapacity, tableStatus) values ('79985', '79981', 4, 4, false);
insert into Tables (tableID,  reservationID, tableNumber, tableSeatCapacity, tableStatus) values ('81221', '27135', 5, 5, true);
insert into Tables (tableID, reservationID, tableNumber, tableSeatCapacity, tableStatus) values ('82800', '04676', 6, 6, true);




insert into Orders (orderID, orderQuantity, orderItem, customerID, menuID) values ('82333', 1, 'Lamb Chops', '22823', '1234');
insert into Orders (orderID, orderQuantity, orderItem, customerID, menuID) values ('10491', 2, 'Filet Mignon','59997', '1235');
insert into Orders (orderID, orderQuantity, orderItem, customerID, menuID) values ('20635', 3, 'Herb-Grilled Chicken', '37725', '1236');
insert into Orders (orderID, orderQuantity, orderItem, customerID, menuID) values ('68706', 1, 'Prime Rib', '00053', '1237');
insert into Orders (orderID, orderQuantity, orderItem, customerID, menuID) values ('61647', 2, 'Stuffed Lobster', '57158', '1238');
insert into Orders (orderID, orderQuantity, orderItem, customerID, menuID) values ('20739', 1, 'Roast duck', '97796', '1239');






insert into Restaurant (rId, rName, rAddress, rPhoneNumber, rWebsite) values ('76005', 1, '921 Grayhawk Parkway', '579-895-7179', 'http://dummyimage.com');
insert into Restaurant (rId, rName, rAddress, rPhoneNumber, rWebsite) values ('41699', 2, '4447 Granby Crossing', '429-924-1808', 'http://dummyimage.com');
insert into Restaurant (rId, rName, rAddress, rPhoneNumber, rWebsite) values ('69636', 3, '0009 Everett Way', '208-994-0177', 'http://dummyimage.com');
insert into Restaurant (rId, rName, rAddress, rPhoneNumber, rWebsite) values ('42057', 4, '449 Evergreen Place', '818-708-1852', 'http://dummyimage');
insert into Restaurant (rId, rName, rAddress, rPhoneNumber, rWebsite) values ('34694', 5, '0 Derek Drive', '797-956-9598', 'http://dummyimage.com');
insert into Restaurant (rID, rName, rAddress, rPhoneNumber, rWebsite) values ('16548', 6, '2050 Melody Junction', '306-302-9411', 'http://dummyimage.com');




insert into Menu (menuID, menuItemName, menuDescription, menuIngredients, menuPrice) values ('1234', 'Lamb Chops', 'tender loin chops', 'garlic, olive oil, red pepper, lamb', 122);
insert into Menu (menuID, menuItemName, menuDescription, menuIngredients, menuPrice) values ('1235', 'Filet Mignon', 'pork tenderloin', 'rosemary, butter, garlic', 167);
insert into Menu (menuID, menuItemName, menuDescription, menuIngredients, menuPrice) values ('1236', 'Herb-Grilled Chicken', 'chicken grilled with herbs', 'olive oil, garlic, chicken, lemon', 77);
insert into Menu (menuID, menuItemName, menuDescription, menuIngredients, menuPrice) values ('1237', 'Prime Rib', 'standing rib roast', 'rib roast, black pepper', 140);
insert into Menu (menuID, menuItemName, menuDescription, menuIngredients, menuPrice) values ('1238', 'Stuffed Lobster', 'lobster tails', 'lemon juice, garlic, crab meat,', 190);
insert into Menu (menuID, menuItemName, menuDescription, menuIngredients, menuPrice) values ('1239', 'Roast duck', 'duck roast', 'soy sauce, black pepper, pekin duck', 173);




insert into Employees (employeeId, rID, orderID, tableID, employeeName, employeePhone, employeeTitle, employeeSchedule, employeeSalary) values ('83071', '76005', '82333', '94855', 'Erinn Gadman', '318-833-7478', 'Chef', 'Afternoon', '126325.25');
insert into Employees (employeeId, rID, orderID, tableID, employeeName, employeePhone, employeeTitle, employeeSchedule, employeeSalary) values ('02784', '41699', '10491', '68404', 'Celine Kingston', '640-242-1956', 'Chef', 'Evening', '$32112.95');
insert into Employees (employeeId, rID, orderID, tableID, employeeName, employeePhone, employeeTitle, employeeSchedule, employeeSalary) values ('76072', '69636', '20635', '70859', 'Egbert Onthank', '875-373-5916', 'Waiter', 'Morning', '$52900.61');
insert into Employees (employeeId, rID, orderID, tableID, employeeName, employeePhone, employeeTitle, employeeSchedule, employeeSalary) values ('18881', '42057', '68706', '79985', 'Mitchell Teck', '357-794-8169', 'Waiter', 'Afternoon', '$79981.16');
insert into Employees (employeeId, rID, orderID, tableID, employeeName, employeePhone, employeeTitle, employeeSchedule, employeeSalary) values ('76220', '34694', '61647', '81221', 'Brnaba Issett', '531-421-2692', 'Host', 'Afternoon', '$22132.06');
insert into Employees (employeeId, rID, orderID, tableID, employeeName, employeePhone, employeeTitle, employeeSchedule, employeeSalary) values ('68777', '16548', '20739', '82800', 'Moishe Mollin', '821-355-6392', 'Host', 'Morning', '$24357.92');



insert into CateringMenu (cmID, orderID, cateringID, cmItemName, cmItemDescription, cmIngredients) values ('9876', '82333', '12374', 'steak', 'tender', 'garlic');
insert into CateringMenu (cmID, orderID, cateringID, cmItemName, cmItemDescription, cmIngredients) values ('9875', '10491', '37917', 'broccoli' , 'steamed', 'butter');
insert into CateringMenu (cmID, orderID, cateringID, cmItemName, cmItemDescription, cmIngredients) values ('9874', '20635', '30700', 'potatoes', ' sweet', 'basil');
insert into CateringMenu (cmID, orderID, cateringID, cmItemName, cmItemDescription, cmIngredients) values ('9873', '68706', '94656', 'salmon', ' honey-glazed', 'mushroom’');
insert into CateringMenu (cmID, orderID, cateringID, cmItemName, cmItemDescription, cmIngredients) values ('9872', '61647', '42154', 'fettuccine alfredo', 'creamy', 'beef');
insert into CateringMenu (cmID, orderID, cateringID, cmItemName, cmItemDescription, cmIngredients) values ('9871', '20739', '46785', 'chicken marsala', ' flavorful', 'pepper');


Insert into Events (eventID, eventName, eventDate, eventType) values ('11111', 'Laugh Riot',2022-06-09, 'comedy');
Insert into Events (eventID, eventName,eventDate, eventType) values ('22222', 'Swinging Soiree',2023-03-06, 'jazzband');
Insert into Events (eventID, eventName, eventDate, eventType) values ('33333',  'Harmony Fest',2022-04-09, 'concert' );
Insert into Events (eventID, eventName, eventDate, eventType) values ('44444', 'Ivory Keys' ,2022-09-21, 'pianist');
Insert into Events (eventID, eventName, eventDate, eventType) values ('55555', 'Community Connect', 2022-05-06, 'social');
Insert into Events (eventID, eventName, eventDate, eventType) values ('66666' , 'Starry Night' , 2022-04-23, 'gala' );


















