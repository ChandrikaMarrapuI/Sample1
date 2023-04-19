
@Test
public void testMarshalSendAndReceiveWithTimeouts() {

   // set up the input parameters
   String endpoint = "http://example.com/webservice";
   Object requestPayload = new Object();

   // create an instance of the class that contains the method
   YourClassName obj = new YourClassName();

   // set up the mock message sender and template
   MockWebServiceMessageSender mockSender = new MockWebServiceMessageSender();
   WebServiceTemplate wsTemplate = obj.getWebServiceTemplate();
   wsTemplate.setMessageSenders(new WebServiceMessageSender[]{mockSender});

   // set up the timeout values
   String readTimeout = "5";
   String connectionTimeout = "10";

   // call the method with the timeout values
   Object response = obj.marshalSendAndReceive(endpoint, requestPayload, readTimeout, connectionTimeout);

   // assert that the message sender was called with the correct timeout values
   assertEquals(Duration.ofMinutes(Integer.parseInt(readTimeout)), mockSender.getReadTimeout());
   assertEquals(Duration.ofMinutes(Integer.parseInt(connectionTimeout)), mockSender.getConnectionTimeout());

   // assert that the response is not null
   assertNotNull(response);
}

(https://www.youtube.com/watch?v=eYVGoXPq2RA
1)	CRUDNode+mongodb(mongoose)  RUN as  nodemon run start
crudModel.js
const mongoose = require('mongoose')

const crudSchema = new mongoose.Schema({

    name: {
        type: String,
        required: true
    },
    tech: {
        type: String,
        required: true
    },
    sub: {
        type: Boolean,
        required: true,
        default: false
    
    }
})
module.exports = mongoose.model('CrudModel',crudSchema)


crudroute.js
const express = require('express')
const router = express.Router()
const CrudModel = require('../models/crudModel')

router.post('/create', async(req,res) => {
    const crudModel = new CrudModel({
        name: req.body.name,
        tech: req.body.tech,
        sub: req.body.sub
    })

    try{
        const c1 = await crudModel.save()
        res.json(c1)
    }catch(err){
        res.send('Error')
    }
})


router.get('/', async(req, res) => {
    try{
        const crudroute = await CrudModel.find()
        res.json(crudroute)
    }catch(err){
        res.send('Error'+err)
    }
})

router.get('/:id', async(req, res) => {
    try{
        const crudroute = await CrudModel.findById(req.params.id)
        res.json(crudroute)
    }catch(err){
        res.send('Error'+err)
    }
})



router.patch('/:id', async(req,res) =>{
    try{
        const crudroute = await CrudModel.findById(req.params.id)
        crudroute.name = req.body.name
        crudroute.tech = req.body.tech
        crudroute.sub = req.body.sub
        const a1 = await crudroute.save()
        res.send({"success": `Document with name: ${req.body.name} has been updated`})  
    }catch(err){
        res.send('Error'+err)
    }
})

router.delete('/:id', async(req,res) =>{
    try{
        const crudroute = await CrudModel.findByIdAndRemove(req.params.id)
        if(!crudroute){
            return res.status(404);
        }
        res.send({"success": `Document with name: ${req.params.id} has been deleted`})
         
    }catch(err){
        res.send('Error'+err)
    }
})

module.exports = router



app.js

const express = require('express')
const mongoose = require('mongoose')
const url = 'mongodb://localhost/DataDB'

const app = express()

mongoose.connect(url, {useNewUrlParser:true})
const con = mongoose.connection
con.on('open', () => {
    console.log('connected')
})

app.use(express.json())   

const crudRouter = require('./routes/crudroute')
app.use('/crudroute',crudRouter)

app.listen(3000, () =>{
    console.log('App is listening to port 5000')
})




Package.json

