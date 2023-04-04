[# Sample1](https://www.youtube.com/watch?v=eYVGoXPq2RA
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
