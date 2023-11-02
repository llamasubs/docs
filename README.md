# How to integrate llamasubs

## Check if the user already has a subscription
```js
function isSubscribed(toAddress, amount, userAddress){
  return fetch("https://api.thegraph.com/subgraphs/name/0xngmi/llamasubs-optimism", {
      method:"post",
      body: JSON.stringify({
          query: `
query Subs($now: BigInt, $userAddress: Bytes, $receiver: Bytes, $minAmountPerCycle:BigInt) {
  subs(
    where: {owner: $userAddress, startTimestamp_lt: $now, realExpiration_gte: $now, receiver: $receiver, amountPerCycle_gte: $minAmountPerCycle}
  ) {
    startTimestamp
    realExpiration
  }
}`,
          variables:{
              now: Math.floor(Date.now()/1e3),
              userAddress,
              receiver: toAddress,
              minAmountPerCycle: amount.toString()
          }
      })
  }).then(r=>r.json()).then(r=>r.data.subs.length>0)
}

// Example
const subscribed = await isSubscribed("0x08a3c2A819E3de7ACa384c798269B3Ce1CD0e437", 4e18, "0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045")
```

## Subscribe popup
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