{
  "name": "crudmongo",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "start": "nodemon app.js"
  },
  "author": "ChandrikaM",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2",
    "mongodb": "^5.1.0",
    "mongoose": "^7.0.3",
    "nodemon": "^2.0.22"
  }
}

 
2)	Nodejs Microservice+postman

const express = require('express');//framework
const { randomBytes } = require('crypto');
const bodyParser = require('body-parser');//
const app = express();

app.use(bodyParser.json());
//Creation of Object that stores data
const posts = {};
//creation of GET method
app.get('/posts', (req, res) => {
    res.send(posts);
});
//***creation of POST method
app.post('/posts', (req, res) => {

    const id = randomBytes(4).toString('hex'); 
    const {title} = req.body;
    posts[id] = {
        id,
        title,
    };
    res.status(201).send(posts[id]);
});
app.listen(3000, () => {
    console.log('App is listening to port 3000');
});

Steps
/*
*npm install nodemon --save
1)create an index.js file in NODESERVICE
2)By using expressjs we used to create an GET and POST methods for Post service.Then we need:
        const express = requires('express');
        const app = express();
3)Creation of GET and POST methods.
        app.get('/posts', (req, res) => {});
        app.post('/posts', (req, res) => {});
-We are going to store all data in memory not in DB.
4)Specific port
        app.listen(3000, () => {
        consolelog('App is listening to port 3000');
        });
--------------------END OF CREATION OF EXPRESS APPLI:--------------------
5)Creation of Object that stores data.
        const posts = {};//blank object
6)For GET method - When we request(req) to GET method , Return posts object back to the user in response(res).
        app.get('/posts', (req, res) => {
            res.send(posts);
        });
7)For POST method - To specify a uniqueness to the each post we are going to generate an "ID"
 using the package {randomBytes} which is inside {crypto} package
        const { randomBytes } = requires('crypto');
    
        app.post('/posts', (req, res) => {
            //To specify a uniqueness to the each post we are going to generate an "ID" using the package {randomBytes}wich is inside {Crypto} package. 
            const id = randomBytes(4).toString('hex'); //calling randomBytes package with some bytes number.
            //627fghjhhx - Assumption of having an unique ID.
            const {title} = req.body;  //Specfying a title to the post.- sends body,then should have title property.
            posts[id] = {   //Assigning ID and title to posts object
                id,
                title,
            };
            res.status(201).send(posts[id]);   //Reponse is sending when an newly post is created.
        });

8){LAST} Adding an BodyParser for Json file.
       
        const bodyParser = require('body-parser');
        app.use(bodyParser.urlencoded({extended : false}));
        

9)In package.json - In script{}, :- To run application
        
        "scripts": {
            "start": "nodemon index.js"
        },
Run as "npm start"

10){TESTING}Postman

*/






/*ISSUES:
1)
const {bodyParser} = requires('body-parser');
app.use(bodyParser.json());
--------------------------------------------------
app.use(bodyParser.json());
TypeError: Cannot read properties of undefined (reading 'json')
--------------------------------------------------
Solution : 
const bodyParser = require('body-parser');
app.use(bodyParser.urlencoded({extended : false}));
*/



a.	CRUDNodejs+Jsonfile.
Steps 
/*
RUN AS :npm run dev
TEST AS :http://localhost:3000/users/post --POST
         http://localhost:3000/users       --GET

npm init
npm i express
npm i -D nodemon

create:
    file: server.js
    package.json:
        "scripts": {
        "dev": "nodemon server"
        },

RUN AS :npm run dev
----------------------------------
app.use(express.json());  -->builtin middleware, parses incoming requests with json payload

Headers: (add)connection-type : application/json

post --> request
{
    "msg_name": "ChandrikaM",
    "msg_dept": "Telecom"
}


*/


Server.js
const express = require('express');
const app = express();


app.use(express.json());

//Routes 
const users = require("./routes/users");
app.use("/users", users);


app.listen(3000, () => {
    console.log('App is listening to port 3000');
});



