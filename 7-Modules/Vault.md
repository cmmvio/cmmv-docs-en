# Vault

Repository: [https://github.com/cmmvio/cmmv/tree/main/packages/vault](https://github.com/cmmvio/cmmv/tree/main/packages/vault)

The **@cmmv/vault** module provides a secure data storage system designed to manage sensitive information using **elliptic curve cryptography (ECC)** and **AES-256-GCM encryption**. The module enables **key generation, secure data insertion, retrieval, and deletion**, ensuring strong cryptographic protection for stored data.

## Features
<br/>

- **Elliptic Curve Encryption (secp256k1)** - Public and private key pair management.
- **AES-256-GCM Encryption** - Secure data encryption with authentication.
- **Key-Based Secure Storage** - Namespaced key-value storage with UUID v5.
- **Integration with CMMV Framework** - Built-in support for repository and authentication.
- **Full CRUD Operations** - Insert, retrieve, and delete secure data.

## Installation

<div style="
    background-color: #DBEAFE;
    border-left: 4px solid #3B82F6;
    color: #1E40AF;
    padding: 1rem;
    border-radius: 0.375rem;
    margin: 1.5rem 0;
    font-size: 12px;
">
    <p style="font-weight: bold; margin-bottom: 0.5rem;">Important Notice</p>
    <p>
        The <strong>@cmmv/vault</strong> module is only available from version <strong>0.8.30</strong> onwards, as it relies on dynamic entity loading features introduced in this version.
        Ensure your project is running on <strong>version 0.8.30 or higher</strong> to use this module without compatibility issues.
    </p>
</div>

To install the module, run:

```sh
npm install @cmmv/vault
```

## Configuration

The module requires a configuration file (`.cmmv.config.cjs`) to define namespace and cryptographic keys:

```javascript
module.exports = {
    vault: {
        namespace: "your-unique-namespace",
        publicKey: "your-public-key",
        privateKey: "your-private-key",
    }
};
```

| **Path**             | **Description**                               | **Default Value / Example** |
|----------------------|-------------------------------------------|--------------------------|
| `vault.namespace`   | Unique identifier for key hashing         | `"your-unique-namespace"` |
| `vault.publicKey`   | Public key for encryption                 | `"your-public-key"` |
| `vault.privateKey`  | Private key for decryption                | `"your-private-key"` |

## API Endpoints

### **1. Insert Secure Data**

Inserts encrypted data into the vault.

**Endpoint:** `POST /vault`

**Request Body:**
```json
{
  "key": "my-secret-key",
  "payload": "Sensitive data to be encrypted"
}
```

**Response:**
```json
{
  "success": true,
  "id": "generated-entry-id"
}
```

### **2. Retrieve Secure Data**

Retrieves and decrypts data using the stored private key.

**Endpoint:** `GET /vault/:key`

**Response:**
```json
{
  "payload": "Decrypted sensitive data"
}
```

### **3. Delete Secure Data**

Removes an entry from the vault.

**Endpoint:** `DELETE /vault/:key`

**Response:**
```json
{
  "success": true
}
```

## Usage Example

You can use the Vault Service to encrypt and store data securely.

```typescript
import { VaultService } from '@cmmv/vault';

const vaultService = new VaultService();

// Insert data
await vaultService.insert('userToken', 'my-secret-value');

// Retrieve data
const secretValue = await vaultService.get('userToken');
console.log(secretValue); // Decrypted data

// Remove data
await vaultService.remove('userToken');
```

## Cryptographic Security

The vault module implements **secp256k1 elliptic curve encryption** combined with **AES-256-GCM** for strong security. The encryption workflow is as follows:

1. A **public key** is used to encrypt the data.
2. The data is securely stored along with an **IV (Initialization Vector)** and an **authentication tag**.
3. When retrieving the data, the stored **private key** is used to decrypt the information securely.

## Key Generation

You can generate new keys using the following function:

```typescript
const vaultService = new VaultService();
const keys = await vaultService.createKeys();

console.log(keys);
/* {
  namespace: 'generated-namespace',
  privateKey: 'generated-private-key',
  publicKey: 'generated-public-key'
} */
```

## Error Handling

| **Error**                  | **Reason**                          | **HTTP Status** |
|----------------------------|----------------------------------|--------------|
| `Invalid public key format` | Public key is missing or invalid | 403 Forbidden |
| `Key 'xyz' not exists`      | Requested key does not exist    | 403 Forbidden |

