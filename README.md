---
icon: hand-wave
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Introduction to BRIJ API and SDK

BRIJ provides an API and SDK designed for seamless integration between wallets, dapps and on-ramp/off-ramp partners. With BRIJ, you can:

- **Share user KYC data** between wallets, dapps and on-ramp partners securely and efficiently.
- **Enable crypto-to-fiat and fiat-to-crypto transactions** directly between wallets, dapps and on-ramp partners.

BRIJ simplifies the process of compliance and transaction handling, ensuring a streamlined and user-friendly experience for all parties involved.

<figure><img src=".gitbook/assets/KYC flow - Frame 3.jpg" alt=""><figcaption><p>System overview</p></figcaption></figure>

Participants:

- **User** – owner of a crypto wallet who wants to perform on-ramp or off-ramp.
- **Wallet** – non-custodial wallet connected to BRIJ network for on/off-ramp with wire transfer. Connected wallets:
  - [Espresso Cash](https://espressocash.com)
- **Dapp** - decentralized application connected to BRIJ network for on/off-ramp with wire transfer.
- **BRIJ Server** – provides storage for users to store encrypted data (e.g. ID documents) and verification info; implements API to create and track on/off-ramp orders.
- **Verifier** – service in charge of verifying information provided by users, e.g. user with wallet `4sUAXgMvL91B4BSYfficvXjDko85gCmbTcN8QH2SSGxf` owns email `john.doe@example.com`.
- **KYC Partner** – service in charge of performing full KYC. Supported partners:
  - SmileID
- **Ramp Partner** – service in charge of converting Fiat to Crypto (On-Ramp service) or Crypto to Fiat (Off-Ramp service).