Routes-> users.js
let data = require('../data/user');

const express = require('express');
const router = express.Router();
const fs = require('fs')

//creation 
router.post("/", (req, res) => {
     //console.log(req.body)
const path = require('path')
const directory = path.join(path.dirname(process.mainModule.filename), 'data', 'user.json')
fs.readFile(directory, (err, content) => {
    let users = []
    if(!err){
        users = JSON.parse(content)
    }
    users.push(req.body)
    fs.writeFile(directory, JSON.stringify(users), (err) => {
        res.send(data)
    })
})
});

//Retrieve 
router.get('/', (req, res) => {
    res.status(200).json(data);
});

//Retrieve by ID
router.get('/:id', (req, res) => {
    let found = data.find(function (item) {
        return item.id === parseInt(req.params.id);
    });
    
    if (found) {
        res.status(200).json(found);
    } else {
        res.sendStatus(404);
    }
});

//update
router.put("/update", (req, res) => {
    //console.log(req.body)
const path = require('path')
const directory = path.join(path.dirname(process.mainModule.filename), 'data', 'user.json')
fs.readFile(directory, (err, content) => {
   let users = []
   if(!err){
       users = JSON.parse(content)
   }
   users = users.filter(user => user.id != req.body.id)
   users.push(req.body)
   fs.writeFile(directory, JSON.stringify(users), (err) => {
    res.send({"success": `Document with id: ${req.body.id} has been updated`})  
    //res.send(req.body)
   })
})
});

//delete
router.delete("/delete", (req, res) => {
    //console.log(req.body)
const path = require('path')
const directory = path.join(path.dirname(process.mainModule.filename), 'data', 'user.json')
fs.readFile(directory, (err, content) => {
   let users = []
   if(!err){
       users = JSON.parse(content)
   }
   users = users.filter(user => user.id != req.body.id)
   
   fs.writeFile(directory, JSON.stringify(users), (err) => {
    res.status(200).send({"success": `Document with id: ${req.body.id} has been deleted`})
    //res.send({"success": `Document with id: ${req.body.id} has been deleted`}) 
   })
})
});

module.exports = router;





data->user.json

[{"id":5,"title":"fifth article","order":5,"completed":false},{"id":3,"title":"updated the article","order":4,"completed":false}]


Package.json:
"scripts": {
    "dev": "nodemon server"
  },



)





////////////////////////////////////


<dependencies>
  <!-- Spring Boot -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  
  <!-- Thymeleaf -->
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>
  
  <!-- MySQL -->
  <dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
  </dependency>
</dependencies>

\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\
Create a Notification entity class:
less
Copy code
@Entity
@Table(name = "notifications")
public class Notification {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String message;

    @Column(nullable = false)
    private Boolean read = false;

    // getters and setters
}
Create a NotificationRepository interface that extends JpaRepository:
csharp
Copy code
public interface NotificationRepository extends JpaRepository<Notification, Long> {

    List<Notification> findByReadOrderByCreatedAtDesc(Boolean read);
}
Create a NotificationService class:
typescript
Copy code
@Service
public class NotificationService {

    @Autowired
    private NotificationRepository notificationRepository;

    public void saveNotification(Notification notification) {
        notificationRepository.save(notification);
    }

    public List<Notification> getUnreadNotifications() {
        return notificationRepository.findByReadOrderByCreatedAtDesc(false);
    }
}
Create a controller that handles requests for displaying notifications:
kotlin
Copy code
@Controller
public class NotificationController {

    @Autowired
    private NotificationService notificationService;

    @GetMapping("/notifications")
    public String showNotifications(Model model) {
        List<Notification> notifications = notificationService.getUnreadNotifications();
        model.addAttribute("notifications", notifications);
        return "notifications";
    }
}
Create a Thymeleaf template for displaying the notifications:
php
Copy code
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Notifications</title>
</head>
<body>
    <h1>Notifications</h1>
    <ul>
        <li th:each="notification : ${notifications}">
            <span th:text="${notification.message}"></span>
            <a href="#" th:if="${!notification.read}" th:href="@{'/notifications/read/' + ${notification.id}}">Mark as Read</a>
        </li>
    </ul>
