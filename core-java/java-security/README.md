# Java Security and Secure Coding

Java protects memory safety and gives applications a mature security API, but it does not make an application secure by itself. Most real defects come from trusting the wrong input, granting too much access, storing credentials badly, or using a sound API in the wrong way.

This guide is a practical companion to [Spring Security](../../spring-security/README.md). It focuses on the Java platform and secure coding habits. Examples use current Java APIs; check the JDK and library versions used by your project.

## A useful way to frame security

For each request or piece of data, ask:

1. **Who is acting?** Authenticate the user or service.
2. **What may they do?** Authorize the action against the resource, not just a broad role.
3. **What can they control?** Validate input at the boundary and encode it for the output context.
4. **What must stay secret or trustworthy?** Protect keys, credentials, tokens, and data in transit.
5. **What happens when something fails?** Fail closed for security decisions, log a safe audit event, and avoid exposing internals.

A logged-in user is not automatically allowed to read every customer's record. A valid token is not proof that it was issued for this API. A string that passed validation in one context may still be unsafe in another.

## Hashing, encryption, signatures, and random values

These tools solve different problems:

| Tool | What it gives you | Typical use |
|---|---|---|
| Cryptographic hash | A fixed-size digest; no key | File fingerprints, integrity checks when a trusted digest is available |
| Password hashing / key derivation | Intentionally expensive verification | Stored user passwords |
| HMAC | Integrity and authenticity for parties sharing a secret key | Signing internal messages where both sides share a key |
| Digital signature | Integrity and signer verification using a private/public key pair | Signed artifacts or messages |
| Encryption | Confidentiality; authenticated encryption can also detect tampering | Data that must be recoverable by authorized code |
| Secure random generator | Unpredictable values | Session IDs, reset tokens, nonces, cryptographic keys |

Do not store passwords with plain SHA-256 or another fast general-purpose hash. Use an adaptive password encoder such as the one managed by Spring Security, with parameters reviewed for the application's traffic and hardware. Passwords should be verified, not decrypted.

For general cryptographic operations, prefer a maintained library or a platform API with a reviewed design. Do not invent an algorithm, reuse an IV/nonce when the chosen mode forbids it, or copy a cryptography snippet without understanding its key and nonce lifecycle. Java's cryptographic APIs use providers, so test the actual JDK/provider combination that will run in production.

### Creating an unpredictable token

Use `SecureRandom`, not `java.util.Random`, for security-sensitive values:

```java
import java.security.SecureRandom;
import java.util.Base64;

final class ResetTokens {
    private static final SecureRandom RANDOM = new SecureRandom();

    static String newToken() {
        byte[] bytes = new byte[32];
        RANDOM.nextBytes(bytes);
        return Base64.getUrlEncoder().withoutPadding().encodeToString(bytes);
    }
}
```

Store only a verifier or hash of a one-time reset token when practical, expire it quickly, and invalidate it after use. Never print the token in an application log.

## Key and credential handling

- Keep private keys, database passwords, API secrets, and signing keys out of source control and container images.
- Use a managed secret store, KMS/HSM, or deployment secret mechanism with access controls and rotation. Environment variables are convenient configuration, but they are not a full key-management plan.
- A **keystore** holds this application's private keys and certificates. A **truststore** holds certificates or certificate authorities the application trusts.
- Rotate keys with an overlap period: publish/accept the new key, keep the previous verification key while old tokens or signatures may still be valid, then remove it.
- Do not log passwords, authorization headers, cookies, private keys, full reset URLs, or raw tokens. Redact at the logging boundary too.
- Keep key permissions and service identities narrow. Separate keys for unrelated purposes instead of reusing one secret everywhere.

## TLS and secure communication

Use HTTPS/TLS for credentials and bearer tokens in transit. Let a supported TLS implementation validate the peer certificate and hostname. A custom “trust all certificates” manager removes the protection TLS is meant to provide and should not be used in production.

For mutual TLS, both sides present certificates and verify the other side. It can help authenticate service workloads, but it still needs certificate issuance, identity mapping, rotation, and authorization rules. TLS answers “which peer has this certificate?”; application authorization answers “what can this peer do?”

## Validate inputs at the boundary

Validation should match the exact field and business rule. Prefer allow-lists for structured values such as sort keys, enum-like options, file types, and permitted destinations. Apply output encoding for the target context; input validation alone does not prevent HTML, JavaScript, or SQL injection.

### SQL: bind values, allow-list identifiers

```java
String sql = "select id, email from customer where email = ?";
try (PreparedStatement statement = connection.prepareStatement(sql)) {
    statement.setString(1, email);
    try (ResultSet results = statement.executeQuery()) {
        // Map the expected rows.
    }
}
```

