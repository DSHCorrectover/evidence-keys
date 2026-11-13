# Correctover Evidence Keys

This repository is the **public trust root** for cryptographic artifacts signed
by Correctover in on-chain investigations and evidence packages.

The signing key is self-generated, so a signature alone proves authorship only
relative to this key. This root binds the key to a published identity: compare
the fingerprint below with the fingerprint printed in every evidence package,
in the email signature block, and on the sample pages. The initial commit and
all commits in this repository are **SSH-signed** (GitHub shows "Verified"),
which makes the publication point itself tamper-evident.

## Current key

- **key_id:** `correctover-evidence-2026-10`
- **algorithm:** Ed25519 (RFC 8032)
- **created:** 2026-10-24T09:09:00Z
- **fingerprint (SHA-256 of raw 32-byte public key):**
  `7a2a87423ae13585d1f04551beafda67456170dc2987ca14e3d0c7773d31af3d`
- **fingerprint (SHA-256 of SPKI DER):**
  `8467101ef5d5b85744851ab00322f01fed9e49d70102fd3ae0ec6f8ce3a0ecbb`
- **public key file:** [`correctover-evidence-2026-10.pub.pem`](./correctover-evidence-2026-10.pub.pem)

## What the signature covers

Each evidence package ships with:

- `evidence_manifest.json` — canonical-JSON manifest binding the package id,
  creation time, key id/fingerprint, hashes of all source receipts, the Merkle
  root, the assertion breakdown, the scope window, and the "as of" timestamp.
- `evidence_manifest.json.sig` — a raw 64-byte Ed25519 signature over the
  canonical manifest.
- `merkle_root.txt` / `merkle_root.txt.ots` — the SHA-256 Merkle root over all
  receipts and its OpenTimestamps Bitcoin calendar proof.

Every receipt embeds its own Merkle leaf, the root, and an inclusion proof, so a
single Bitcoin timestamp bounds the whole receipt set.

## Verify in under 5 minutes

```sh
# 1. check the manifest signature against the published public key
openssl pkeyutl -verify -pubin \
  -inkey correctover-evidence-2026-10.pub.pem \
  -rawin -in evidence_manifest.json \
  -sigfile evidence_manifest.json.sig

# 2. (optional) full package check: manifest sig, file hashes, all 46 Merkle
#    inclusion proofs, and the assertion/as-of accounting
python3 verify_evidence.py

# 3. (once the Bitcoin proof confirms) prove the Merkle root existed at a time
ots upgrade merkle_root.txt.ots
ots verify merkle_root.txt.ots
```

Then confirm the fingerprint printed by the verifier matches the one on this
page. `openssl` and `python3` are the only requirements; the verifier installs
nothing.

## Key management

- The private key is held offline with restricted access and is never committed,
  packaged, or transmitted.
- Key rotation, if it ever happens, will be announced here by a new SSH-signed
  commit, with the old key retained and marked retired (not deleted) so historical
  signatures remain verifiable.
- Correctover. Contact: wangguigui@correctover.com
