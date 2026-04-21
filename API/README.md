# What is an API?

**API (Application Programming Interface)** is a way for two systems to communicate with each other.

It acts as a **bridge between client (frontend) and server (backend)**, allowing them to exchange data.

### Simple Analogy

A **waiter in a restaurant**:

* Customer → places order
* Waiter → takes request to kitchen
* Chef → prepares food
* Waiter → delivers response

👉 Waiter = API


## Real-world Example (Common Across All Types)

Suppose you are building a **weather app**.

Instead of measuring temperature manually, your app:

1. Sends a request to a weather service API
2. API processes request
3. Returns structured data (JSON/XML)

Example:

```json
{
  "city": "Mumbai",
  "temperature": 32,
  "condition": "Sunny"
}
```

---

# Types of APIs

## 1. REST API (Representational State Transfer)

REST is an **architectural style** used to design APIs over HTTP.

### Key Characteristics

* Uses HTTP methods:

  * `GET` → Read data
  * `POST` → Create
  * `PUT/PATCH` → Update
  * `DELETE` → Remove

* **Stateless**
  Each request contains all required information
  Server does not remember previous requests

* **Client-Server Separation**

  * Client → UI/UX
  * Server → data + logic

* Returns data in formats like:

  * JSON (most common)
  * XML


### Example (Weather API)

Request:

```http
GET /weather?city=Mumbai
```

Response:

```json
{
  "city": "Mumbai",
  "temperature": 32
}
```

### Advantages

* Simple and easy to use
* Lightweight (JSON)
* Scalable
* Widely adopted


### Limitations

* Over-fetching (extra data)
* Under-fetching (multiple calls needed)


### Best Use Cases

* Web and mobile applications
* Public APIs
* Microservices
* CRUD-based systems

---

## 2. SOAP API (Simple Object Access Protocol)

SOAP is a **protocol** for exchanging structured information using **XML**.

It is more **strict, standardized, and secure** than REST.

### Key Characteristics

* Uses **XML only**
* Strict message format
* Supports:

  * Security (WS-Security)
  * Transactions
  * Reliability
* Uses **WSDL (contract)**

### Structure

```xml
<Envelope>
  <Header>
    <!-- metadata -->
  </Header>
  <Body>
    <!-- request data -->
  </Body>
</Envelope>
```

### Example (Weather Request)

```xml
<Envelope>
  <Body>
    <GetWeather>
      <City>Mumbai</City>
    </GetWeather>
  </Body>
</Envelope>
```

Response:

```xml
<Envelope>
  <Body>
    <GetWeatherResponse>
      <Temperature>32</Temperature>
    </GetWeatherResponse>
  </Body>
</Envelope>
```

### Advantages

* High security
* Strong standards
* Reliable transactions

### Limitations

* Heavy (XML is verbose)
* Slower than REST
* More complex

### Best Use Cases

* Banking systems
* Payment gateways
* Enterprise applications
* High-security environments

---

## 3. GraphQL

GraphQL is a **query language for APIs** that allows clients to request **exactly the data they need**.

### Key Characteristics

* Single endpoint (usually `/graphql`)
* Client decides response structure
* Avoids:

  * Over-fetching
  * Under-fetching

## Example (Weather API)

### Query (GET / Read)

```graphql
query {
  weather(city: "Mumbai") {
    temperature
  }
}
```

Response:

```json
{
  "data": {
    "weather": {
      "temperature": 32
    }
  }
}
```

---

### Mutations (Write Operations)

In GraphQL, **all write operations (POST, PUT, DELETE)** are handled using `mutation`.

---

#### 1. POST (Create)

Example: Add a new city to track weather

```graphql
mutation {
  createCity(name: "Mumbai") {
    id
    name
  }
}
```

Response:

```json
{
  "data": {
    "createCity": {
      "id": 1,
      "name": "Mumbai"
    }
  }
}
```

---

#### 2. PUT / PATCH (Update)

Example: Update city name

```graphql
mutation {
  updateCity(id: 1, name: "Mumbai City") {
    id
    name
  }
}
```

👉 GraphQL does not differentiate between PUT and PATCH

Response:

```json
{
  "data": {
    "updateCity": {
      "id": 1,
      "name": "Mumbai City"
    }
  }
}
```

---

## 3. DELETE

Example: Delete a city

```graphql
mutation {
  deleteCity(id: 1) {
    success
  }
}
```

Response:

```json
{
  "data": {
    "deleteCity": {
      "success": true
    }
  }
}
```

---

## Key Insight

* `query` → Read (GET)
* `mutation` → Create, Update, Delete (POST, PUT, DELETE)
* All operations usually go through a **single endpoint (`/graphql`)**

---

## Interview One-liner

GraphQL uses queries for fetching data and mutations for all write operations like create, update, and delete, typically through a single endpoint.


Only requested field is returned

### Advantages

* Precise data fetching
* Flexible
* Reduces multiple API calls


### Limitations

* Complex backend
* Harder caching
* Query cost management needed


### Best Use Cases

* Mobile apps (limited bandwidth)
* Complex UIs (different data needs)
* Multiple clients (web + mobile)
* Nested/relational data


# Final Comparison

| Feature     | REST                | SOAP     | GraphQL        |
| ----------- | ------------------- | -------- | -------------- |
| Type        | Architectural Style | Protocol | Query Language |
| Data Format | JSON/XML            | XML only | JSON           |
| Flexibility | Medium              | Low      | High           |
| Performance | Fast                | Slow     | Efficient      |
| Complexity  | Low                 | High     | Medium-High    |
| Endpoint    | Multiple            | Multiple | Single         |


# Final Interview Summary

* **REST** → Simple, scalable, best for general APIs
* **SOAP** → Secure, strict, best for enterprise systems
* **GraphQL** → Flexible, efficient, best for complex data fetching