</body>
</html>
Create a controller that handles marking a notification as read:
kotlin
Copy code
@Controller
public class NotificationController {

    @Autowired
    private NotificationService notificationService;

    @GetMapping("/notifications")
    public String showNotifications(Model model) {
        List<Notification> notifications = notificationService.getUnreadNotifications();
        model.addAttribute("notifications", notifications);
        return "notifications";
    }

    @GetMapping("/notifications/read/{id}")
    public String markAsRead(@PathVariable Long id) {
        Notification notification = notificationService.getNotificationById(id);
        notification.setRead(true);
        notificationService.saveNotification(notification);
        return "redirect:/notifications";
    }
}
This is just a basic example of how you could implement a dynamic notification system using Spring Boot, Thymeleaf, and MySQL. You can add more features like real-time notifications, email notifications, etc., based on your requirements.







\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\\NODEJS
What is Nodejs: Java script runtime env:
Why express:Framework,to create an CRUD operaitoions*


Structure:
App.js(main file) -> routes(folder)->controller(B.L) ->model(S.DB)




Orders management:

We are going to build orders, when an users is about to fill the cart and going to checkout and submit an order.
Here checkout will come with the address of the person and also shipping details and how he will pay the order.
And after LoggedIn,how he will get his data and fill the order data automatically so without letting the user to fill the billing details again.
In this video we are goint to see, how product is linked with the order.
In the backend we are going to see how the state is changing for  an particular order.(Delivered,Shipped,Hold and Cancelled).



1)Orders -> Products?
2)Status Change -> Backend?
3)Deleting the order
4)Calculating Total price of the order.
5)How the owner checks the total sales
6)Order count



*)mongoose is an Object modeling tool -> we can create and manage data in Mongodb.
*)Structure and parameters of model mongoose -> type, required, unique, lowercase, validate, ref, default.
*)populate - like filtering the child records for specified colletion.
*)req.params.id -->params is self defined parameter for receiving request.


*****
ecommerce:
model ->order.js, product.js, order-item.js, user.js, category.js   (order->orderItem->product)
routes ->categories.js, users.js, products.js, orders.js 			
we will generate a Hexstring number for product name and userid

1)orders.js(on submiting the order) - only order ID's and shipping address -POST
2)Get order details with user data and products in orderItems.  		-GET
		.populate('user', 'name').sort({'dateOrdered': -1});  //-1 represents newest to oldest dates
3)orderitems by user ID   -GET
		.populate('user', 'name')
			.populate({ 
				path: 'orderItems', populate: {
					path : 'product', populate: 'category'} 
				});
4)updating order status and delete it.   -PUT
		orders.js(sending some status to order.js and updates in DB)
5)Deleting the order		->orders.js
	deleting the orderitems after deleting the order.
6)TotalPrice
		orders.js
7)Order count
	


Video:
1)order schema and order item schema

ORDERS TABLE:

id:string
orderitems:orderitem[]
shippingAddress1:string
shippingAddress2:string
city:string
zip:string
country:string
phone:number
status:string
totalPrice:number
user:User
dateOrdered:date

OrderItems table:

id:string
product:Product
quantity:number





updating objects in json requirments.
update/delete --> response(rep:)-object or message
Json data?




CRUD - Mongodb

entry point - app.js

1)      npm init -->
The npm init command is used to create a Node. js project and 
create a package where the project files will be stored. 
All the modules you download will be stored in the package.

2)		npm install express
It is used to build a single page, multipage, and hybrid web application. 
It's a layer, built on the top of the Node js that helps manage servers and routes.

