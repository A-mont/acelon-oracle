# Acelon Oracle Gear smart contract Tutorial

This smart contract implements the logic to receive price updated from [Acelon](https://acelon.io/) oracles.

The contract is written using [Sails and Gear](https://wiki.vara.network/docs/build), which is the smart contract technology supported by the [Vara Network](https://vara.network/).

## Build

### Prerequisites

Please follow the [Vara setup guide](https://wiki.vara.network/docs/getting-started-in-5-minutes).

### Build contract

```shell
cargo build --release
```

The command above will generate the contract WASM code and metadata.

## Tests

```shell
cargo test
```

## Deploy

For detailed steps, please see the [Upload program](https://wiki.vara.network/docs/build/deploy) page on the Vara wiki.

## Usage

The core functionality the contract offers is the `update_price_feeds` call, which takes as input a list of updates and corresponding signatures that have been produced by the Acelon oracles.

See the test in [gtest.rs](tests/gtest.rs) for an example.

The contract also stores a list of valid signers (Oracle addresses) and certificate hashes, only prices provided by the those Oracles (signers) and certificates will be accepted.

# 🦾 How to Update Price Feeds on Vara with the Acelon Oracle Client

This step-by-step guide explains how to update asset prices (e.g., VARA/USDT) using an oracle contract on Vara Network, following the Acelon standard and including trusted signers, certificates, and signature threshold.

---

## 1. Deploy Your Oracle Contract on Vara

- Deploy your contract (e.g., via Gear Frontend or CLI).
- Save your deployed contract’s address (`program_id`).

---

## 2. Configure Trusted Signers, Certificates, and Threshold

### a) Add Trusted Signers (Oracles)

1. Use the `update_trusted_signer` function.
2. Enter the signer’s public key (as a byte array or hex, as required).
   - If your contract expects the **hash** of the public key.
   - Example (hex):  
     ```
     0x024bfc159a52463ce19ed8c4e7f46378d4111fc9ebf83dd4fef17a099d7b5937cb
     ```
3. Submit the transaction to register the signer.

### b) Add Certificates to the Trust Store

If your contract requires certificates (to validate the authenticity of signers):

1. Use the `update_certificate_to_trust_store` function.
2. Enter each certificate as a 32-byte hex string or array.
   - Example:
     ```
     0x7f09db6060e6ab8c7cc96eda8cd93cf629c619e3c522dadc61130530a3681140
     ```
3. Submit a transaction for each certificate you need to add.

> **Tip:** Certificates are usually needed to verify that a particular public key is authorized by a recognized authority, adding an extra security layer.

### c) Configure the Signature Threshold

1. Use the `configure` function.
2. Set the `SignersThreshold` (minimum required valid signatures).
   - For quick tests, use `0`.
   - For production, always use `1` or higher.
3. Submit the transaction.

---

### 3.  Call update_price_feeds in the UI

1. Go to the update_price_feeds function in your dApp frontend (Gear, Polkadot.js Apps, etc).

2. In the update_data field, paste your packed data example:
```json
["0x040a810600000000000000000000000000845306d297010000087f09db6060e6ab8c7cc96eda8cd93cf629c619e3c522dadc61130530a3681140d01c1a02125c916f90b158105a9d3cd02c4683f6df4da6decd183560a95f56bac7d60632fe55f42dfc753a8fa1526293ff5808845da7c1e3b3fab0e7ca6a9675"]

```

3. In the signatures field, paste your array of hex signatures (65 bytes each):

Example: 
```json
["0x0982e0a84d19d70629dd26ac8d87389134148b10c211bce536e2f12631d730b2456cc7e17da46927baf6a5dee4d669e7f89bf97edef59f8a8c792f50740645931c0000"]
```
4. Submit the transaction

Event:
```json
{
  "request_hash": "0x...",
  "price_entry": {
    "timestamp": ...,
    "prices": [ ... ]
  }
}

```