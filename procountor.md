# Procountor integration

It would be neat if the guild starts using Procountor that the invoices would be sent automatically to Procountor using their API. To futureproof this implementation, backend API should be structured so that it would be easy to fill in all the information for Procountor.

[See Procountor API reference](https://dev.procountor.com/api-reference/)
[See Procountor Machine-to-Machine authentication](https://dev.procountor.com/m2m-authentication/)

## Invoices

We can use the `POST /api/invoices` to create a new invoice.

Here's example request from Procountor's site:

```json
{
  "type": "SALES_INVOICE",
  "status": "UNFINISHED",
  "date": "2020-10-01",
  "counterParty": {
    "counterPartyAddress": {
      "name": "Buyer name"
    }
  },
  "paymentInfo": {
    "paymentMethod": "BANK_TRANSFER",
    "currency": "EUR",
    "bankAccount": {
      "accountNumber": "FI2799999900031563"
    },
    "dueDate": "2020-10-11",
    "currencyRate": 1
  },
  "extraInfo": {
    "accountingByRow": false,
    "unitPricesIncludeVat": false
  },
  "discountPercent": 0,
  "invoiceRows": [
    {
      "product": "Coffee cup",
      "quantity": 2,
      "unit": "PIECE",
      "unitPrice": 3.5,
      "discountPercent": 0,
      "vatPercent": 24
    }
  ],
  "vatStatus": 1,
  "invoiceChannel": "NO_SENDING",
  "language": "FINNISH"
}
```

Relevant general fields:

| Field           | Value                                                                                           |
| --------------- | ----------------------------------------------------------------------------------------------- |
| type            | Should investigate which one is the right one, `BILL_OF_CHARGES` sounds most promising          |
| date            | Let's just use the date when user submits the form                                              |
| status          | Should be `UNFINISHED`                                                                          |
| discountPercent | 0 obviously                                                                                     |
| invoiceChannel  | `NO_SENDING` sounds best, but double check this                                                 |
| vatStatus       | Might be required based on `type`. Should not be required for expense claims. Check right type! |

We can fill in some of the personal details in counterParty field:

```json
"counterParty": {
  "counterPartyAddress": {
    "name": "Matti Meikäläinen",
    "street": "Otakaari 18 B 123",
    "zip": "00420",
    "city": "Espoo",
  }
}
```

We can fill all invoice rows using invoiceRows field:

```json
"invoiceRows": [
  {
    "product": "Kauppalasku",
    "quantity": 1,
    "unit": "kpl",
    "unitPrice": 50.26,
    "discountPercent": 0,
    "vatPercent": 24
  }
]
```

**vatPercent needs to match with the VAT percents that have been specified for us in Procountor**

We can specify payment details in paymentInfo:

```json
"paymentInfo": {
  "paymentMethod": "BANK_TRANSFER",
  "currency": "EUR",
  "bankAccount": {
    "accountNumber": "FI2799999900031563"
  },
  "dueDate": "2020-12-31",
  "currencyRate": 1
}
```

Questions raised:

- Where to put `subject` and `reasoning` of the expense claim?
- Where to put `email` and `phoneNumber` of the person claiming the expense?
- What should `dueDate` be in paymentInfo?

## Attachments

`POST /api/attachments`

- `multipart/form-data` with metadata as JSON
- max size for images is 10 megabytes
- I guess we have to first create the invoice and then send the attachments?

In metadata, we need following fields:

| Field           | Value                                                                    |
| --------------- | ------------------------------------------------------------------------ |
| name            | filename with correct file extension, e.g. `file.png`                    |
| referenceType   | `INVOICE`                                                                |
| referenceId     | I guess ID of the invoice?                                               |
| sendWithInvoice | Not sure :D I guess we don't send invoices out, so this could be false?  |
