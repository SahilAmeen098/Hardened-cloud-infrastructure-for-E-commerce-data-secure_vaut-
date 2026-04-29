# 🔐 SecureVault — E-Commerce File Encryption Platform

A Django-based secure file storage system implementing **AES-256-GCM** encryption
for e-commerce sensitive data protection (invoices, customer data, payment info, contracts).

---

## 🛡️ Security Features

| Feature | Details |
|---|---|
| Encryption Algorithm | AES-256-GCM (authenticated encryption) |
| Key Derivation | PBKDF2-HMAC-SHA256, 480,000 iterations |
| Salt | 256-bit cryptographically random per file |
| Nonce | 96-bit unique per encryption operation |
| Integrity | GCM authentication tag detects tampering |
| Audit Trail | Every action timestamped and logged |

### Why AES-256-GCM?
- **Confidentiality**: 256-bit symmetric encryption — computationally infeasible to brute-force
- **Integrity**: The GCM authentication tag will fail instantly if the file is tampered with or the wrong password is used
- **No Password Storage**: Passwords are never saved. They are used only to derive the AES key via PBKDF2


```
http://127.0.0.1:8000/
```

---

## 📁 Project Structure

```
securevault/
├── manage.py
├── requirements.txt
├── README.md
├── securevault/          # Django project config
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── vault/                # Main application
    ├── encryption.py     # ← AES-256-GCM core module
    ├── models.py         # EncryptedFile, AuditLog
    ├── views.py          # Upload, Download, Dashboard
    ├── forms.py          # Django forms
    ├── urls.py
    └── templates/vault/
        ├── base.html
        ├── login.html
        ├── register.html
        ├── dashboard.html
        ├── upload.html
        ├── download.html
        ├── audit_log.html
        └── confirm_delete.html
```

---

## 🔬 Encryption Module (`vault/encryption.py`)

The core encryption logic uses Python's `cryptography` library:

```python
# Key derivation
PBKDF2HMAC(algorithm=SHA256, length=32, salt=salt, iterations=480000)

# Encryption
aesgcm = AESGCM(key)
ciphertext = aesgcm.encrypt(nonce, plaintext, None)

# Decryption (raises InvalidTag if wrong password/tampered)
plaintext = aesgcm.decrypt(nonce, ciphertext, None)
```

### Encrypted File Format (.vault)
```
[MAGIC: 8 bytes] [SALT: 32 bytes] [NONCE: 12 bytes] [CIPHERTEXT: N bytes]
```

---

## 📋 Pages

| URL | Description |
|---|---|
| `/register/` | Create a new secure vault account |
| `/login/` | Authenticate and access your vault |
| `/dashboard/` | View all encrypted files + stats |
| `/upload/` | Encrypt and store a new file |
| `/download/<id>/` | Decrypt and download a file |
| `/delete/<id>/` | Permanently delete a file |
| `/audit/` | View complete security audit trail |

---

## ⚠️ Important Notes

1. **Password Recovery**: Encryption passwords cannot be recovered. If lost, the file is permanently inaccessible.
2. **Production**: Change `SECRET_KEY` in `settings.py` and set `DEBUG=False`
3. **HTTPS**: Always use HTTPS in production to protect passwords in transit
4. **Storage**: For production, use AWS S3 or equivalent for `MEDIA_ROOT`

---

## 🧪 Technologies Used

- **Django 4.2** — Web framework
- **Python `cryptography` library** — AES-256-GCM, PBKDF2
- **SQLite** — Database (swap for PostgreSQL in production)
- **HTML/CSS** — Custom dark industrial UI with Space Mono + Syne fonts

