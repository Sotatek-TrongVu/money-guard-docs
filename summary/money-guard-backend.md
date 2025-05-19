# Money Guard Backend

## Blockchain Webhook Integration

### Update Database Schema

A new table, `agreement_blockchain_logs`, was introduced to track all blockchain-related events for agreements.

**Table: `agreement_blockchain_logs`**

| Column               | Type     | Description                                                               |
| -------------------- | -------- | ------------------------------------------------------------------------- |
| id                   | UUID     | Primary key                                                               |
| event\_type          | String   | Blockchain event type (`issued`, `initialized`, `completed`, `cancelled`) |
| agreement\_id        | UUID     | Reference to the agreement                                                |
| tx\_hash             | String   | Blockchain transaction hash (unique)                                      |
| tx\_block\_number    | Integer  | Blockchain block number                                                   |
| tx\_block\_timestamp | DateTime | Block timestamp                                                           |
| contract\_address    | String   | Smart contract address                                                    |
| data                 | JSONB    | Additional event data                                                     |

* **Idempotency**: The table enforces uniqueness on `tx_hash` to prevent duplicate processing.
* **Purpose**: Every relevant blockchain event (creation, initialization, update) is logged here for traceability and auditing.



The `agreements` table now includes the following fields to store blockchain information related to the issued event:

| Column               | Type     | Description                 |
| -------------------- | -------- | --------------------------- |
| tx\_hash             | String   | Blockchain transaction hash |
| tx\_block\_number    | Integer  | Blockchain block number     |
| tx\_block\_timestamp | DateTime | Block timestamp             |
| contract\_address    | String   | Smart contract address      |

These fields are populated when an agreement is issued on the blockchain and are used to track the on-chain state directly within the agreement record.

***

### Sending Blockchain Events

To send events to the blockchain service, configure the following environment variable in your `.env`:

```
# Blockchain API Info
BLOCKCHAIN_APPLICATION_ID=your_application_id
BLOCKCHAIN_APPLICATION_SECRET_KEY=your_application_secret
BLOCKCHAIN_APPLICATION_PUBLIC_KEY=your_application_public_key
BLOCKCHAIN_WEBHOOK_URL=http://example.com
```

To obtain `BLOCKCHAIN_APPLICATION_ID`, `BLOCKCHAIN_APPLICATION_SECRET_KEY`, please create a new application using Money Guard Blockchain via `[POST] /api/v1/application`. These environment variables are crucial for encrypting requests sent to Money Guard Blockchain to enhance security and prevent unauthorized access

