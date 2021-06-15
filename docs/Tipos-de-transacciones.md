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
