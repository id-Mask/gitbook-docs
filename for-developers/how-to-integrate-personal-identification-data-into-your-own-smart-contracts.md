# ⛏ How to integrate personal identification data into your own smart contracts?



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
