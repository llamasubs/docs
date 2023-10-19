# How to integrate llamasubs

## Check if the user already has a subscription
https://api.thegraph.com/subgraphs/name/0xngmi/llamasubs-optimism/graphql

## Get user subscribed
Add an iframe to your html like the following:
```html
<iframe src="https://llamasubs.vercel.app/subscribe?to=0x08a3c2A819E3de7ACa384c798269B3Ce1CD0e437&amount=4"></iframe>
```

These are the properties you can use in the querystring:
| Property | Usage                                                 | Example                                    |
|----------|-------------------------------------------------------|--------------------------------------------|
| to       | Address that will receive the subscription money      | 0x08a3c2A819E3de7ACa384c798269B3Ce1CD0e437 |
| amount   | Amount in tokens (DAI) that will be charged per month | 5                                          |

After the subscription has been purchased, an event will be sent to the parent window, which can be listened to like this:
```js
window.addEventListener(
  "message",
  (event) => {
    if (event.origin !== "https://llamasubs.vercel.app") return;
    if(event.data.subscribed === true){
      // Handle subscription fulfillment (eg hide iframe, go to the next step...)
    }
  },
  false,
);
```
