# Integration Flow

1. Once the user card number has been captured, it should be included in a request to the `informationRequest` service.

2. The service response is analyzed to provide the following options:
-  If the response contains types of "credits," they must be shown to the user to let them select which one to use.
- If the response contains the "displayInterest" parameter set to true, and after a type of credit is selected, the `interestCalculation` service should be called, and the user should be shown the response; this should be done every time the user changes the type of credit.
- If the response contains the "requireOtp" parameter set to true, the consumption data should be sent to the otpGeneration service, and the 6-digit user code should be obtained to then send it in the OTP validation (`otpValidate`) service; once there is a response, the signature result will be sent under the OTP field in the processing request.

3.  Once the type of credit is obtained (if any), the OTP (if required) calls the processTransaction service, and the response is analyzed; this response may be final (approved, declined, or failed) or pending.In both cases, a value will be returned for internalReference, which should be stored because it will allow you to perform subsequent queries with the `queryTransaction` service.
- If the transaction is approved, it is important to also save the authorization number, which will allow you to perform rollbacks at a later time, if needed.
- Transactions are usually completed in a short time (less than 3 seconds), but if the transaction is left pending, it is important to have a cron job or the like to perform queries every 5 minutes for pending transactions until they are resolved.

# Additional Information

The Placetopay API connection is used in different countries, so the use of elements such as "credits," "displayInterest," "requireOtp,” and "MPIlookUp" will depend on whether the country where you are performing the integration has these mechanisms.