3)		npm install mongodb
MongoDB Node. js driver supports database connection, authentication, CRUD operations, and observations

4)		npm install mongoose
Mongoose acts as a front end to MongoDB.
Provide programmers with a way to model their data.

5)		npm install nodemon --save
If we make small changes to the server, then we need to restart the server. If we don't want to restart manually ,
So by using nodemon package we can do automatically.

6)		npm install -g nodemon --save-dev

npm config get prefix.
C:\Users\marrach\AppData\Roaming\npm

*)Mongoose Schema plugins: string,number,date,buffer,boolean,mixed,objectID,Array,map,uuid,schema.
*)Mongoose schema types:(Type is common while declaraing a schema)--> required,default,select,validate,get,set,alias,immutable,transfrom.
*)mongoClient vs mongoose.connect(advanced):
	
	
![image](https://user-images.githubusercontent.com/112848114/229802426-39ec6b77-3b24-4d2f-a039-6b5b446e8fa7.png)




module Java {


//        string,substring
//        special character count
//        stringbuilder->odd chars combine
//        join
//        collections
//        Method overloading and overriding
//        final -class(N-Inheritance),method(N-OR,method can be inherited,N-final word to constructor) and variable(N-M)
//        super
}



package com.example.demo.controller;

 

import java.util.List;

 

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;

 

import com.example.demo.entity.Employee;
import com.example.demo.repository.EmployeeRepository;
import com.example.demo.service.EmployeeService;

 

@Controller
public class EmployeeController {

    @Autowired
    private EmployeeService employeeService;


    @PostMapping("/employee")
    public Employee addEmployee(@RequestBody Employee employee){
        return employeeService.saveEmployee(employee);
    }

 

         


    @GetMapping("/gallemployee")
    public List<Employee> getAllEmployees(){
        return employeeService.getAllEmployees();
    }

    @PutMapping("/update/{id}")
    public Employee updateEmployee(@RequestBody Employee employee,@PathVariable("id") Long id)
    {
        return employeeService.updateEmployee(employee);
    }


    @DeleteMapping("/delete/{id}")
    public String deleteEmployeeById(@PathVariable("id") Long id)
    {
        employeeService.deleteEmployeeById(id);
        return "Deleted Successfully";
    }

}


package com.example.demo.entity;     import... by Marrapu Chandrika
Marrapu Chandrika
12:45 PM

package com.example.demo.entity;

 

 

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.Table;

 

@Entity
@Table(name="empdetails")
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column
    private String first_name;

    @Column
    private String last_name;

    @Column
    private String email;

 

    public Long getId() {
        return id;
    }

 

    public void setId(Long id) {
        this.id = id;
    }

 

    public String getFirst_name() {
        return first_name;
    }

 

    public void setFirst_name(String first_name) {
        this.first_name = first_name;
    }

 

    public String getLast_name() {
        return last_name;
    }

 

    public void setLast_name(String last_name) {
        this.last_name = last_name;
    }

 

    public String getEmail() {
        return email;
    }

 

    public void setEmail(String email) {
        this.email = email;
    }

 

    public Employee(String first_name, String last_name, String email) {
        super();
        this.first_name = first_name;
        this.last_name = last_name;
        this.email = email;
    }

    public Employee() {}

 

    
}


package com.example.demo.repository;   impo... by Marrapu Chandrika
Marrapu Chandrika
12:46 PM

package com.example.demo.repository;

 

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

 

import com.example.demo.entity.Employee;

 

@Repository
public interface EmployeeRepository extends JpaRepository<Employee, Long>{

 

}


package com.example.demo.service;   import ... by Marrapu Chandrika
Marrapu Chandrika
12:46 PM

package com.example.demo.service;

 

import java.util.List;

 

import com.example.demo.entity.Employee;

 


public interface EmployeeService {

 

    Employee saveEmployee(Employee employee);

    List<Employee> getAllEmployees();

    Employee getEmployeeById(Long id);

