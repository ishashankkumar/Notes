Based on the video transcript, here are the concise study notes on authentication and authorization for backend engineers.

### 1. Key Concepts, Definitions, and Terminologies

#### 1.1 Core Definitions
*   **Authentication**: The process of assigning an identity to a subject, essentially answering the question, "Who are you?" in a given context (like a platform or OS).
*   **Authorization**: The process of determining a subject's capabilities or permissions, answering the question, "What can you do?" in a given context.
*   **HTTP Protocol**: The backbone of client-server communication on the web. By design, it is **stateless**, meaning it treats every request as an isolated interaction with no memory of past exchanges.

#### 1.2 Fundamental Components
*   **Sessions**: A mechanism to establish a temporary, server-side context for each user, adding a stateful layer to the stateless HTTP protocol. This allows servers to "remember" users across multiple requests.
*   **JSON Web Tokens (JWTs)**: A stateless mechanism for transferring claims between parties. JWTs are self-contained tokens that include user data and a cryptographic signature, eliminating the need for server-side session storage.
*   **Cookies**: A small piece of information that a server can store in a user's browser. Cookies are automatically sent with subsequent requests to the same server, enabling mechanisms like session management and token transfer.
*   **API Keys**: Cryptographically safe random strings used to grant programmatic access to a server, ideal for machine-to-machine communication.

#### 1.3 Advanced Concepts
*   **OAuth (Open Authorization)**: A protocol that addresses the "delegation problem," allowing one website to access a user's resources on another website without the user sharing their password. It works by sharing tokens with specific permissions instead of credentials.
*   **OpenID Connect (OIDC)**: A layer built on top of OAuth 2.0 to add an authentication component. It introduces an `ID token` (typically a JWT) containing user identity information like user ID, name, and email.
*   **Role-Based Access Control (RBAC)**: An authorization technique where permissions are assigned to specific roles (e.g., admin, user, moderator) rather than individual users. Users are assigned roles, which in turn grant them a set of capabilities.

### 2. Step-by-Step Explanations and Processes

#### 2.1 Stateful Authentication (Session-based)
1.  **Login**: The client (browser) sends credentials (e.g., username, password) to the server.
2.  **Verification**: The server validates the credentials.
3.  **Session Creation**: If valid, the server generates a unique **Session ID**, bundles it with user data, and stores this information in a persistent store like Redis or a database.
4.  **Cookie Issuance**: The server sends the Session ID back to the client, stored in an HTTP-only cookie.
5.  **Subsequent Requests**: The browser automatically includes the cookie (with the Session ID) in all future requests to that server.
6.  **Server-side Lookup**: For each request, the server reads the Session ID from the cookie and looks up the corresponding user data in its persistent store to identify and authorize the user.

#### 2.2 Stateless Authentication (JWT-based)
1.  **Login**: The client sends credentials to the server.
2.  **Verification**: The server validates the credentials.
3.  **Token Generation**: If valid, the server generates a **signed JWT** containing user information (e.g., user ID, role) using a secret key.
4.  **Token Issuance**: The server sends the JWT back to the client.
5.  **Subsequent Requests**: The client sends the JWT back to the server with each request, typically in the `Authorization` header.
6.  **Server-side Verification**: The server extracts the token and verifies its signature using the secret key. If the signature is valid, the server trusts the user information within the token to identify and authorize the request, with no need for a database lookup.

#### 2.3 OAuth 2.0 & OpenID Connect Flow (Example: "Sign in with Google")
1.  **Redirection**: A client application (e.g., a note-taking app) redirects the user to the authorization server (Google's login page) when they click "Sign in with Google".
2.  **Authentication & Consent**: The user logs into their Google account and grants the requested permissions (e.g., access to email, name) to the client app.
3.  **Code & Token Issuance**: The authorization server sends an **authorization code** and an **ID token** back to the client app.
4.  **Token Exchange**: The client app securely exchanges the authorization code with the authorization server to receive an **access token**.
5.  **Resource Access**: The client app can now use the access token to request the user's resources (e.g., notes from Google Keep) from the resource server (Google's servers) on behalf of the user. The ID token is used to verify the user's identity.

### 3. Examples, Analogies, and Use-Cases

