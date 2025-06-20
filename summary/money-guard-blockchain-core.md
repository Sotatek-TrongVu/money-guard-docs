# Money Guard Blockchain Core

### About

MoneyGuard BLC Core is a NestJS-based backend service that interfaces with blockchain smart contracts to manage IOU records. It provides a secure and transparent way to create, update, and manage financial obligations between parties using blockchain technology.

Key Features:

* Create new IOU records on the blockchain
* Add data to existing IOUs
* Track IOU events and transactions
* Webhook and Discord integration for notifications
* Modular architecture for extensibility

### Usage

#### Installation

1.  Clone the repository:

    ```bash
    git clone https://github.com/your-username/moneyguard-blc-core.git
    cd moneyguard-blc-core
    ```
2.  Install dependencies:

    ```bash
    npm install
    ```
3.  Configure environment variables:

    ```bash
    cp .env.example .env
    # Edit .env with your configuration
    ```

4. Generate application credentials:
Go to https://www.uuidgenerator.net/ to generate a random UUID for `MONEY_GUARD_APPLICATION_ID` then run the following command to generate the application credentials:
   ```bash
   npm run generate-money-guard-application
   ```
More details, see [Generate Money Guard Application Script](generate-money-guard-application.md)

#### Commands

```bash
# Development
npm run start:dev

# Production build
npm run build
npm run start:prod

# Run tests
npm run test

# Run crawler process
npm run crawler

# Run consumer process
npm run consumer

# Run application signature example
npm run application-signature-example

# Run local script
npm run local

# Generate Money Guard application with keys
npm run generate-money-guard-application

# Lint and format
npm run lint
npm run format

# TypeORM migrations
npm run migration:generate --name=MigrationName
npm run migration:create --name=MigrationName
npm run migration:run
npm run migration:revert
```

### Development

#### System Requirements

* OS: Ubuntu 22.04.4 LTS x86\_64
* CPU: 2 Core(s)
* Memory: 4GB
* Disksize: 80GB

#### Pre-Requisites

* Node.js (20 or higher)
* npm or yarn
* BSC node access (local or testnet)
* TypeScript knowledge
* NestJS framework familiarity
* PostgreSQL database
* RabbitMQ
* Redis

#### Development Environment

1. Set up your blockchain environment:
   * Configure RPC URL in `.env`
   * Set up wallet private key
   * Deploy required smart contracts
2.  Install development dependencies:

    ```bash
    npm install --save-dev
    ```

#### File Structure

```
src/
├── main.ts
├── app.module.ts
├── crawler.ts
├── consumer.ts
├── application-signature-example.ts
├── local.ts
├── modules/
│   ├── iou/                # IOU-related functionality (controllers, services, dtos, entities, interfaces)
│   ├── blockchain/         # Blockchain interaction services
│   ├── webhook/            # Webhook handling (controllers, services, dtos)
│   ├── consumer/           # Consumer process logic
│   ├── crawler/            # Crawler process logic
│   ├── discord/            # Discord integration (webhook, API)
│   ├── application/        # Application management (controllers, services, dtos, entities)
├── shared/                 # Shared utilities, constants, decorators, guards, etc.
├── external/               # External integrations (e.g., money-guard)
```

| Directory           | Purpose                          |
| ------------------- | -------------------------------- |
| modules/iou         | IOU-related functionality        |
| modules/blockchain  | Blockchain interaction services  |
| modules/webhook     | Webhook handling                 |
| modules/consumer    | Consumer process logic           |
| modules/crawler     | Crawler process logic            |
| modules/discord     | Discord integration              |
| modules/application | Application management           |
| shared              | Shared utilities and common code |
| external            | External integrations            |
| migrations          | Database migrations              |
| test                | Test files                       |

#### Build

```bash
npm run build
# The build artifacts will be stored in the dist/ directory
```

#### Deployment

1. Build the application
2. Set up environment variables
3.  Start the application:

    ```bash
    npm run start:prod
    ```

### API Documentation

API documentation is available at: [http://localhost:3000/swagger](http://localhost:3000/swagger)

### Migration Process

MoneyGuard BLC Core uses TypeORM for database migrations. The configuration is in `ormconfig.ts`.

#### Running Migrations

```bash
# Generate a new migration
npm run migration:generate --name=MigrationName

# Create a new migration
npm run migration:create --name=MigrationName

# Run migrations
npm run migration:run

# Revert last migration
npm run migration:revert
```

Migrations are stored in the `migrations` directory.

### Crawler Process

The crawler process monitors blockchain events and indexes them in the database for faster access and querying.

#### Starting the Crawler

```bash
npm run crawler
# Or in development mode with watching
npm run start:dev -- --entryFile=crawler
```

Configure the crawler in `.env`:

```sh
# Crawler Configuration
IOU_FACTORY_CONTRACT_ADDRESS=...
IOU_FACTORY_CONTRACT_START_BLOCK=...
IOU_FACTORY_CONTRACT_INCREMENT_BLOCK=...
IOU_FACTORY_CONTRACT_CHAIN_ID=...
IOU_OPERATOR_PRIVATE_KEY=...
READ_RPC_URL=...
WRITE_RPC_URL=...
```

### Consumer Process

The consumer process handles asynchronous tasks and event processing.

#### Starting the Consumer

```bash
npm run consumer
# Or in development mode with watching
npm run start:dev -- --entryFile=consumer
```

Configure queue connections in `.env`:

```sh
# RabbitMQ Configuration
RABBITMQ_USER=...
RABBITMQ_PASS=...
RABBITMQ_PORT=...
RABBITMQ_MANAGEMENT_PORT=...
RABBITMQ_URLS=...
```

### Webhook

The Webhook module handles **incoming webhooks** from Money Guard Backend system, specifically for MoneyGuard IOU events. It validates, parses, and emits these events to RabbitMQ for further processing by internal consumers. Then it sends webhooks to Money Guard Backend system to sync up blockchain data

#### Purpose

* Receives POST requests at `/webhook/money-guard`.
* Supports two event types: `moneyGuard.createIOU` and `moneyGuard.updateIOUData`.
* Validates and transforms the payload.
* Emits the event to RabbitMQ for asynchronous processing.
* Send webhook to Money Guard Backend to sync up blockchain data

#### Supported Event Types

* `moneyGuard.createIOU`: Create a new IOU record.
* `moneyGuard.updateIOUData`: Update IOU data (only `CANCELLED` or `COMPLETED` event types allowed).

#### Example Payload

```json
{
  "type": "moneyGuard.createIOU",
  "timestamp": 1710000000,
  "data": {
    "agreement_id": "abc123",
    "amount": 1000,
    "interest_rate": 5,
    "interest_payment_date": 1710000000,
    "repayment_start_date": "2024-06-01T00:00:00.000Z",
    "repayment_end_date": "2024-12-01T00:00:00.000Z",
    "pdf_file_url": "https://example.com/file.pdf",
    "creditor_id": "user1",
    "debtor_id": "user2"
  }
}
```

#### Configuration

```bash
MONEY_GUARD_WEBHOOK_URL=http://example.com
MONEY_GUARD_APPLICATION_ID=<random uuid>
```

> **Note:** Need to generate an application ID by use the [Generate Money Guard Application Script](generate-money-guard-application.md).

#### Usage

* **Incoming:** External systems POST to `/webhook/money-guard` with a valid payload.
* **Processing:** The backend validates, transforms, and emits the event to RabbitMQ for further processing.

For more details, see the code in `src/modules/webhook/` and the API documentation.
