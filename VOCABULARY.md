# x402 calls: terms, principles and failure modes

**Version 1.0** · nohumans.directory and probe402

A shared vocabulary for why x402 calls fail, ordered by the stage at which the call stopped, from the first unpaid call to delivery after payment. It is meant to be used by anyone who measures x402 calls, so that two usages of the same word mean the same thing.

## 1. Terms

### Agreed

| Term | Definition |
|---|---|
| **Quote** | The payment terms a route returns to an unpaid call: a 402 with a readable payment request. |
| **Blocked** | The caller was refused as a client: a rate limit (429), a refusal (403) or a challenge page. It says nothing about whether the service is up. |
| **Settled** | A transfer on chain from the buyer to the quoted payment address, at the signed amount. |
| **Usable answer** | An answer the buyer can use: a non-empty body that parses as the content type the route declares, for example JSON, CSV, plain text, or a media file (audio, image, video, PDF). It is not judged for correctness. |
| **Delivered** | Settled, and a usable answer came back. |
| **Buyer-side failure** | A failure caused by the buyer or its tooling, not by the seller. |

### Defined differently

Each definition is shown with its author.

| Term | nohumans.directory | probe402 |
|---|---|---|
| **The unit measured** | Listing: one endpoint URL as submitted or imported, in canonical form, query string included. | Route: a path on a host, called with a stated method. A URL that quotes on GET and on POST is two routes. |
| **Live** | Verified: three consecutive unpaid calls answered with a quote. Failing: five consecutive failures. Blocked answers and 400/422 count neither way. | Each reading is classed on its own. A summary is a count of readings over a stated period. |

## 2. Principles

What instruments hold to when they class a call and publish a result.

| No. | Principle |
|---|---|
| 1 | Quoting says nothing about delivery. |
| 2 | Blocked is not dead. |
| 3 | Delivered means usable, not correct. |
| 4 | Buyer-side failures are counted apart and never published as a seller's fault. |
| 5 | Where a payment was sent, the failure records whether it settled. |
| 6 | Unknown is not paid. A settlement that cannot be shown either way is recorded as unknown. |
| 7 | A correction is dated and added beside the original. Nothing published is overwritten. |

## 3. Failure modes

### Stage 1. Reading the price

| No. | Failure mode | Definition |
|---|---|---|
| 1.1 | **No answer** | The call got no reply in time: the name did not resolve, the connection failed or timed out, or the TLS handshake failed. |
| 1.2 | **Not payment-gated** | The route answers without asking for payment. |
| 1.3 | **Unreadable payment request** | A 402 whose payment terms cannot be read. |
| 1.4 | **Terms not buyable** | The terms can be read and the buyer cannot meet them: the scheme, network or asset is unsupported, or the route sells on a testnet. |
| 1.5 | **Quote differs from the listing** | The quote differs from the catalogue's listing, in price or in payment address. |
| 1.6 | **Blocked** | The caller was blocked, as defined under Terms. This can happen at any stage. |

### Stage 2. Building the request

| No. | Failure mode | Definition |
|---|---|---|
| 2.1 | **Undeclared input required** | The route rejects the request for an input its payment terms do not declare. |
| 2.2 | **Declared inputs not enough** | Every declared input was sent and the request was still rejected. |
| 2.3 | **Own placeholder rejected** | The route publishes a placeholder as its example (the word "example", a type name such as "string", or the input's own name) and rejects it. |
| 2.4 | **Own example rejected** | The route publishes a real example value and rejects it. |
| 2.5 | **Wrong path or method** | A 404 or 405 before payment. |

### Stage 3. Paying

| No. | Failure mode | Definition |
|---|---|---|
| 3.1 | **Payment asked again** | The route asks for payment again after receiving a signed payment. |
| 3.2 | **Asked for more than quoted** | The payment request on the paid attempt names a higher amount than the unpaid quote did. A careful buyer declines, so usually nothing settles. Both amounts are recorded, with when each was seen. If the higher amount was paid, principle 5 applies. |
| 3.3 | **Settlement cannot be shown** | The route answered as if paid and no matching transfer is found on chain. |

### Stage 4. Delivery

| No. | Failure mode | Definition |
|---|---|---|
| 4.1 | **Error after payment** | The payment settled and the route answered with an error, or did not answer. |
| 4.2 | **Accepted, no result** | After a settled payment the route accepts the call for later (usually 202, sometimes another 2xx) and no result can be fetched: either no job reference (job id, status URL or result location) is given, or the one given yields no result within the time the route states. Where the route states no time, the default is 10 minutes. |
| 4.3 | **Unusable answer** | A success status with a body the buyer cannot use: empty, unreadable, or an error message under a success status. |
| 4.4 | **Wrong shape** | The answer does not match the output shape the route publishes. |
| 4.5 | **Stale** | The answer has the promised type and shape, but the date it carries (a timestamp, an as-of field or a stated delay) is older than the route promises. An answer that carries no date is undated, not stale. Where the route promises "live" or "real-time" without a number, the allowed age is agreed per kind of data, starting with market prices and weather. |

### Stage 5. After the call

| No. | Failure mode | Definition |
|---|---|---|
| 5.1 | **Refunded** | The payment settled, the call failed, and the seller sent the payment back. |
| 5.2 | **No settlement reference** | The payment settled and the reply carries no transaction reference to reconcile it. |