Prepared statements bind values as data. They do not make a dynamically concatenated table name or sort direction safe. If a query needs a dynamic identifier, map a small validated choice to a known identifier.

### XML: disable external entity processing

XML parser defaults differ between providers and versions. When parsing untrusted XML, configure the parser to reject DTDs and external entities, enable secure processing where supported, and fail startup or parsing if a required security feature cannot be set. Do not silently continue with unsafe parser defaults. Prefer a well-maintained parser configuration that is tested with the exact provider in use.

### SSRF and URL fetching

Do not fetch arbitrary URLs supplied by a user. Resolve the requested target through an allow-list of schemes, hosts, and ports; block loopback, link-local, and private network destinations when they are not part of the product; and re-check redirects and resolved addresses. Network egress controls provide another layer of protection.

## Java serialization and deserialization

Deserializing attacker-controlled Java object streams is dangerous. Prefer a simple data format with explicit DTO validation for network input. If legacy serialization cannot be removed, use an allow-list filter and bounds for graph depth, references, array size, and stream size. A filter is not automatically active just because the JDK supports it.

Illustrative stream-specific filter (include the concrete DTO types the application really expects):

```java
ObjectInputFilter filter = ObjectInputFilter.Config.createFilter(
        "maxdepth=12;maxrefs=1000;maxarray=100000;" +
        "com.example.api.TrustedMessage;!*");
input.setObjectInputFilter(filter);
```

Review every class needed by the serialized object graph. A filter that is too broad defeats the allow-list; one that is too narrow should reject the stream rather than be weakened without review.

## Advanced Java features are not security boundaries

- **Records** make concise data carriers, but a record containing a mutable list or array is not deeply immutable. Copy mutable values on input and avoid exposing internal mutable state.
- **Sealed classes and interfaces** constrain which types can extend a hierarchy at compile time. They do not decide whether a user is authorized.
- **Modules and encapsulation** help structure access to code. They are not a sandbox for hostile plugins.
- **Virtual threads** reduce the cost of blocking tasks; they do not make shared state thread-safe or propagate authorization safely by magic.
- **The Security Manager** was permanently disabled in JDK 24. Do not rely on it as a process sandbox. Use OS/container isolation, least-privilege service accounts, network policy, and application authorization instead.

Use defensive copies when values cross a trust boundary. Treat deserialized data, reflection, native libraries, runtime agents, and dynamically loaded plugins as separate security concerns that need explicit controls.

## Interview answers worth remembering

- **Why is `SecureRandom` different from `Random`?** `SecureRandom` is designed to produce cryptographically strong, unpredictable bytes. `Random` is for ordinary repeatable simulation and is not a token generator.
- **Hashing or encryption for passwords?** Passwords should use a salted, adaptive one-way password hash. Encryption is reversible and creates a key that can expose every password.
- **What does TLS prove?** It protects the connection and authenticates the peer according to certificate validation. It does not replace user authorization or input validation.
- **Does a record guarantee immutability?** No. Its component references are final, but referenced objects can still be mutable.
- **Can Java's Security Manager sandbox plugin code on JDK 24?** No. It is permanently disabled; use process and deployment isolation for untrusted code.
- **How do you reduce deserialization risk?** Avoid Java object streams for untrusted input; if they are unavoidable, use a narrow filter and strict size/depth limits.

For Spring's password encoders, authorization rules, JWT validation, and OAuth/OIDC flows, continue to the [Spring Security module](../../spring-security/README.md) and its [OAuth 2.0 and OpenID Connect guide](../../spring-security/oauth2-oidc-guide.md).

## References

- [Java Security Overview (JDK 24)](https://docs.oracle.com/en/java/javase/24/security/java-security-overview1.html)
- [Java Cryptography Architecture reference](https://docs.oracle.com/en/java/javase/24/security/java-cryptography-architecture-jca-reference-guide.html)
- [SecureRandom API (JDK 24)](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/security/SecureRandom.html)
- [KeyStore API (JDK 24)](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/java/security/KeyStore.html)
- [SSLContext API (JDK 24)](https://docs.oracle.com/en/java/javase/24/docs/api/java.base/javax/net/ssl/SSLContext.html)
- [Serialization Filtering (JDK 24)](https://docs.oracle.com/en/java/javase/24/core/serialization-filtering1.html)
- [The Security Manager is permanently disabled (JDK 25 security guide)](https://docs.oracle.com/en/java/javase/25/security/security-manager-is-permanently-disabled.html)
