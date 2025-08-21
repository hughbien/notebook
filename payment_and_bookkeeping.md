# Payment And Bookkeeping

Summary of three articles:

* [Journey of an Ecommerce Payment](https://web.archive.org/web/20231010091125/https://paymentshandbook.substack.com/p/journey-of-an-ecommerce-payment-authorization)
* [Payment Processing in a Nutshell](https://web.archive.org/web/20231010074005/https://paymentshandbook.substack.com/p/payment-processing-in-a-nutshell)
* [Double Entry Counting Method](https://beancount.github.io/docs/the_double_entry_counting_method.html)

# Journey of an Ecommerce Payment

The payment flow is divided into 2 main operations:

* authorization - happens at order time. Business checks with customer's bank if customer has enough
  to pay for the order. Payment info goes between multiple parties to verify customer presented
  valid card, it is not fraudulent, and they have enough money. An order will be placed and an
  authorization hold on customer's account occurs (money still in their account but they can't use it).
  Authorization has an expiration time, it needs to be captured before then.
* capture - shipment is ready, business asks for money movement from bank when it ships out the order.
  Bank verifies that request is received, but money movement doesn't occur until settlement. Settlement
  is when money is actually moved and usually happens as a batch process at the end of the day for
  the bank.

Parties involved:

* customer/cardholder
* ecommerce store (merchant)
* payment service provider (payment processor), eg Stripe/Paypal
* business's bank (acquirer, acquiring bank, merchant acquirer)
* card network (card scheme), eg Visa/Mastercard/Amex - this is the infrastructure between acquirer/issuer
  and is often referred to as payment rails
* customer's bank (issuer, issuing bank)

```mmd
sequenceDiagram
  participant Customer
  participant Merchant
  participant PSP as Payment Service Provider (PSP)
  participant Acquirer
  participant CardNetwork as Card Network
  participant Issuer

  Customer->>Merchant: 1. Selects Products and Initiates Payment
  Merchant->>PSP: 2. Sends Customer and Cart Details
  PSP->>Acquirer: 3. Sends Authorization Request
  Acquirer->>CardNetwork: 4. Forwards Authorization Request
  CardNetwork->>Issuer: 5. Routes Authorization Request
  Issuer-->>CardNetwork: 6. Sends Authorization Response
  CardNetwork-->>Acquirer: 7. Forwards Authorization Response
  Acquirer-->>PSP: 8. Sends Authorization Response
  PSP-->>Merchant: 9. Sends Authorization Response
  Merchant-->>Customer: 10. Confirms Order Details
```

Capturing can happen in three ways:

* separate authorization/capture, requires an explicit capture request, more complex but allows
  business to perform fulfillment checks
* automatic capture, simple but no time to perform additional checks
* delayed capture, automatic trigger after time interval

Once the capture occurs, the money is gone, and returning requires a more expensive refund process.
Selling digital products would prefer automatic capture, while physical products might prefer
a separate authorization/capture.

# Payment Processing in a Nutshell

* authorization - acquirer asks issuer if customer has enough funds and runs fraud risk check
* capture - request from merchant to move funds from customer account to merchant's bank account
* cancel (authorization release/voiding) - can happen before capture to return money
* refund - can only happen after capture, more complex/costly operation than cancel
* preauthorization - type of authorization that allows for changing authorization amount, useful
  for services that involve tips like restaurants or Uber/Lyft
* payment service provider (PSP) - company that accepts/processes payments on behalf of merchant,
  integrates with merchant platform with single API, and has features like fraud protection and
  PCI compliance
* payment gateway (& payment processor) - service initiating requests, collects payment information.
  The payment processor is responsible for processing payment which involves sending it to
  other participants. The PSP is the payment gateway and processor combined.

```mmd
sequenceDiagram
  participant Customer
  participant Checkout as Merchant Checkout (browser side)
  participant Backend as Merchant Backend
  participant Gateway as Payment Gateway
  participant Processor as Payment Processor

  Customer->>Checkout: 1. Initiates checkout
  Checkout->>Gateway: 2. Initialize (gateway SDK)
  Gateway-->>Checkout: 3. Load payment form
  Customer->>Gateway: 4. Enter payment information
  Gateway->>Processor: 5. Send payment info securely
  Processor->>Processor: 6. Process payment
  Processor-->>Gateway: 7. Send back payment response
  Gateway-->>Checkout: 8. Send back payment response (gateway SDK)
  Checkout->>Backend: 9. Update order status
  Backend-->>Customer: 10. Checkout complete
  Gateway-->>Backend: 11. Server-side webhook with payment status update
```

* alternative payment methods - methods like wallets (Apple/Google Pay), BNPL (Buy Now Pay Later)
* processor tokenization (PCI/vault tokenization) - service offered by PSPs to exchange sensitive
  information for tokens for security and customer convenience (eg customers can save their card
  info and re-use later without re-entering)

# Double Entry Counting Method

**Accounts & Postings**

An account is conceptualized as a container (like a bag) that holds a quantitative balance (e.g.,
dollars), evolving over time via postings-additions or subtractions. Postings can be positive or
negative; balances may even go negative (e.g., overdrafts).

**Transactions & Balance Rule**

A transaction groups multiple postings under a common date and description. The fundamental rule:
the sum of all postings in a transaction must equal zero—this is the bedrock of double-entry
bookkeeping.

**Handling Multiple Accounts**

Transactions typically involve two or more accounts (e.g., expense vs. credit card). Negative
balances (like on credit card accounts) are intentional and meaningful, as they reflect amounts owed
or other contextual viewpoints.

**Multi-Leg Transactions (e.g., Payroll)**

Complex transactions (e.g., salary receipts with tax withholdings) can involve multiple
postings, still adhering to the "sum zero" rule. Negative postings here may represent the owner
"giving away" something (like labor represented as salary).
