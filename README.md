# Cripto

A personal Python cryptography library implementing a symmetric block cipher based on the **Feistel Network** and using the secure **Cipher Block Chaining (CBC) mode** for operation.

This library is designed for developers who want to experiment with custom cryptographic implementations or require column-level encryption/decryption within their personal applications.

## 🌟 Features

* **Custom Block Cipher:** Implementation of the Feistel Network (8 rounds) from scratch.
* **CBC Mode:** Uses Cipher Block Chaining (CBC) for data security, which provides diffusion and addresses the weaknesses of ECB (Electronic Codebook) mode.
* **PKCS#7 Padding:** Ensures data integrity by correctly padding and unpadding input data to match the required block size (64 bits / 8 bytes).
* **Key Management Tools:** Includes utilities to securely generate and load the 256-bit Key (KEY) and the Initialization Vector (IV).
* **Modular Design:** Optimized for reuse within Python applications (e.g., protecting passwords or tokens in a SQLite database column).

## 📦 Installation

This package is designed to be easily installed via pip once published, or installed locally for testing.

### Local Installation (Developer Setup)

If you have the source code with the `setup.py` file:

```bash
# Navigate to the project root directory (where setup.py is located)
pip install .
```

# 🛠️ Usage

## 1. Generating Key Material
Before encryption, you must securely generate a master key file (`clave_secreta.txt` is the default).

You can use the built-in generation function:

```python
import cripto
import os

KEY_FILE = "master_key.txt"

# Generates the KEY (256-bit) and IV (64-bit) and saves them.
if not os.path.exists(KEY_FILE):
    cripto.generar_componentes_criptograficos(KEY_FILE)
    print(f"Key material saved to {KEY_FILE}")

# Load the generated material for use in the application
KEY, IV = cripto.cargar_componentes_clave(KEY_FILE)
```

## 2. Encryption and Decryption (Core Functions)
The primary functions operate on `bytes` objects.
```python
import cripto

# Assume KEY and IV are loaded as bytes objects
plaintext = "The data to be hidden.".encode('utf-8')

# --- ENCRYPTION ---
try:
    ciphertext = cripto.cifrar_cbc(plaintext, KEY, IV)
    print(f"Ciphertext (Hex): {ciphertext.hex()}")
except ValueError as e:
    print(f"Encryption Failed: {e}")

# --- DECRYPTION ---
try:
    decrypted_bytes = cripto.descifrar_cbc(ciphertext, KEY, IV)
    decrypted_text = decrypted_bytes.decode('utf-8')
    
    print(f"Decrypted: {decrypted_text}")
    assert decrypted_text == plaintext.decode('utf-8')
except ValueError as e:
    print(f"Decryption Failed (Wrong key or corrupt data): {e}")
```

## 3. Application Use Case: Database Column Encryption
This is ideal for securing sensitive data columns (like passwords, tokens, or personal identifiers) in a database like SQLite or PostgreSQL.

### Logic Flow:
1. **On Write/Insert:** The application calls `cifrar_cbc()` on the plaintext data. The resulting `bytes` object (BLOB) is stored in the database column.

2. **On Read/Retrieve:** The application reads the BLOB from the database and calls `descifrar_cbc()`. The resulting plaintext is used for validation or display. 

## ⚙️ Technical Details
| Component | Description | Specification |
|--- |--- |---
| **Cipher Type** | Symmetric Block Cipher | Feistel Network |
| **Mode of Operation** | Block Chaining (CBC) | Provides strong diffusion and hides patterns. |
| **Block Size** | Fixed | 8 bytes (64 bits) |
| **Key Size** | Fixed | 32 bytes (256 bits) |
| **Padding Scheme** | PKCS#7 | Ensures input data length is a multiple of the block size. |
| **Round Function (F)** | Simplified implementation | Includes bitwise XOR and byte rotation. |

## ⚠️ Disclaimer
This library is intended for **educational purposes and personal projects*. While the implementation follows standard cryptographic principles (Feistel, CBC), it has not been rigorously audited by security professionals and should **NOT** be used to protect high-value, sensitive, or regulated data in production environments. Always rely on established libraries like PyCryptodome for professional applications.

## 🤝 Contributing
Feedback and suggestions for improving the code or the simplified F-function are welcome!

## LICENSE: [MIT](https://github.com/Nooch98/cripto/blob/main/LICENSE)
