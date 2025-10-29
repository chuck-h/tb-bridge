# tb-bridge
Token bridge service between Telos and Base

A backend API service intended for exchanging HYPHA v3 utility tokens on Base mainnet to Rainbow contract tokens on Telos and back. This API registers a correlation between a Telos account name and a Base account address.

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
curl "https://<host>/api/status?telosAccount=mytelosaccount"
```

**Response:**

```json
{
  "success": true,
  "accountmatch": true,
  "message": "matching account found",
  "data": {
    "matchrecord": {
      "signedTelos": true,
      "signedEth": true,
      "telosAccount": "myaccount",
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

### `POST /api/transfer-hybrid`

Execute conversion 

**Request Body:**

```json
{
  "telosAccount": "myaccount",
  "ethAddress": "0x1234567890123456789012345678901234567890"
}
```