*   **Pre-industrial Authentication**: A village elder vouching for a person is an analogy for authentication based on implicit, contextual trust.
*   **Medieval Seals**: Wax seals on documents acted as early "authentication tokens," representing identity through possession ("something you have").
*   **Telegraph Passphrases**: Pre-agreed passphrases used by telegraph operators were an early form of shared secrets, representing a shift to "something you know".
*   **E-commerce Shopping Cart**: An e-commerce site needing to remember items in a user's cart is a prime use case for stateful sessions, as HTTP is inherently stateless.
*   **Chat GPT API Keys**: OpenAI provides API keys so developers can programmatically access their models from other servers, which is a classic example of machine-to-machine communication.
*   **Delegation Problem**: A travel app needing to scan your Gmail for flight tickets or a social media app wanting to import your Google contacts are use-cases that led to the development of OAuth.
*   **"Sign in with Google/Facebook"**: This common feature is a direct application of OpenID Connect, where one platform authenticates a user for another platform.

### 4. Code Snippets and Formulas (Conceptual)

While no specific code was provided, the structure of a **JWT** was detailed:

A JWT consists of three parts, separated by dots (`.`): `header.payload.signature`.

1.  **Header (Metadata)**: Specifies the signing algorithm.
    *   Example: `{"alg": "HS256", "typ": "JWT"}`
2.  **Payload (Claims/Data)**: Contains user data and token information.
    *   `sub`: Subject (user ID).
    *   `iat`: Issued At (timestamp).
    *   Custom claims like `name` or `role`.
3.  **Signature**: A cryptographic signature created using the header, payload, and a secret key to verify the token's integrity.

### 5. Comparisons and Contrasts

| Feature | Stateful Authentication (Sessions) | Stateless Authentication (JWTs) |
| :--- | :--- | :--- |
| **State Management** | Server stores session data in a persistent store (e.g., Redis). | **No server-side storage is needed**; all required user data is in the token itself. |
| **Scalability** | Can face scalability challenges in distributed systems due to the need for session data synchronization. | **Highly scalable**; any server with the secret key can verify the token, making it ideal for microservices. |
| **Control & Revocation** | **Centralised control**; sessions can be easily revoked in real-time by deleting them from the server store. | **Revocation is complex**. Once a JWT is issued, it is valid until it expires, making immediate revocation difficult without extra mechanisms. |
| **Performance** | Requires a database or cache lookup for every authenticated request. | Verification is a fast, self-contained cryptographic operation, avoiding storage lookups. |
| **Best For** | Traditional web applications with moderate traffic and strict session control requirements. | APIs, distributed/microservice architectures, and mobile applications. |

**Authentication vs. Authorization**:
*   Authentication answers "**Who are you?**" (Identity).
*   Authorization answers "**What can you do?**" (Permissions).

### 6. Practical Applications, Limitations, and Cautions

#### 6.1 Practical Applications & Recommendations
*   **Hybrid Approach**: For complex systems, use a hybrid authentication model. Use **stateful authentication** for web apps (browsers) and **stateless authentication** for mobile apps or third-party API integrations.
*   **External Auth Providers**: For medium-to-large systems, it is strongly recommended to use an external authentication provider (e.g., Auth0, Clerk) to handle the complexities of secure authentication.
*   **API Keys**: Use API keys for server-to-server or machine-to-machine communication where a user is not directly involved.

#### 6.2 Limitations and Challenges
*   **JWT Token Theft**: Since JWTs are stateless, if a token is stolen, an attacker can impersonate the user until the token expires. There is no built-in mechanism to invalidate a single stolen token.
*   **JWT Revocation**: To revoke a user's access before token expiry, you might need a "blacklist" in a persistent store. This re-introduces statefulness, partially negating the core benefit of JWTs.
*   **Biometric Systems**: While advanced, biometric authentication introduces its own challenges, such as false positives/negatives and template security issues.

#### 6.3 Security Cautions for Developers
1.  **Never Store Passwords in Plain Text**: This was a lesson learned from the MIT project in 1961. Always use secure hashing algorithms to store irreversible, fixed-length representations of passwords.
2.  **Use Generic Error Messages**: During authentication, avoid specific error messages like "User not found" or "Incorrect password." These messages provide clues to attackers. Instead, always use a generic message like "**Authentication failed**" for all error cases to keep attackers guessing.
3.  **Defend Against Timing Attacks**: Attackers can measure the server's response time to guess whether a failure was due to an invalid username or an invalid password. To prevent this, ensure authentication response times are equalized, for instance by:
    *   Using **constant-time comparison functions** for password hashes.
    *   **Simulating a response delay** to make all authentication failure paths take the same amount of time.
