# All about OAuth 2.0
Everything there is to know about OAuth 2.0 and related concepts. I have handwritten these in my own powerpoint presentations but then had to clean it up using ChatGPT to make sure that everything in grammatically and logically correct.

## OAuth 2.0 Grant Types

### Overview

OAuth 2.0 specifies the following grant types:

1. **Authorization Code**: Used when the client is a web server. This grant type utilizes redirection and can be used to obtain both access tokens and refresh tokens.
2. **Implicit**: Previously recommended for clients without a secret, such as applications running in a browser. This grant type is less secure and has been superseded by the Authorization Code with PKCE.
3. **Resource Owner Password**: Suitable if the client has a high degree of trust with the user. Should be used sparingly as it requires users to disclose their credentials.
4. **Client Credentials**: Used when the client is acting on its own behalf, not on behalf of a user.
5. **Device Code**: Used when the client is a device that doesn't have the ability to launch a browser and receive a redirect.
6. **Authorization Code with PKCE (Proof Key for Code Exchange)**: The recommended grant type for mobile and native applications. Adds an additional layer of security that protects the authorization code from being intercepted.

### Decision Tree for Grant Type Implementation

1. Are you authorizing on behalf of a User?
    - No: Use the **Client Credential Grant Type**.
    - Yes: Does the system in question have a web browser available?
        - No: Use the **Device Grant Type**.
        - Yes: Is the application entirely server-side, or does it have a client-side component?
            - No: Use the **Authorization Code Flow with PKCE**.
            - Yes: Use the **Authorization Code Flow**. Occasionally, the **Resource Owner Password Flow** can be used temporarily for legacy systems migrating to support OAuth 2.0.

> Note: These are general guidelines and the specific implementation may vary depending on the specific use case and security requirements of the application. Always consult with a security expert when implementing OAuth 2.0 in your application.

## OAuth 2.0 Tokens

1. **Access Token**
   - **Type**: Can be either opaque or a JSON Web Token (JWT).
   - **Usage**: Passed in the request to gain access to protected resources.
   - **Validation**: 
     - Opaque tokens are typically validated through the introspection endpoint of the Authorization (AuthZ) Service.
     - JWT tokens are self-contained and can be validated locally using their signature.
   - **Purpose**: Serves as a replacement for username and password.
   - **Expiration**: Access tokens have a limited lifespan, expiring at certain intervals (e.g., minutes or hours).

2. **Refresh Token**
   - **Type**: Typically opaque.
   - **Usage**: Used to obtain a new access token once the existing one expires without requiring the user to re-authenticate.
   - **Purpose**: Solely used for refreshing the access token; it is not used for accessing resources directly.

## OpenID Connect (OIDC)

1. **JSON Web Tokens (JWT) / ID Token**
   - **Implementation**: OIDC’s implementation of OAuth 2.0’s access token.
   - **Encoding**: Base64 encoded JSON document.
   - **Structure**: The format and contents of the ID Token are clearly specified by OIDC standards, including claims such as issuer, subject, audience, and expiration.

2. **Access Token**
   - **Type**: Can also be a JWT, similar to OAuth 2.0, or opaque.
   - **Usage**: Used to access protected resources.
   - **Validation**: JWT access tokens can be validated locally using their signature, while opaque tokens require introspection.

## Validating JWT

1. **Establish Trust Through Validation**
   - Always validate the JWT before using it. Do not trust the token without validation.

2. **Structure of JWT**
   - JWTs consist of three parts separated by periods:
     1. **Header**: Specifies the algorithm used to generate the token.
     2. **Payload**: Contains the actual data (claims) but needs to be validated before use.
     3. **Signature**: Contains the signature used to verify the authenticity of the token.

3. **Steps Involved in Validating the Payload**
   - **Step 1**: Take the signed data in the payload.
   - **Step 2**: Use the algorithm specified in the header.
   - **Step 3**: Use the public key from the sender (issuer) to verify the signature.
   - **Step 4**: Calculate the signature using the header and payload, then compare it with the signature in the JWT.
     - If the calculated signature and the signature in the JWT do not match, reject the JWT.
     - If both signatures match, the JWT is valid, and you can trust the payload.

4. **Inspect the Payload**
   - Within the payload, there are claims and other important information. Key claims to verify include:
     - **iss (Issuer)**: Identifies who issued the token. This should be verified against your authorization server.
     - **sub (Subject)**: Identifies the end-user or entity this token represents.
     - **aud (Audience)**: Identifies the recipient(s) that the token is intended for. Ensure the audience matches your application.
     - **iat (Issued At)**: The timestamp when the token was issued. Check to ensure the token is not too old.
     - **exp (Expiration Time)**: The timestamp when the token expires. Ensure the current time is before this time to confirm the token is still valid.

### Additional Considerations
- **Algorithm Security**: Ensure that the algorithm specified in the header is secure and acceptable by your application.
- **Clock Skew**: Account for potential clock skew when checking `iat` and `exp` claims.

### Summary
1. **Do not trust JWTs without validation**.
2. **Understand the three parts of a JWT**: Header, Payload, Signature.
3. **Follow the steps to validate the JWT**: Verify the signature and check the claims in the payload.
4. **Verify important claims** like `iss`, `sub`, `aud`, `iat`, and `exp`.

By following these steps and considerations, you can ensure the JWT is authentic and its payload data is reliable.

## Handling Tokens Safely

### Guidelines

1. **Tokens as Credentials**: Treat tokens as credentials and ensure they are protected accordingly within the application.

2. **Token Storage**: Avoid storing tokens in client-side code. Instead, opt for secure storage options such as secure cookies, HTTP Only cookies, secure server-side sessions, or secure databases.

3. **Token Transmission**: Always transmit tokens via HTTPS to ensure they are encrypted during transmission. Additionally, validate the tokens on the server-side to ensure they have not been tampered with.

4. **Sensitive Information**: Refrain from storing any sensitive information in the ID token.

5. **Token Lifetime**: The lifetime of the token should be determined based on the use case. Aim to keep it as short as possible without negatively impacting the end user experience, while still maintaining security.

6. **Token Expiration**: Tokens can expire in two ways. They can naturally expire after a set amount of time or an application can forcefully revoke the token after use. Implementing token expiration and revocation is a good practice for security reasons.

7. **OAuth 2.0 and Token Expiration/Revocation**: OAuth 2.0 doesn’t provide specific recommendations for token expiration or revocation. However, it's generally a good practice to implement these features for enhanced security. Note that the implementation may vary as OAuth providers do not standardize these features.

> Remember, these are general guidelines and the specific implementation may vary depending on the specific use case and security requirements of the application. Always consult with a security expert when implementing token handling in your application.
