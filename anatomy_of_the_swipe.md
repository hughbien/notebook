#→ Anatomy Of The Swipe

[Anatomy Of The Swipe](https://www.academyoftheswipe.com) by Ahmed Siddiqui.

```mmd
flowchart TD
  A[Customer] -->|swipes card| B[Merchant / POS Terminal]
  B -->|sends transaction data| C[Acquirer / Merchant's Bank]
  C -->|routes via| D[Card Network - Visa/Mastercard/etc.]
  D -->|forwards authorization request| E[Issuer / Customer's Bank]
  E -->|approve/decline| D
  D --> C
  C --> B
  B -->|gives approval or decline| A

  %% Settlement Layer
  E -.settles funds.-> D
  D -.passes funds.-> C
  C -.credits merchant.-> B
```

**Authorization (seconds)**

* Customer swipes/taps -> Merchant -> Acquirer -> Network -> Issuer.
* Issuer checks balance, fraud risk, credit line. Approves or declines.

**Clearing (end of day)**

* Merchants batch transactions to the acquirer.
* Data flows back through the network to issuers.

**Settlement (1-2 days)**

* Issuer sends funds (minus interchange fee) -> Network -> Acquirer -> Merchant.
* Merchant ends up with ~$97-98 on a $100 transaction after fees.

## Key Themes & Insights

**1. The Swipe in Action**

* A "swipe" isn't just a transaction - it's a chain of communication between:
  Cardholder -> Merchant -> Acquirer -> Card Network -> Issuer (bank).
* Each swipe triggers authorization, clearing, and settlement, often in seconds, but
  involving multiple institutions.

**2. The Payment Value Chain**

* Cardholder: Wants convenience and security.
* Merchant: Wants sales but pays fees (interchange, assessment, acquiring).
* Acquirer (merchant's bank): Processes transactions on behalf of the merchant.
* Issuer (consumer's bank): Extends credit or funds and charges fees/interest.
* Networks (Visa, Mastercard, etc.): Provide the rails, charge network fees.

Each takes a cut of the transaction. A $100 swipe might leave the merchant with ~$97-$98 after fees.

**3. Interchange & Fees**

* Interchange fees are the central revenue stream, paid by merchants to issuing banks.
* They vary by card type (premium, rewards, debit) and risk factors.
* Merchants often don't realize how much of their revenue goes to fund card rewards programs.

**4. Fraud & Risk**

* Fraud prevention is built into every swipe: authentication, authorization limits, monitoring.
* Costs of fraud are spread across issuers, networks, and merchants.
* EMV chip adoption reduced counterfeit fraud but shifted fraud patterns (e.g.,
  card-not-present fraud in e-commerce).

**5. Technology Evolution**

* From magstripe -> chip (EMV) -> contactless (NFC) -> mobile wallets (Apple Pay, Google Pay).
* Each step is partly security-driven, partly about user experience.
* Tokenization, encryption, and biometrics now protect sensitive data.

**6. Economic Incentives**

* Consumers are nudged to use credit cards through rewards, even though merchants bear higher fees.
* Banks and networks prefer credit (more profitable) to debit (lower interchange).
* Innovation (e.g., real-time payments) often struggles against entrenched incentives.

**7. Global Variations**

* The U.S. has the highest interchange fees (and the richest card rewards programs).
* Europe has stricter regulation (interchange capped), leading to fewer rewards.
* Emerging markets often leapfrog to mobile payments (e.g., M-Pesa in Kenya, UPI in India).

**8. Future of Payments**

* Real-time payment systems (like FedNow, RTP) could challenge card networks.
* Cryptocurrencies and blockchain may play a role but face adoption/regulation hurdles.
* AI-driven fraud detection, embedded finance, and open banking will reshape incentives.

## Takeaways
 
* Every swipe funds a large ecosystem - banks, networks, processors, fraud systems.
* Merchants pay the most, consumers get rewards, issuers and networks profit.
* Innovation in payments is less about technology and more about shifting incentives, regulation, and trust.
* Understanding the economics helps explain why payment systems evolve slowly despite rapid tech advances.
