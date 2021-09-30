# Transaction Types

# Authorization
An authorization refers to the process executed after the user enters the requested information and it is sent to the network to collect the payment.

# Recurring Payment
It is a recurring charge made by Placetopay for the same amount and at a specific interval (daily, monthly, yearly), per the instruction given in the request.

To use this function, you need to send the required data for this structure in the recurring object of the Recurring Payment structure.

# Pre-Authorization

This type of transaction is used to reserve (checkin) an amount of money on a credit card to subsequently debit it (checkout).Over time, this amount may change (reauthorization) according to the business needs or changes in the services selected by the cardholder. 

## Check-In
The CHECKIN transaction is used to obtain an authorization from the bank (provider).It is defined as: making a debit to a credit/debit card that is used as a security deposit for the use of a good or service.


### Checkin Request

To allow for a fast integration by businesses, this type of request is similar to those described in the `processTransaction` method, with a few differences listed below:

- Check that "action" equals "checkin."
- The payment reference used here will be also used in the subsequent REAUTHORIZATION and CHECKOUT.
- The payment currency used here will be also used in the subsequent REAUTHORIZATION and CHECKOUT.
- Distribution details will not be considered in the request.
- Recurrence details will not be considered in the request.(Recurring charges from a proprietary database.)


IMPORTANT: Pay special attention to the “preAuthorization” node. These are the data to be used with the subsequent REAUTHORIZATION and CHECKOUT operations.This section also shows the general status of the preauthorization process.

## Reauthorization

```json
Does not apply to Puerto Rico
```

REAUTHORIZATION type transactions are used to modify the amount  defined as security deposit, which was previously separated through a CHECKIN type transaction.This performs a new authorization (reauthorization) from the bank (provider).

### Reauthorization Request

You must first make a CHECKIN request to process a REAUTHORIZATION because:

- The payment reference for REAUTHORIZATION cases is overwritten by the original CHECKIN session.
- The payment currency for REAUTHORIZATION cases is overwritten by the original CHECKIN session.
- The data used in the “internalReference” and “authorization” of the request correspond to the data delivered in the CHECKIN response in the "preAuthorization” node.
- It possible to perform n (several) REAUTHORIZATION operations, of course, all of them before the CHECKOUT operation.
- Reauthorizations should always have a value greater than 0.


Keep in mind that after this operation, the preauthorization process is still pending since it is waiting on the CHECKOUT operation.

## Check-out

This formalizes the transaction (purchase/settlement) in the (provider).


### Checkout Request

Per this definition, in Evertec, with this type of transaction:

- A CHECKOUT can be performed without having to do a REAUTHORIZATION.
- The payment reference for CHECKOUT cases is overwritten by the original CHECKIN session.
- The payment currency for CHECKOUT cases is overwritten by the original CHECKIN session.
- The data used in the “internalReference” and “authorization” of the request correspond to the data delivered through the CHECKIN process in the "preAuthorization” node.
- If the value of the CHECKOUT is 0, the preauthorization is cancelled, and the amount reserved in the previous requests is released.

IMPORTANT: Note that the "status," "authorization," and "receipt" of the "preAuthorization" node changes during a successful CHECKOUT; therefore, these new values should be used in the REVERSE requests.

## Dispersion

This type of transaction is used to divide the payment between different entities besides the main site. In other words, when processing a transaction, part of the value is directed to the transaction authenticated site, and another part is sent to an airline or other sites. Also, that allows each part of the transaction to be processed by different providers.

The dispersion transaction is made up of a type `DISPERSION` parent session that contains the transaction total value and the process' general status, and also of type` AUTH_ONLY` children sessions that contain the information of each of the parties dispersed. The authorization and receipt data of the parent transaction will be the same as the first transaction processed.

When a transaction is pending, the other pending transactions will not be processed and have a `PENDING_PROCESS` status,until the pending transaction is resolved. The parent transaction will keep its `PENDING` state until all child sessions are resolved.

When a transaction fails or is rejected, the other pending transactions will automatically be rejected as well, without establishing contact with the network. If a child transaction has already been approved before, it maintains its state and the parent transaction will change its state to `APPROVED_PARTIAL`.

Transactions can be reversed together or separately. If a reverse is sent to a child transaction, only the child will be reversed. If it sent to the parent transaction, all transactions will be reversed.

### Airline dispersion
When a dispersal is made to an airline, it will be prioritized and processed first. If 3DS or credit is configured, this setting will only be used to process the airline transaction.

Airline dispersions are limited to just 2 parts: one from the main merchant and the other from the airline. It is also possible to carry out a dispersion in which the transaction total value is redirected to the airline, by not sending the merchant dispersion or sending it with total equals to zero.

### Merchant dispersion
Merchant dispersion allow you to perform a transaction divided into up to 3 sites, including the authenticated one. Child transactions will be processed in the request submitted order.
