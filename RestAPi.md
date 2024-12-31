### A Guide to REST API Methods

REST (Representational State Transfer) is an architectural style used for designing networked applications. REST APIs allow systems to communicate using HTTP methods, parameters, headers, body content, and authorization.

---

### **HTTP Methods**

1. **GET**  
   - **Purpose**: Retrieve data from the server.  
   - **Parameters**: Typically passed as query parameters in the URL.  
   - **Body**: Not applicable.

2. **POST**  
   - **Purpose**: Submit data to the server to create resources.  
   - **Parameters**: May include query parameters in the URL.  
   - **Body**: Contains data (usually in JSON format).

3. **PUT**  
   - **Purpose**: Update or replace a resource.  
   - **Parameters**: Typically passed as query parameters in the URL.  
   - **Body**: Contains data (usually in JSON format).

4. **PATCH**  
   - **Purpose**: Partially update a resource.  
   - **Parameters**: Similar to PUT.  
   - **Body**: Contains partial data.

5. **DELETE**  
   - **Purpose**: Remove a resource.  
   - **Parameters**: Passed as query parameters.  
   - **Body**: Rarely used.

---

### **Request Components**

1. **Parameters**
   - Sent as query strings in URLs:  
     `https://api.example.com/resource?param1=value1&param2=value2`
   - Example:
     ```bash
     GET https://api.example.com/users?page=2&sort=desc
     ```

2. **Headers**
   - Metadata about the request, such as:
     - `Content-Type`: Format of the data sent (e.g., `application/json`).
     - `Authorization`: Bearer token or API key for authentication.
     - `Accept`: Expected response format.
   - Example:
     ```json
     {
       "Content-Type": "application/json",
       "Authorization": "Bearer your_token"
     }
     ```

3. **Body**
   - Used in methods like POST, PUT, and PATCH to send data.
   - JSON format is common:
     ```json
     {
       "name": "John Doe",
       "email": "john@example.com"
     }
     ```

4. **Authorization**
   - Most APIs require authentication for secure access:
     - **Bearer Token**:
       ```bash
       Authorization: Bearer <token>
       ```
     - **API Key**:
       ```bash
       Authorization: <API_KEY>
       ```
     - **OAuth 2.0**: Often involves retrieving a token from an authorization server.

---

### **Advanced Topics**

#### 1. **Error Handling**
   - Properly handle HTTP status codes:
     - **200-299**: Successful responses.
     - **400-499**: Client errors (e.g., 400 Bad Request, 401 Unauthorized).
     - **500-599**: Server errors (e.g., 500 Internal Server Error).
   - Example:
     ```python
     if response.status_code == 200:
         print("Request succeeded!")
     elif response.status_code == 404:
         print("Resource not found.")
     else:
         print(f"Error: {response.status_code}")
     ```

#### 2. **Rate Limiting**
   - APIs often enforce limits on the number of requests.
   - Use headers like `X-RateLimit-Remaining` and `Retry-After` to manage retries.

#### 3. **Pagination**
   - Many APIs return large datasets in chunks. Look for:
     - `next` or `prev` links in the response.
     - Parameters like `page` and `limit`.
   - Example:
     ```python
     params = {"page": 1, "limit": 100}
     while True:
         response = api_client.get("resource", params=params)
         process(response["data"])
         if not response.get("next"):
             break
         params["page"] += 1
     ```

#### 4. **Authentication Mechanisms**
   - **API Keys**: Simple but less secure.
   - **OAuth 2.0**: Standard for user-based permissions.
   - **JWT (JSON Web Tokens)**: Tokens containing user info.
     ```python
     headers = {"Authorization": f"Bearer {jwt_token}"}
     response = api_client.get("secure-endpoint", headers=headers)
     ```

#### 5. **Advanced Querying**
   - Use filters, sorts, and nested queries for complex data retrieval.
     ```bash
     GET /items?filter[price][gte]=100&sort=-created_at
     ```

#### 6. **Webhooks**
   - APIs can push updates to your server using webhooks.
   - Example payload:
     ```json
     {
       "event": "order.created",
       "data": { "id": "12345", "status": "created" }
     }
     ```

#### 7. **Versioning**
   - APIs evolve over time. Use versioned endpoints:
     ```bash
     GET /v1/resource
     ```

---

### **Extended Example: Python Implementation with Requests**

```python
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

class RestApiClient:
    def __init__(self, base_url, retries=3, retry_delay=1):
        self.base_url = base_url
        self.retries = retries
        self.retry_delay = retry_delay
        self.session = self.init_requests_session()

    def init_requests_session(self):
        retry_strategy = Retry(
            total=self.retries,  # Maximum number of retries
            backoff_factor=self.retry_delay,  # Delay between retries (in seconds)
            status_forcelist=[500, 502, 503, 504]  # HTTP status codes to retry
        )
        session = requests.Session()
        adapter = HTTPAdapter(max_retries=retry_strategy)
        session.mount("https://", adapter)
        session.mount("http://", adapter)
        return session

    def get(self, endpoint, params=None, headers=None):
        url = f"{self.base_url}/{endpoint}"
        response = self.session.get(url, params=params, headers=headers)
        if response.status_code != 200:
            raise Exception(f"GET request failed: {response.status_code}, {response.text}")
        return response.json()

    def post(self, endpoint, headers=None, body=None):
        url = f"{self.base_url}/{endpoint}"
        response = self.session.post(url, headers=headers, json=body)
        if response.status_code not in [200, 201]:
            raise Exception(f"POST request failed: {response.status_code}, {response.text}")
        return response.json()

    def handle_rate_limiting(self, endpoint, headers=None):
        import time
        response = self.get(endpoint, headers=headers)
        if response.status_code == 429:  # Too Many Requests
            retry_after = int(response.headers.get("Retry-After", 1))
            print(f"Rate limit exceeded. Retrying in {retry_after} seconds...")
            time.sleep(retry_after)
            return self.get(endpoint, headers=headers)
        return response

# Usage
if __name__ == "__main__":
    api_client = RestApiClient(base_url="https://api.example.com")

    # GET example with error handling
    try:
        response = api_client.get(
            "users",
            params={"page": 1, "per_page": 10},
            headers={"Authorization": "Bearer your_token"}
        )
        print("GET Response:", response)
    except Exception as e:
        print("Error during GET request:", e)

    # POST example with validation
    try:
        response = api_client.post(
            "users",
            headers={"Authorization": "Bearer your_token", "Content-Type": "application/json"},
            body={"name": "John Doe", "email": "john@example.com"}
        )
        print("POST Response:", response)
    except Exception as e:
        print("Error during POST request:", e)
```
