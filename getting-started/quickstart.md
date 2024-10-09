---
icon: bullseye-arrow
description: >-
  To join the XFlow Network as an on-ramp/off-ramp partner you will need to do 3
  steps:
---

# Start accepting on-ramp orders

1. Provide to XFlow your webhook URL
2. Create and Configure your webhook cloud function to accept an on-ramp/off-ramp order
3. Add a line of code in your system to update the status of the order when it is completed.

### 1. Provide to Xflow your webhook URL

You will need to create a cloud function in your backend. This webhook will receive incoming request from Xflow in order to accept or reject on-ramp/off-ramp order from users.

Once you have a created this URL, please communicate with us on telegram the URL, so you can whitelist you on the test environnement

### 2. Import our SDK and edit the logic of the webhook

First you need to import javascript SDK by doing  (The documentation of the library can be found here [https://github.com/espresso-cash/xflow-partner-client](https://github.com/espresso-cash/xflow-partner-client))

```
npm install https://github.com/espresso-cash/xflow-partner-client
```

Second, you need a write a cloud function for the webhook below. You can clone the full example below&#x20;

[https://github.com/espresso-cash/xflow-partner-webhook-example](https://github.com/espresso-cash/xflow-partner-webhook-example)\
\
or copy the main function and adapt to your need. Make sure to edit the logic in the comment section

```javascript
import { XFlowPartnerClient } from "xflow-partner-client";
import nacl from "tweetnacl";
import base58 from "bs58";

export async function webhookHandler(body) {
  try {

    // You can use the Keypair for test purpose below
    // publicKey: 'F2etcaJ1HbPVjjKfp4WaZFF1DoQRNUETkXyM1b98u76C'
    // seed: 'GVb2FXnG64Xpr6KbWP6Jp4hXWSkWU4uL9AgBTsdBjnZp'
    const seed = "GVb2FXnG64Xpr6KbWP6Jp4hXWSkWU4uL9AgBTsdBjnZp";

    // Initialize partner client with your auth key pair
    const client = await XFlowPartnerClient.fromSeed(seed);

    const orderId = body.orderId;
    const order = await client.getOrder({orderId: orderId});

    const userPK = order.userPublicKey;

    // Get user secret key
    const secretKey = await client.getUserSecretKey(userPK);

    // Get KYC result
    const kycValidationResult = await client.getValidationResult({
      key: "kycSmileId",
      secretKey: secretKey,
      userPK: userPK,
    });

    // KYC should return JSON result of validation. Ie: for Nigeria, it is SmileID result
    // For now, in Test Environment, we don't validate the KYC result
    // In Production, you will be able validate the KYC result and reject the order if the KYC is not valid

    const { cryptoAmount, cryptoCurrency, fiatAmount, fiatCurrency, type } = order;

    let canProcessOrder;

    // -------------------------------------------------------------------------------------------------
    // HERE IS WHERE YOU CAN ADD YOUR OWN LOGIC TO PROCESS THE ORDER
    // -------------------------------------------------------------------------------------------------

    if (type === "ON_RAMP") {
      // On-Ramp order
      // You should check the here that:
      // cryptoAmount, cryptoCurrency, fiatAmount, fiatCurrency are correct
      // and match your exchange rates
      // And decide if you can process the order
      canProcessOrder = true;
      if (!canProcessOrder) {
        await client.rejectOrder({ orderId, reason: "Unable to process order" });
        console.log("Order rejected: Unable to process order");
        return;
      }

      // Once you are ready to create the order, you can create your order into your own system
      // And then accept the order here
      await client.acceptOnRampOrder({
        orderId,
        bankName: "Your Bank Name2", // Bank name that will be displayed to the user
        bankAccount: "Your Bank Account2", // Bank account that will be displayed to the user
        externalId: "EXTERNAL_ID", // ID that you will use to identify the order in your own system
      });
      console.log("On-Ramp order accepted successfully");
    } else if (type === "OFF_RAMP") {
      // Off-Ramp order
      // You should check the here that:
      // cryptoAmount, cryptoCurrency, fiatAmount, fiatCurrency are correct and match your exchange rates
      // And decide if you can process the order
      canProcessOrder = true;
      if (!canProcessOrder) {
        await client.rejectOrder({ orderId, reason: "Unable to process order" });
        console.log("Order rejected: Unable to process order");
        return;
      }

      // Once you are ready to create the order, you can create your order into your own system
      // And then accept the order here
      await client.acceptOffRampOrder({
        orderId,
        cryptoWalletAddress: "CRYPTO_WALLET_ADDRESS",
        externalId: "EXTERNAL_ID",
      });
      console.log("Off-Ramp order accepted successfully");
    }

    // -------------------------------------------------------------------------------------------------
    // END OF YOUR LOGIC
    // -------------------------------------------------------------------------------------------------
  } catch (error) {
    console.error("Error in webhook handler:", error);
    throw error;
  }
}
```

#### Once your webhook is up and running

you can test that it is working properly by simulating a user order here

In the partner key, you can input the public key for testnet: F2etcaJ1HbPVjjKfp4WaZFF1DoQRNUETkXyM1b98u76C

[https://espresso-cash.github.io/xflow-user-test-app/#/simple](https://espresso-cash.github.io/xflow-user-test-app/#/simple)

### 3. Add a line of code in your system to update the status

Once the order has been accepted, you should be able to tell Xflow when the money has been sent with this command for&#x20;

For On ramp:

```javascript
await client.completeOnRampOrder({
    externalId: 'YOUR_ORDER_ID',
    transactionId: 'TRANSACTION_ID',
});
```

For off ramp:

```javascript
await client.completeOffRampOrder({
    externalId: 'YOUR_ORDER_ID',
});
```