```bash
# Example
curl -X 'POST' \
  '<money-guard-blockchain-url>/api/v1/applications' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "Money Guard Backend",
  "description": "This is an application for Money Guard Backend",
  "url": "http://example.com",
  "logoUrl": "http://example.com"
}'

# Response
{
  "data": {
    "id": "0826d116-298e-44b2-922b-dec19e766a36",
    "name": "Money Guard Backend",
    "description": "This is an application for Money Guard Backend",
    "logoUrl": "http://example.com",
    "privateKey": "-----BEGIN PRIVATE KEY-----\nMIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQCbXM8Nn0AuBdwf\nSy9D4DxWMGAWJUqYYD1bOqhyZH3KWd2giRRIIsGSgevVusZpLdrYDBoGQMmF5/3j\n+93kOBHghDLbds6gaitiVVD7UrLbK1JXtgww5r/ZEfsfz6w3D8yKB4lWXZwFdFGy\nvsITjRXRBthbgkBg4p15XVInjpIolNnBV+wU0JZ8hk7Y88onSOw3f2252CUiwm4F\nCrJCPVpXMrRv+VCAyvxXaMk4lpYqXeQl+aPxSzPVPpXpW/YpW3xlsLnG9d3EGLZd\nBnKiJcJCCe6IeyqRFMw5es5rpIGnPs1/0uzqqezthGsSn/SAkg31aNNcJzvUDxFw\nW0prdkdVAgMBAAECggEAPv7PT+EyR1cF7cRxGk8aeAIue9xMFmPzjQHIWnXn0XxF\n0rmyeRtqrOq0Qxn0KKK4uWCSztVHQV4TgvVWABtCGGzSKE3Qs5fJFpqsdvON5TN4\nNGtzHwL5aF2Tm078L+lekNtEczoKsOV82QLjM8VBwttJMDWsv/wzSQovbS2wkvZC\naC9oAPSYRxEOjQXZT29dlkX0mhv1T39Gf0Y4XyDrjKCTpPFRf5BU1h8o3qzMHMdI\nBRT4wq4l/cVnV+Hsdm1D09QjPvftEoJ9Qesg1MgYHEScXM97AF3AIUzy/T77GKDt\njF8sF2iZsMl/ahJ7PbuPRYi1JBMgP596HtKd3Kn4owKBgQDH8Pccq2dPluGHO2u3\n8q606n9UFmr65MaI8+UGrAlxXlLiCeVAlRZgYbtP4F7GyA6ciKaMoY/tN20HiVpV\nuz2I0Yvj7lcDyYYon8TdfK65cPZttPKGeHIHXYniOrilDTe3pMfr+484qVC72KDQ\nYy4WZhkz4OPrkJeWueAOQuUo4wKBgQDG7CYjsDZ9qa91IFuJTIB3DNg6Rs7QAkdO\nn1treyOWw0zhw6VHz8ARxScm6TZrikG1NpJyxZ1bW8fm8bGO2t+9XAwkv+dZY6zS\nc8WQ7XwJDJrxCjdFqe2fXojBgxn+d73DC/D5TuUNqbvoQw7f6A0EtrLMv1E9nX22\n/NuYCC4cZwKBgFZLnHxVRmvFlWWGsgFczybqpzYlztqm70Gg8tKkYhb6PQ+ucbIk\nJtpGlE/vaTimlBffqvK7sKET8Qz1rXWfMPniZ1xjPE84JZ1XB+MIs2j6ZoIHPT3o\nsl6XcrGf4p3qTIgIqPkjRDeSbHQHFbGbfQLR4kLP589+Qs8dPqUpHjs9AoGANpSJ\nCnXJHcW3TM3qD8wb2458rdDL4isd4aa78MvCwhUoPTHvYbrRGo8b5QNMWUmAKfal\nTDSIro6KVQWwEGBhz5U4B8VX9yDwRHZbmR5wUBNTWBvTepvbhUTtBRuxwC6etKvM\nHLvXAV63Hk14AVusZsSyXpeH9P4HhnPu1NI5xLUCgYBDHGvPMOqUCsEteM+w8jgh\nyDO4BCvtEWaTCFF5fRs4iElLdzRJuVHuUM4jTQuoV7lE0Fh+dRu9baBxY4weBi2F\n3GFIsyQmc4djz0tr0pCTAY4+rCT/b+vqA6ZkanAH/3cJEERh4rj6aiDFf/34Kzan\nboVY7O1XxmVMFqoJ6WhTTw==\n-----END PRIVATE KEY-----\n",
    "publicKey": "-----BEGIN PUBLIC KEY-----\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAm1zPDZ9ALgXcH0svQ+A8\nVjBgFiVKmGA9WzqocmR9ylndoIkUSCLBkoHr1brGaS3a2AwaBkDJhef94/vd5DgR\n4IQy23bOoGorYlVQ+1Ky2ytSV7YMMOa/2RH7H8+sNw/MigeJVl2cBXRRsr7CE40V\n0QbYW4JAYOKdeV1SJ46SKJTZwVfsFNCWfIZO2PPKJ0jsN39tudglIsJuBQqyQj1a\nVzK0b/lQgMr8V2jJOJaWKl3kJfmj8Usz1T6V6Vv2KVt8ZbC5xvXdxBi2XQZyoiXC\nQgnuiHsqkRTMOXrOa6SBpz7Nf9Ls6qns7YRrEp/0gJIN9WjTXCc71A8RcFtKa3ZH\nVQIDAQAB\n-----END PUBLIC KEY-----\n"
  },
  "meta": {
    "path": "/api/v1/applications",
    "timestamp": "2025-05-16T10:19:41.167Z"
  }
}
```



