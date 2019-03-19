# Learning Competencies

- Install Mongo and start a Mongo Server
- Perform CRUD operations in a Mongo console
- Define essential terms like schema and model
- Connect to a Mongo database using Mongoose
- Perform CRUD operations with Mongoose
- Compare and contrast different CRUD methods on Mongoose models and documents
- Compare and contrast embedding and referencing in Mongo
- Add references to Mongoose models
- Build a CRUD app with references between models


## Why Databases?
So far we have been using an array to store our data. Since this array is being stored in memory on the server, it only lasts as long as our server is running. The second our server goes down, we lose all our data! This simply will not work for any practical application. At some point, we will need to be able to save, or persist, our data. To do that, we'll need to use a database.

## CRUD
To perform CRUD operations in mongo, start up a mongo server by typing `mongod` in the terminal. Then open up a new tab and open up a mongo shell by typing `mongo`. We can see our databases by using `show dbs` and our collections in that database by using `show collections`. To connect to database simply type `use NAME_OF_DATABASE`

More commands [here](https://docs.mongodb.com/manual/crud/).

### Create

To create in mongo we use the ``insert`` function, which belongs to a collection we are operating on. ``insert`` accepts an object of keys and values.

You can also insert an array of objects using the ``insertMany`` function.
```js
db.users.insert({
    first: 'Elie'
    last: 'Schoppik',
    isInstructor: true,
    favoriteColors: ['Red', 'Blue', 'Purple'],
    favoriteFood: 'sushi'
});

db.users.insert({
    first: 'Mary',
    last: 'Malarkin',
    isInstructor: false,
    favoriteColors: ['Green', 'Yellow'],
    hometown: 'Omaha'
});

db.users.insertMany( [t branc
     { first: "tim", fun_fact: 'owns a boat!' },
     { first: "matt", fun_fact: 'has a pet dog!' },
  ] );
  ```
### Read

To read in mongo, we use the find command to find multiple objects and findOne for a single one
```js
db.users.find()
/*
{
  "_id": ObjectId("58aa39cb62a53c60f58471c7"),
  "first": "Mary",
  "last": "Malarkin",
  "isInstructor": false,
  "favoriteColors": [
    "Green",
    "Yellow"
  ],
  "hometown": "Omaha"
}
{
  "_id": ObjectId("58aa39cc62a53c60f58471c8"),
  "first": "tim",
  "fun_fact": "owns a boat!"
}
{
  "_id": ObjectId("58aa39cc62a53c60f58471c9"),
  "first": "matt",
  "fun_fact": "has a pet dog!"
}
*/

db.users.findOne({first:'Mary'})
/*{
  "_id": ObjectId("58aa39cb62a53c60f58471c7"),
  "first": "Mary",
  "last": "Malarkin",
  "isInstructor": false,
  "favoriteColors": [
    "Green",
    "Yellow"
  ],
  "hometown": "Omaha"
}
*/
```
### Update

To update documents in mongo, we use the update method and pass in different key-value pairs for the values we want to update. We can both update existing keys create new key-value pairs using this function.
```js
db.users.update(
  // what to find the user by
   { first: "tim" },
   // what data to update
   {
      first: "Bob",
      moreInfo: "Is a new instructor",
      favorite_numbers: [11,12,13]
   }
)

/*
WriteResult({
  "nMatched": 1,
  "nUpserted": 0,
  "nModified": 1
})
*/
```
We can also use update to add a new document, if we pass it the upsert option! upsert is a [portmanteau](https://en.wikipedia.org/wiki/Portmanteau) of update and insert. When you upsert into a collection, a document will be updated if it is found, and inserted if no matching document is found.
```js
db.users.update(
   { name: "fjlsadkdfjsdaklfjdklsajfklds" },
   {
      name: "Upsert will insert if it can not find!",
      moreInfo: "How cool is that?",
      favorite_numbers: [11,12,13]
   },
   // if it is not found, insert it! (upsert = update or insert)
   { upsert: true }
)

/*
WriteResult({
  "nMatched": 0,
  "nUpserted": 1,
  "nModified": 0,
  "_id": ObjectId("58aa3a859c4f01de70dcd04b")
})
*/
```
Finally, if you want to update multiple documents, you can set multi equal to true.
```js
// update every single one that is found using multi:true
db.users.update(
  // find all where the name is not equal to 'nope'
  { name: { $ne: 'nope' } },
  // set a new key of isHilarious to true
  {$set: { isHilarious: true }},
  // for more than 1 record
  { multi: true }
)

/*
WriteResult({
  "nMatched": 4,
  "nUpserted": 0,
  "nModified": 4
})
*/

```
For more on update, check out the [docs](https://docs.mongodb.com/manual/reference/method/db.collection.update/#db.collection.update).

### Delete

To delete documents in mongo, we use the remove method.
```js
// remove a single user
db.users.remove({name:'Bob'})
/*
Removed 0 record(s) in 1ms
WriteResult({
  "nRemoved": 1
})
*/

// remove all the users
db.users.remove({})
/*
Removed 1 record(s) in 2ms
WriteResult({
  "nRemoved": 3
})
*/
```
### Additional Find Methods
Since the process of deleting or updating involves first finding a record, we can use some built-in methods to perform both operations at once.
```js
db.users.findAndModify({
    // find someone with a name of elie and a score greater than 10
    query: { name: "Elie", score: { $gt: 10 } },
    // increment their score by 1
    update: { $inc: { score: 1 } }
})

db.users.findOneAndUpdate(
   { name: "Elie" },
   { $inc: { "points" : 5 } }
)

db.users.findOneAndDelete(
   { "name" : "Elie" }
)
```
You can read more about the difference between modify and update [here](http://stackoverflow.com/questions/10778493/whats-the-difference-between-findandmodify-and-update-in-mongodb). 

And for documentation on all of these methods (and others we haven't discussed), go [here](https://docs.mongodb.com/manual/reference/method/js-collection/).

## Mongoose
So far we have seen how to connect to a database with mongo and perform CRUD and query operations on a mongo collection. While we can connect mongo with our node applications, there is a useful tool called mongoose, which gives us access to additional methods to help with querying and building larger applications.

Mongoose also introduces two concepts which we will examine:

- **schema** - a schema is a structure for a collection. Even though mongo is "schemaless" (hmm, what does that even mean!?), mongoose allows us to create a structure for our collection and add validations, class methods, instance methods and associates (which we will examine later).
- **model** - a [model](https://en.wikipedia.org/wiki/Database_model) is an object which is created from the schema and it contains methods for creating, finding, updating and deleting documents.

To get started with mongoose, let's install and require it and connect to our database. We will be placing all of our models inside of a folder called `models`, so let's create a file called index.js and write the following code inside of it.
```js
var mongoose = require('mongoose');
mongoose.set('debug',true); // this will log the mongo queries to the terminal
mongoose.connect('mongodb://localhost/first_mongoose_app'); // connect to the DB

mongoose.Promise = global.Promise // let's use ES2015 promises for mongoose! No callbacks necessary!
```
### Schema
Now let's add our first Schema. You can read more about the different data types [here](http://mongoosejs.com/docs/guide.html). For now, let's create a file called instructor.js and place the following inside of the models folder.
```js
// create our schema which describes what each document will look like
var instructorSchema = new mongoose.Schema({
    firstName: String,
    lastName: String,
    isHilarious: {type: Boolean, default: true},
    favoriteColors: [String],
    createdAt: { type: Date, default: Date.now }
});

// create our model from the schema to perform CRUD actions on our documents (which are objects created from the model constructor)
var Instructor = mongoose.model('Instructor', instructorSchema);

// Now it would be nice if we could aggregate our models into one single file and export them out to be used in our routes and other files, so let's export this model out to another file!

module.exports = Instructor;
```
Now let's add to our models/index.js file
```js
var mongoose = require('mongoose');
mongoose.set('debug',true); // this will log the mongo queries to the terminal
mongoose.connect('mongodb://localhost/first_mongoose_app'); // connect to the DB

mongoose.Promise = global.Promise // let's use ES2015 promises for mongoose! No callbacks necessary!

// let's export out an object and attach to it a property called Instructor
module.exports.Instructor = require('./instructor') // the value of this property will be the Instructor model that we exported out in our instructor.js file!
```
We will be using this index.js file in our routes files so it's important to isolate our model logic from our routing (or controller) logic.

You can see how a basic folder structure and model would look like [here](mongoose_intro).


Mongoose in many ways "completes" Mongo in ways that most SQL people demand - that is:

- It creates nested, typed schemas
    - Schema field specs can be as loose or specific as you like.
    - They can include type requirements (String, Number, Date... YourCustomType)
    - They can insist a record has a field value (Required)
    - They can be restricted to a set of values (a.k.a. Enum)
    - They can be set to a single or array of another Schema allowing arbitrarily nested documents.
- It gives you transactional integrity
    - If the "non dirty" values for a record have changed since you retrieve a record, mongoose assumes that there is a transactional violation and rejects your changes when you save.
- It employs the ActiveRecord pattern
- It gives you Joins, foreign keys, and effortless map/reduce.
    - Well, not really. Sorry. But nobody else does either so there you go.
- It gives you through and complete documentation.
    - Well -- the SCHEMA documentation is complete. But apparently there is nothing else important you will ever do with Mongo other than create schemas so that's pretty much all you get.

## CRUD
Now that we have created a schema and a corresponding model, let's see what kinds of methods exist on our model to perform CRUD operations! Remember, CRUD stands for Create, Read, Update, and Delete.

### Create

In mongoose, documents are instances of the model. As a result, so there are two ways of creating documents:
```js
// invoke the constructor
var elie = new Instructor({firstName: 'Elie' });
// call .save on the object created from the constructor (which is the model)
elie.save().then(function(){
    console.log('saved!');
}, function(err){
    console.log('Error saving!', err);
})

// OR

// invoke the create method directly on the model
Instructor.create({firstName: 'Elie'}).then(function(instructor){
    console.log('saved!');
}, function(err){
    console.log('Error creating!');
})
```
### Read

There are quite a few ways to query for information in mongoose.
```js
// finding multiple records
Instructor.find({}).then(function(instructors){
    console.log(instructors);
}, function(err){
    console.log('error!', err);
})

// finding a single record
Instructor.findOne({firstName: 'Elie'}).then(function(data){
    console.log(data)
}, function(err){
    console.log('error!', err);
})
// finding by id - this is very useful with req.params!
Instructor.findById(2).then(function(data){
    console.log(data)
}, function(err){
    console.log('error!', err);
})

// Finding in a nested object
var query = Person.findOne({ 'name.first': 'Elie' });

// selecting the `name` and `occupation` fields
query.select('name occupation');

// execute the query at a later time
query.exec().then(function(person) {
    console.log(person);
}, function(err){
    console.log("ERROR!");
})
```
You can read more about querying [here](http://mongoosejs.com/docs/queries.html)

### Update

There are quite a few ways to update with mongoose as well. Here are some examples:
```js
// update multiple records
Instructor.update({firstName:'Elie'}, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// update a single record
Instructor.findOneAndUpdate({firstName:'Elie'}, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// update a single record and find by id (very useful with req.params!)
Instructor.findByIdAndUpdate(1, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});
```
You can read more about each of these methods here:

- http://mongoosejs.com/docs/api.html#model_Model.update

- http://mongoosejs.com/docs/api.html#model_Model.findOneAndUpdate

- http://mongoosejs.com/docs/api.html#model_Model.findByIdAndUpdate

## Delete

As you might have guessed, there are also quite a few ways as well to remove a document with mongoose. Here are some examples:
```js
// remove multiple records
Instructor.remove({firstName:'Elie'}, {firstName:'Bob'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// find and remove
Instructor.findOneAndRemove({firstName:'Elie'}).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});

// find by id and remove (very useful with req.params)
Instructor.findByIdAndRemove(1).then(function(data){
    console.log(data);
}, function(err){
    console.log('error!', err);
});
```
You can read more here about [findOneAndRemove](http://mongoosejs.com/docs/api.html#model_Model.findOneAndRemove) and [findByIdAndRemove](http://mongoosejs.com/docs/api.html#model_Model.findByIdAndRemove).

Now that we have seen how to use mongoose, let's add this logic to a CRUD application. Let's also think about a better folder structure! Let's start with our application in the terminal. We're going to build a simple CRUD application with pets as our resource. Let's start with our folder structure in terminal.

    mkdir pets-app && cd pets-app
    touch app.js
    npm init -y
    npm install express pug body-parser method-override mongoose
    mkdir views routes models
    touch views/{base,index,new,edit,show}.pug
    touch models/{index,pet}.js
    touch routes/pets.js
Let's start with our models/pet.js:
```js
var mongoose = require("mongoose");
var petSchema = new mongoose.Schema({
    name: String
})

var Pet = mongoose.model('Pet',petSchema);

module.exports = Pet;
```
Next, our models/index.js:
```js
var mongoose = require("mongoose");
mongoose.set('debug', true);
mongoose.connect('mongodb://localhost/pets-app');
mongoose.Promise = Promise;

module.exports.Pet = require('./pet');
```
Now let's move to our routes/pets.js:
```js

var express = require("express");
var router = express.Router();
var db = require('../models'); // by default, require will look for a file called index.js so we dont need ../models/index

router.get('/', function(req, res, next){
    db.Pet.find().then(function(pets){
        res.render('index', {pets});
    });
});

router.get('/new', function(req, res, next){
    res.render('new');
});

router.get('/:id', function(req, res, next){
    db.Pet.findById(req.params.id).then(function(pet){
        res.render('show', {pet});
    });
});

router.get('/:id/edit', function(req, res, next){
    db.Pet.findById(req.params.id).then(function(pet){
        res.render('edit', {pet});
    });
});

// instead of app.post...
router.post('/', function(req, res, next){
    db.Pet.create(req.body).then(function(pet){
        res.redirect('/users');
    });
});

// instead of app.patch...
router.patch('/:id', function(req, res, next){
    db.Pet.findByIdAndUpdate(req.params.id, req.body).then(function(pet){
        res.redirect('/users');
    });
});

// instead of app.delete...
router.delete('/:id', function(req, res, next){
    db.Pet.findByIdAndRemove(req.params.id).then(function(pet){
        res.redirect('/users');
    });
});

module.exports = router;
```
Before we move onto our views, let's finish our app.js:
```js
var express = require("express");
var app = express();
var methodOverride = require("method-override");
var morgan = require("morgan");
var bodyParser = require("body-parser");
var petsRoutes = require("../routes/pets");

app.set("view engine", "pug");
app.use(express.static(__dirname + "/public"));
app.use(morgan("tiny"));
app.use(bodyParser.urlencoded({extended:true}));
app.use(methodOverride("_method"));

app.use('/pets', petsRoutes);

app.get("/", function(req, res, next){
  res.redirect("/pets");
});

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handlers

// development error handler
// will print stacktrace
if (app.get('env') === 'development') {
  app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
      message: err.message,
      error: err
    });
  });
}

// production error handler
// no stacktraces leaked to user
app.use(function(err, req, res, next) {
  res.status(err.status || 500);
  res.render('error', {
    message: err.message,
    error: {}
  });
});

app.listen(3000, function(){
  console.log("Server is listening on port 3000");
});
```

Now let's move onto our views. First, here's views/base.pug:
```html
<!DOCTYPE html>
html(lang="en")
head
    meta(charset="UTF-8")
    title Document
body
    block content
```
Now our views/index.pug:
```html
extends base.pug

block content
    a(href="/pets/new") Create a new pet
    each pet in pets
        p `Name ${pet.name}` | a(href=`/pets/${pet.id}/edit`) Edit

```
Now our views/new.pug
```html
extends base.pug

block content
    h1 Add a new pet!
    form(action="/pets", method="POST")
        input(type="text", name="name")
        input(type="submit", value="Add a Pet!")
```
Now our views/show.pug
```html
extends base.pug

block content
    h1 Welcome to `${pet.name}'s` show page!
```
Finally, our views/edit.pug
```html
extends base.pug

block content
    h1 Edit a pet!
    form(action=`/pets/${pet.id}?_method=PATCH`, method="POST")
        input(type="text", name="name", value=`${pet.name}`)
        input(type="submit", value="Edit a Pet!")
    form(action=`/pets/${pet.id}?_method=DELETE`, method="POST")
        input(type="submit", value="X")
```

### Resources
Wrapping up and Example App :

As you can see above, building these applications is a process that requires repetition to understand and become comfortable with. Take the time to build a few of these applications and see and debug errors as you go along!

You can see another sample CRUD app with Mongoose [here](mongoose_crud).



## Associates

When we start building larger applications, we will need to create additional models. Many times, we will want to connect or associate data between one or more models. While this is a foundational part of relational databases, it is implemented a bit differently with non relational databases like MongoDB. There are two different ways to associate data with MongoDB: referencing and embedding.


### Embedding
The idea with embedding is that we place data we want to associate inside of an existing document. Let's imagine we wanted to create an application where we can create authors and books for each author. With embedding, our documents might look like this
```js
{
  id: 1,
  name: 'JK Rowling',
  countryOfOrigin: 'England',
  books: [{
    title: "Harry Potter and the Sorcerer's Stone",
    }, {
    title: "Harry Potter and the Goblet of Fire",
    }]
}
```
This is an example of a one-to-many relationship: one author has many books. To read more about embedding in mongo, read through [this](https://docs.mongodb.com/manual/tutorial/model-embedded-one-to-many-relationships-between-documents/) chapter.

### Referencing
When referencing, we use two separate collections for managing our data. In the example above, we would have a collection for authors and a collection for books.
```js
// authors
{
  id: 1,
  name: 'JK Rowling',
  countryOfOrigin: 'England'
}
// books
{
  id: 1,
  author_id: 1,
  title: "Harry Potter and the Sorcerer's Stone",
}

{
  id: 2,
  author_id: 1,
  title: "Harry Potter and the Goblet of Fire",
}
```
You can read more about referencing [here](https://docs.mongodb.com/manual/tutorial/model-referenced-one-to-many-relationships-between-documents/). We'll see a more explicit example of referencing in just a minute.

### So which one?
In general, it's best to use embedding when you have smaller subdocuments and you want to read information quickly (since you do not need an additional collection query). Ideally you embed when data does not change frequently and when your documents do not grow frequently.

Referencing is useful when you have large subdocuments that change frequently and need faster writes (since you have a single collection and do not need a nested query). Referencing is also useful when you want to exclude parent data (show all the books, but don't worry about the authors of each one).

You can read more about how to think about which to choose [here](http://stackoverflow.com/questions/5373198/mongodb-relationships-embed-or-reference), [here](http://openmymind.net/Multiple-Collections-Versus-Embedded-Documents/), and [here](https://coderwall.com/p/px3c7g/mongodb-schema-design-embedded-vs-references).

### Building off our pets app
With Mongoose, we can easily embed and reference, but what makes Mongoose really helpful is that when we choose to reference, we can easily populate documents with their subdocuments without writing more complex mongo queries. If we correctly set up our schema, we will be able to easily query across multiple collections.

Let's revisit our previous pets application and create a new model called owner. Each owner will have an array of pets and each pet will have a single owner.

Let's look at our models/owners.js
```js
var mongoose = require("mongoose");
var ownerSchema = new mongoose.Schema({
    name: String,
    // every owner should have an array called pets
    pets: [{
      // which consists of a bunch of ids (we will use mongoose to populate the entire pet object, let's just store the id for now)
      type: mongoose.Schema.Types.ObjectId,
      // make sure that we reference the Pet model ('Pet' is defined as the first parameter to the mongoose.model method)
      ref: 'Pet'
    }]
});

var Owner = mongoose.model('Owner',ownerSchema);

module.exports = Owner;
Let's look at our models/pets.js

var mongoose = require("mongoose");
var petSchema = new mongoose.Schema({
    name: String,
    // let's create a reference to the owner model
    owner: {
      // the type is going to be just an id
      type: mongoose.Schema.Types.ObjectId,
      // but it will refer to the Owner model (the first parameter to the mongoose.model method)
      ref: 'Owner'
    }
});

var Pet = mongoose.model('Pet',petSchema);

module.exports = Pet;
```
## RESTful routing for nested resources
Since we now have two resources, pets and owners and pets are dependent on owners (we can't have a pet without an owner), we need to nest or place our pets routes inside of owners. Here is what the RESTful routes for each resource will look like:

### Owners

 |Verb  |Path|  Description
|:--:|:--:|:--:|
GET|  /owners|  Show all owners
GET|  /owners/new |Show a form for creating a new owner
GET|  /owners/:id |Show a single owner
GET |/owners/:id/edit |Show a form for editing a owner
POST| /owners |Create a owner when a form is submitted
PATCH |/owners/:id  |Edit a owner when a form is submitted
DELETE  |/owners/:id| Delete a owner when a form is submitted
### Pets

HTTP Verb|  Path  |Description
|:--:|:--:|:--:|
GET|  /owners/:owner_id/pets  |Show all pets for an owner
GET |/owners/:owner_id/pets/new |Show a form for creating a new pet for an owner
GET|  /owners/:owner_id/pets/:id  |Show a single pet for an owner
GET |/owners/:owner_id/pets/:id/edit  |Show a form for editing an owner's pet
POST| /owners/:owner_id/pets  |Create a pet for an owner when a form is submitted
PATCH|  /owners/:owner_id/pets/:id  |Edit an owner's pet when a form is submitted
DELETE| /owners/:owner_id/pets/:id  |Delete an owner's pet when a form is submitted

As you can see, we will have quite a few more routes so this is where having a file for each resource is quite helpful! If you are dealing with nested routes, in the file you are placing nested routes, it is important to include the following when creating the express router:
```js
var express = require("express");
var router = express.Router({mergeParams: true}); // this is essential when working with nested routes
CRUD with nested resources

Let's now examine what it looks like to save a pet:

router.post('/owners/:owner_id/pets', function(req, res, next){
  var fido = new Pet(req.body);
  fido.owner = req.params.owner_id;
  fido.save().then(function(pet) {
    db.Owner.findById(req.params.owner_id).then(function(owner){
      owner.pets.push(fido)
      owner.save().then(function(owner) {
        res.redirect(`/owners/${owner.id}/pets`);
      });
    });
  });
});
```
How about finding all of the pets for an owner? Here we need to make use of a mongoose method called populate, which will populate the entire object for one or more object id's
```js
router.get('/owners/:owner_id/pets', function(req, res, next){
  db.Owner.findById(req.params.owner_id)
    .populate('pets')
    .exec()
    // owner now has a property called pets which is an array of all the populated pet objects!
    .then(function(owner){
      res.render('pets/index', {owner});
  });
});
```
# Resources
You can see a sample CRUD application with associations [here](mongoose_associations)

- Some[questions](http://www.w3resource.com/mysql-exercises/join-exercises/) covering both querying and joins


