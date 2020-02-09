# R14 Server
The R14 Server is a modern node based architecture for rapidly developing application servers, seamlessly integrating industry standard cutting edge technologies like Node, Express, GraphQl, Mongo, and Redis. Built for speed and rapid development, the R14 Server avoids boilerplate and allows the developer to easily access everything through the app context via namespacing, factories, and method chaining. Abbreviations are also offered for many of the context objects to promote concise, fun to write, code. The idea is, spend less time building the app and more time developing the business logic. Below are the core concepts used to build a R14 Server.

## Table of Contents<!-- omit in toc -->

- [R14 Server](#r14-server)
  - [Contribute to R14 Server](#contribute-to-r14-server)
  - [Getting Started](#getting-started)
    - [Tools](#tools)
    - [Create a Project](#create-a-project)
    - [Project structure](#project-structure)
    - [Run your project](#run-your-project)
    - [Managing your project with Git](#managing-your-project-with-git)
  - [App Context](#app-context)
  - [Entities](#entities)
    - [Entities Config](#entities-config)
      - [Entity Config Options](#entity-config-options)
      - [Field object options](#field-object-options)
      - [Field Types](#field-types)
    - [Related Entities](#related-entities)
      - [Related Field Options](#related-field-options)
    - [Custom Events and Validators](#custom-events-and-validators)
    - [Defining Search filters](#defining-search-filters)
    - [Using Entities in the App](#using-entities-in-the-app)
      - [Entity Factory Method Options](#entity-factory-method-options)
      - [Entity Fields](#entity-fields)
      - [Creating and Updating Entities](#creating-and-updating-entities)
      - [Entity Relationship Methods](#entity-relationship-methods)
      - [Entity Sets](#entity-sets)
        - [EntitySet()](#entityset)
        - [EntitySet.forEach()](#entitysetforeach)
        - [EntitySet.map()](#entitysetmap)
        - [EntitySet.save()](#entitysetsave)
        - [EntitySet.delete()](#entitysetdelete)
    - [About the Entity Manager](#about-the-entity-manager)
  - [GraphQL](#graphql)
    - [Auto Generated Entity Schema](#auto-generated-entity-schema)
    - [Custom Resolvers](#custom-resolvers)
    - [Schema Config](#schema-config)
  - [Resolvers](#resolvers)
    - [Resolver Arguments](#resolver-arguments)
  - [Domain](#domain)
    - [Creating a domain class](#creating-a-domain-class)
    - [Using Domains in the App](#using-domains-in-the-app)
  - [Database](#database)
    - [The Query Builder](#the-query-builder)
      - [Choosing the base collection or table](#choosing-the-base-collection-or-table)
      - [Inserting Documents or Rows](#inserting-documents-or-rows)
      - [Updating Documents or Rows](#updating-documents-or-rows)
      - [Deleting Documents or Rows](#deleting-documents-or-rows)
      - [Using the select method to choose result keys](#using-the-select-method-to-choose-result-keys)
      - [Selecting values with Query Functions](#selecting-values-with-query-functions)
      - [Query Functions](#query-functions)
      - [Selecting values from joined or embedded documents](#selecting-values-from-joined-or-embedded-documents)
      - [Ordering Results](#ordering-results)
      - [Aggregating Results](#aggregating-results)
      - [Query Accumulator Functions](#query-accumulator-functions)
      - [Joining Results](#joining-results)
      - [Pagination and Offset](#pagination-and-offset)
        - [On cursor based pagination](#on-cursor-based-pagination)
      - [Caching Results](#caching-results)
      - [Matching and Filtering Results](#matching-and-filtering-results)
      - [Query Expression Builder](#query-expression-builder)
        - [Building Expressions](#building-expressions)
        - [Using like() for string comparison](#using-like-for-string-comparison)
        - [Full Text Search](#full-text-search)
        - [Using fields as values](#using-fields-as-values)
        - [Query Expression Builder Functions](#query-expression-builder-functions)
      - [Executing a Query](#executing-a-query)
      - [Query Result Sets](#query-result-sets)
    - [Database Client Instance](#database-client-instance)
      - [Mongo Client](#mongo-client)
  - [Caching](#caching)
    - [Setting Cache Values](#setting-cache-values)
    - [Getting Cache Values](#getting-cache-values)
  - [Storage](#storage)


## Contribute to R14 Server
We want this to grow like an open source project. We strongly encourage any Rule14 developers to contribute code and their unique skill sets. Whether it's a bug, a new core class, or just edits to this document, your help would be greatly appreciated. 

## Getting Started
The following documentation will help a developer get started with creating a R14 Server. Most of the examples and code are written in node using javascript ES2018. If you are not familiar with either of these technologies, it is highly recommended that you become comfortable with them before proceeding. At this time all scripts and tools have been written in linux. Any help to port this to windows and mac would be greatly appreciated. 

### Tools
1. VSCode code editor (or any ide with full Node integration) https://code.visualstudio.com/
2. Chrome 
3. Install Git
4. Install npm
  
### Create a Project
As previously noted, installation instructions have been written for **LINUX**. If you would like to help convert everything to node and test for different platforms, that would be very helpful.

Using git, clone r14-server from https://github.com/hgmventures/r14-server into your workspace.
```console
$ git clone https://github.com/hgmventures/R14-server r14-server
``` 
From your workspace (for example, /var/www/html) execute **./r14-server/install**. 

```console
$ ./r14-server/install
```

You will be first asked for a project name. The project name will become the folder name of the project added to your workspace (for example, /var/www/html/my-project). 

The code for your project will be located in the ./app folder. The ./app folder should be the root of the projects git repository. To develop an existing R14 Server project, add it's git url when prompted. Existing projects will be cloned into the ./app folder.

### Project structure
```
project
└───app
│   │
│   └───domains
│   └───config
│   
└───core
└───scripts
```

### Run your project
From the ./scripts folder execute run-server.
```console
$ cd ./scripts
$ ./run-server
```
### Managing your project with Git
The r14 server will install several repositories when creating an app. This makes it possible to update core code and scripts without modifying the apps code. All project specific code will be in the ***./app*** folder, and this folder should be used as the project repository.

```console
$ cd ./app
$ git push
``` 

<!-- ## What's Next?
Once an instance of a R14 Server has been installed,  -->

## App Context
An important concept that is seen throughout this documentation is the app context. An instance of the R14 app context is available to Resolvers, Domains, and most user defined objects and functions. Abbreviations are also offered for many of the context objects to promote concise, fun to write, code. What will not be found is a lot of boilerplate code. Almost everything the developer needs to find is readily available through the app context and core components. The app context is design to use a consistent mix of the namespacing, method chaining, and factory patterns.

## Entities
Entities are the object instance representation of a data-source mapped business object. For example, a user, client, car, graph, etc... Usually they are directly mapped to a database row or document. Entities make it very simple and straight forward to perform CRUD functionality. Entities are very powerful in their simplicity. Other features include validation using strict typing on insert or update, and auto generated GraphQL queries and mutations.

### Entities Config
The entities config is very similar to a relational database schema. It is located at ***config/entities.config.js***. The entities config defines each entities fields and options. Each field must have at least a fieldType.  The entity config should return a simple schema object, with the name of the entity as each object key. The entity objects have the following options:

#### Entity Config Options
|Name|Type|Description|
|:----|:----|:----|
|fields|Object|A object with field names as keys  and field options (required)|
|timestamps|Boolean|If true, createdAt and updatedAt will automatically be created and updated|

In the following example, a basic user entity is defined. The **uid** is defined as type **ID**, which is a special kind of type. The **ID** fieldType is considered a unique identifier field, and is auto generated using a shortid and indexed. Shortids are short universal identifiers with a very low collision rate, https://www.npmjs.com/package/shortid. If a field is required, the field type can use the **isRequired** property. The **age** field would not be required, and if no data is given it would be saved as NULL.
```js
const R14 = require('../../core/R14');
let types = R14.fieldTypes;
module.exports = {
  user: {
    fields: {
      uid: types.ID,
      name: types.String.isRequired,
      username: types.String.isRequired,
      email: types.String.isRequired,
      age: types.Int
    }
  }
}
```
In the below example several new fields are added. The **clientUid** field is type **ForeignID**, which is another special type similar to **ID**. The **ForeignID** field is the **ID** field from another collection, much like a foreign key in SQL. They are automatically indexed on the data source level for relational mapping of entities. Fields can also be objects of options. In the **active** field below, an object is used instead of a fieldType to specify additional options.

```js
const R14 = require('../../core/R14');
let types = R14.fieldTypes;
module.exports = {
  user: {
    timestamps: true,
    fields: {
      uid: types.ID,
      clientUid: types.ForeignID.isRequired,
      name: types.String.isRequired,
      username: types.String.isRequired,
      email: types.String.isRequired,
      age: types.Int,
      active: {type: types.Boolean, default: true},
    }
  }
}
```
#### Field object options ####
When using objects instead of a fieldType, the following options are available:
|Name|Type|Description|
|:------|:------|:------|
|type|FieldType (required)|A FieldType |
|default|Based on FieldType|Default Value if field is not set|
|isRequired|Boolean|Denotes where the field is required

#### Field Types ####
The below table lists the entity field types.
|Type|Description|
|:------|:------|:------|
|ID|An auto generated shortid unique identifier|
|ForeignID|A foreign ID key shortid unique identifier|
|Int|Integer Type|
|String|String Type|
|Boolean|Boolean Type|
|Date|Javascript Date Type|
|Float|A Float Type|
|Entity|A one to one entity relationship type|
|Entities|A one to many entity relationship type|

### Related Entities
In the next example, an entity relationship is defined. This allows the entity manager to automatically generate GraphQL relationships in queries, and creates entity helper methods to easily manipulate relative entities. Entities can be related to entities as one to one, or one to many. In the below example, a user has one client, and the client may have many users. In the config, the user **client** field object type is **Entity**, which defines a one to one relationship between a user and a client. In the client **user** field, the object type is **Entities**, which defines a one to many relationship between a client and users. The following options are available for related field objects:

#### Related Field Options
|Name|Type|Description|
|:----|:----|:----|
|type|FieldType (required)|A FieldType of either Entity or Entities.<br>Entity defines a one to one relationship.<br>Entities defines a one to many relationship.|
|entity|String|The name of the related entity|
|localField|String|The local ForeignKey field name for the related entities ID|
|foreignKey|String|The related entities ID field name

```js
const R14 = require('../../core/R14');
let types = R14.fieldTypes;
module.exports = {
  user: {
    timestamps: true,
    fields: {
      uid: types.ID,
      name: types.String.isRequired,
      username: types.String.isRequired,
      email: types.String.isRequired,
      clientUid: types.ForeignID.isRequired,
      age: types.Int
      active: {type: types.Boolean, default: true},
      client: {
        type: types.Entity,
        entity: 'client',
        localField: "clientUid",
        foreignField: "uid",
      }
    }
  },
  client: {
    timestamps: true,
    fields: {
      uid: types.ID,
      name: types.String.isRequired,
      email: types.String.isRequired,
      users: {
        type: types.Entities,
        entity: 'user',
        localField: 'uid',
        foreignField: 'clientUid'
      }
  }
}
```
### Custom Events and Validators
Entities can define custom entity and field validators. ***Please note, this functionality is still in Development.***

```js
const R14 = require('../../core/R14');
let types = R14.fieldTypes;
module.exports = {
  car: {
    timestamps: true,
    onAction: ({action, values, app})=>{
      switch(action){
        case 'create':
          values = app.dm.car.create(values);
          if(! values) throw new Error('Invalid Car');
          break;
      }
      return {
        success: true,
        values: values
      }
    }
    onUpdate: ({values, app})=>{
      values = app.dm.car.update(values);
      if(! values) throw new Error('Invalid Car');
      return {
        success: true,
        values: values
      }
    }
    fields: {
      uid: types.ID,
      clientUid: types.ForeignID.isRequired,
      name: types.String.isRequired,
      username: {
        type: types.String.isRequired,
        validator: ({value, values, app})=>{
          if(app.dm.user.usernameExists(value, values.uid)) throw new Error('Username Exists');
          return { success: true }
        }
      }
      email: types.String.isRequired,
      age: types.Int,
      active: {type: types.Boolean, default: true},
    }
  }
}
```

### Defining Search filters
Search filters allow graphql find queries, and related entities methods to define custom filters. ***Please note, this functionality is still in Development.***

### Using Entities in the App
Entities are available in the app context using the member namespace **app.entity** or **app.ent**. Each entity has a corresponding, by key name, entity factory method in app entity context The entity method returns a promise white resolves to a instantiated an entity. 

The following instantiates a new user entity.
```js
let user = await this.ent.user();
```

The entity method can also initialize an existing entity. To hydrate an existing entity, either pass it's **ID** field or a key / value object as the first parameter of the entity method.

Using the **ID** field to hydrate a user entity.
```js
let user = await this.entity.user(uid);
let userData = {
  name: user.name,
  username: user.username,
  email: user.email
};
```

Using an object to hydrate a user entity. If more than one result is found, the first result will be used.
```js
let user = await this.entity.user({uid: uid});
```
To initialize a cached entity, options can be passed as the second parameter. Read below in the database section about cacheTime and cacheRefresh options. ***Please note, this functionality is still in Development.***
```js
let user = await this.entity.user(uid, { cacheTime: 5 });
let userData = {
  name: user.name,
  username: user.username,
  email: user.email
};
```
#### Entity Factory Method Options
|Name|Type|Description|
|:----|:----|:----|
|cacheTime|Integer|Minutes to cache entity if not cached. Otherwise the cached entity is returned|
|cacheRefresh|Boolean|Indicates if the entity should be re-cached. Default to false.|

The entity object utility method **init(key, options)** can be called on non-hydrated entities, and works the same as above. It returns a promise the resolves to true if the entity is successfully initialized.
```js
let user = await this.entity.user();
let isNew = await user.init(user, { cacheTime: 5 });
```
The entity object utility method **isNew()** returns true if the entity has not been hydrated.  
```js
let user = await this.ent.user();
if(user.isNew()) throw new Error(`User ${user} not found`);
```
#### Entity Fields
Entity objects contain getter setter properties, based on the field name keys defined in the entities config. 

In the example below, the entities getter properties are accessed to set values in an other object.
```js
let user = await this.entity.user(uid);
let userData = {
  name: user.name,
  username: user.username,
  email: user.email
};
```
The entity object utility method **extract()** returns a simple key value object containing the entities field values.
```js
let allUserData = user.extract();
```
The next example demonstrates the use of setter properties, which are used to update field values. Like getter properties, their name corresponds to the field name defined in the entities config.
```js
let user = await this.ent.user();
user.username = 'foob';
user.name = 'Foo Bar';
user.email = 'foob@rule14.com';
```
The entity object utility method **populate(object)** sets the field values based on object keys.  
```js
let user = await this.ent.user();
user.populate({
  username: 'foob',
  name: 'Foo Bar',
  email: 'foob@rule14.com';
});
```
#### Creating and Updating Entities

To entity object utility method **save()** validates and saves an entity, and returns a promise that resolves to true on a successful update. Once an entity is saved it's generated **ID** will be available, as seen below.

```js
let user = await this.ent.user();
user.username = 'steve';
user.name = 'Steve';
user.email = 'steve@gmail.com';
await user.save();
let generatedUid = user.uid;
```
In the next example the name field of the user entity is updated.
```js
let user = await this.ent.user(uid);
user.name = 'Steven';
await user.save();
```

#### Entity Relationship Methods
If an entity has relationships defined in the entity config, related entity factory methods will be available in the entities context with a name corresponding to the field key defined in the entities config. The related entity methods accept the same options as entities.

In the following example, the users client entity is initialized.
```js
let user = await this.ent.user(uid);
let client = await user.client({cacheTime: 5});
```

The next example would return a clients users in an EntitySet.
```js
let client = await this.ent.client(uid);
let users = await client.users();
users.forEach(user=>{
  console.log(`User: ${user.uid}`);
});
```
#### Entity Sets
Entity sets are an iterable object returned by the entity manager. They make it easy to query and update sets of entities. Like an entity, Entity sets are available in the app context using the member namespace **app.entity** or **app.ent**. Instead of using the entity key name (like user for example), an entity set is instantiated using the pluralized version of the key name (like, users). The entity set factory method has two parameters, the query parameter and an option parameter, and returns a promise which resolves an entity set of hydrated entities. The query parameter can be either a query expression or a key value equality object.

The following example would return set of active users.
```js
let users = await this.ent.users({active: true});
users.forEach(user=>{
  console.log(`User: ${user.uid}`);
});
```
Entity sets make is simple to update an entity. The following example would update all inactive users to active and save the changes.
```js
let users = await this.ent.users({active: false});
users.forEach(user=>{
  user.active = true;
});
await users.save();
```

The next example demonstrates using a query expression to create more complicated entity sets.
```js
let exp = this.db.qryExp();
let users = await this.ent.users(
  exp.eq(active, false).orGt('age',25)
);
```

##### EntitySet()
The entity set factory method that returns a promise resolving to an entity set.
|Name |type| Description |
| :------ | :------ | :------ |
|query|Query Expression<br>Key Value Equality Object|The object to filter entities by
|options|Object|Options for initializing the entities

##### EntitySet.forEach()
Iterates the entity set passing the current entity by reference to the callback function. Similar to Array.forEach()
|Name |type| Description |
| :------ | :------ | :------ |
|callback|Function(entity)|The function to be called with the current entity in the iteration 

##### EntitySet.map()
Iterates the entity set passing the current entity by reference to the callback function. The returned values is mapped to an array, similar to Array.map()
|Name |type| Description |
| :------ | :------ | :------ |
|callback|Function(entity)|The function to be called with the current entity in the iteration. 

##### EntitySet.save()
Saves all updated entities in the entity set. Returns a promise which returns to true on success.

##### EntitySet.delete()
Delete all entities in the entity set. Returns a promise which returns to true on success.

### About the Entity Manager
The Entity Manager controls entities and how they interact with the rest of the app. Using the entities defined in **entities.config.js**, the entity manager validates and filters any data changes to an entity using strict data typing, controls caching, and it's connection to the underlying data source. The entity manager keeps entity instances for persistent consistency between equal entity objects. ***Please note, this functionality is still in Development.***

## GraphQL
At the core of the R14 Server App is it's built in integration with GraphQl. GraphQL allows the developer to request and update only the data they need, at the same time using strict type validation. It also makes it possible to query complex and dynamic data sets from the GraphQL server without needing to create extra backend endpoints, saving time on development and backend change requests. In GraphQL there are 2 main request types, mutations and queries. Mutations are any request that causes an insert, update, or delete on the api data source. Queries fetch data. Mutations and queries use nearly identical syntax, but the separation of usage helps developers organize API concerns by function, creating more maintainable and upgradable code. Before proceeding with this section you should be very comfortable with GraphQl, if not  https://graphql.org is a great place to start.

For access to the app specific api documentation, R14 Server apps uses a web based GraphQL API explorer called GraphiQL. A link and credentials for the GraphiQL development instance associated with a R14 App should be provided by the back-end development team.   

### Auto Generated Entity Schema
The R14 Server entity manager uses the entity config to auto-generates a GraphQl schema. Automatically creating GraphQl query and mutation crud functionality per entity, including insert, insertMany, update, updateMany, delete, deleteMany, find one, and find by filters. By default the queries and mutations are named [action][entityName][+s for many], so, for example: insertUser for insert or insertUsers for insertMany. Furthermore, the auto-generated find query allows for complex queries on one to one and one to many relationships between entities without allowing (N+1) leaks. This also allows the developer to quickly add new fully functional entities in minutes.

The following example shows the GraphQl that would be auto-generated based on the first entities config example. The mutations and queries are automatically bound to the entity manager. Using GraphiQl, you can easily use and test the generated and extended schema. 

```graphql
type User {
  uid: ID
  name: String
  username: String
  email: String
  clientUid: ID
  client: Client
  active: Boolean
  createdAt: String
  updatedAt: String
}

input UserFilter {
  uid: ID
  name: String
  username: String
  email: String
  clientUid: ID
  active: Boolean
  createdAt: String
  updatedAt: String
}

type UserNode {
  uid: ID
  name: String
  username: String
  email: String
  clientUid: ID
  client: ClientNode
  active: Boolean
  createdAt: String
  updatedAt: String
}

type UserPayload {
  user: User
  success: Boolean
}

type UserPayloads {
  users: [UserPayload]
  success: Boolean
}

type UserResultSet {
  nodes: [UserNode]
  totalCount: Int
  pageInfo: PageInfo
}

input CreateUserInput {
  name: String!
  username: String!
  email: String!
  clientUid: ID!
  active: Boolean
  createdAt: String
  updatedAt: String
}

input UpdateUserInput {
  uid: ID!
  name: String
  username: String
  email: String
  clientUid: ID
  active: Boolean
  createdAt: String
  updatedAt: String
}

type PageInfo {
  page: Int!
  resultsPerPage: Int!
  hasNextPage: Boolean!
}
enum SortDirection {
  ASC
  DESC
}
input SortOption {
  field: String!
  order: SortDirection!
}

type Query {
  """Lookup a user."""
  user(uid: ID): User
  """Lookup users by filters."""
  users(filter: UserFilter, page: Int, resultsPerPage: Int, sort: [SortOption!]): UserResultSet
}

type Mutation {
  """Create a new user."""
  createUser(input: CreateUserInput): UserPayload

  """create a set of users."""
  createUsers(input: [CreateUserInput!]): UserPayloads

  """Update a user."""
  updateUser(input: UpdateUserInput): UserPayload

  """Update a set of users."""
  updateUsers(input: [UpdateUserInput!]): UserPayloads

  """Delete a user."""
  deleteUser(uid: ID): UserPayload

  """Deletes a user."""
  deleteUsers(uid: [ID]): UserPayloads
}
```

### Custom Resolvers
Custom resolvers can extend the auto-generated entity GraphQl. To bind a custom resolver to a GraphQl function, simple add the resolver to the resolvers config, using the functions name as the resolver key. Form more information on resolvers, see below. ***Please note, this functionality is still in Development.***

### Schema Config
While the entity manager will auto-generate many of the GraphQl queries and mutations that an app will need, in most cases the schema will need to be extended. To extend the schema, simple add or extend new types, inputs, and functions. Queries and Mutations, and any other generated GraphQl must be extended using the extend keyword. The GraphQl Schema config is located at ***config/schema.config.js***.

The below is an example extended GraphQl schema, which adds support for simple user authentication functions.

```graphql
input AuthUserInput {
  password: String!
  username: String!
}

type AuthUserPayload{
  user: User!
  accessToken: String
  success: Boolean!
}

input ValidateUserInput {
  uid: String!
  accessToken: String!
}

type ValidateUserPayload{
  uid: String!
  success: Boolean!
}

extend type Query {
  """ Validate that an accessToken belongs to a user uid. """
  validateUser(input: ValidateUserInput!) : ValidateUserPayload
}

extend type Mutation {
  """ Authenticate, update lastAuthAt, and return a newly generated user AccessToken. """
  authUser(input: AuthUserInput!) : AuthUserPayload
}
```
## Resolvers
The **Resolvers** class defines the GraphQl resolvers for the application. In GraphQl, when a query or mutation is validated and executed, it calls a root field resolver method. In the R14 Server, GraphQl queries and mutations defined in **schema.config.gql** are automatically mapped by name to the corresponding method in Resolvers.js. Resolver methods have two parameters, an input arguments object and a query info object. They and should return a simple object representing the return values as described by the schema. Resolvers can be async, return promises, or just data. Like other components in the R14 Server, the resolver methods are in the app context, and can access all methods and properties through **this**. To learn more about resolvers please read https://graphql.org/learn/execution/.

The Resolvers class must be located at **./app/Resolvers.js**.

A resolver method is analogous to a controller action in the MVC pattern. In general it is best to keep them simple, thin, and easy to understand. In general, they should only be concerned with controlling input and output, leaving any business logic or data manipulation to the model. 

### Resolver Arguments ###
|Name|Description
|:----|:----|
|**args**|The input arguments as defined by the schema.|
|**info**|Info about the query or mutation request, such as requested fields.|

In the example Resolvers class below two resolvers are defined. They would be automatically mapped by name to their corresponding mutation and query, as defined in the example below. 

```js
const R14 = require("../../core/R14");
module.exports = class Resolvers extends R14.Resolvers {
  async authUser({ input }, info) {
    let user = await this.dm.user.initByCredentials(input.username, input.password);
    if (user.isNew()) throw Error("User not found.");
    let accessToken = await this.dm.user.generateAuthToken(user);
    return {
      user: {
        uid: user.uid,
        name: user.name,
        username: user.username,
        clientUid: user.clientUid
      },
      success: success,
      accessToken: accessToken
    }
  }
  async validateUser({ input }, info) {
    let tokenData = await this.utils.auth.decodeToken(input.accessToken);
    return {
      uid: input.uid,
      success: input.uid === tokenData.uid
    }
  }
}
```

## Domain
In the R14 server, application domains are user defined classes which represent specific areas of knowledge as related to the business architecture of the application. Domains are responsible for executing buisness and application logic, as well as updating the model. In the simplest sense, this is where most of the applications code should be written. Most importantly, domains provide a consistent and easily accessible way to organize business logic.

### Creating a domain class
Domains classes should be added to the ***domains/*** folder, the file should have the same name as the domain. The domain must be named like ***[key]|Domain***. For example, the **UserDomain** class would be saved to ***domains/UserDomain.js*** and would be accessed from the app context at **app.domain.user**.

The following is an example of a simple user domain, with several authentication methods.
```js
const R14 = require("../../core/R14");
module.exports = class UserDomain extends R14.Domain {
  async generateAuthToken(user) {
    return await this.utils.auth.generateToken(`user${user.uid}`, {
      uid: user.uid
    });
  }
  async initByCredentials(username, password) {
    let user = await this.ent.user({ username: username });
    if (user.isNew()) throw new Error("User not found.");
    if (! await this.utils.auth.isPassword(password, user.password)) throw new Error("Wrong password.");
    return user;
  }
}
```
### Using Domains in the App
Domains are available in the app context using the member namespace **app.domain** or **app.dm**. Each domain instance is available through app domain context corresponding to the domain class name. So, for example, app.cache.user would return the UserDomain instance. Domain instances are singleton and persist for the duration of the current request.

The example below shows the UserDomain being accessed by a resolver to initialize a user entity by credentials. 
```js
let user = await this.dm.user.initByCredentials(input.username, input.password);
if (user.isNew()) throw Error("User not found.");
```

## Database
R14 Server provides access to databases through adapters. Adapters abstract and normalize database behaviors, allowing for easier migration between technologies, as well as coding consistency between projects. The database is available in the app context using the member namespace **app.database** or **app.db**.

### The Query Builder
The R14 Server Query Builder provides the developer with a simple way to programmatically create complex queries using methods abstracted from the underlying database technology. The query builder uses a lot of terminology and ideas from relational database queries, but with modern twists found in NoSql databases. The **app.db.query** or **app.db.qry** factory methods create and return an instance of a query builder class. 

The following is a very basic example of a select style query, which returns all documents from the user collection. 

```js
let qry = this.db.query();
let results = await qry.from('user').run();
```
The following query will retrieve the username and email for all users that are 25 years old. Notice the similarities to a SQL query.

```js
qry.select(['username','email'])
   .from('user')
   .where({age: 25})
   .orderBy({username: 'DESC'});
```
###### SQL<!-- omit in toc -->
```sql
SELECT username, email
FROM user
WHERE age = 25
ORDER BY username DESC
```

#### Choosing the base collection or table
The **from** or **frm** method is similar to both FROM statement in sql or the db.collection() method in mongo. It is required for any query that finds data. The from method accepts either a string, or another query build object.
```js
qry.from('user');
```
###### SQL<!-- omit in toc -->
```sql
FROM user
```

Subqueries are sometime nessessary to complete complex problems. The below example uses the from method to pass another query. ***Please note, this functionality is still in Development.***

```js
  let subquery = this.db.qry();
  subquery.from(users);
  qry.from(subquery);
```
###### SQL<!-- omit in toc -->
```sql
SELECT *
FROM (
  SELECT *
  FROM users
)
```
#### Inserting Documents or Rows ####
The **insert** or **ins** method is similar to the INSERT statements in sql or the insertOne() and insertMany() methods in mongo. A key value object containing the fields to be inserted must be passed to the query **values** or **vals** method, like in the example below.
```js
qry.insert('user').values({
  username: 'foo',
  email: 'bar',
  age: 30
});
```
###### SQL<!-- omit in toc -->
```sql
INSERT INTO user (username, email, age)
VALUES ('foo','bar',30)
```
#### Updating Documents or Rows ####
The **update** or **upd** method is similar to the UPDATE statements in sql or the updateMany() and updateOne() methods in mongo. A key value object containing the fields to be updated must be passed to the query **values** or **vals** method. The **where** or **whr** method is use to update records based on a criteria, as in the following example. 
```js
qry.update('user').values({
  username: 'foo',
  email: 'bar',
  age: 30
}).where({uid:'asdf123'});
```
###### SQL<!-- omit in toc -->
```sql
UPDATE users
SET username: 'foo', email = 'bar', age = 30
WHERE uid = 'asdf123'
```
#### Deleting Documents or Rows ####
The **delete** or **del** method is similar to the DELETE statement in sql or the deleteMany() method in mongo. Again, in the next example the **where** or **whr** method is use to update records based on a criteria.
```js
qry.delete('user').where({uid:'asdf123'});
```
###### SQL<!-- omit in toc -->
```sql
DELETE FROM users WHERE uid = 'asdf123'
```

#### Using the select method to choose result keys

The **select** or **sel** method is similar to both SELECT statement in sql or $project in mongo. If no parameters are given, all data is selected.

```js
qry.select();
```
###### SQL<!-- omit in toc -->
```sql
SELECT *
```

Passing an array of strings will return the given value as results
```js
qry.select(['username','email']);
```
###### SQL<!-- omit in toc -->
```sql
SELECT username, email
```

When as object is passed to select, each key value pair is evaluated. If a string is returned, the key is used as an alias. If true is returned, the key is used as is. 

```js
qry.sel({
  username: true,
  emailAlias: 'email'
});
```
###### SQL<!-- omit in toc -->
```sql
SELECT username, email as emailAlias
```
#### Selecting values with Query Functions ####
Sometimes it is necessary or convenient to format or filter select values. In the example below the selcted values are formatted using query functions available in **query.fn** or **query.function**. See the ***Query Functions*** section for a list of available functions.

```js
qry.select({
  email: qry.fn.toLower('email'),
  username: qry.fn.toUpper('email'),
  tagline: qry.fn.trim('tagline')
});
```
###### SQL<!-- omit in toc -->
```sql
SELECT uid, LOWER(email) as email, UPPER(username) as username, TRIM(tagline) as tagline
```
In the following example, query functions are called inside one another to filter the data in a more complex way. The tagline will first have whitespace removed from the left, and will then be converted to lowercase.
```js
qry.select({
  tagline: qry.fn.toLower(
    qry.fn.ltrim('tagline')
  )
});
```
###### SQL<!-- omit in toc -->
```sql
SELECT uid, LOWER(email) as email, UPPER(username) as username, TRIM(tagline) as tagline
```

#### Query Functions ####
Query functions can be used to format a fields value in the select object, or before it is processed in each stage of groups, joins, ordering, of filtering. Unlike queries and expressions, they do not follow the builder pattern, but returns a single immutable function object. A large set of query functions are available in **query.fn** or **query.function**. Many of the function names and parameters are based on mongoDB Aggregation Operators. For reference please refer to following: https://docs.mongodb.com/manual/reference/operator/aggregation/

#### Selecting values from joined or embedded documents ####
Selecting an object from a joined or embedded documents works in the same was a regular selection. The select key matching the joined or embedded document will automatically set it's select object to use it's context. The following example show how to select client uid and name. If the select key matches the joined or embedded document, the select object will be set to the child objects context. So in the example below, it is not necessary to use client.uid and client.name, because the select object is in the context of client.
```js
qry.sel({
  name: true
  client: ['uid','name']
});
```
In many cases, there may be many levels of embedded or joined data in one document. The below example demonstrates using a more complex select object using aliases and selecting values from the city object embedded in client.
```js
qry.sel({
  nameAlias: 'name',
  client: {
    uid: true,
    clientNameAlias: 'name',
    city: ['longitude','latitude']
  }
});
```

#### Ordering Results ####
To **orderBy** or **ord** method is used to order results. It accepts either an key value object or an array of order objects. When an object is passed to the orderBy method, it's keys should match fields and have order values of **ASC** or **DESC**.  When an array of objects is passed, each object should have the keys **field** and **order**.

In the below example, the results will be ordered by name descending and createdAt descending.
```js
qry.orderBy({
  name: 'DESC',
  createdAt: 'ASC'
});
``` 
###### SQL<!-- omit in toc -->
```sql
ORDER BY name DESC, createdAt ASC
```
In the next example, an array of order objects is passed to the ord method, which allows for the usage of query functions.
```js
qry.ord([
  {
    field: qry.fn.lower(
      qry.fn.trim('name')
    ),
    order: 'DESC'
  },
  {
    field: 'createdAt',
    order: 'ASC'
  }
]);
```
###### SQL<!-- omit in toc -->
```sql
ORDER BY LOWER(TRIM(name)) DESC, createdAt ASC
```

#### Aggregating Results ####
The ***groupBy*** or ***grp*** methods are similar to the GROUP BY statement in sql or aggregate() method in mongo. It is used to process and return computed results by grouping documents into a single result. The groupBy method accepts an ordered object similar to the select object. The keys represent the fields that can be used in the select object. If the select method is not used, the keys in the groupBy object will be returned directly. The group by objects keys values can either be boolean, string, or a query accumulator function. 

In the following example the user results will be grouped by role. If the keys value is true the corresponding fields data will be used as a primary group identifier in the order in which they are given.
```js
qry.groupBy({
  role: true,
});
```
###### SQL<!-- omit in toc -->
```sql
SELECT role
FROM users
GROUP BY role
```
In the next example the user results will again be grouped by role, but the alias roleAlias will be returned. Like in the select object, if a field string is given, the key will be used as an alias. 
```js
qry.groupBy([{
    roleAlias: 'role',
}]);
```
###### SQL<!-- omit in toc -->
```sql
SELECT role as roleAlias
FROM users
GROUP BY role
```
Query Accumulator Functions are used to process data which is grouped by the primary group identifiers. In the example below, results will be grouped by role and will return the total count and average age of users by role.

```js
qry.groupBy({
  role: true,
  total: qry.fn.count('uid'),
  avgAge: qry.fn.avg('age')
});
```
###### SQL<!-- omit in toc -->
```sql
SELECT role, COUNT(uid), AVG(age)
FROM users
GROUP BY role
```

#### Query Accumulator Functions ####
Query accumulator functions are used in the groupBy object to process grouped data. Unlike queries and expressions, they do not follow the builder pattern, but returns a single immutable function object. A large set of query accumulator functions are available in **query.fn** or **query.function**. Below is a list of the available accumulator functions. 

|Function | Description |
| :------ | :------ |
|sum(field)|The ***sum*** query accumulator function return the sum of numerical values.|
|count()|The ***count*** query accumulator function return total count of results by group.|
|avg(field)|The ***avg*** query accumulator function returns an average of numerical values.|
|first(field)|The ***first*** query accumulator function returns the first result in each grouped set.|
|last(field)|The ***last*** query accumulator function returns the last result in each grouped set.|
|max(field)|The ***max*** query accumulator function returns the highest value for each group.|
|min(field)|The ***min*** query accumulator function returns the lowest value for each group.|
|push(field)|The ***push*** query accumulator function returns an array of values for each group.|
|array(field,&nbsp;isUnique)|The ***array*** query accumulator function returns an array of values for each group. If the second parameter is true, only unique values will be returned, defaults to false|
|addToSet(field)<br>unique(field)|The ***addToSet*** and ***unique*** query accumulator functions return an array of unique values.|
|stdDevPop(field)|The ***stdDevPop*** query accumulator function returns the population standard deviation of the grouped values.|
|stdDevSamp(field)|The ***stdDevSamp*** query accumulator function returns the sample standard deviation of the grouped values.|

#### Joining Results ####
There are many instances where it is impossible or inappropriate to use completely normalized data collections. 

The ***join*** or **jn** methods are similar to join statements in sql or $lookup in mongo. This method is used join data collections with a one to many relationship. In the example below, all todo items from a user are inserted into the user result set as an embedded document named by the **as** key. Each result will contain an array of todo objects with the uid and description keys.

```js
qry.frm('user').sel({ name: true, todos: ['uid','description'] });
qry.join({
  from: 'todo',
  localField: 'uid',
  foreignField: 'userUid',
  as: 'todos'
});
```
As shown in the next example, a select object can be passed to the **select** key and all of it's keys will be available to the select object. Also, instead of localField and foreignField, a query expression is passed to the ***where*** key. ***Please note, this functionality is still in Development.***
```js
qry.jn({
  select: ['uid','name'],
  from: 'client',
  where: qry.exp().eq('clientUid','$client.uid'),
  as: 'client'
});
```
The ***leftJoin*** or ***ljn*** methods work like a LEFT JOIN statement in sql or $lookup with $arrayElemAt in mongo. This method is used to join data collections with a one to one relationship. In the example below, a single client object will be inserted into the user result set. 

```js
qry.leftJoin({
  from: 'client',
  localField: 'clientUid',
  foreignField: 'uid',
  as: 'client'
});
```

#### Pagination and Offset ####
The query object supplies several methods for limiting the number of results returned from a query.

The **page** method returns data limited by page and results per page. The following example would the first page of results, with 10 results per page. 
```js
qry.page(1, 10);
```
The **pageRange** method returns data limited by a page range and results per page. The following example would return pages 3 to 5, with 10 results per page. 
```js
qry.pageRange(3, 5, 10);
```
The **offset** method returns data limited between a start and end result row count. The following example would return results 20 to 30.
```js
qry.offset(20, 30);
```

The **resultSet.count()** method returns the total result count of the limited query. Please be aware that this is a separate query and can have a performance impart if not cached.
```js
let qry = this.db.qry();
let res = await qry.from('user1').page(1,10).run();
let totalUsers = await res.count();
```

##### On cursor based pagination #####
Cursor based pagination is generally much more efficient than offset pagination. It is widely used for things such as fetching infinite data sets. ***Please note, this functionality is still in Development.***

#### Caching Results ####
By using the cache adapter, the query builder can cache the results of queries and return all subsequent requests from memory within a time frame, for extremely performant results. This is very powerful to improving performance with often called queries or complex slow queries that only need to be re-run at specific intervals.

The ***cacheTime*** method sets the amount of time in minutes the results of a query will be cached. If cacheTime is set, and the results of the given query is in the cache, it will return the cached results. If the given query is not in the cache, it will be executed and cached for the amount of time given. 
```js
qry.cacheTime(5);
``` 
The ***cacheRefresh*** method will always execute the query whether or not the results are in the cache. If cacheTime is set, the new results will be cached. ***Please note, this functionality is still in Development.***
```js
qry.cacheRefresh(true);
``` 
The **cache** and **csh** methods accept either a number representing time in minutes, or an object containing cache option keys **time** and **refresh**. Cache options set the behaviors above in cacheTime and cacheRefresh ***Please note, this functionality is still in Development.***
```js
qry.cache({
  time: 5,
  refresh: true
});
```

#### Matching and Filtering Results ####
The **where** and **whr** methods are very similar to both WHERE statement in sql or $match in mongo. The where method is used to filter results based on the evaluation of a boolean expression and accepts either a key / value object or a Query Expression Object. If a key / value object the expression will evaluate to true based on a simple equality match.

The following example would return all users who's age is 25 and username is foo.
```js
qry.from('user')
   .where({username: 'foo',age: 5});
```
###### SQL<!-- omit in toc -->
```sql
SELECT *
FROM users
WHERE username = 'foo' AND age = 5
```
#### Query Expression Builder #####
Query expressions use the builder pattern to create complex logical expressions. A new instance is created by calling **query.expression()** or **query.exp()**. Query expressions can be passed to the where method, or used in joins and groups. Building expressions makes it very easy and transparent to programmatically create expressions when doing things like dynamic filtering.

The following example demonstrates the use of a query expression passed to the where method. The below expression will evaluate to true if the users are is 25, and the query will return results matching this expression.
```js
let exp = qry.expression();
exp.eq('age',25);
let res = await qry.from('user').whr(whr).run();
```
###### SQL<!-- omit in toc -->
```sql
SELECT *
FROM users
WHERE age = 25
```
##### Building Expressions #####
Query Expressions follow the builder pattern. Each method call returns the updated query expression object. Functions can be called individually, or be built using method chaining. By default each expression is added to the overall expression as a logical AND. To add an expression as a logical OR, either the **or(expression)** method can be used or the prefix **or** can be added to the method name. 

In the example below, each expression method call adds a new expression to the compound expression object as a logical boolean AND.  
```js
let exp = qry.exp();
exp.like('username','%eve%', false);
exp.eq('age',25).eq('role','admin');
```
###### SQL<!-- omit in toc -->
```sql
username like '%eve%' AND age = 25 AND role = 'admin'
```
In the next example, the ***gte*** method is used with ***or*** as it's prefix, added it to the compound expression as a logical boolean OR.
```js
exp.lt('age',25).orGte('age',30);
```
###### SQL<!-- omit in toc -->
```sql
age < 25 OR age >= 30
```
Sometimes it is necessary to have finer control over order of operations. In the following example the **or(expression)** method is used to add a logical OR to compare with the given sub-expression
```js
exp.eq('gender','m').or(
  qry.exp().eq('gender','f').btw('age',25,30)
);
```
###### SQL<!-- omit in toc -->
```sql
gender = 'm' OR (gender = 'f' AND age BETWEEN 25 AND 30)
```

##### Using like() for string comparison #####
The **like** or **lk** method work in a similar way to LIKE statement in sql. The % operator is used to denote a wildcard match before or after the given value. The third parameter caseSensitive accept a boolean value to toggle case sensitivity and defaults to true.

```js
exp.like('name','ste%',false)
```
###### SQL<!-- omit in toc -->
```sql
name LIKE 'ste%'
```
The like method can also be used to test for case insensitive matches, like shown below
```js
let exp = qry.exp();
exp.like('make','ford',false);
```

##### Full Text Search #####
***Please note, this functionality is still in Development.***
```js
let exp = qry.exp();
exp.search('user','ste');
```

##### Using fields as values #####
By default all expression functions either evaluate based on a field, or a field and literal values. To use secondary fields instead of values, use the $ operator. ***Please note, this functionality is still in Development.***
```js
exp.eq('name','$user2.name')
```
###### SQL<!-- omit in toc -->
```sql
name = user2.name
```

##### Query Expression Builder Functions #####
|Function | Description |
| :------ | :---------- |
or(exp)|Adds a logical or operator and evaluates the given expression|
and(exp)|Adds a logical and operator and evaluates the given expression|
eq(field,&nbsp;value)<br>equals(field,&nbsp;value)|Matches values that are equal to a given value|
neq(field,&nbsp;value)<br>notEquals(field,&nbsp;value)|Matches values that are not equal to a given value|
lt(field,&nbsp;value)<br>lessThan(field,&nbsp;value)|Matches values that are less than to a given value|
lte(field,&nbsp;value)<br>lessThanOrEqual(field,&nbsp;value)|Matches values that are less than or equal to a given value|
gt(field,&nbsp;value)<br>greaterThan(field,&nbsp;value)|Matches values that are greater than to a given value|
gte(field,&nbsp;value)<br>greaterThanOrEquals(field,&nbsp;value)|Matches values that are greater than or equal to a given value|
ex(field)<br>exists(field)|Matches values that exist in a set of values|
nex(field)<br>notExists(field)|Matches values that do not exist in a set of values|
btw(field,&nbsp;value1,&nbsp;values2)<br>between(field,&nbsp;value1,&nbsp;values2)|Matches values that are between two given value|
in(field,&nbsp;value)<br>in(field,&nbsp;value)|Matches values that are in a set of values|
nin(field,&nbsp;value)<br>notIn(field,&nbsp;value)|Matches values that are not in a set of values|
nl(field)<br>isNull(field)|Matches values that are equal to null|
nnl(field)<br>isNotNull(field)<br>notNull(field)|Matches values that are not equal to null|
lk(field,caseSensitive)<br>like(field,caseSensitive)|Matches values by pattern using the % operator.<br>caseSensitive defaults to true|
nlk(field,caseSensitive)<br>notLike(field)|Matches values that do not match a pattern using the % operator.<br>caseSensitive defaults to true|

#### Executing a Query ####
The **run** method executes a query and returns a Promise. If the promise does not throw an error, it will resolve a QueryResultSet object. 
```js
try {
  let qry = this.db.qry();
  let res = await qry.from('user').run();
  console.log(res.toArray());
}
catch (err) {
  console.log(err.message);
}
```
#### Query Result Sets ####
The Query Result Set object returned from an executed query is an iterable abstracted representation of the returned data. It contains methods to process, filter, and iterate through the query results.

In the example below a query is executed and the result set is iterated through using the **forEach** method.
```js
let res = await qry.from('user').run();
res.forEach(val=>{
  console.log(`Username: ${val.username}`)
});
```

In the example below a query is executed and the result set is filtered using the **map** method.
```js
res.map(val=>{
  return val.username.toUpperCase();
});
```

The **toArray** method returns the result set in a simple array object.
```js
let resArr = res.toArray();
```

The **first** property contains first result in the result set.
```js
let firstResult = res.first;
```

The **isEmpty** method returns true if there are no results in the result set.
```js
let hasResults = ! res.isEmpty();
```

The **isCached** method returns true if the result set contains a cached query.
```js
let isCachedQuery = res.isCached();
```

### Database Client Instance
While most queries can and should be written using the Query Builder, there are cases where it is either impossible or impractical to do so. In these rare cases, the low level database client drivers are available in the database context. 

#### Mongo Client 
In the example below a connected instance of the mongo client.db(dbName) is used to run a simple find query.
```js
let db = await this.db.getDatabaseInstance();
let res = await db.collection('car').find();
```

## Caching
R14 Server provides access to the in-memory data stores, such as Redis and Memcache, through adapters. Adapters abstract and normalize caching behaviors, allowing for easier migration between technologies, as well as coding consistency between projects. The cache is available in the app context using the member namespace **app.cache** or **app.csh**.

Effectively using a in-memory data store can drastically improve the performance of an application, taking valuable load off databases and api requests. Javascript Objects and primitive types are cached using key value store, providing extremely performant lookups. Javascript objects will be automatically serialized into JSON when stored in the cache, and return as an Object.

### Setting Cache Values
In the following example, the variable myVals will be cached by the app for 1 minute.
```js
this.cache.set('myVals',value, 1);
```
The cache method **app.cache.set(key, value, minutes=null)** sets a cache key to the value provided.

#### Cache.set() Method Arguments<!-- omit in toc -->
|Name |type| Description |
| :------ | :------ | :------ |
|Key|String| The key used to cache and lookup the cache value
|value|Object or basic type| The value to be cached
|minutes|Integer|The time in minutes to cache the value

### Getting Cache Values
In the following example, the variable myVals will be retrieved from the cache. If it is not found, null will be returned.
```js
let myVal = this.cache.get('myVals');
```
The cache method **app.cache.get(key** sets a cache key to the value provided.

#### Cache.get() Method Arguments<!-- omit in toc -->
|Name |type| Description |
| :------ | :------ | :------ |
|Key|String| The key used to cache and lookup the cache value

## Storage
R14 Server provides access to local and remote storage, as well as file based operations, through storage adapters. Storage adapters are available in the app context using the member namespace **app.storage** or **app.stor**. 

***Please note, this functionality is still in Development.***