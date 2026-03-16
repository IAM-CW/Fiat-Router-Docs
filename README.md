# Fiat Router

> Where Fiat Becomes Crypto — A unified onramp aggregator for easy fiat-to-crypto conversion.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](#license)
[![Docs](https://img.shields.io/badge/docs-live-brightgreen)](#documentation)

---

## Overview

Fiat Router is a protocol-level aggregator that routes fiat-to-crypto transactions through the best available onramp provider based on fees, speed, token availability, and regional support. Instead of integrating with a single onramp (MoonPay, Transak, Ramp Network, etc.), applications integrate once with Fiat Router and get access to all of them with automatic best-rate routing.

### The Problem

The fiat-to-crypto onramp landscape is fragmented. Each provider has different fee structures, supports different regions, covers different tokens, and offers different payment methods. Developers building wallets, dApps, or exchanges must either pick one provider (limiting their users) or build and maintain multiple integrations (expensive and complex).

For users, this means overpaying for conversions because they don't know which provider offers the best rate for their specific transaction and they shouldn't have to.

### The Solution

Fiat Router sits between the application and the onramp providers, acting as an intelligent routing layer. When a user initiates a fiat-to-crypto transaction, Fiat Router queries all supported providers in real time, compares the total cost (including fees, spread, and network charges), and routes the transaction through the optimal path.

---

## How It Works

```
┌──────────┐     ┌──────────────┐     ┌──────────────────┐
│   User   │────▶│  Fiat Router │────▶│  Best Available  │
│  (Wallet │     │  Aggregator  │     │  Onramp Provider │
│   or App)│     └──────┬───────┘     └──────────────────┘
└──────────┘            │
                        │ Queries all providers
                        │ in real time
              ┌─────────┼─────────┐
              ▼         ▼         ▼
         ┌─────────┐ ┌────────┐ ┌──────┐
         │ MoonPay │ │Transak │ │ Ramp │  ...
         └─────────┘ └────────┘ └──────┘
```

**Step 1 — Quote Request:** The user specifies the fiat amount, fiat currency, target crypto asset, and payment method.

**Step 2 — Multi-Provider Query:** Fiat Router simultaneously queries all integrated providers for real-time quotes, including fees, estimated delivery time, and exchange rates.

**Step 3 — Optimal Routing:** The aggregator ranks providers by total cost to the user and selects the best option. If the top provider fails (KYC issues, regional restrictions, downtime), it automatically falls back to the next best.

**Step 4 — Transaction Execution:** The user completes the transaction through the selected provider's flow, with Fiat Router handling the handoff seamlessly.

---

## Supported Providers

| Provider | Regions | Payment Methods | Tokens |
|----------|---------|-----------------|--------|
| MoonPay | 160+ countries | Card, Bank, Apple Pay | 100+ |
| Transak | 170+ countries | Card, Bank, UPI | 130+ |
| Ramp Network | 150+ countries | Card, Bank, Apple Pay | 90+ |
| Banxa | 130+ countries | Card, Bank, PIX | 60+ |

*Provider coverage is subject to change. Fiat Router updates provider data in real time.*

---

## Integration

### Quick Start

Install the SDK:

```bash
npm install @fiat-router/sdk
```

### Basic Usage

```javascript
import { FiatRouter } from '@fiat-router/sdk';

const router = new FiatRouter({
  apiKey: process.env.FIAT_ROUTER_API_KEY,
  environment: 'production',
});

// Get the best quote for buying ETH with USD
const quote = await router.getQuote({
  fiatCurrency: 'USD',
  fiatAmount: 100,
  cryptoAsset: 'ETH',
  network: 'ethereum',
  paymentMethod: 'card',
});

console.log(quote);
// {
//   provider: 'transak',
//   fiatAmount: 100,
//   cryptoAmount: 0.0412,
//   totalFees: 2.49,
//   exchangeRate: 2427.18,
//   estimatedTime: '2-5 minutes',
//   alternativeQuotes: [...]
// }

// Execute the transaction
const transaction = await router.execute(quote);
```

### Configuration Options

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `apiKey` | string | Yes | Your Fiat Router API key |
| `environment` | string | No | `production` or `sandbox` (default: `sandbox`) |
| `preferredProviders` | string[] | No | Priority list of providers to prefer |
| `excludeProviders` | string[] | No | Providers to exclude from routing |
| `timeout` | number | No | Quote request timeout in ms (default: `10000`) |

---

## API Reference

### `router.getQuote(params)`

Returns the best available quote across all providers.

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `fiatCurrency` | string | Yes | ISO 4217 currency code (e.g., `USD`, `EUR`, `GBP`) |
| `fiatAmount` | number | Yes | Amount in fiat currency |
| `cryptoAsset` | string | Yes | Target crypto asset symbol (e.g., `ETH`, `BTC`, `USDC`) |
| `network` | string | Yes | Target blockchain network |
| `paymentMethod` | string | No | Preferred payment method (default: `card`) |
| `walletAddress` | string | No | Destination wallet address |
| `userCountry` | string | No | ISO 3166 country code for regional filtering |

**Returns:** `Quote` object containing the best rate, provider details, fee breakdown, and alternative quotes.

### `router.execute(quote)`

Initiates the transaction through the selected provider.

**Parameters:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `quote` | Quote | Yes | Quote object returned by `getQuote()` |

**Returns:** `Transaction` object with status, provider redirect URL, and tracking ID.

### `router.getProviders()`

Returns the list of available providers and their current status.

### `router.getStatus(transactionId)`

Returns the current status of a transaction.

**Status values:** `pending` → `processing` → `completed` or `failed`

---

## Security

Fiat Router does not handle, store, or have access to user funds or payment information at any point. All payment processing occurs directly between the user and the selected onramp provider. Fiat Router only facilitates the routing decision and provider handoff.

For security concerns, please contact security@fiatrouter.example.io.

---

## Documentation

- [Integration Guide](docs/integration-guide.md) — Step-by-step integration walkthrough
- [API Reference](docs/api-reference.md) — Complete API documentation
- [Provider Coverage](docs/providers.md) — Detailed provider comparison
- [Error Handling](docs/error-handling.md) — Error codes and recovery strategies
- [FAQ](docs/faq.md) — Common questions and answers

---

## Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.