This is used internally by the backend to notify the blockchain service of relevant events. The variable is accessed via `settings.BLOCKCHAIN_WEBHOOK_URL` in `app/services/blockchain.py`.

#### When are webhooks sent?

Webhooks are sent to the blockchain service in the following scenarios:

* **Agreement Status Changes**: When an agreement transitions to certain statuses (`BEFORE_PROGRESS`, `IN_PROGRESS`, `OVERDUE`, `COMPLETED`), a webhook is sent.
* **Payment Cancellations**: If a payment is cancelled (e.g., by admin), a webhook is sent to update the blockchain.

#### How are webhooks sent?

* The backend constructs a webhook body using the current agreement data and status.
* The webhook is sent asynchronously using Celery (`send_blockchain_webhook_async`).
* The webhook body includes all necessary agreement and event details.

**Example (from agreements API):**

```python
webhook_body = get_send_blockchain_webhook_body(agreement)
send_blockchain_webhook_async.delay(jsonable_encoder(webhook_body))
```

**Example (from payments service):**

```python
webhook_body = get_send_blockchain_webhook_body(agreement)
send_blockchain_webhook_async.delay(jsonable_encoder(webhook_body))
```

#### Webhook Body Construction

The webhook body is constructed based on the agreement's status:

* For creation or progress: includes full agreement details.
* For completion or cancellation: includes event type and agreement ID.

See `get_send_blockchain_webhook_body` in `app/apis/agreements.py` for details.

#### Asynchronous and Reliable

* All webhook sends are handled asynchronously via Celery.
* On failure, the task is retried up to 3 times.
* On repeated failure, a Discord notification is sent.

***

#### Example: Agreement Status Change Flow

1. User or system changes the status of an agreement.
2. The backend updates the agreement and logs the change.
3. If the status is relevant, a webhook is constructed and sent to the blockchain service.
4. The blockchain service processes the event and may respond with a webhook back to `/blockchain/webhook`.
5. All blockchain events are logged in `agreement_blockchain_logs`.

For more details, see the implementation in `app/services/blockchain.py` and the API definition in `app/apis/blockchain.py`.

***

### Receiving Blockchain Events

Your backend exposes a webhook endpoint for blockchain events:

* **POST** `/blockchain/webhook`

This endpoint receives webhook events from the blockchain service related to agreement contracts. It processes events such as contract creation, initialization, and updates, and logs them in the system.

#### Request Body

The body must match the `BlockChainReceivedWebhookBody` schema. Example:

```json
{
  "type": "CREATE", // or "INIT_DATA", "UPDATE"
  "data": {
    "agreement_id": "uuid-string",
    "tx_hash": "0x...",
    "tx_block_number": 123456,
    "tx_block_timestamp": 1712345678,
    "contract_address": "0x...",
    "data": { /* event-specific data */ }
  }
}
```

* `type`: The event type (`CREATE`, `INIT_DATA`, `UPDATE`).
* `data`: Event-specific data, must include `agreement_id`, transaction details, and contract address.

#### Processing Flow

1. **Receive Webhook**: The API receives the webhook and validates the event type.
2. **Async Processing**: The event is queued for asynchronous processing via Celery.
3. **Agreement Lookup**: The agreement is fetched from the database.
4. **Event Handling**: Depending on the event type:
   * `CREATE`: Updates agreement with contract details and logs the event.
   * `INIT_DATA`: Logs the initialization event.
   * `UPDATE`: Logs the update event.
5. **Idempotency**: Duplicate transaction hashes are ignored.
6. **Error Handling**: Errors are retried up to 3 times. On failure, a Discord notification is sent.

#### Example cURL

```sh
curl -X POST https://your-domain.com/blockchain/webhook \
  -H "Content-Type: application/json" \
  -d '{
    "type": "CREATE",
    "data": {
      "agreement_id": "uuid-string",
      "tx_hash": "0x...",
      "tx_block_number": 123456,
      "tx_block_timestamp": 1712345678,
      "contract_address": "0x...",
      "data": {}
    }
  }'
```



