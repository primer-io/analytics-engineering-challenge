# Analytics Engineer Technical Challenge

The challenge for Analytics Engineer is comprised of two parts

1. A presentation on the vision for BI at Primer 
2. A data modelling and SQL technical exercise

## Presentation

1. 10 minute presentation on the vision for BI - this can be a slide deck, bullet points, or a document. You should address these questions along with anything else you think is relevant.
    - How do you set, monitor, and evaluate KPIs?
    - How do you ensure data is accurate? What do you do if you find inaccurate data?
    - How do people learn about our data/tables/reports? How would you keep data definitions updated and ensure that everyone in Primer is using the same definition?
    - Would we have a self-serve data strategy? If so, what would it look like?
    - What is the purpose of BI within a company?
    - How are insights/reports communicated to the business?

## Technical

2. We have provided 4 csv files - each file represents a table in a database. Your task is to load the files into a database, create 1 table that can be used to answer the questions below, and then write the SQL to answer the questions. In total, you should submit 5 SQL queries.
    - You can use any database that supports SQL that you'd like - I'd suggest [DuckDB](https://duckdb.org/docs/installation/) or [SQLite](https://www.sqlite.org/index.html) to keep it simple.
    - Beneath the questions is a description for each table

### Questions

1. What is the authorization rate per processor?
    
    **Authorisation Rate = Count of successfully authorised payments ÷ Count of all payments where status ≠ PENDING**
    
    A payment is considered successfully authorised if the status is one of 
    
    - AUTHORIZED
    - CANCELLED
    - SETTLING
    - SETTLED
    - PARTIALLY_SETTLED
    
    A payment is not authorised if the status is 
    
    - DECLINED
    - FAILED
    
    Payments with a status of PENDING should not be included in the Authorisation or Decline Rate as they are pending a authorisation response
    
2. What is the mean number of successfully authorized payments per processor per day?
3. Which network has the most declined payments?
4. Using company_name to identify merchants, which merchants provide values for metadata?

### Table Descriptions

**PAYMENT_DATA**

| Column | Type | Nullable | Description |
| --- | --- | --- | --- |
| PAYMENT_ID | VARCHAR | FALSE | unique id for each payment |
| CURRENCY_CODE | VARCHAR | FALSE | 3 digit currency code the payment was made in |
| CUSTOMER_DETAILS | OBJECT | TRUE | contains billing and shipping information for a customer |
| STATEMENT_DESCRIPTOR | VARCHAR | TRUE | Description of the what the payment is for provided by a merchant |
| CREATED_AT | TIMESTAMP | FALSE | Time when payment was created |
| UPDATED_AT | TIMESTAMP | FALSE | Time when payment was last updated |
| TOKEN_ID | VARCHAR | FALSE | One-time use token that authenticates merchants and enables them to make payments |
| VAULTED_TOKEN_ID | VARCHAR | TRUE | Multi-use token that authenticates merchants and enables them to make payments |
| MERCHANT_PAYMENT_ID | VARCHAR | FALSE | Shortened payment_id used to display in the customer dashboard - there is a 1-1 mapping between payment_id and merchant_payment_id |
| PRIMER_ACCOUNT_ID | VARCHAR | FALSE | Unique id for each merchant |
| AMOUNT | INTEGER | FALSE | Payment amount in minor units (e.g. 100 USD would be $1) |
| STATUS | VARCHAR | FALSE | The status of the payment - AUTHORIZED / CANCELLED / DECLINED / FAILED / PARTIALLY_SETTLED / PENDING / SETTLED |
| PROCESSOR_MERCHANT_ID | VARCHAR | FALSE | Unique id of the merchant's processor that processes the payment |
| PROCESSOR | VARCHAR | FALSE | Common name of the payment processor that processed the payment |
| AMOUNT_CAPTURED | INTEGER | FALSE | Amount in lowest form that was captured |
| AMOUNT_AUTHORIZED | INTEGER | FALSE | Amount in lowest form that was authorized |
| AMOUNT_REFUNDED | INTEGER | FALSE | Amount in lowest form that was refunded |

**PAYMENT_INSTRUMENT_TOKEN_DATA**

| Column | Type | Nullable | Description |
| --- | --- | --- | --- |
| TOKEN_ID | VARCHAR | FALSE | Token that authenticates merchants and enables them to make payments. Can be joined to payment_data using  payment_data.token_id  |
| TOKEN_TYPE | VARCHAR | FALSE | Single Use or Multi Use |
| THREE_D_SECURE_AUTHENTICATION | OBJECT | TRUE | 3ds secure data; present if 3ds challenge was presented |
| PAYMENT_INSTRUMENT_TYPE | VARCHAR | FALSE | Type of instrument used to make the payment; typcially PAYMENT_CARD |
| NETWORK | VARCHAR | FALSE | The payment instrument network (VISA/AMEX/MASTERCARD/etc) |

**PAYMENT_REQUEST_DATA**

| Column | Type | Nullable | Description |
| --- | --- | --- | --- |
| PAYMENT_REQUEST_ID | VARCHAR | FALSE | Unique id for the payment request |
| CURRENCY_CODE | VARCHAR | FALSE | 3 digit currency code the payment was made in |
| CREATED_AT | TIMESTAMP | FALSE | Timestamp when the payment request was made |
| PAYMENT_REQUEST_TYPE | VARCHAR | FALSE | PAYMENT_REFUND_REQUEST / PAYMENT_CAPTURE_REQUEST / PAYMENT_CREATION_REQUEST / PAYMENT_CANCEL_REQUEST |
| PAYMENT_INSTRUMENT_VAULT_INTENTION | VARCHAR | TRUE | Should the payment instrument be vaulted |
| PAYMENT_ID | VARCHAR | FALSE | Unique id for the payment - can be joined to payment_data using payment_data.payment_id |
| PRIMER_ACCOUNT_ID | VARCHAR | FALSE | Unique id for each merchant |
| METADATA | OBJECT | TRUE | Arbitrary data that merchants can provide about their payments |
| PAYMENT_INSTRUMENT_TOKEN_ID | VARCHAR | FALSE | Token_id linked to the payment_instrument - can be joined to payment_instrument_token_data using payment_instrument_token_data.token_id |
| AMOUNT | NUMBER | FALSE | Payment amount in minor units (e.g. 100 USD would be $1) |
| MERCHANT_REQUEST_ID | VARCHAR | FALSE | Unique id for the request made from the merchant |

**PRIMER_ACCOUNT**

| Column | Type | Nullable | Description |
| --- | --- | --- | --- |
| PRIMER_ACCOUNT_ID | VARCHAR | FALSE | Unique id for each merchant |
| COMPANY_NAME | VARCHAR | FALSE | Name of each merchant |
| CREATED_AT | TIMESTAMP | FALSE | Timestamp when the account was created for the merchant |
