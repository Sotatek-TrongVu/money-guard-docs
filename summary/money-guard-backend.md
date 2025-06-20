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

To obtain `BLOCKCHAIN_APPLICATION_ID`, `BLOCKCHAIN_APPLICATION_SECRET_KEY`, please check [Generate Money Guard Application Script](generate-money-guard-application.md)


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



