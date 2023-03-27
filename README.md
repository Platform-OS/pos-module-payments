# Payments

Handle payments via various payment gateways. Payment process is handled by payment provider website where we redirect user.

## Installation

        git clone git@github.com:Platform-OS/pos-module-payments.git modules/payments

## Development

This module delivers:
- transaction model: universal interface for payments. It has transaction status and handler for payable objects.
- gateway_requests model: stores requests sent to payment gateway and recivied from gateway via webhook. Every payment gateway should store outgoing and ingoing communication with their api so we track it.
- pay_url function: for a given payment type generates url where you redirect user.

Payment gateway should:
- implement `pay_url` function that returns url where we redirect user to pay.
- implement `modules/<gateway>/commands/statuses/get`
- fire `modules/payments/commands/transactions/udpate_status` function once the payment status changes.
- fire `modules/payments/commands/gateway_requests/store_request` once communication with their external api happens.
- any necessary page for gateway should be implemented in this namespace `/payments/<gateway_name>/*`

## Usage

Include this module and one of the payment gateways. There is a example payment gateway.

## Events

Defined events to which your application can listen:
- payments_transaction_succeeded
- payments_transaction_failed

## Examples

1. Create transaction `commands/transactions/create`. Read to gateway module docs to check if there are any required params that you have to pass to gateway, such as: buyer name, address, line items.


        assign ids = '["1", "2"]' | parse_json
        assign object = null | hash_merge: gateway: 'payments_example_gateway', payable_ids: ids, amount_cents: 1001, currency: 'USD'
        function object = 'modules/payments/commands/transactions/create', object: object

2. Generate url and redirect user to this url
        
        function url = 'modules/payments/helpers/pay_url', transaction: object

3. Implement consumer that listen on those events:
- payments_transaction_succeeded
- payments_transaction_failed

## Versioning

```
git fetch origin --tags
npm version major | minor | patch
```
