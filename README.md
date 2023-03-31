# SQL-Fine-Dining-
SQL fine dining database project

Christian Myrie:
Ben Satinoff:
Josh Berman
Chandler Denton: 

We’re an upscale, fine dining restaurant that is reservation only. We occasionally lease out our space for guests to hosts events and entertainment, and we also offer catering services. We have six different restauraunt locations. Our data model displays the functions of our Fine Dining, reservation only, 6 restaurant franchise. To increase sales for our company and grow our customer demographic, our CEO decided to implement catering services with its own specific menu. Additionally, due to our CEO’s love for nonprofits, he allows one non-profit to use the restaurant to host a nonprofit event, free of charge. In the near future, we look to expand into takeout ordering for our customers as well as bringing our “classic sauce” to grocery stores by branding it.



<img width="593" alt="Screenshot 2023-03-31 at 4 35 07 PM" src="https://user-images.githubusercontent.com/95833587/229224537-c4e03ffd-37c9-435e-a68d-d2b7a5f9ba32.png">

Restaurant : We have six different restaurant locations across different regions, each having a name, address, phone number, and website. The Restaurant table has a one to many relationship with Customers and a many-to-many relationship with Tables. This forms a one-to-many relationship with the Employees table. To prevent long commutes to the restaurant, each restaurant requires a set number of employees to be staffed exclusively at one of the six restaurants. 


Employees: There is a one to many relationship between Restaurants and Employees as stated above. There is a one to many relationship between orders and employees. One order is handled by many employees, such as the waiter/waitress, the chef, and the food runner.
There is a one to many relationship between tables and employees. For each table there are several employees responsible for satisfying the needs of the specific table. The employees required to provide excellent service to a table include waiter, food runner, and table busser. 


