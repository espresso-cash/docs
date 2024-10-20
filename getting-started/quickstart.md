---
icon: bullseye-arrow
description: >-
  To join the XFlow Network as an on-ramp/off-ramp partner you will need to do 3
  steps:
---

# Start accepting on-ramp orders

1. Provide to XFlow your webhook URL
2. Create and Configure your webhook handler to accept an on-ramp/off-ramp order
3. Add a line of code in your system to update the status of the order when it is completed.

### Provide to XFlow your webhook URL

You will need to create a webhook handler in your backend. This webhook will receive incoming request from XFlow when new order is created.

Once you have a created this URL, please communicate with us on telegram the URL, so we can whitelist you on the test environement.

### Install Client SDK

First you need to import [javascript SDK](https://github.com/espresso-cash/xflow-partner-client) by running the following command:

```
npm install https://github.com/espresso-cash/xflow-partner-client
```

### Import our SDK and edit the logic of the webhook

Second, you need a write a cloud function for the webhook below. You can clone the full example [here](https://github.com/espresso-cash/xflow-partner-webhook-example) or copy the main function and adapt to your need. Make sure to edit the logic in the comment section.

```javascript
import { XFlowPartnerClient } from "xflow-partner-client";

export async function webhookHandler(body) {
  console.log("--- Webhook Example Usage ---");

  try {
    // You can use the following Keypair for test purpose:
    // - publicKey: 'F2etcaJ1HbPVjjKfp4WaZFF1DoQRNUETkXyM1b98u76C'
    // - seed: 'GVb2FXnG64Xpr6KbWP6Jp4hXWSkWU4uL9AgBTsdBjnZp'
    const seed = "GVb2FXnG64Xpr6KbWP6Jp4hXWSkWU4uL9AgBTsdBjnZp";

    // Initialize partner client with your auth key pair.
    const client = await XFlowPartnerClient.fromSeed(seed);

    const orderId = body.orderId;
    const order = await client.getOrder({ orderId: orderId });

    const userPK = order.userPublicKey;

    // Get user secret key.
    const secretKey = await client.getUserSecretKey(userPK);

    // KYC should return JSON result of validation, i.e. for Nigeria, it is
    // SmileID result. For now, in Test Environment, we don't validate the KYC
    // result. In Production, you will be able to validate the KYC result and
    // reject the order if the KYC is not valid.

    const userData = await client.getUserData({ userPK, secretKey });
    console.log(userData);
    // Example response:
    // {
    //   email: [
    //     {
    //       value: 'test@example.com',
    //       dataId: '20946f48-9068-453d-a124-a5f5d764b3cc',
    //       status: 'APPROVED'
    //     }
    //   ],
    //   phone: [
    //     {
    //       value: '+1234567890',
    //       dataId: 'b31b5b28-d55f-4eda-b20f-e581f0d16bfe',
    //       status: 'UNSPECIFIED'
    //     }
    //   ],
    //   name: [],
    //   birthDate: [],
    //   document: [],
    //   bankInfo: [],
    //   selfie: [],
    //   custom: {}
    // }

    const {
      cryptoAmount, cryptoCurrency, fiatAmount, fiatCurrency, type,
    } = order;
    console.log({
      cryptoAmount,
      cryptoCurrency,
      fiatAmount,
      fiatCurrency,
      type,
    });
    // Example response:
    // {
    //   cryptoAmount: '10000000',
    //   cryptoCurrency: 'USDC',
    //   fiatAmount: '15000',
    //   fiatCurrency: 'NGN',
    //   type: 'ON_RAMP'
    // }

    let canProcessOrder;

    // -------------------------------------------------------------------------
    // HERE YOU CAN ADD YOUR OWN LOGIC TO PROCESS THE ORDER
    // -------------------------------------------------------------------------

    if (type === "ON_RAMP") {
      // On-Ramp order
      // You should check the here that: cryptoAmount, cryptoCurrency,
      // fiatAmount, fiatCurrency are correct and match your exchange rates.
      // After that, you can decide if you can process the order.
      canProcessOrder = true;
      if (!canProcessOrder) {
        await client.rejectOrder({
          orderId, reason: "Unable to process order",
        });
        console.log("Order rejected: Unable to process order");
        return;
      }

      // Once you are ready to process the order, you can create the order in
      // your own system, and then accept the order here.
      await client.acceptOnRampOrder({
        orderId,
        // Bank name that will be displayed to the user in the app
        bankName: "Your Bank Name2",
        // Bank account that will be displayed to the user in the app
        bankAccount: "Your Bank Account2",
        // ID that you can use to identify the order in your own system
        externalId: Math.random().toString(),
      });
      console.log("On-Ramp order accepted successfully");
    } else if (type === "OFF_RAMP") {
      // Off-Ramp order
      // You should check the here that: cryptoAmount, cryptoCurrency,
      // fiatAmount, fiatCurrency are correct and match your exchange rates.
      // After that, you can decide if you can process the order.
      canProcessOrder = true;
      if (!canProcessOrder) {
        await client.rejectOrder({
          orderId, reason: "Unable to process order",
        });
        console.log("Order rejected: Unable to process order");
        return;
      }

      // Once you are ready to process the order, you can create the order in
      // your own system, and then accept the order here.
      await client.acceptOffRampOrder({
        orderId,
        cryptoWalletAddress: "CRYPTO_WALLET_ADDRESS",
        externalId: Math.random().toString(),
      });
      console.log("Off-Ramp order accepted successfully");
    }

    // -------------------------------------------------------------------------
    // END OF YOUR LOGIC
    // -------------------------------------------------------------------------
  } catch (error) {
    console.error("Error in webhook handler:", error);
    throw error;
  }
}
```

Once your webhook is up and running you can test that it is working properly by simulating a user order [here](https://espresso-cash.github.io/xflow-user-test-app/#/simple) (in the Partner public key, you can input the public key for test environment: `F2etcaJ1HbPVjjKfp4WaZFF1DoQRNUETkXyM1b98u76C`).

### Add the code in your system to update the status

Once the order has been accepted and processed, you should be able to tell XFlow that money has been sent.

For on-ramp:

```javascript
await client.completeOnRampOrder({
    externalId: 'YOUR_ORDER_ID',
    transactionId: 'TRANSACTION_ID',
});
```

For off-ramp:

```javascript
await client.completeOffRampOrder({
    externalId: 'YOUR_ORDER_ID',
});
```
