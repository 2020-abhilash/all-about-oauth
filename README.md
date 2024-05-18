# all-about-oauth
Everything there is to know about OAuth 2.0 and related concepts

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
