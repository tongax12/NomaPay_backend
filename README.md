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


## ACLARACIÓN
wallets.user_id sigue teniendo la constraint UNIQUE en el SQL real (eso no cambió), solo que en el diagrama visual lo marco como FK porque Mermaid no admite combinar dos etiquetas en una sola palabra.