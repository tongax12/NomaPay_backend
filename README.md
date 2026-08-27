```mermaid
erDiagram
  USERS ||--|| WALLETS : tiene
  WALLETS ||--|{ BALANCES : tiene
  WALLETS ||--o{ TRANSACTIONS : envia
  WALLETS ||--o{ TRANSACTIONS : recibe

  USERS {
    serial id PK
    varchar name
    varchar surname
    varchar document_type
    varchar document_number
    varchar email UK
    varchar country
    varchar password_hash
    varchar username UK
    varchar alias UK
    varchar cbu UK
    timestamptz created_at
    boolean is_admin
    boolean is_verified
  }

  WALLETS {
    serial id PK
    integer user_id FK
    timestamptz created_at
  }

  BALANCES {
    serial id PK
    integer wallet_id FK
    enum currency
    numeric amount
    timestamptz updated_at
  }

  TRANSACTIONS {
    serial id PK
    integer sender_wallet_id FK
    integer receiver_wallet_id FK
    enum type
    enum status
    enum currency_origin
    enum currency_destination
    numeric amount
    numeric fee
    numeric final_amount
    numeric exchange_rate
    timestamptz transaction_date
  }
```

## ACLARACIÓN
wallets.user_id sigue teniendo la constraint UNIQUE en el SQL real (eso no cambió), solo que en el diagrama visual lo marco como FK porque Mermaid no admite combinar dos etiquetas en una sola palabra.