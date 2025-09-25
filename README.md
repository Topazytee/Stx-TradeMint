

# Stx-TradeMint – Decentralized Trading Platform

**TradeMint** is a smart contract-based decentralized trading platform for digital assets built on the Clarity language. It enables secure and transparent trading with features such as escrow-like listing, offer management, listing expiry, and cleanup.

---

## 🚀 Features

* **Create Listings**: Sellers can list digital assets for sale with a price and expiry block height.
* **Cancel Listings**: Sellers can cancel active listings before expiry.
* **Expire Listings**: Anyone can clean up expired listings and mark them as expired (admin-like utility).
* **Offer Handling** *(coming soon)*: Placeholder for buyer offers against listings.
* **Secure Escrow Logic**: Listings are only valid when active and within the expiry block range.
* **Event Emission**: Emits on-chain events for transparency and indexing.

---

## 🏗️ Data Structures

### Listings Map

Stores listings by ID:

```clojure
(define-map listings
  uint ; listing-id
  {
    seller: principal,
    asset: (string-ascii 32),
    price: uint,
    status: (string-ascii 10), ; e.g., "active", "cancelled", "expired"
    expiry: uint, ; block height
    created-at: uint
  }
)
```

### Offers Map

Stores buyer offers keyed by listing and buyer:

```clojure
(define-map offers
  {listing-id: uint, buyer: principal}
  {
    amount: uint,
    status: (string-ascii 10),
    created-at: uint
  }
)
```

---

## 🔐 Access Control

* Only the **seller** can cancel their own active listings.
* Anyone can trigger listing expiration after expiry block height.

---

## 📦 Public Functions

### `create-listing`

Creates a new listing.

```clojure
(define-public (create-listing (asset (string-ascii 32)) (price uint) (expiry uint)) → (response uint uint))
```

* Validates expiry is in the future and price is non-zero.
* Emits: `listing-created`

---

### `cancel-listing`

Cancels an active listing.

```clojure
(define-public (cancel-listing (listing-id uint)) → (response bool uint))
```

* Only seller can cancel.
* Emits: `listing-cancelled`

---

### `cleanup-expired-listing`

Marks a listing as expired if it passed expiry.

```clojure
(define-public (cleanup-expired-listing (listing-id uint)) → (response bool uint))
```

* Anyone can call.
* Emits: `listing-expired`

---

## 🔎 Read-Only Functions

### `get-listing`

Fetch a listing by ID.

```clojure
(define-read-only (get-listing (listing-id uint)) → (optional ...))
```

---

### `get-offer`

Fetch an offer for a listing by buyer.

```clojure
(define-read-only (get-offer (listing-id uint) (buyer principal)) → (optional ...))
```

---

### `get-listing-count`

Get total number of listings (nonce-based ID generator).

```clojure
(define-read-only (get-listing-count) → uint)
```

---

### `is-expired`

Check if a listing is expired.

```clojure
(define-read-only (is-expired (listing-id uint)) → bool)
```

---

## 📌 Constants

| Constant Name              | Description                          |
| -------------------------- | ------------------------------------ |
| `contract-owner`           | Owner of the contract (tx-sender)    |
| `err-not-authorized`       | Unauthorized action (u100)           |
| `err-listing-not-found`    | Listing does not exist (u101)        |
| `err-invalid-status`       | Invalid listing status (u102)        |
| `err-insufficient-balance` | Placeholder for token logic (u103)   |
| `err-no-active-offer`      | Placeholder for offers (u104)        |
| `err-listing-expired`      | Listing expired (u105)               |
| `err-invalid-amount`       | Price or offer amount invalid (u106) |
| `err-offer-not-found`      | Offer not found (u107)               |
| `err-listing-closed`       | Listing already closed (u108)        |
| `err-already-has-offer`    | Buyer already placed offer (u109)    |

---

## 📋 Events

TradeMint emits structured events for off-chain indexing:

```clojure
(print {
  event-id: uint,
  event-type: (string-ascii 20),
  listing-id: uint,
  principal: (optional principal)
})
```

Event types:

* `listing-created`
* `listing-cancelled`
* `listing-expired`

---

## 🛠️ Future Improvements

* Implement offer creation, cancellation, and acceptance logic.
* Integrate STX/token escrow.
* Add seller/buyer reputation metrics.
* Enable dispute resolution mechanisms.

---
