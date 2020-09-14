# Strapi Traversy Media

Strapi is an open source Headless CMS, that makes it easy to create REST APIs with an admin area similar to WP but you are not bound to a specific Front-end. All it does si serve JSON data. We will create a REST API and user Auth using Strapi. We will also override and customize things in our Strapi. We can use the npx command to install Strapi, with the --quickstart flag it will start with SQL Lite, which will not require any additional setting of a DB. However, we could easily implement Strapi with any DB like Postgres or Mongo.

## Installing Strapi

We run `npx create-strapi-app . --quickstart` which will create our Strapi app inside our current directory. It takes about 3 minutes to install, and once it is done it will open a browser window running `http://localhost:1337/admin/auth/register-admin`. It asks for first name, last name, email, and password, which must contain at least 1 number. I used my default pss for testing. This takes us to our /admin area.

## Admin Area Overview

Here we can see that in the tab on the right we have a **COLLECTION TPYES** tab that comes with Users already created. We go to the **PLUGINS** tab under **Content-Types-Builder** where we can see what our collection types are. We can see that User comes with a lot of things out of the box such as provider for different auth types, resetPasswordToken, and role. We have another two Collection Types already: Permission and Role.

## Creating a new Collection Type

We get asked to create a Display name, which _should be singular_ as Strapi will make it plural, much like Mongo does. We the create the fields, we have options like Text, Rich Text, Number, Date, Boolean, Relation, Email, Password, Enumeration, Media, JSON, UID. And in the bottom Component, and Dynamic Zone.

We create a Text field with name title. We get the option to create Short text, or Long text. If we go to **ADVANCED SETTINGS** We can see that we can add a Default value, RegExp pattern or have settings like Private, Required, Unique, Max and Min. We add required and unique to the field and go to **add new field**.

We then create a description field using long text, and a number field for price that uses decimal. We can use integer, bit integer, decimal, and float as number types for number fields. We then create a qty field that uses integer.

Once we are done we can see the **Product** we just created and the fields we have for it. We hit Save which restarts our Strapi server and now we have our Product type under our Collection Types as plural.

### Category Type and Relation Field

We create a new Collection called **Category** which has a name field that is required and unique, and then we create a new Relation field. We create a relation to our Product Collection. There are visual representations of the different kinds of relationships we can have such as: one to one, one to many, many to one, many to many, etc. We choose **many to many**. We get a description: **Categores has and belongs to many Products**. We then hit save. We then have a Categores Collection Type as well.

## Creating Collection Types Items

We go to Categories and we can see on top that we can choose **Add New category** which is based on the Collection Type that we created. We create Laptops, Mobile Phones, and Tablets categories. We then go to **Products** and Add New product, where we can see that we can create an item based on the fields we created earlier. We create an iPhone 11, add a Description, proce, qty, and we can see that we have a dropdown for Categories. We can see the Categories we created, and since the relation we created is One to Many, we can choose more than one category here. We proceed to create 2 more products, Samsung Galaxy S10, and iPad.

## Postman and Roles and Permissions

We create a new GET request to `http://localhost:1337/products` which is the end point of our API. By default we get a statusCode of 403, meaning that it is Forbidden. Everything we create will be set to private / forbidden at first. To change this we have to go to the **Roles & Permissions** section under PLUGINS. It comes with 2 roles, Autheticated and Public, however you can _create new roles_ if you want to.

We go to edit in our Public role and make count, find and findone public in Product and Category, because we want to be able to know the quantity of items we have, be able to list the items, and be able to find one item in our project. We go back to Postman and run the request and we get a respones that lists all of our products.

## Response JSON and other queries

We can see that we have a JSON response that has the fields we created with some additional fields: id, created_at, updated_at, and a categories array that has the category we assigned with created_at and updated_at as well. The id auto increments, so we can see that the items have id 1, 2, and 3 respectively in our response.

We then change our request to `http://localhost:1337/products/1` which returns the item with the id 1 in our products collection which is the **findone** we enabled. We then run `http://localhost:1337/products?_limit=2&_sort=title` We get 2 results and we get them sorted by title, however the sort is strange because we get Samsung before iPad. We get these and other things built into the API.

## POST request and Users

We send a post request to /products using raw JSON as the Body. We get a 403 res because don't have that method under public. We want to keep it that way, so we need to create a User in order to use the API for CRUD operations. We got Users and **Add new User** where we create a user moerse, with moerse@gmail.com and password password. We then set confirm as ON which we might change if we wanted to have email verficiation through tokens. In role, we choose Authenticated, so that this user can do the CRUD operations that are not public.

Just doing that won't work yet because we have to set the **Roles & Permissions** for Authenticated users. Here we just **Select All** for Product and Category, which will allow our user to do anything we want in our API.

### Strapi Auth and login using Postman

