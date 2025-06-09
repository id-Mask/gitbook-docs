# ✅ How to consume proofs?

## ☑️ Easiest & Most Reliable Way: Use the ID-Mask App

The recommended way to consume proofs is through the [**ID-Mask app**](https://idmask.xyz):

1. Open [idmask.xyz](https://idmask.xyz) on your device. Go to 'Verify' > 'QR Code'.
2. Optionally **precompile** the proofs you're about to consume in the settings.
3. Use the **built-in scanner** to scan the user’s **pass QR code** from their wallet.
4. The app handles all cryptographic verification **and** ensures the pass actually belongs to the user by checking their **passkey signature**.

This is the only method that guarantees:

* The proof is cryptographically valid.
* The pass is truly owned by the person presenting it.

## &#x20;🧑‍💻 Advanced: Programmatic Proof Verification

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
</strong><strong>  publicOutput: [...],
</strong><strong>  maxProofsVerified: 0,
</strong><strong>  proof: "KChzdGF...KSkpKSkp"
</strong><strong>}
</strong><strong>const { verificationKey } = await proofOfAge.compile()
</strong><strong>const isProofValid = await verify(proof, verificationKey)
</strong><strong>
</strong><strong>console.log(
</strong><strong>  `Is proof valid? ${isProofValid}`,
</strong><strong>  `Proof of age of at least ${proof.publicInput[0]} years`
</strong><strong>)
</strong></code></pre>

## &#x20;🧑‍💻 Advanced: Check if provided public address has an associated proof

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
