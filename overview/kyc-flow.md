---
icon: passport
---

# KYC Flow

Before user can create orders on the platform, they need to pass KYC.

<figure><img src="../.gitbook/assets/KYC flow - Frame 1.jpg" alt=""><figcaption><p>KYC flow overview</p></figcaption></figure>

1. **User** initiates KYC in the compatible **Wallet**. **User** provides required data (email, phone number, document etc.)
2. **Wallet** locally encrypts data and uploads them to **BRIJ Server.**
3. **Wallet** requests KYC from one of the compatible **Verifiers** (currently, there's only one Verifier available).
4. **Verifier** verifies some data on its own (e.g. email and phone number) by sending verification code.
5. **User** enters the code in the **Wallet.**
6. **Wallet** sends code to **Verifier.**
7. **Verifier** confirms the code and marks the corresponding data as verified in the **BRIJ Server**.
8. For other data, **Verifier** can use one of the **KYC Partners** (e.g. SmileID). **Verifier** uploads data to **KYC Partner** and requests verification.
9. **KYC Partner** returns verification response to **Verifier**.
10. **Verifier** uploads verification result and verification response from **KYC Partner** to **BRIJ Server**.
11. **BRIJ Server** returns encrypted verification result to **Wallet**. KYC is completed.

Detailed sequence diagram of the KYC Flow is provided here:

```mermaid
sequenceDiagram
    autonumber

    actor User
    participant Wallet
    participant KYC as BRIJ Server
    participant Verifier
    participant Partner as KYC Partner

    User->>+Wallet: Initiates KYC

    Note over User,Verifier: Email validation
    User->>Wallet: Enters email
    Wallet->>KYC: Sends encrypted email
    Wallet->>KYC: Grants access to Verifier on behalf of user

    Wallet->>Verifier: Requests email validation
    Verifier->>+KYC: Requests data
    KYC->>-Verifier: Returns encrypted data
    Verifier->>User: Sends TOTP to email
    User->>Wallet: Enters TOTP
    Wallet->>+Verifier: Sends TOTP
    Verifier->>KYC: Sends encrypted validation status
    Verifier->>-Wallet: Email verified

    Note over User,Verifier: Phone validation
    User->>Wallet: Enters phone number
    Wallet->>KYC: Sends encrypted phone

    Wallet->>Verifier: Requests phone validation
    Verifier->>+KYC: Requests data
    KYC->>-Verifier: Returns encrypted data
    Verifier->>User: Sends TOTP to phone
    User->>Wallet: Enters TOTP
    Wallet->>+Verifier: Sends TOTP
    Verifier->>KYC: Sends encrypted validation status
    Verifier->>-Wallet: Phone verified

    Note over User,Partner: Document validation
    User->>Wallet: Enters document info, selfie image, personal data
    Wallet->>KYC: Sends encrypted data

    Wallet->>+Verifier: Requests document validation
    Verifier->>+KYC: Requests data
    KYC->>-Verifier: Returns encrypted data
    Verifier->>+Partner: Sends data and initiates validation
    Partner->>-Verifier: Returns validation result
    Verifier->>KYC: Sends encrypted validation result
    Wallet->>+KYC: Requests validation status
    KYC->>-Wallet: Returns encrypted validation status

    Wallet->>-User: KYC completed
```
