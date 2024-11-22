---
icon: hand-wave
cover: .gitbook/assets/Android-Blog.png
coverY: 0
layout:
  cover:
    visible: true
    size: full
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

# Welcome

Here is the documentation to integrate the BRIJ, an API and an SDK for wallet and on-ramp/off-ramp partner to:

* Share user KYC datas between Wallets and On-ramp Partners
* Buy or Sell Crypto from Fiat between Wallets and On-ramp Partners

<figure><img src=".gitbook/assets/KYC flow - Frame 3.jpg" alt=""><figcaption><p>System overview</p></figcaption></figure>

Participants:

* **User** – owner of a crypto wallet who wants to perform on-ramp or off-ramp.
* **Wallet** – non-custodial wallet connected to XFlow network for on/off-ramp with wire transfer. Connected wallets:
  * [Espresso Cash](https://espressocash.com) (work in progress)
* **BRIJ Storage Server** – provides storage for users to store encrypted data (e.g. ID documents) and verification info;&#x20;
* **BRIJ Orders Server** – implements API to create and track on/off-ramp orders.
* **Verifier** – service in charge of verifying information provided by users, e.g. user with wallet `4sUAXgMvL91B4BSYfficvXjDko85gCmbTcN8QH2SSGxf` owns email `john.doe@example.com`.
* **KYC Partner** – service in charge of performing full KYC. Supported partners:
  * SmileID
* **Ramp Partner** – service in charge of converting Fiat to Crypto (On-Ramp service) or Crypto to Fiat (Off-Ramp service).
