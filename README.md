# AFoodDeliveryApplication
A Food Delivery Application(with Microservices, Spring Cloud, RabbitMQ, NoSQL, SQL)

## 1 Change Log
Version 1.0 released on 28th March 2017.

## 2 objectives
* search restaurant
User can search a restaurant based on restaurant name.
* order food
Then user can order food by choosing different menu item, quantity and add a note about his/her diet restrictions and etc. User can also fills in the delivery address. After user places an order, the order should contain food items user ordered, quantity, price and order time. 
* payment 
User then needs to pay for his/her order by providing credit card number, expiration date, and security code. 
* delivery
After payment is made successfully, it should return payment ID, timestamp and then the order is considered as completed so the user can see the estimated delivery time.

## 3 microservices


## 4 RESTful State diagram(some parts are simplified to focus on xxx)
![restaurant order and payment](https://cloud.githubusercontent.com/assets/17025949/25528059/1b4042ac-2c5f-11e7-9db6-3ee56c450916.jpg)



