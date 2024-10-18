---
icon: passport
---

# KYC Flow

Before user can create orders on the platform, they need to pass KYC.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>KYC Flow overview</p></figcaption></figure>

1. **User** initiates KYC in the compatible **Wallet**. **User** provides required data (email, phone number, document etc.)
2. **Wallet** locally encrypts data and uploads them to **XFlow Platform.**
3. **Wallet** requests KYC from one of the compatible **Validators** (currently, there's only one Validator available).
4. **Validator** verifies some data on its own (e.g. email and phone number) by sending verification code.
5. **User** enters the code in the **Wallet.**
6. **Wallet** sends code to **Validator.**
7. **Validator** confirms the code and marks the corresponding data as verified in the **XFlow Platform**.
8. For other data, **Validator** can use one of the **KYC Partners** (e.g. SmileID). **Validator** uploads data to **KYC Partner** and requests verification.
9. **KYC Partner** returns verification response to **Validator**.
10. **Validator** uploads verification result and verification response from **KYC Partner** to **XFlow Platform**.
11. **XFlow Platform** returns encrypted verification result to **Wallet**. KYC is completed.

Detailed sequence diagram of the KYC Flow is provided here:

```mermaid
sequenceDiagram
    autonumber
    
    actor User
    participant Wallet
    participant KYC as KYC Platform
    participant Validator
    participant Partner as KYC Partner
    
    User->>+Wallet: Initiates KYC
    
    Note over User,Validator: Email validation
    User->>Wallet: Enters email
    Wallet->>KYC: Sends encrypted email
    Wallet->>KYC: Grants access to Validator on behalf of user

    Wallet->>Validator: Requests email validation
    Validator->>+KYC: Requests data
    KYC->>-Validator: Returns encrypted data
    Validator->>User: Sends TOTP to email
    User->>Wallet: Enters TOTP
    Wallet->>+Validator: Sends TOTP
    Validator->>KYC: Sends encrypted validation status
    Validator->>-Wallet: Email verified

    Note over User,Validator: Phone validation
    User->>Wallet: Enters phone number
    Wallet->>KYC: Sends encrypted phone

    Wallet->>Validator: Requests phone validation
    Validator->>+KYC: Requests data
    KYC->>-Validator: Returns encrypted data
    Validator->>User: Sends TOTP to phone
    User->>Wallet: Enters TOTP
    Wallet->>+Validator: Sends TOTP
    Validator->>KYC: Sends encrypted validation status
    Validator->>-Wallet: Phone verified

    Note over User,Partner: Document validation
    User->>Wallet: Enters document info, selfie image, personal data
    Wallet->>KYC: Sends encrypted data
    
    Wallet->>+Validator: Requests document validation
    Validator->>+KYC: Requests data
    KYC->>-Validator: Returns encrypted data
    Validator->>+Partner: Sends data and initiates validation
    Partner->>-Validator: Returns validation result
    Validator->>KYC: Sends encrypted validation result
    Wallet->>+KYC: Requests validation status
    KYC->>-Wallet: Returns encrypted validation status
    
    Wallet->>-User: KYC completed
```