    Employee updateEmployee(Employee employee);

    void deleteEmployeeById(Long id);
}


package com.example.demo.service;   import ... by Marrapu Chandrika
Marrapu Chandrika
12:46 PM

package com.example.demo.service;

 

import java.util.List;

 

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

 

import com.example.demo.entity.Employee;
import com.example.demo.repository.EmployeeRepository;

 


@Service
public class EmployeeServiceImpl implements EmployeeService {

    @Autowired
    private EmployeeRepository employeeRepository;

    @Override
    public Employee saveEmployee(Employee employee) {
        return employeeRepository.save(employee);
    }

 


    @Override
    public List<Employee> getAllEmployees() {
        return employeeRepository.findAll();
    }


    @Override
    public Employee getEmployeeById(Long id) {

        return employeeRepository.findById(id).get();
    }

 

    @Override
    public Employee updateEmployee(Employee employee) {
        return employeeRepository.save(employee);
    }

 

    @Override
    public void deleteEmployeeById(Long id) {
        employeeRepository.deleteById(id);

    }

 

 

}


<?xml version="1.0" encoding="UTF-8"?> <pro... by Marrapu Chandrika
Marrapu Chandrika
12:46 PM

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-parent</artifactId>
<version>2.7.1</version>
<relativePath/> <!-- lookup parent from repository -->
</parent>
<groupId>com.example</groupId>
<artifactId>Cfood</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>Cfood</name>
<description>POC Project</description>
<properties>
<java.version>17</java.version>
</properties>
<dependencies>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
</dependency>

 

        <dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-devtools</artifactId>
<scope>runtime</scope>
<optional>true</optional>
</dependency>
<dependency>
<groupId>mysql</groupId>
<artifactId>mysql-connector-java</artifactId>
<scope>runtime</scope>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-test</artifactId>
<scope>test</scope>
</dependency>
</dependencies>

 

    <build>
<plugins>
<plugin>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
</plugins>
</build>

 

</project>

 

spring.datasource.url=jdbc:mysql://localh... by Marrapu Chandrika
Marrapu Chandrika
12:46 PM


spring.datasource.url=jdbc:mysql://localhost:3306/employeecrud

 

spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto = update

 

server.port=8082  

 

#spring.datasource.driver-class-name:com.mysql.cj.jdbc.Driver
#server.port=8082  
# 
## Hibernate
#
## The SQL dialect makes Hibernate generate better SQL for the chosen database
#spring.jpa.properties.hibernate.dialect = org.hibernate.dialect.MySQL5InnoDBDialect
#
## Hibernate ddl auto (create, create-drop, validate, update)
#spring.jpa.hibernate.ddl-auto = update
#
#logging.level.org.hibernate.SQL=DEBUG
#logging.level.org.hibernate.type=TRACE?useSSL=false&serverTimezone=UTC&useLegacyDatetimeCode=false

has context menu
Compose


//////////////////pom.xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
<modelVersion>4.0.0</modelVersion>
<parent>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-parent</artifactId>
<version>2.7.1</version>
<relativePath/> <!-- lookup parent from repository -->
</parent>
<groupId>com.example</groupId>
<artifactId>Cfood</artifactId>
<version>0.0.1-SNAPSHOT</version>
<name>Cfood</name>
<description>POC Project</description>
<properties>
<java.version>17</java.version>
</properties>
<dependencies>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
</dependency>

 

        <dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-devtools</artifactId>
<scope>runtime</scope>
<optional>true</optional>
</dependency>
<dependency>
<groupId>mysql</groupId>
<artifactId>mysql-connector-java</artifactId>
<scope>runtime</scope>
</dependency>
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-test</artifactId>
<scope>test</scope>
</dependency>
</dependencies>

 

    <build>
<plugins>
<plugin>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-maven-plugin</artifactId>
</plugin>
</plugins>
</build>

 

</project>

 

