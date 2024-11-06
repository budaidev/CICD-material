class: inverse, center, middle

# API Testing Postman és Newman eszközökkel

---

class: inverse, center, middle

# Postman és Newman alapok

---

## Mi a Postman és Newman?

* Postman: API fejlesztő és tesztelő platform
* Newman: Postman CLI futtatókörnyezet
* Főbb funkciók:
  * API dokumentáció
  * Automatizált tesztelés
  * Request kollekciók
  * Környezeti változók
  * CI/CD integráció
  * Team collaboration

---

## HTTP Methódusok

### REST API alapok:
* GET - Adatok lekérése
* POST - Új adat létrehozása
* PUT - Meglévő adat módosítása
* DELETE - Adat törlése
* PATCH - Részleges módosítás

```json
// GET /api/users/1 response
{
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
}
```

---

## REST API áttekintés

### Főbb koncepciók:
* Erőforrás alapú megközelítés
* Stateless kommunikáció
* Uniform interface
* CRUD műveletek
* Response kódok:
  * 2xx - Sikeres műveletek
  * 4xx - Kliens hibák
  * 5xx - Szerver hibák

---

## Postman használata

### UI elemek:
* Collections panel
* Request builder
* Response viewer
* Environment selector
* Test scripts panel
* Console log

### Request építés:
```http
GET https://api.example.com/users
Authorization: Bearer {{token}}
Content-Type: application/json
```

---

## GET kérések tesztelése

### Request paraméterek:
```http
GET /api/users?page=1&limit=10
```

### Response ellenőrzés:
```javascript
pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Response has users", () => {
    const response = pm.response.json();
    pm.expect(response.users).to.be.an('array');
    pm.expect(response.users).to.have.lengthOf(10);
});
```

---

## Authentikáció típusok

* Basic Auth
* Bearer Token
* OAuth 2.0
* API Key
* JWT

### Bearer Token példa:
```javascript
const token = pm.environment.get('authToken');
pm.request.headers.add({
    key: 'Authorization',
    value: `Bearer ${token}`
});
```

---

## Response validáció

### Schema validáció:
```javascript
const schema = {
    type: 'object',
    required: ['id', 'name', 'email'],
    properties: {
        id: { type: 'integer' },
        name: { type: 'string' },
        email: { type: 'string' }
    }
};

pm.test("Schema is valid", () => {
    pm.response.to.have.jsonSchema(schema);
});
```

---

## Collection automatizáció

### Collection struktúra:
```
MyAPI Collection
├── Auth
│   └── Login
├── Users
│   ├── Get Users
│   ├── Create User
│   ├── Update User
│   └── Delete User
└── Products
    ├── Get Products
    └── Create Product
```

---

## Environment kezelés

### Environment változók:
```javascript
{
    "baseUrl": "https://api.example.com",
    "authToken": "",
    "userId": ""
}
```

### Változó használat:
```javascript
// Pre-request script
pm.environment.set("timestamp", Date.now());

// Test script
const baseUrl = pm.environment.get("baseUrl");
```

---

## Request chaining

### Login és token mentés:
```javascript
// Login request test script
pm.test("Save auth token", () => {
    const response = pm.response.json();
    pm.environment.set("authToken", response.token);
});

// Következő request pre-request script
const token = pm.environment.get("authToken");
pm.request.headers.add({
    key: 'Authorization',
    value: `Bearer ${token}`
});
```

---

## Newman CLI használat

### Parancsok:
```bash
# Collection futtatás
newman run collection.json

# Environment használata
newman run collection.json -e env.json

# Report generálás
newman run collection.json \
    --reporters cli,junit,htmlextra \
    --reporter-htmlextra-export report.html
```

---

## Jenkins integráció

### Jenkinsfile példa:
```groovy
pipeline {
    agent any
    
    stages {
        stage('API Tests') {
            steps {
                sh 'npm install -g newman'
                sh 'newman run collection.json \
                    -e env.json \
                    --reporters cli,junit \
                    --reporter-junit-export results.xml'
            }
        }
    }
    
    post {
        always {
            junit 'results.xml'
        }
    }
}
```

---

## Python Web App API tesztek

### Test collection példa:
```javascript
// Pre-request script
const payload = {
    username: "test",
    password: "test123"
};

// Test script
pm.test("Login successful", () => {
    pm.response.to.have.status(200);
    const response = pm.response.json();
    pm.environment.set("authToken", response.token);
});

// CRUD műveletek tesztelése
pm.test("Create item", () => {
    // Item létrehozás tesztelése
});

pm.test("Get items", () => {
    // Items lekérés tesztelése
});
```

---

## Best Practices

* Környezeti változók használata
* Tesztek szervezése collection-ökbe
* Pre-request scriptek használata
* Response validáció minden esetben
* Megfelelő hibaüzenetek
* CI/CD integráció
* Report generálás
* Version control használata
* Team collaboration

---

class: inverse, center, middle

# Kérdések?

