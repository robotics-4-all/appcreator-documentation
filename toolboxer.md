# Toolboxer DSL Documentation

Welcome to the Toolboxer DSL! This guide will walk you through creating your own custom toolboxes for the [LocSys](https://locsys.issel.ee.auth.gr/) platform. With Toolboxer, you can define palettes of tools (nodes) that can be used in [AppCreator DSL](https://github.com/robotics-4-all/appcreator-documentation/blob/main/README.md) to build powerful visual workflows.

The basic idea is simple:

1.  **Define Connections:** Tell Toolboxer how to connect to your external services like databases, APIs, and message brokers.
2.  **Create a Toolbox:** Design the look and feel of your tool palette.
3.  **Build Nodes:** Create the individual tools, define what they do (`action`), and what fields the user can configure (`parameters`).

## 1. Defining Your Connections

In order for tools to communicate with external services, you define connections. These connections can then be reused by any node in your toolbox.

### Message Brokers (Redis, MQTT & AMQP)

For real-time messaging, you can define connections to brokers.

**Example:**

```python
// A connection to a  Redis server
Broker<Redis> HomeRedis
    host: "localhost"
    port: 6379
    db: 0
    auth:
      username: "default"
      password: "password"
end

// A connection to an MQTT server
Broker<MQTT> HomeMQTT
  host: "localhost"
  port: 1883
  transport: "tcp"
  auth:
    username: "user"
    password: "pass"
end

// A connection to an AMQP server
Broker<AMQP> HomeAMQP
  host: "192.168.1.100"
  port: 5672
  vhost: "/"
  auth:
    username: "guest"
    password: "guest"
end
```

Here, we've created three connections: `HomeRedis`, `HomeMQTT`, and `HomeAMQP`. We'll use these names later in our nodes to tell them which broker to communicate with.

**The available parameters for defining brokers are:**

-   **host**: Host IP address or hostname for the Broker
-   **port**: Broker Port number
-   **auth**: Authentication credentials. Unified for all communication brokers
    -   **username**: Username used for authentication
    -   **password**: Password used for authentication
-   **vhost (Optional)**: Vhost parameter. Only for AMQP brokers
-   **db (Optional)**: Database number parameter. Only for Redis brokers

### Rest APIs

To interact with web services, you define a `RestAPI`.

**Example:**

```python
RestAPI TodosAPI
  host: "https://jsonplaceholder.typicode.com"
  port: 443
  headers: {
    "x-auth-header": "authkey"
    "x-test": "this is a test header"
  }
end
```

This `TodosAPI` connection points to a public API and specifies that any request made using this connection should include the defined `headers`.

**The available parameters for defining Rest APIs are:**

-   **host**: Base URL for the API
-   **port**: Port number
-   **headers**: Default headers to include in every request

### Databases (MySQL & MongoDB)

For direct communication with databases and executing queries.

**Example (MySQL):**

```python
Database<MySQL> HomeSQL
  host: "localhost"
  port: 3306
  auth:
      username: "user"
      password: "password"
  database: "products"
end
```

This defines a connection named `HomeSQL`to a local MySQL database named `products` with the specified credentials.

**The available parameters for defining a MySQL database are:**

-   **host**: Host IP address or hostname for the Database
-   **port**: Database Port number
-   **auth**: Authentication credentials. Unified for all database types
    -   **username**: Username used for authentication
    -   **password**: Password used for authentication
-   **database**: Target database name

**Example (MongoDB):**

```python
Database<MongoDB> HomeMongo
  connection_url: "mongodb://myUser:myPassword@localhost:27017"
  database: "warehouses"
end
```

This defines a connection named `HomeMongo` to a MongoDB database named `warehouses` with the specified connection URL.

**The available parameters for defining a MongoDB database are:**

-   **connection_url**: Full connection string for MongoDB
-   **database**: Target database name

## 2. Creating Your Toolbox

The `Toolbox` is the container for all your nodes. It defines the category that will appear in the `appcreator` sidebar.

**Example:**

```python
Toolbox Brokers
    name: "My Custom Toolbox"
    backgroundColor: "#43BAC0"
    fontColor: "#fff"
    nodes:
        // All your nodes go here
        - Node MyNode
        ...
        end
        - Node AnotherNode
        ...
        end
end
```

-   `name`: The display name of your toolbox in the UI.
-   `backgroundColor`: The background color of the toolbox category.
-   `fontColor`: The text color for the toolbox name.
-   `nodes`: A list where you will define all the tools for this toolbox.

## 3. Building Nodes

Nodes are the functional blocks that users will drag and drop to create workflows.

### Anatomy of a Node

Let's look at a simple node that publishes data.

```python
- Node PublisherTest
    documentation: "Publishes alarm data"
    inputs: 1
    outputs: 1
    action:
        type: publish
        broker: HomeRedis
        topic: "humidity"
        payload: {number: "{humidity}"}
    parameters:
      - id: humidity
        type: input
        label: "Humidity Number"
        value: 50
        required: true
end
```

-   `Node PublisherTest`: Defines a new node with the unique ID `PublisherTest`.
-   `documentation`: This text will appear as a tooltip to help users understand what the node does.
-   `inputs` / `outputs`: The number of connection dots on the upper and lower part of the node, respectively.
-   `action`: It defines **what the node does** when it's executed in a workflow.
-   `parameters`: This block defines the configuration fields that the user can edit in the `appcreator` UI.

### Making Nodes Functional: The `action` Block

The `action` block determines the node's behavior.

**Types of Actions:**

-   **`publish` / `subscribe` / `rpc` (Message Brokers):**

    -   Use `broker:` to reference one of your pre-defined broker connections (e.g., `HomeRedis`).
    -   Use `topic:` to specify the channel or topic to communicate on.
    -   For publishing, a `payload:` can be sent.

-   **`RestCall` (APIs):**

    -   Use `RestAPI:` to reference a defined API connection (e.g., `TodosAPI`).
    -   `verb:` can be `GET`, `POST`, `PUT`, or `DELETE`.
    -   `path:` is the endpoint to hit (e.g., `/posts`).
    -   `query_params:` can be added for `GET` requests.

-   **`Database`:**
    -   Use `database:` to reference a database connection (e.g., `HomeSQL` or `HomeMongo`).
    -   Use `query:` to write the database command to be executed.

Here are examples of a node that makes a GET request and of a node that executes a
MySQL query:

```python
- Node AllTodosGET
    documentation: "Gets all data from an endpoint"
    inputs: 1
    outputs: 1
    action:
        type: RestCall
        RestAPI: TodosAPI
        verb: GET
        path: "/posts"
        query_params:
        - "userId" : "1"
        - "userId" : "2"
end

- Node SelectAllWarehouses
    documentation: "Selects all warehouses"
    inputs: 1
    outputs: 1
    action:
        type: Database
        database: HomeSQL
        query: "SELECT * from Warehouse;"
end
```

### Making Nodes Configurable: `parameters`

You can give users control over a node's behavior by adding `parameters`. This creates UI elements for the node. The following types are available:

#### `input`

Creates a standard text, number, or boolean input field.

**Syntax:**

```python
- id: param_name
  type: input
  label: "User-Friendly Label"
  value: "Default Value" // Can be a string, number, or boolean
  required: true
  show: true // Controls visibility in the UI
  locked: false // If true, user cannot edit the default value
```

#### `select`

Creates a dropdown menu with predefined options.
**Syntax:**

```python
- id: Operation
  type: select
  label: "Operation"
  required: true
  value: "start" // Default selected value
  options:
    - { label: "Start Process", value: "start" }
    - { label: "Stop Process", value: "stop" }
```

<!-- TODO: Add more inputs here -->

### Using Parameter Values in your Action

To use the value a user enters, you reference the parameter's `id` inside curly braces `{}` within your `action` block.

Notice in our `PublisherTest` node:

```python
payload: {number: "{humidity}"}
```

This tells the system to take the value from the `humidity` parameter and insert it into the `payload` before sending.

Similarly, for a database query:

```python
query: 'INSERT INTO Product_Category (name) VALUES ("{product_category}");'
```

### Handling Node Results: `storage`

When a node's action produces a result (e.g., the response from an API call or a database query), you need a way to store it so it can be passed to the next node in the workflow. This is done with `storage` and `literalVariables`.

**Example:**

```dsl
- Node AllTodosGET
    documentation: "Gets all data from an API"
    inputs: 1
    outputs: 1
    literalVariables:
        - "todos_get_response"  // 1. Declare a variable name that will
                                      also be visible to the user
    action:
        type: RestCall
        RestAPI: TodosAPI
        verb: GET
        path: "/posts"
        storage: "todos_get_response" // 2. Store the action's result in the variable
```

1.  **`literalVariables`**: First, you must declare a name for the variable that will hold the result and be visible to the user.
2.  **`storage`**: Then, in the `action` block, you use the `storage` property to specify that the output of this action should be saved in that variable.

Now, the data inside `todos_get_response` is available to any subsequent node connected to this one's output.
