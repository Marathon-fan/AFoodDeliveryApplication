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
* microservices1 -- Customer Order(menu, make order)  
User can see menu and order food.
* microservices2 -- Payment  
Finish the payment.
* microservices3 -- Delivery  
Prepare food and deliver food. Since this process(microservices3) is often slower than microservices1(customer order process)(for instance, when we order pizza, we always need to wait for pizza to be baked in the oven and delivered. In the peak hour, the deliver staff will be very busy),  we use a message queue to decouple and asynchronize them.
* microservices4 -- Restaurant Discovery(find and show all the restaurants)   
For the sake of simplicity, the data store and logic here are omitted.
* microservices5 -- User(registration and login, logout)  
For the sake of simplicity, the data store and logic here are omitted.


## 4 RESTful State diagram(some parts are simplified to focus on the main logic)

* microservices1 -- Customer Order(menu, make order)  
| microservice1 resources | GET read                      |                 POST create                | PUT update                    | DELETE delete                |
|-------------------------|-------------------------------|:------------------------------------------:|-------------------------------|------------------------------|
| /Rests                  | Returns a list of Restaurant  |                                            |                               |                              |
| /Rests/RestId           | Returns a specific Restaurant | Create/update a new Restaurant(idempotent) | Updates a specific Restaurant | Delete a specific Restaurant |

* GET  
Get a representation of the resource
* POST  
Create a new resource. For instance, when we order food, we use POST
* PUT
Replace the state of a resouce.

![restaurant order and payment](https://cloud.githubusercontent.com/assets/17025949/25528059/1b4042ac-2c5f-11e7-9db6-3ee56c450916.jpg)



