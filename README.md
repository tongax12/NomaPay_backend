```mermaid
erDiagram

    users ||--o| subscriptions : "tiene (1:0..1)"
    users ||--|| wallets : "posee (1:1)"
    wallets ||--o{ balances : "contiene (1:N)"
    currencies ||--o{ balances : "corresponde_a (1:N)"
    currencies ||--o{ wallets : "moneda_preferida (1:N)"
    wallets ||--o{ transactions : "origen (sender_wallet_id)"
    wallets ||--o{ transactions : "destino (receiver_wallet_id)"
    currencies ||--o{ transactions : "moneda_origen (currency_origin)"
    currencies ||--o{ transactions : "moneda_destino (currency_destination)"

    users {
        SERIAL id PK
        VARCHAR_100 name
        VARCHAR_100 surname
        VARCHAR_20 document_type
        VARCHAR_30 document_number
        VARCHAR_255 email UK
        VARCHAR_2 country
        VARCHAR_255 password_hash
        VARCHAR_50 username UK
        VARCHAR_50 alias UK
        VARCHAR_22 cbu UK
        VARCHAR_500 profile_picture_url
        TIMESTAMPTZ password_changed_at
        VARCHAR_255 reset_password_token
        BOOLEAN is_admin
        TIMESTAMPTZ email_verified_at
        kyc_status_type kyc_status
        TIMESTAMPTZ kyc_reviewed_at
        TIMESTAMPTZ created_at
    }

    subscriptions {
        SERIAL id PK
        INTEGER user_id FK,UK
        VARCHAR_50 plan
        VARCHAR_30 status
        TIMESTAMPTZ started_at
        TIMESTAMPTZ expires_at
        TIMESTAMPTZ created_at
    }

    wallets {
        SERIAL id PK
        INTEGER user_id FK,UK
        VARCHAR_10 preferred_currency FK
        TIMESTAMPTZ created_at
    }

    currencies {
        VARCHAR_10 code PK
        VARCHAR_50 name UK
        VARCHAR_5 symbol
        BOOLEAN is_active
        TIMESTAMPTZ created_at
    }

    balances {
        SERIAL id PK
        INTEGER wallet_id FK
        VARCHAR_10 currency_code FK
        NUMERIC_20_8 amount
        TIMESTAMPTZ updated_at
    }

    transactions {
        SERIAL id PK
        INTEGER sender_wallet_id FK
        INTEGER receiver_wallet_id FK
        transaction_type type
        transaction_status status
        VARCHAR_10 currency_origin FK
        VARCHAR_10 currency_destination FK
        NUMERIC_20_8 amount
        NUMERIC_20_8 fee
        NUMERIC_20_8 final_amount
        NUMERIC_20_8 exchange_rate
        TIMESTAMPTZ transaction_date
    }
```

| # | Relación | Cardinalidad | Descripción |
|---|---|---|---|
| 1 | users → wallets | 1 : 1 | Cada usuario tiene exactamente una wallet. |
| 2 | users → subscriptions | 1 : 0..1 | Un usuario puede no tener suscripción o tener como máximo una. |
| 3 | wallets → balances | 1 : N (mín. 1) | Cada wallet tiene uno o más balances, uno por moneda. |
| 4 | wallets → transactions (emisor) | 1 : N (puede ser 0) | Una wallet puede haber originado transacciones. |
| 5 | wallets → transactions (receptor) | 1 : N (puede ser 0) | Una wallet puede haber recibido transacciones. |
| 6 | currencies → balances | 1 : N (puede ser 0) | Una moneda puede estar en varios balances. |
| 7 | currencies → transactions (origen) | 1 : N (puede ser 0) | Una moneda puede ser origen de transacciones. |
| 8 | currencies → transactions (destino) | 1 : N (puede ser 0, nullable) | Nullable porque en transfer no hay conversión. |
| 9 | currencies → wallets (preferida) | 1 : N (puede ser 0) | Una moneda puede ser preferida por varias wallets. |

## ACLARACIÓN
wallets.user_id sigue teniendo la constraint UNIQUE en el SQL real (eso no cambió), solo que en el diagrama visual lo marco como FK porque Mermaid no admite combinar dos etiquetas en una sola palabra.