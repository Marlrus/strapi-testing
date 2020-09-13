# Strapi Traversy Media

Strapi is an open source Headless CMS, that makes it easy to create REST APIs with an admin area similar to WP but you are not bound to a specific Front-end. All it does si serve JSON data. We will create a REST API and user Auth using Strapi. We will also override and customize things in our Strapi. We can use the npx command to install Strapi, with the --quickstart flag it will start with SQL Lite, which will not require any additional setting of a DB. However, we could easily implement Strapi with any DB like Postgres or Mongo.

## Installing Strapi

We run `npx create-strapi-app . --quickstart` which will create our Strapi app inside our current directory. It takes about 3 minutes to install, and once it is done it will open a browser window running **http://localhost:1337/admin/auth/register-admin**. It asks for first name, last name, email, and password, which must contain at least 1 number. I used my default pss for testing. This takes us to our /admin area.

## Admin Area Overview

Here we can see that in the tab on the right we have a **COLLECTION TPYES** tab that comes with Users already created. We go to the **PLUGINS** tab under **Content-Types-Builder** where we can see what our collection types are. We can see that User comes with a lot of things out of the box such as provider for different auth types, resetPasswordToken, and role. We have another two Collection Types already: Permission and Role.

## Creating a new Collection Type
