# GraphQL & Relay & React 

## Simple Express App to Serve GraphQL Backend



``` js 
// npm install following 
//axios
//express
//express-graphql
//graphql
//json-server
//lodash

const express = require('express');
const expressGraphQL = require('express-graphql');
const schema = require('./schema/schema');

const app = express();

app.use('/graphql', expressGraphQL({
    schema,
    graphiql: true,
}));


app.listen(4000, () => {
    console.log('Listening');
});
```

``` js
// db.json
{
  "users": [
    {
      "id": "40",
      "firstName": "Alex",
      "age": 40,
    },
    {
      "id": "41",
      "firstName": "Nick",
      "age": 40,
    }
  ]
}
```

``` js
// schema.js
const graphql = require('graphql');
const axios = require('axios');

const {
    GraphQLObjectType,
    GraphQLString,
    GraphQLInt,
    GraphQLSchema,
    GraphQLList,
} = graphql;

const UserType = new GraphQLObjectType({
    name: 'User',
    fields: {
        id: { type : GraphQLString} ,
        firstName: { type : GraphQLString },
        age: { type : GraphQLInt },
    }
});

const RootQuery = new GraphQLObjectType({
    name: 'RootQueryType',
    fields: {
        user: {
            type: UserType,
            args: { id : { type: GraphQLString }},
            resolve(parentValue, args) {
                // calling json-server
                return axios.get(`http://localhost:3000/users/${args.id}`)
                    .then(response => response.data);
            }
        },
    }
});

module.exports = new GraphQLSchema({
    query: RootQuery,
});
```

``` json
query {
    user(id : "40" ) {
        firstName
        age
    }
}

// returns
{
  "data": {
    "user": {
      "firstName": "Alex",
      "age": 40
    }
  }
}
```

## Returning a Collections

Add the following relationship to the json-server
``` 
// db.json
{
  "users": [
    {
      "id": "23",
      "firstName": "Bill",
      "age": 25,
      "companyId": "1"
    },
    {
      "id": "40",
      "firstName": "Alex",
      "age": 40,
      "companyId": "1"
    },
    {
      "id": "41",
      "firstName": "Nick",
      "age": 40,
      "companyId": "2"
    }
  ],
  "companies": [
    {
      "id": "1",
      "name": "Apple",
      "description": "iPhone"
    },
    {
      "id": "2",
      "name": "Google",
      "description": "Search"
    }
  ]
}
```

``` js
const { 
    ... 
    GraphQLList,
} = graphql;

const CompanyType = new GraphQLObjectType({
    name: 'Company',
    fields: () => ({
        id: { type : GraphQLString},
        name: { type : GraphQLString},
        description: { type : GraphQLString},
        users: { 
            type: new GraphQLList(UserType),
            resolve(parentValue, args) {
                return axios.get(`http://localhost:3000/companies/${parentValue.id}/users`)
                    .then(response => response.data);
            }
        }
    })
});
...

const UserType = name: 'User',
    fields: () => ({
        ...
    })

const RootQuery = new GraphQLObjectType({
    name: 'RootQueryType',
    fields: {
        user: {
            ...
        },
        company: {
            type: CompanyType,
            args: { id : { type: GraphQLString }},
            resolve(parentValue, args) {
                return axios.get(`http://localhost:3000/companies/${args.id}`)
                    .then(response => response.data);
            }
        }
    }
});
```

- Notice *New GraphQLList(UserType)*. This is how you return a collecion of a type
- Due to the cyclic depdency, you cannot define the type as is. You have to change it to look like closure in order to lazily evaluate this.

## Mutation
``` js 
const { 
    ... 
    GraphQLNonNull,
} = graphql;

const mutation = new GraphQLObjectType({
    name: 'Mutation',
    fields: {
        addUser: {
            type: UserType,
            args: { 
                firstName: { type: new GraphQLNonNull(GraphQLString) },
                age: { type: new GraphQLNonNull(GraphQLInt) },
                companyId: { type: GraphQLString } ,
            },
            resolve(parentValue, { firstName, age }) {
                return axios.post('http://localhost:3000/users', { firstName, age })
                    .then(response => response.data);
            }
        },
    }
});

module.exports = new GraphQLSchema({
    query: RootQuery,
    mutation,
});
```

- Notice GraphQLNonNull type in order to require user to put in the type. For the optional value, this type was not enforced
- Added mutation alongside query. Thanks to ES6, I don't have to explicitly to spell out key and value. This applies to args in resolve method in addUser.
- During mutation, returnType is not necessarily the object type you are modifiying. More on this later.

``` js
// more mutation
const mutation = new GraphQLObjectType({
    name: 'Mutation',
    fields: {
        ...
        deleteUser: {
            type: UserType,
            args: { 
                userId: { type: new GraphQLNonNull(GraphQLString) },
            },
            resolve(parentValue, { userId }) {
                return axios.delete(`http://localhost:3000/users/${userId}`)
                    .then(response => response.data);
            }
        },
        editUser: {
            type: UserType,
            args: {
                id : { type: new GraphQLNonNull(GraphQLString)},
                firstName: { type: GraphQLString },
                age: { type: GraphQLInt },
                companyId: { type: GraphQLString }, 
            },
            resolve(parentValue, args) {
                return axios.patch(`http://localhost:3000/users/${args.id}`, args)
                    .then(response => response.data)
            }
        }
```

- json-server will ignore id field of the args. So you can just pass in args

## Relay / Apollo

If you go to the dev console of a browser, and make graphQL query via GraphiQL. You can see the resopnse is a raw json.
But the request is seemingly different...

```
operationName : null
query: "{ user(id: "40") { firstname  age} }"
variables: null
```

You need a GraphQL Client that sits between a frontend framework (i.e. React) and GraphQL server. Frontend framework will handout query, and GraphQL client will make the query.

3 Big Clients
- Lokka
    - basic queries and mutations.
    - some simple caching
- Apollo Client
    - Good balance between features and complexity
- Relay
    - Amazing performance for mobile. But complex
