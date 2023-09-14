# ⛏ How to integrate personal identification data into your own smart contracts?

Ideally, you'd set up your own zkOracle to fetch data from Smart-ID backend :smile:. But don't worry, we've got you covered. Follow the steps below to fetch the data and use it inside your own smart contract.

```javascript
const get_oracle_signature = (name, surname, country, pno) => {

  const privateKey = PrivateKey.fromBase58(process.env.PRIVATE_KEY)
  const publicKey = privateKey.toPublicKey()

  // encode and sign the data
  const merged_array_of_fields = [
    ...Encoding.stringToFields(name),
    ...Encoding.stringToFields(surname),
    ...Encoding.stringToFields(country),
    ...Encoding.stringToFields(pno)
  ]
  const signature = Signature.create(privateKey, merged_array_of_fields)

  return [signature, publicKey]
}

```
