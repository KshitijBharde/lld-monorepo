# Problem Statement:

Design a ledger system for tracking mutual funds and cash balances.

The system should support the following functionalities:

- Record transactions related to mutual funds (e.g., buy, sell) and cash (e.g., deposit, withdraw).
- Maintain a balance for each mutual fund and cash account.
- Provide a history of transactions for auditing purposes.
- Handle concurrent transactions and ensure data consistency.
- Implement basic error handling and logging



## User flow:

- User logs in to the app (assumed to be handled already).

- User has to complete the KYC using aadhaar or any other id proof (skip for now)

- User needs to add a primary bank account and verify it (skip for now)

- User views the total current value of his investments and the cash amount in the wallet

- User can view the break up of all the investments done with the total value invested and the todays current value and P/L value, percentage

- On clicking on a particular investment user can see the transactions history with the invested value and the units

- User can view the wallet transactions history(credit and debit)

- User can credit and debit money from the wallet using different payment options

- User can invest in an instrument using the wallet balance

- User can sell units of the instruments to get the amount in the wallet



### Objects from the user flow:

- User (has a wallet,  has many investments, has many walletTransactions, has many instrumentTransactions)
- Wallet (belongs to a user, has many walletTransactions)
- Instruments (has many investments)
- Investments (belongs to a user, has many InstrumentTransactions)
- WalletTransaction (belongs to a wallet)
- InstrumentTransactions (belongs to a investment)


### APIs

- Get total balances (Investment + Wallet)

    Endpoint: GET /api/v1/users/:userId/balance

    Response:
    ```
    {
        "walletBalance": 5000,
        "totalInvestmentValue": 15000,
        "totalPL": 2000,
        "totalPLPercentage": 15.38
    }
    ```

- Get Investments breakdown

    Endpoint: GET /api/v1/users/:userId/investments

    Response:
    ```
    [
        {
            "mutualFundId": "UUID",
            "mutualFundName": "Fund A",
            "units": 100,
            "investedAmount": 10000,
            "currentValue": 12000,
            "pl": 2000,
            "plPercentage": 20
        },
        ...
    ]
    ```

- Get Investment Transaction History

    Endpoint: GET /api/v1/users/:userId/investments/:instrumentId/transactions

    Response:
    ```
    [
        {
            "id": "UUID",
            "type": "BUY",
            "amount": 5000,
            "units": 50,
            "status": "COMPLETED"
            "metaData": "{ "fundHouseTransId": "XYZ" }"
            "createdAt": "TIMESTAMP"
        }
    ]
    ```

- Get Wallet Transaction History

    Endpoint: GET /api/v1/users/:userId/walletTransactions

    Response:
    ```
    [
        {
            "id": "UUID",
            "type": "DEPOSIT",
            "amount": 1000,
            "walletBalanceAfter": 5000,
            "createdAt": "2024-08-23T12:34:56Z"
        }
    ]
    ```

- Credit Money to Wallet

    Endpoint: POST /api/v1/users/:userId/wallet/credit

    Request:
    ```
    {
        "amount": 1000,
        "paymentMethod": "UPI"
    }
    ```

    Response:
    ```
    {
        "transactionId": "UUID",
        "walletBalanceAfter": 6000
    }
    ```

- Withdraw Money from Wallet to bank account

    Endpoint: POST /api/v1/users/:userId/wallet/debit

    Request:
    ```
    {
        "amount": 500
    }
    ```

    Response:
    ```
    {
        "transactionId": "UUID",
        "walletBalanceAfter": 5500
    }
    ```

- Invest in Mutual Fund

    Endpoint: POST /api/v1/users/:userId/investments/:instrumentId/buy

    Request:
    ```
    {
        "currentValue": 10
        "units": 100
        "amount": 1000
    }
    ```

    Response:
    ```
    {
        "transactionId": "UUID",
        "unitsPurchased": 100,
        "walletBalanceAfter": 4500
    }
    ```

- Sell Mutual fund units

    Endpoint: POST /api/v1/users/:userId/investments/:instrumentId/sell

    Request:
    ```
    {
        "units": 5
    }
    ```

    Response:
    ```
    {
        "transactionId": "UUID",
        "amountToBeCredited": 500
    }
    ```