# ✅ How to consume proofs?

## Check if user provided JSON proof is valid

The proof source code is stored in this [repository](https://github.com/id-Mask/smart-contracts/tree/main/src), and all the necessary utilities are bundled in an [npm package](https://www.npmjs.com/package/idmask-zk-programs). To install the package, follow these steps:

```bash
npm i idmask-zk-programs
```

To verify user provided JSON proof:

<pre class="language-javascript"><code class="lang-javascript">import { verify } from 'o1js'
import { proofOfAge } from 'idmask-zk-programs'
<strong>
</strong><strong>// this is a user supplied JSON proof
</strong><strong>const proof = {
</strong><strong>  publicInput: ["21"],
</strong><strong>  publicOutput: ["1"],
</strong><strong>  maxProofsVerified: 0,
</strong><strong>  proof: "KChzdGF...KSkpKSkp"
</strong><strong>}
</strong><strong>const { verificationKey } = proofOfAge.compile()
</strong><strong>const isProofValid = await verify(proof, verificationKey)
</strong><strong>
</strong><strong>console.log(
</strong><strong>  `Is proof valid? ${isProofValid}`,
</strong><strong>  `Proof of age of at least ${proof.publicInput[0]} years`
</strong><strong>)
</strong></code></pre>

## Check if provided public address has an associated proof

```javascript
// user Mina address
const address = 'B62qqgtZqqnDr7BzyNnYguqnHQnpMDyTSEYfWj4r1qkEYcfvszkp8zt' 
const graphQLArchiveNodeUrl = 'https://berkeley.graphql.minaexplorer.com/'
const zkAppAddress = 'B62qqpAFkz374qJpuFKYZPjT1KxSmnLoY4zEc878FaW4DSxgYNXZiny'

const response = await fetch(graphQLArchiveNodeUrl, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    query: `
      query MyQuery {
        events(query: {
          zkAppCommandHash: {
            zkappCommand: {
              accountUpdates: {
                body: {publicKey: "${zkAppAddress}"}
              },
              feePayer: {
                body: {
                  publicKey: "${address}"
                }
              }
            }
          },
          canonical: true
        }) {
          dateTime
          event
          zkAppCommandHash {
            zkappCommand {
              feePayer {
                body {
                  publicKey
                }
              }
            }
          }
        }
      }
    `,
  }),
})
const response_ = await response.json()

const hasProof = response_.data.events.length > 0

console.log(
  `Address has an asocaited proof? ${hasProof}`,
  `Age of at least ${response_.data.events[0].event[0]}`
)
```
