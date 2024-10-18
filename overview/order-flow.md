---
icon: money-bill-transfer
---

# Order flow

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>Order flow overview</p></figcaption></figure>

1. **User** initiates onramp or offramp in **Wallet**.
2. **Wallet** shares access to the selected **Partner** and creates order on **XFlow Platform**.
3. **XFlow Platform** notifies **Partner** about new order using webhook provided by **Partner**.
4. **Partner** gets order details, confirms the order, and retrieves user data using [Client SDK](../getting-started/quickstart.md).
5. **Partner** processes the order using their internal business logic.
6. **Partner** notifies **XFlow Platform** about order completion using Client SDK.
7. **XFlow Platform** notifies **Wallet** about order completion.

Detailed sequence diagram of the Order Flow is provided here:

```mermaid
sequenceDiagram
    autonumber

    actor User
    participant Wallet
    participant KYC as KYC Platform
    participant Partner as Ramp Partner
    
    User->>+Wallet: Initiates on/off-ramp
    
    Wallet->>KYC: Grants access to partner
    Wallet->>KYC: Creates order
    
    KYC->>Partner: Notifies about order
    Partner->>+KYC: Requests user and order information
    KYC->>-Partner: Returns information
    
    alt Partner accepts order
        Partner->>KYC: Accepts order
        
        Wallet->>+KYC: Requests order status
        KYC->>-Wallet: Returns order status
        
        alt Off-ramp
            Wallet->>Partner: Sends crypto
        else On-ramp
            User->>Partner: Sends fiat
        end
        
        alt Fiat/crypto amount is correct
            alt Off-ramp
                Partner->>User: Sends fiat
            else On-ramp
                Partner->>Wallet: Sends crypto
            end
            
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

