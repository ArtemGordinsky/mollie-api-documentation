Update payment
==============
.. api-name:: Payments API
   :version: 2

.. endpoint::
   :method: PATCH
   :url: https://api.mollie.com/v2/payments/*id*

.. authentication::
   :api_keys: true
   :organization_access_tokens: true
   :oauth: true

This endpoint can be used to update some details of a created payment when it is not yet finalized.

Parameters
----------
.. list-table::
   :widths: auto

   * - ``description``

       .. type:: string
          :required: false

     - The description of the payment. This will be shown to your customer on their card or bank
       statement when possible. We truncate the description automatically according to the limits of the used payment
       method. The description is also visible in any exports you generate.

       We recommend you use a unique identifier so that you can always link the payment to the order in your back
       office. This is particularly useful for bookkeeping.

   * - ``redirectUrl``

       .. type:: string
          :required: false

     - The URL your customer will be redirected to after the payment process.

       It could make sense for the ``redirectUrl`` to contain a unique identifier – like your order ID – so you can show
       the right page referencing the order when your customer returns.

   * - ``webhookUrl``

       .. type:: string
          :required: false

     - Set the webhook URL, where we will send payment status updates to.

   * - ``metadata``

       .. type:: mixed
          :required: false

     - Provide any data you like, for example a string or a JSON object. We will save the data alongside the
       payment. Whenever you fetch the payment with our API, we'll also include the metadata. You can use up to
       approximately 1kB.

.. _payment-method-specific-parameters:

Payment method specific parameters
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
If you specify the ``method`` parameter, optional parameters may be available for the payment method. If no method is
specified, you can still send the optional parameters and we will apply them when the consumer selects the relevant
payment method.

Bank transfer
"""""""""""""
.. list-table::
   :widths: auto

   * - ``dueDate``

       .. type:: string
          :required: false

     - The date the payment should :doc:`expire </payments/status-changes>`, in ``YYYY-MM-DD`` format.
       **Please note:** the minimum date is tomorrow and the maximum date is 100 days after tomorrow.

Access token parameters
^^^^^^^^^^^^^^^^^^^^^^^
If you are using :doc:`organization access tokens </guides/authentication>` or are creating an
:doc:`OAuth app </oauth/overview>`, the only mandatory extra parameter is the ``profileId`` parameter. With it, you can
specify which profile the payment belongs to. Organizations can have multiple profiles for each of their websites. See
:doc:`Profiles API </reference/v2/profiles-api/get-profile>` for more information.

.. list-table::
   :widths: auto

   * - ``profileId``

       .. type:: string
          :required: true

     - The website profile's unique identifier, for example ``pfl_3RkSN1zuPE``.

   * - ``testmode``

       .. type:: boolean
          :required: false

     - Set this to ``true`` to make this payment a test payment.

Response
--------
``201`` ``application/hal+json``

A payment object is returned, as described in :doc:`Get payment </reference/v2/payments-api/get-payment>`.

Example
-------
.. code-block-selector::
   .. code-block:: bash
      :linenos:

      curl -X PATCH https://api.mollie.com/v2/payments/tr_7UhSN1zuXS \
         -H "Authorization: Bearer test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM" \
         -d "description=Order #98765" \
         -d "redirectUrl=https://example.org/webshop/order/98765/" \
         -d "webhookUrl=https://example.org/webshop/payments/webhook/" \
         -d "metadata={\"order_id\": \"98765\"}"

Response
^^^^^^^^
.. code-block:: http
   :linenos:

   HTTP/1.1 200 OK
   Content-Type: application/hal+json

   {
       "resource": "payment",
       "id": "tr_7UhSN1zuXS",
       "mode": "test",
       "createdAt": "2018-03-20T09:13:37+00:00",
       "amount": {
           "value": "10.00",
           "currency": "EUR"
       },
       "description": "Order #98765",
       "method": null,
       "metadata": {
           "order_id": "98765"
       },
       "status": "open",
       "isCancelable": false,
       "expiresAt": "2018-03-20T09:28:37+00:00",
       "details": null,
       "profileId": "pfl_QkEhN94Ba",
       "sequenceType": "oneoff",
       "redirectUrl": "https://example.org/webshop/order/98765/",
       "webhookUrl": "https://example.org/webshop/payments/webhook/",
       "_links": {
           "self": {
               "href": "https://api.mollie.com/v2/payments/tr_7UhSN1zuXS",
               "type": "application/json"
           },
           "checkout": {
               "href": "https://www.mollie.com/payscreen/select-method/7UhSN1zuXS",
               "type": "text/html"
           },
           "documentation": {
               "href": "https://docs.mollie.com/reference/v2/payments-api/update-payment",
               "type": "text/html"
           }
       }
   }
