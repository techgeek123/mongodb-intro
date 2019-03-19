## Release 0
Consider this data
 ```json
 {
  "address": {
     "building": "1007",
     "coord": [ -73.856077, 40.848447 ],
     "street": "Morris Park Ave",
     "zipcode": "10462"
  },
  "borough": "Bronx",
  "cuisine": "Bakery",
  "grades": [
     { "date": { "$date": 1393804800000 }, "grade": "A", "score": 2 },
     { "date": { "$date": 1378857600000 }, "grade": "A", "score": 6 },
     { "date": { "$date": 1358985600000 }, "grade": "A", "score": 10 },
     { "date": { "$date": 1322006400000 }, "grade": "A", "score": 9 },
     { "date": { "$date": 1299715200000 }, "grade": "B", "score": 14 }
  ],
  "name": "Morris Park Bake Shop",
  "restaurant_id": "30075445"
}
```
- Write a MongoDB query to display all the documents in the collection restaurants
- Write a MongoDB query to display the fields restaurant_id, name, borough and cuisine for all the documents in the collection restaurant
- Write a MongoDB query to display all the restaurant which is in the borough Bronx
- Write a MongoDB query to find the restaurants that achieved a score, more than 80 but less than 100.
- Write a MongoDB query to find the restaurants that do not prepare any cuisine of 'American' and their grade score more than 70 and latitude less than -65.754168
- Write a MongoDB query to find the restaurant name, borough, longitude and latitude and cuisine for those restaurants which contain 'Mad' as first three letters of its name


## Release 1

Let's once again rebuild our previous example using a shopping list, but this time we will not be using an array to store our data. Instead, we will be using a Mongo database! The resource we will be working with once again is `item`.

The application should have one model for `Item` and one model for `related_items` for each item should have:

- a unique id
- a name which should be a string
- a quantity which should be a number

The application should have the following routes:

- `GET /items` to show all items in the shopping list
- `GET /items/new` to show a form for creating a new item
- `GET /items/:id` to show a single item
- `GET /items/:id/edit` to show a form for editing a item
- `POST /items` to create an item when a form is submitted
- `PATCH /items/:id` to edit an item when a form is submitted
- `DELETE /items/:id` to delete an item when a form is submitted

and similar routes for `related items`


## Release 2

1. Create a route for `GET /items/search` which allows a user to search for items in the shopping list.
2. Create a route for `DELETE /items` which allows a user to remove all the items in the shopping list.
3. Create another model called `User`, which should have a reference to Item. A user should be able to have their own list of items and have full CRUD over items in their list.
4. Make sure you keep track of `related_items`