To login using Postman we create a new POST request to **/auth/local** with a raw JSON body that has identifier, which is our Username or Email, and the password. We then send the request with a faulty password and get a res of 400, Bad Request. We get a message [] and data [] that have messages which we can then call in our front end to display some information easily.

_Note_ I had problems with this req, I tried everything sending raw JSON as text, as JSON, as JavaScript and it failed. I had to send **x-www-form-urlencoded** with identifier and password for it to finally work.

After sending the POST request to `http://localhost:1337/auth/local` I got a response with a jwt field and a user field. We copy the **jwt**, which we would add to our cookies or localstorage to send in our requests to a protected route.

### Postman Auth

We go to our /products post request in Postman and go to the Auth tab. We choose **Bearer Token** as the TYPE and then paste the JWT there. Now when we send our POST request we get our response back with the created product. Now if we go back to our GET request and call /products we can see that the product was created. We can change the product from Strapi as well, we add a Category of Laptops to our Test Product and save it. We can do all the operations we do through Postman in our frontend using HTTP requests.

## Strapi Code

We can see what we have installed in our package.json file. We have a bunch of strapi plugins as well as **knex** which is probably used for GraphQL implementation, and sqlite3 which is what we are using as a db at the moment. We can stop and restart the server using our scripts, specifically **yarn develop** which runs `strapi develop`. We have a public/uploads dir for images or videos that we upload to Strapi.

We see in the video that there is a config/environments/ directory which is NOT in our project that has the settings for different aspects of Strapi on different environments. We only have a functions/ dir, database.js and server.js. If we were to use a different DB we would change the client option in database.js to mongodb and paste the rest of the settings from the Strapi DOCS.

## API

In the api/ folder we can find the most important part of our Strapi API. Here we can see the different collections we created. We navigate to category/config/routes.json where we can see the different routes that Strapi creates. We can see that we have an array of routes, with a method, path, handler, and config. The handler is the controller that will be executed on the API call.

If we go to the controllers or services file, we can see that it is empty. This is beacause we are using the default Strapi settings, however we can override the methods if we know how to and we can find it by following the link in the file to the Strapi DOCS.

### Overriding Find

We navigate to product/controllers/product.js and we create our own find method that will override the find route:

```javascript
module.exports = {
  async find(context) {
    console.log(context);
    const products = await strapi.services.product.find(context.query);

    return products.map((product) => product.title);
  },
};
```

Here we create a find method that uses a context, which holds the req information. Then we get the products using the strapi find method, here we use strapi.services.product.find(context.query), which uses the find method in our product Strapi collection. If we log the query we see that it is an empty object because we are not sending a query string in the req. Then we choose to just return the product title which will return the following array:

```javascript
["iPhone 11", "Samsung Galaxy S10", "iPad", "Test Product"];
```

We delete our method to return things to normal.

### Overriding Services

We go to product/services/product.js and override the service using:

```javascript
module.exports = {
  find(params, populate) {
    console.log("Service called...");

    return strapi.query("product").find(params, populate);
  },
};
```

Now if we run the query, we see that we log Service called. If we want to get more details on how to override the core services we just follow the link to the DOCS inside this file.

### Models

In models we have two files, product.js and product.settings.json. This holds our Schema, we can see it under attributes:

```javascriot
"attributes": {
  "title": {
    "type": "string",
    "unique": true,
    "required": true
  },
  "description": {
    "type": "text"
  },
  "price": {
    "type": "decimal"
  },
  "qty": {
    "type": "integer"
  },
  "categories": {
    "via": "products",
    "collection": "category"
  }
}
```

There are also options like increments and timestamps which are set to true. These are the additional props that we see in our response that we didn't set directly in Strapi. This is similar to the Schema that we see when we use Mongoose. This can be modified how we want.

#### product.js

Inside this file we can create different lifecycle methods, like Mongoose hooks. Where we can trigger different things to happen whenever certain things happen such as after saving a value, before fetching a value, etc etc. The details can be found in the DOCS that are linked in the file. This has changed since the video and I digged into the docs to find the new lifecycle methods.

```javascript
module.exports = {
  lifecycles: {
    async afterFind(results, params, populate) {
      console.log("After Find");
      console.log(results);
    },
  },
};
```

This will trigger after we use the find method in the find GET route /products where we get to intercept the response. This allows us to do something with the data anytime that any method in our API gets called. I did:

```javascript
console.log(results.map((result) => result.title));
```

Which logged the title for each product, meaning that it is working as expected. This is great because we can **hook into any kind of lifecycle method that we want**.This are the files that we will be using for customization for Strapi.

## Conclusion

Strapi is actually a lot more powerful than I imagined. We can create a visual REST API that can be modified through the use of the GUI or the front end using HTTP requests. It takes a lot of the load of having to do a backend for basic applications. I will definitely be using this in the future if I am to create a React app that is not using Gatsby. There are plenty of options for customization and you can find all the information you need on the routes that get created by the API through the DOCS. This is actually a very powerfull tool with more customization than what I had originally thought.
