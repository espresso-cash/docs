---
icon: money-bill-transfer
---

# Order flow

<figure><img src="../.gitbook/assets/KYC flow - Frame 2.jpg" alt=""><figcaption><p>Order flow overview</p></figcaption></figure>

1. **User** initiates onramp or offramp in **Wallet**.
2. **Wallet** shares access to the selected **Ramp Partner** and creates order on **BRIJ Server**.
3. **BRIJ Server** notifies **Ramp Partner** about new order using webhook provided by **Ramp Partner**.
4. **Ramp Partner** gets order details, confirms the order, and retrieves user data using [Client SDK](../for-on-ramp-off-ramp-partner/quickstart.md).
5. **Ramp Partner** processes the order using their internal business logic.
6. **Ramp Partner** notifies **BRIJ Server** about order completion using Client SDK.
7. **BRIJ Server** notifies **Wallet** about order completion.

Detailed sequence diagram of the On-Ramp Flow is provided here:

```mermaid
sequenceDiagram
    autonumber

    actor User
    participant Wallet
    participant KYC as BRIJ Server
    participant Partner as Ramp Partner

    User->>+Wallet: Initiates on-ramp

    Wallet->>KYC: Grants access to partner
    Wallet->>KYC: Creates order

    KYC->>Partner: Notifies about order
    Partner->>+KYC: Requests user and order information
    KYC->>-Partner: Returns information

    alt Partner accepts order
        Partner->>KYC: Accepts order

        Wallet->>+KYC: Requests order status
        KYC->>-Wallet: Returns order status

        User->>Partner: Sends fiat

        alt Fiat/crypto amount is correct
            Partner->>Wallet: Sends crypto

            Partner->>KYC: Completes order
        else
            Partner->>KYC: Fails order
        end
    else
        Partner->>KYC: Rejects order
    end

    Wallet->>+KYC: Requests order status
    KYC->>-Wallet: Returns order status

    Wallet->>-User: Ramp completed
```

Detailed sequence diagram of the Off-Ramp Flow is provided here:

```mermaid
sequenceDiagram
    autonumber

    actor User
    participant Wallet
    participant KYC as BRIJ Server
    participant Partner as Ramp Partner

    User->>+Wallet: Initiates off-ramp

    Wallet->>KYC: Grants access to partner
    Wallet->>KYC: Creates order

    KYC->>Partner: Notifies about order
    Partner->>+KYC: Requests user and order information
    KYC->>-Partner: Returns information

    alt Partner accepts order
        Partner->>KYC: Accepts order

        Wallet->>+KYC: Requests order status
        KYC->>-Wallet: Returns order status

        Wallet->>Partner: Sends crypto

        alt Fiat/crypto amount is correct
            Partner->>User: Sends fiat
            Partner->>KYC: Completes order
        else
            Partner->>KYC: Fails order
        end
    else
        Partner->>KYC: Rejects order
    end

    Wallet->>+KYC: Requests order status
    KYC->>-Wallet: Returns order status

    Wallet->>-User: Ramp completed
```
