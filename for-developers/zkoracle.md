# 🔮 zkOracle

## Swagger Docs UI

[https://id-mask-oracle-e6ngsd55oa-uc.a.run.app/api-docs/](https://id-mask-oracle-e6ngsd55oa-uc.a.run.app/api-docs/)

## Integrating Smart-ID provided personal identification data into zkApps

Follow the steps below to fetch the data and use it inside your own smart contract.

### Mock data

For testing purposes use mock data provider. It does not require real personal identification number and data request approval by the user. This is useful for testing environment and quick prototyping.

```javascript
const url = 'https://id-mask-oracle-e6ngsd55oa-uc.a.run.app/'
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
    "name": "Douglas",
    "surname": "Springclean",
    "country": "LV",
    "pno": "PNOLV-56507022581",
    "currentDate": 20231128
  },
  "signature": {
    "r": "11624374872413079570738404457399910526826583697231105175070815741369504895452",
    "s": "1363230429367483560421055409858563811811972473922881490971709844227493053902"
  },
  "publicKey": "B62qmXFNvz2sfYZDuHaY5htPGkx1u2E2Hn3rWuDWkE11mxRmpijYzWN"
}
```

### Real data

To fetch real personal data follow the example provided below. Keep in mind that to do this, you must ask the user to provide valid personal identification code and country of citizenship.

{% hint style="info" %}
In order to fetch read data of a person, the individual must have installed and set up the Smart-ID app and account on their mobile device.
{% endhint %}

```javascript
 // prepare input data
const url = 'https://id-mask-oracle-e6ngsd55oa-uc.a.run.app/'
const body = {
  pno: 34204108221, // personal idintification number
  country: 'EE', // country of citizenship
  displayText: '🙋, this is zkApp requesting your data 🙌' // message to be shown on user's phone
}
```

```javascript
// get session data
const sessionData = await fetch(url + 'initiateSession', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify(body),
})
const sessionData_ = await sessionData.json()

// check verification code
console.log(`Verification code on users mobile: ${sessionData_.verificationCode}`)

// get peronal identification data
const response = await fetch(url + 'getData', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify(sessionData_),
})

const response_ = await response.json()
console.log(response_)
```

```json
{
  "data": {
    "name": "Douglas",
    "surname": "Springclean",
    "country": "LV",
    "pno": "PNOLV-56507022581",
    "currentDate": 20231128
  },
  "signature": {
    "r": "11624374872413079570738404457399910526826583697231105175070815741369504895452",
    "s": "1363230429367483560421055409858563811811972473922881490971709844227493053902"
  },
  "publicKey": "B62qmXFNvz2sfYZDuHaY5htPGkx1u2E2Hn3rWuDWkE11mxRmpijYzWN"
}
```

### Verifying data signature

```javascript
const { PublicKey, Signature, CircuitString } = require('o1js')

const data = {
  "data": {
    "name": "Douglas",
    "surname": "Springclean",
    "country": "LV",
    "pno": "PNOLV-56507022581",
    "currentDate": 20231128
  },
  "signature": {
    "r": "11624374872413079570738404457399910526826583697231105175070815741369504895452",
    "s": "1363230429367483560421055409858563811811972473922881490971709844227493053902"
  },
  "publicKey": "B62qmXFNvz2sfYZDuHaY5htPGkx1u2E2Hn3rWuDWkE11mxRmpijYzWN"
}

const signature = Signature.fromJSON(data.signature)
const validSignature = signature.verify(PublicKey.fromBase58(data.publicKey), [
  ...CircuitString.fromString(data.data.name).toFields(),
  ...CircuitString.fromString(data.data.surname).toFields(),
  ...CircuitString.fromString(data.data.country).toFields(),
  ...CircuitString.fromString(data.data.pno).toFields(),
  Field(data.data.currentDate),
])

console.log(`Is signature valid? ${validSignature.toBoolean()}`)
```
