# Project Name: Lunch.ly Reservation System

## Table of Contents

- [Project Name: Lunch.ly Reservation System](#project-name-lunchly-reservation-system)
  - [Table of Contents](#table-of-contents)
  - [Assignment Criteria](#assignment-criteria)
    - [1. Setting Up](#1-setting-up)
    - [2. Look at the Code!](#2-look-at-the-code)
    - [3. Class (Static) Methods](#3-class-static-methods)
    - [4. Nunjucks](#4-nunjucks)
    - [5. Full Names](#5-full-names)
    - [6. Saving Reservations](#6-saving-reservations)
    - [7. Add Search Functionality](#7-add-search-functionality)
    - [8. Best Customers](#8-best-customers)
    - [9. Getters and Setters](#9-getters-and-setters)
    - [10. Getters and setters allow you to use an object like this:](#10-getters-and-setters-allow-you-to-use-an-object-like-this)
    - [11. Other fun things!](#11-other-fun-things)
  - [How I refactored this project (Files/Folders added/refactored)](#how-i-refactored-this-project-filesfolders-addedrefactored)
    - [`.env`](#env)
    - [`.gitignore`](#gitignore)
    - [`db.js` refactor](#dbjs-refactor)
      - [Database Connection Refactor Explanation](#database-connection-refactor-explanation)
        - [1. Environment Variable Management](#1-environment-variable-management)
        - [2. Dynamic Database URI Selection](#2-dynamic-database-uri-selection)
        - [3. Improved Error Handling and Logging](#3-improved-error-handling-and-logging)
        - [4. Security and Configuration Best Practices](#4-security-and-configuration-best-practices)
        - [5. Package.json Adjustments](#5-packagejson-adjustments)
    - [`src` folder](#src-folder)
    - [`routes` folder](#routes-folder)
    - [`db` folder](#db-folder)
    - [`utils` folder](#utils-folder)

## Assignment Criteria

Lunchly is an Express app that is _not_ an API server, nor is it RESTful.

Instead, it’s a server-side templated application with custom URLs.

[Back to TOC](#table-of-contents)

### 1. Setting Up

1. Create a project folder, Git repository, and **_package.json_**.
2. Create a database, **lunchly** and **lunchly_test**
- a. **Open your terminal**: Make sure you're in a command line interface where you can execute `psql` commands.
- b. **Access the PostgreSQL command line interface**: You can access the PostgreSQL CLI by running:
```
psql
```
- c. **Create the database**: Once inside the PostgreSQL CLI, you can create the "biztime" database by executing the following SQL command:

```
CREATE DATABASE lunchly;
```

This command creates a new database named "lunchly".  Repeat this step but change "lunchly" to "lunchly_test" to create the test database.

- d. **Exit the PostgreSQL CLI**: After creating the database, you can exit the PostgreSQL CLI by typing:

```
\q
```

- e. **Verify the database creation** (optional): To ensure that the "biztime" database has been created successfully, you can list all databases by accessing the PostgreSQL CLI again with `psql` and then running:

```
\l
```

Look for "lunchly" and "lunchly_test" in the list of databases.


3. Read in the sample data in **\*data.sql**(\*

   _Later, if you’re curious and want to see the script that generated all this sample data, it’s in the solution directory as **seed.py**)_

The aforementioned instructions weren't helpful to me.  Instead I populated the `lunchly` and `lunchly_test` databases with data from the `data.sql` and `data_test.sql` files.

1. I opened my terminal and navigated to `src/db` then logged into my `psql` account and ran the command `\i data.sql` and then `\i data_test.sql` to populate both databases.  Both `sql` files have code at the beginning of the file that connect to its respective database so I didn't need to worry about that step.  After populating each database I checked if it worked by running `SELECT * FROM customers;`

4. Install the required node modules:
   _`$ **npm install**`_
5. Start the server with **_nodemon_**
6. Take a tour of the site at **_http://localhost:3000_** — you should be able to see the list of customers, view a customer detail page, and add a customer. Adding a reservation is not yet complete, so expect errors if you try to create a new reservation.

[Back to TOC](#table-of-contents)

### 2. Look at the Code!

There’s lots of existing sample code. **Give yourself a quick tour!**

**app.js :** Our application object; can be imported from other files/tests

**models/ :** Model objects as as classes, to “abstract away” database handling

**routes.js :** Routes for the web interface

**server.js :** Functionality to start the server (this is the file that is run to start it)

**templates/ :** Jinja templates, rendered with the JS “Nunjucks” library

[\*\*seed.py](http://seed.py) :\*\*File to create sample data (you don’t need to use this, but may find it interesting as an example of how to create realistic fake data.)

[Back to TOC](#table-of-contents)

### 3. Class (Static) Methods

Many methods on objects are called on an *instance* of a class (called on an individual cat, to use our example above). In some cases, though, you may want a method that isn’t called on a particular cat, but is called on the **_Cat_** class *itself*.

This is most often useful for methods that do things like create a new cat, or look up a cat in a database — you’d want to call this function *before* we had an individual cat yet, since the job of this function is to find/create one.

While many languages call these “class methods,” in Javascript they’re often called “static methods,” so JS uses the **_static_** keyword to create these methods.

Take a look at the `get(id)` method of the **_Customer_** classes. These methods are meant to be called on the class. They look up the corresponding customer in the database, and return a JS instance of the correct class. Find where this code is being used in the handlers and make sure you understand it.

Read about these here:

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/static

[Back to TOC](#table-of-contents)

### 4. Nunjucks

So far, we’ve used Express to render JSON or simple strings. In many cases, this might be all our backend does, and you’d have a dedicated front-end to parse JSON responses and create a UI.

However, it is possible for Express to template complete HTML pages, the way we did in Flask. To do this, we use the “Nunjucks” library, which is an implementation of the Jinja2 language in Javascript.

Take a quick look at the templates in **_/templates/_**. There’s nothing particular you need to do here yet, but you may find it useful to see how easy it is to template in JS.

By default, **_nodemon_** will not listen for changes to .html files. This means that when you start editing your templates, you’ll need to manually stop and start your server in order to see your changes take effect.

To fix this, you can tell nodemon what extensions to watch with the **_-e_** flag. For this assignment, it’s worth telling it to listen for changes to HTML, CSS, and Javascript, so you should start your server with the following command:

```
$ nodemon -e js,html,css
```

[Back to TOC](#table-of-contents)

### 5. Full Names

In several templates, we show customer names as `{{ firstName }} {{ lastName }}`. This is slightly tedious, that we have to write out both fields, but also might be inflexible for future data changes: what if we added a middle name field later? What if we added a prefix field for labels like “Ms.” or “Dr.”?

Add a function, `fullName`, to the Customer class. This should (for now) return first and last names joined by a space. Change the templates to refer directly to this.

[Back to TOC](#table-of-contents)

### 6. Saving Reservations

We’ve already written a **_.save()_** method for customers. This either adds a new customer if they’re new, or updates the existing record if there are changes.

We don’t yet have a similar method for reservations, but we need one in order to save reservations. Write this.

[Back to TOC](#table-of-contents)

### 7. Add Search Functionality

It would be nice to search for a customer by name, rather than having to find them in a list. Add a quick search form to the bootstrap navigation bar to search for a customer by name.

Do this by continuing the pattern of abstracting database operations to the model classes — any route(s) you write shouldn’t directly use the database. Think of a good name for any new methods on the class.

You can either make a new route and template to show results, or you could probably make it work with the existing listing index route and template (as you’d prefer).

[Back to TOC](#table-of-contents)

### 8. Best Customers

We like to show our best customers — those that have made the most reservations. Add a new route that finds our top 10 customers ordered by most reservations.

Like before, do this by adding functionality to the model class, so that there isn’t SQL directly in your route handlers.

Make sure you do this counting work in the database, rather than trying to do all the counting in Javascript.

[Back to TOC](#table-of-contents)

### 9. Getters and Setters

Read about getters/setters in JS:

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set

[Back to TOC](#table-of-contents)

### 10. Getters and setters allow you to use an object like this:

```
cat = new Cat();
cat.color = "orange";

console.log("This cat is ", cat.color);
```

[Back to TOC](#table-of-contents)

while having the color property incorporate business logic. For example, our class could be:

```
class Cat {
  get color() {
    return this._color;
  }

  set color(val) {
    if (val === "green")
      throw new Error("No green cats!");
    this._color = val;
  }
}
```

Here, we can have validation when we set a property, while “abstracting away” this operation. The user of this object doesn’t need to know/understand that this happens — just say things like `cat.color = "orange"`.

(Of course, we could have done this by writing a function named something like _setColor_, and then users of the class would set the color with `cat.setColor("orange")`, but these kind of explicit setter functions, or corresponding `getColor()` getter functions can be tedious to use, with all of those parentheses).

There are plenty of opportunities to use getters and setters in this codebase. Here are some examples of ways you could use them:

- For notes on a customer or reservation, use a hidden **_\_notes_** property to ensure that if someone tries to assign a falsey value to a customer’s notes, the value instead gets assigned to an empty string.
- Turn **_fullName_** into a getter.
- Use the getter/setter pattern with **_numGuests_** on a reservation, such that the setter throws an error if you try to make a reservation for fewer than 1 person.
- Use the getter/setter pattern with **_startAt_** on a reservation, so that you must set the start date to a value that is a **_Date_** object.
- Use the getter/setter pattern with **_customerId_** on a reservation, such that once a reservation is assigned a **_customerId_**, that key can never be assigned to a new value (attempts should throw an error).

Talk with your partner about other ways you could exploit the getter/setter pattern.

[Back to TOC](#table-of-contents)

### 11. Other fun things!

Should you wish to continue with these ideas, there is plenty of other functionality you could add to this system:

- Add a new field for middle name, which can be optional, but should appear in the full name displays.
- Add a feature to edit existing reservations. Make sure to keep SQL out of the routes themselves.
- On the customer listing page, show the most recent reservation for each. Make sure when you do this that you continue to list all customers, even those without any reservations!
- This uses the “Moment.js” library to format dates prettily. This is a powerful library for handling all sorts of time/date features. This is often used by websites to show “pretty relative dates”, like “5 minutes ago”, “1 week ago”, “more than 2 years ago”, and so on. Learn about this feature and change the customer detail page to show the most recent reservation for every customer, but with one of these pretty versions.
- Research how to write tests with supertest when you have a template-based Express app. Then write some tests for your routes! (You can unit test your model methods too.)
- Add proper full-text search (that can handle things more faster and more flexibly than **_ILIKE_** queries). PostgreSQL has a very comprehensive FTS (full text search) system.

This is a pretty big feature to learn about and add, but this might be a neat weekend day project if you’re interested in learning more about real-world backend search.

[Back to TOC](#table-of-contents)

## How I refactored this project (Files/Folders added/refactored)

### `.env` 
### `.gitignore`
### `db.js` refactor

#### Database Connection Refactor Explanation

The refactor of the `db.js` file introduces several improvements and best practices for managing database connections in a Node.js application, particularly one that uses PostgreSQL with the `pg` library. Below are the key changes and their implications:

##### 1. Environment Variable Management
- **Before**: The database URI was hardcoded (`"postgresql:///lunchly"`), making it inflexible and insecure for different environments (development, test, production).
- **After**: The refactor uses the `dotenv` package to load environment variables from a `.env` file. This allows for different database URIs for different environments (e.g., development and test) and keeps sensitive information like database credentials out of the codebase.

##### 2. Dynamic Database URI Selection
- **Before**: A single, static connection string was used, limiting the application to one database.
- **After**: The connection string is dynamically selected based on the `NODE_ENV` environment variable. This enables the use of a separate test database when running tests (`TEST_DB_URI` for `NODE_ENV=test`) and a development or production database (`DB_URI`) otherwise. This separation is crucial for maintaining a clean test environment and ensuring tests do not interfere with production data.

##### 3. Improved Error Handling and Logging
- **Before**: The connection was established without any feedback or error handling.
- **After**: The refactor adds a callback to the `connect` method, providing console logs for successful connections or errors. This improves debugging and monitoring of the database connection status.

##### 4. Security and Configuration Best Practices
- The inclusion of `.env` in the `.gitignore` file ensures that sensitive information (like database URIs and credentials) is not accidentally committed to version control, aligning with security best practices.
- The `dotenv` package is added as a dependency in `package.json`, formalizing its role in the project's environment variable management.

##### 5. Package.json Adjustments
- The `scripts` section of `package.json` has been updated to explicitly set `NODE_ENV` for different commands, ensuring that the correct database URI is used for each context (e.g., development, testing).

In summary, this refactor significantly improves the flexibility, security, and maintainability of the database connection setup. It allows for easy switching between different environments and databases, enhances error handling and logging, and ensures sensitive information is kept out of the codebase.

[Back to TOC](#table-of-contents)

### `src` folder

I moved the `app.js`, `server.js`, `templates` folder into the `src` folder.  

### `routes` folder
### `db` folder
### `utils` folder

