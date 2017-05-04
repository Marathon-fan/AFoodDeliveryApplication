# AFoodDeliveryApplication
A Food Delivery Application(with Microservices, RESTful, Spring Cloud, RabbitMQ, NoSQL(MongoDB), SQL, Eureka, Hystrix)

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

| microservice1 resources          | GET (read)                                      |                POST (create)               | PUT (update)                                                                                     | DELETE (delete)              |
|----------------------------------|-------------------------------------------------|:------------------------------------------:|--------------------------------------------------------------------------------------------------|------------------------------|
| /Rests                           | Returns a list of Restaurant                    |                                            |                                                                                                  |                              |
| /Rests/RestId                    | Returns a specific Restaurant                   | Create/update a new Restaurant(idempotent) | Updates a specific Restaurant                                                                    | Delete a specific Restaurant |
| /Rests/RestId/MenuList           | Returns this restaurant's menu                  |                                            |                                                                                                  |                              |
| /Rests/RestId/FoodDetails        | Returns this restaurant's all food details      |                                            |                                                                                                  |                              |
| /Rests/RestId/FoodDetails/FoodId | Returns this restaurant's specific food details | Create/update a new food with detail       | Updates specific food                                                                            | Delete specific food         |
| /Rests/RestId/Orders/            | Returns this restaurant's all orders            |                                            |                                                                                                  |                              |
| /Rests/RestId/Orders/OrderId     | Returns this restaurant's specific order        | Create/update a new order                  | Update this order's state 0) UNPAID  1) PAID_UNDELIVERED 2) DELIVERING 3) DELIVERED 4) CANCELLED | Delete an order              |
|                                  |                                                 |                                            |                                                                                                  |                              |
* microservices2 -- Payment 

| microservice2 resources | GET (read)                 |       POST (create)       | PUT (update)                                                                     | DELETE (delete)           |
|-------------------------|----------------------------|:-------------------------:|----------------------------------------------------------------------------------|---------------------------|
| /payments               | Returns a list of payments |                           |                                                                                  |                           |
| /payments/paymentId     | Returns a specific payment | Create a specific payment | Update this payment's state 0) PAYMENT_SUCCESS,1) PAYMENT_FAILURE 2) OTHER_STATE | Delete a specific payment |

* microservices3 -- Delivery   

| microservice3 resources | GET  | POST | PUT  | DELETE (delete) |
|-----------------------------------------------|--------------------------------------------|:----------------------------------:|------------------------------------------------------------------------------------------|-------------------------------------------|
| /delivery/estimatedDeliveryTime/v1 | Returns a list of estimated delivery time |  |  |  |
| /delivery/estimatedDeliveryTime/v1/deliveryId | Returns a specific estimated delivery Time | Create a specific payment |  | Delete a specific estimated delivery Time |
| /delivery/deliveryTime/v2 | Future version |  |  |  |
| /delivery/deliveryProcesses/v1 | Returns a list of delivery processes |  |  |  |
| /delivery/deliveryProcesses/v1/deliveryId | Returns a specific delivery process | Create a specific delivery process | Update this delivery process' state 0) PREPARING, 1) ON_GOING 2) FINISHED 3) OTHER STATE |  |

* microservices4 -- Restaurant Discovery(find and show all the restaurants)

For the sake of simplicity, we don't list the RESTful States for microservices4.

* microservices5 -- User(registration and login, logout)

For the sake of simplicity, we don't list the RESTful States for microservices5.

![restaurant order and payment](https://cloud.githubusercontent.com/assets/17025949/25528059/1b4042ac-2c5f-11e7-9db6-3ee56c450916.jpg)


## 5 Microservices calling each other

_(only critical calls are listed here. You can also see how we handle payment failure here)_ 
* microservices1(Customer Order) calling other microservices 

1. Once an order is created, microservices1 will call microservices2(Payment) to finish the corresponding payment

* microservices2(Payment) calling other microservices 

1. Once a payment is successful, microservices2 will call microservices3(Delivery) to prepare food and deliver it. Also, microservices2 will call microservices3(Delivery) to generate an estimated delivery time so that the customer knows how long possibly will be needed for the delivery process.  
2. Once a payment is failed, microservices2 will call microservices1(Customer Order) to to handle payment error case. Here to make it simple, we change original OrderId's state to 'CANCELLED'.(In practice, we may relaunch a payment logic or use other business logic, though )

* microservices3(Delivery) calling other microservices 
1. Once a delivery process is finished, microservices3(Delivery) will call microservices1(Customer Order) to update the original order's state to 'DELIVERED'.

* microservices4 -- Restaurant Discovery(find and show all the restaurants)

To make it simple, we don't list microservices4's calling other microservices.

* microservices5 -- User(registration and login, logout)

To make it simple, we don't list microservices5's calling other microservices.

## x Microservice Registration and Discovery

_Spring Cloud and Netflix's Eureka_

## x Microservice fault tolerance
_hystrix_

