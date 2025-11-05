# tb-bridge
(draft, design revisions expected)

Token bridge service between Telos and Base

A backend API service intended for exchanging HYPHA v3 utility tokens on Base mainnet to Rainbow contract tokens on Telos and back. The service holds copies of several private keys in order to execute bridge transactions. Management of the bridge status occurs through the backend API service and a Telos smart contract.

# Hypha proposal

We thought of a simpler scenario with Vlad that is achievable for the gathering. A few manual actions are required but we think this should be achievable in the context of the gathering as admins will be in person with the participants.

### Accounts
User create a Base account in Hypha v3 and a Telos account in the Seeds wallet
We ask them to fill in an online form to indicate both their accounts (can be verified in the UI)

### Tokens
Spaces create their token in hypha v3
We ask them to fill in an online form when token is voted on with the name of their space and token (can be verified in the UI)
Tokens are then created by admins in the Rainbow seeds contract

### From Base To Telos
We create a space on Hypha called "Bridge Space", admins (such as yourself) will be members of this space
We enable the hypha v3 wallet feature in the web user interface to send space tokens to this space.
This space plays the role of the bridge ledger as admins can see all transactions in the treasury view
When transfer is visible on the "Bridge Space", admins mint the same amount from rainbow seeds and send it to the user seeds wallet.

### From Telos To Base
When a transfer to rainbow seeds contract is done to migrate to back to base, the bridge space sends the token to the user using the deploy funds proposal.
The user receives the amount in their base wallet

### This would be an acceptable scenario from our point of view as limited manual actions:
User: 1 short online form
Space creator: 1 short online form
Admin: easy way to monitor and limited transfers required

# Original 

## Concept
A pair of tokens, one on Telos mainnet and one on Base mainnet, are treated as equivalent, so that a user can interchange freely between them depending on the activities they are performing.
A currency administrator registers a pair of tokens with the service, and provides liquidity into an escrow account on each chain.
Each user registers with the service, attesting that they own a specific pair of accounts, one on each chain. Once the registration is successful, the service holds the user private key on Base and treats a signature on the Telos chain as authorizing a bridge action, which involves transfer transactions on both chains.
Bridging is executed by transfer of tokens between the corresponding user accounts and escrow accounts.

## Features

- ✅ Register matching Telos and Base blockchain accounts
- ✅ Execute HYPHA v3 token transfer on Base mainnet to or from escrow account
- ✅ Execute Rainbow token transfer on Telos to or from escrow account
- ✅ Built for hosted deployment
- ✅ CORS enabled for frontend integration
- ✅ Comprehensive error handling and logging
- ✅ Health checks and status endpoints


## API Endpoints

### `GET /api/health`
Health check endpoint to verify API status and configuration.

**Response:**

```json
{
  "status": "healthy",
  "timestamp": "2024-01-01T00:00:00.000Z",
  "services": {
    "api": "operational",
    "baseNetwork": "connected (chainId: 8453)",
    "telosNetwork": "connected (head_block: 123456)"
  },
  "configuration": {
    "walletConfigured": true,
    "walletAddress": "0x1234...5678"
  },
  "endpoints": {
    "/api/health": "GET - Health check",
    "/api/status": "GET/POST - Check migration status",
    "/api/transfer-hybrid": "POST - Execute HYPHA mint (migration table verification)"
  }
}
```


### `GET/POST /api/status`

Check status without executing any transfers.

**Parameters:**

- `account` (string): Telos account name or Ethereum address


**Example Request:**

```bash
curl "https://<host>/api/status?account=mytelosaccount"
```

**Response:**

```json
{
  "success": true,
  "accountmatch": true,
  "message": "matching account found",
  "data": {
    "matchrecord": {
      "authTelos": true,
      "authEth": true,
      "telosAccount": "mytelosaccount",
      "ethAddress": "0x1234567890123456789012345678901234567890",
    },
    "telosToken": {
      "contract": "tokensmaster",
      "escrowAvailable": "1000.0000 IKA"
    },
    "ethToken": {
      "address": "0x1234567890123456789012345678901234567890",
      "ticker": "IKA",
      "decimals": 4,
      "escrowAvailable": 1000.00
    }
  }
}

### `POST /api/convert`

Initiate conversion 

**Request Body:**

```json
{
  "account": "mytelosaccount",
  "amount": "10.0000 IKA",
  "toTelos": true
}
```

**Response:**
The response is an esr: uri, encoding a Telos transaction which must be signed and submitted by the user.

### `POST /api/complete`

Complete conversion 

**Request Body:**

```json
{
  "account": "mytelosaccount",
  "amount": "10.0000 IKA",
  "nonce": 123456
}
```

**Response:**

```json
{
  "success": true,
  "message": "bridge transfer completed",
  "data": {
    "amount": "10.0000 IKA",
    "ethTx": "0x1234567890123456789012345678901234567890",
    "telosTx": "0x1234567890123456789012345678901234567890"
  }
}

