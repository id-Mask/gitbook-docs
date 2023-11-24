# 🔮 zkOracle

## Swagger Docs UI

[https://id-mask-oracle-2qz4wkdima-uc.a.run.app/api-docs/](https://id-mask-oracle-2qz4wkdima-uc.a.run.app/api-docs/)

## Integrating Smart-ID provided personal identification data into zkApps

Ideally, you'd set up your own zkOracle to fetch data from Smart-ID backend :smile:. But don't worry, I've got you covered. Follow the steps below to fetch the data and use it inside your own smart contract.

### Mock data

For testing purposes use mock data provider. It does not require real personal identification number and data request approval by the user. This is useful for testing environment and quick prototyping.

```javascript
const url = 'https://id-mask-oracle-2qz4wkdima-uc.a.run.app/'
const response = await fetch(url + 'getSmartIDMockData', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
  },
})
const response_ = await response.json()

console.log(response_)
```

```json
{
  "data": {
    "name": "Spruce",
    "surname": "Ouse",
    "country": "EE",
    "pno": "PNOEE-34204108221",
    "currentDate": "2023-11-22"
  },
  "signature": {
    "r": "13321981410189637427210129143013654176766761390896169413813228709629380683341",
    "s": "19215708859877984477029527137419056491967878259437001340192969029580449858462"
  },
  "publicKey": "B62qmXFNvz2sfYZDuHaY5htPGkx1u2E2Hn3rWuDWkE11mxRmpijYzWN"
}
```

### Real data

To fetch real personal data follow the example provided below. Keep in mind that to do this, you must ask the user to provide valid personal identification code and country of citizenship.&#x20;

```javascript
// prepare input data
const url = 'https://id-mask-oracle-2qz4wkdima-uc.a.run.app/'
const body = {
  pno: 39911110000, // personal idintification number
  country: 'EE', // country of citizenship
  displayText: '🙋, this is zkApp requesting your data 🙌' // message to be shown on user's phone
}
```

```json
{
  "data": {
    "name": "Spruce",
    "surname": "Ouse",
    "country": "EE",
    "pno": "PNOEE-34204108221",
    "currentDate": "2023-11-22"
  },
  "signature": {
    "r": "13321981410189637427210129143013654176766761390896169413813228709629380683341",
    "s": "19215708859877984477029527137419056491967878259437001340192969029580449858462"
  },
  "publicKey": "B62qmXFNvz2sfYZDuHaY5htPGkx1u2E2Hn3rWuDWkE11mxRmpijYzWN"
}
```

### Verifying data signature

```javascript
const { PublicKey, Signature, CircuitString } = require('o1js')

const data = {
  "data": {
    "name": "Spruce",
    "surname": "Ouse",
    "country": "EE",
    "pno": "PNOEE-34204108221",
    "currentDate": "2023-11-22"
  },
  "signature": {
    "r": "13321981410189637427210129143013654176766761390896169413813228709629380683341",
    "s": "19215708859877984477029527137419056491967878259437001340192969029580449858462"
  },
  "publicKey": "B62qmXFNvz2sfYZDuHaY5htPGkx1u2E2Hn3rWuDWkE11mxRmpijYzWN"
}

const signature = Signature.fromJSON(data.signature)
const validSignature = signature.verify(PublicKey.fromBase58(data.publicKey), [
  ...CircuitString.fromString(data.data.name).toFields(),
  ...CircuitString.fromString(data.data.surname).toFields(),
  ...CircuitString.fromString(data.data.country).toFields(),
  ...CircuitString.fromString(data.data.pno).toFields(),
  ...CircuitString.fromString(data.data.currentDate).toFields(),
])

console.log(`Is signature valid? ${validSignature.toBoolean()}`)
```
