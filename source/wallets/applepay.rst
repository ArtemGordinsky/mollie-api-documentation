Apple Pay
=========

**Apple Pay** is a digital wallet service by Apple Inc. that allows shoppers to make purchases with credit or debit cards
stored on their Apple devices.

.. note:: Apple Pay is only available in a `limited number of countries
          <https://www.apple.com/ios/feature-availability/#apple-pay>`_. This limitation applies to the shopper's
          country and not the merchant's.

Accepting Apple Pay via Mollie
------------------------------

In order to accept Apple Pay via Mollie, you need:

#. The payment method `credit card <https://www.mollie.com/creditcard>`_ must be enabled on your website profile;
#. You must enable Apple Pay on your website profile in the `Dashboard <https://www.mollie.com/dashboard>`_.

Shoppers using an Apple device with Apple Pay configured will automatically be offered Apple Pay in the :doc:`Mollie
Checkout </guides/checkout>`. Additionally, limited support for integrating Apple Pay in your own checkout is available.

Using the Mollie Checkout
-------------------------

If you are using the Mollie Checkout and the :doc:`Create Payment API </reference/v2/payments-api/create-payment>`
without the ``method`` parameter, the Apple Pay button will be shown in the checkout during method selection:

.. figure:: images/checkout-with-apple-pay@2x.jpg

Adding Apple Pay to the method selection
----------------------------------------

At the moment, only limited support for integrating Apple Pay in your own checkout is available. You can not yet add the
"Checkout with Pay" button to your own checkout, but you can add Apple Pay to the method selection. The "Checkout
with Pay" button will then be shown in the Mollie Checkout.

First, you must indicate to the :doc:`List Methods API </reference/v2/methods-api/list-methods>` which wallets you
support in your checkout by adding the ``includeWallets=applepay`` parameter.

.. code-block:: bash
   :linenos:

   curl -X GET https://api.mollie.com/v2/methods?includeWallets=applepay \
      -H "Authorization: Bearer test_dHar4XY7LxsDOtmnkVtjNVWXLSlXsM"

If Apple Pay is enabled on the website profile, a method with the id ``applepay`` will be returned in the list of
payment methods. Apple Pay can be enabled in the `Dashboard <https://www.mollie.com/dashboard>`_.

Then, during checkout, you should check if the Apple Pay method is available on the shopper's device by using the
``canMakePayments`` method on the ``window.ApplePaySession`` object. Apple requires you that you only show the Apple Pay
option when it is actually supported by the device. For more details, see the `article by Apple
<https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api/checking_for_apple_pay_availability>`_.

.. code-block:: javascript
   :linenos:

   if (window.ApplePaySession && window.ApplePaySession.canMakePayments()) {
     // Add ApplePay to the method selection in your checkout
   }

Finally, when the shopper selects Apple Pay from your method selection, you should use the Create Payment API to create
a payment with the ``method`` parameter set to ``applepay`` and redirect the shopper to the ``_links.checkout`` URL
returned.

When the shopper authorizes the payment, Mollie will create a payment with the method ``creditcard``. We will then call
your :doc:`webhook </guides/webhooks>` and redirect the shopper back to your website as normal.

Examples
^^^^^^^^

.. _adding-apple-pay-client-side:

Option 1. Adding Apple Pay client-side with JavaScript
""""""""""""""""""""""""""""""""""""""""""""""""""""""

When using a ``<select>`` dropdown or equivalent, we recommend handling adding Apple Pay to your checkout entirely with
JavaScript. This prevents you from having to edit your HTML or CSS.

If you need a more control or have complex HTML, see :ref:`Adding Apple Pay server-side <adding-apple-pay-server-side>`.

.. code-block:: html
   :linenos:

   <form>
     <!-- Other checkout fields like billing address etc. -->

     <select class="js-select-method">
       <option value="creditcard">Credit card</option>
       <option value="ideal">iDEAL</option>
       <option value="paypal">PayPal</option>
       <option value="banktransfer">Bank transfer</option>
     </select>
   </form>

   <!-- 
   Only load this script if Apple Pay is enabled on your Mollie profile. 
   For example with PHP:

   <?php if ($applePayEnabled) : ?>
   -->
   <script>
      if (!window.ApplePaySession || !ApplePaySession.canMakePayments()) {
        // Apple Pay is not available
        return;
      }

      // Create Apple Pay option
      const option = document.createElement('option');
      option.value = 'applepay';
      option.textContent = 'Apple Pay';

      // Find the payment method dropdown in the document, this depends on your HTML
      const select = document.querySelector('.js-select-method');

      // Add Apple Pay to dropdown
      select.prepend(option);
   </script>
   <!--
   <?php endif; ?>
   -->

For more details on detecting the support of Apple Pay on a device, see `Apple’s documentation
<https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api/checking_for_apple_pay_availability>`_.


.. _adding-apple-pay-server-side:

Option 2. Adding Apple Pay server-side
""""""""""""""""""""""""""""""""""""""

If you use a more complex HTML structure, or you need data which is only available server-side, your
best option is to add Apple Pay to the DOM and hide it both visually as well as from screen-readers.

.. code-block:: html
   :linenos:

   <html>
     <head>
       <style>
         /* You should probably add this block to an existing stylesheet instead of inlining it. */
         .payment-methods > [aria-hidden="true"] {
           display: none;
         }
       </style>
     </head>
     <body>
       <form>
         <!-- Other checkout fields like billing address etc. -->

         <ul class="payment-methods">
           <!-- 
           Only show this method if Apple Pay is enabled on your Mollie profile. 
           For example with PHP:

           <?php if ($applePayEnabled) : ?>
           -->
           <li class="js-apple-pay" aria-hidden="true">
             <label>
               <input name="method" value="applepay" type="radio"> 
               Apple Pay
             </label>
           </li>
           <!--
           <?php endif; ?>
           -->
           <li>
             <label>
               <input name="method" value="creditcard" type="radio"> 
               Credit card
             </label>
           </li>
           ...
         </ul>
       </form>

       <!-- 
       Only load this script if Apple Pay is enabled on your Mollie profile. 
       For example with PHP:

       <?php if ($applePayEnabled) : ?>
       -->
       <script>
           if (!window.ApplePaySession || !ApplePaySession.canMakePayments()) {
             // Apple Pay is not available
             return;
           }

           // Find the hidden Apple Pay element
           const applePay = document.querySelector('.js-apple-pay');

           // Remove the aria-hidden attribute
           applePay.removeAttribute('aria-hidden');
       </script>
       <!--
       <?php endif; ?>
       -->
     </body>
   </html>


For more details on detecting the support of Apple Pay on a device, see `Apple’s documentation
<https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api/checking_for_apple_pay_availability>`_.

Direct integration of Apple Pay
-------------------------------

Additionally, you can `display <https://developer.apple.com/documentation/apple_pay_on_the_web/displaying_apple_pay_buttons>`_
the "Checkout with Pay" button to your own checkout as you deem fit. Apple provides some `guidelines
<https://developer.apple.com/design/human-interface-guidelines/apple-pay/overview/introduction/>`_ on how you should do this.

In this case, you are responsible for implementing `Apple's JS APIs <https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api>`_.
Mollie will provide API endpoints needed by the JS APIs and the ability to send the encrypted Apple Pay Payment Token
when creating a payment.

#. Place the `domain validation file <http://www.mollie.com/.well-known/apple-developer-merchantid-domain-association>`_
   on your server at: ``https://[domain]/.well-known/apple-developer-merchantid-domain-association``.

#. Check if Apple Pay is `available on the device <https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api/checking_for_apple_pay_availability>`_
   using the `canMakePayments <https://developer.apple.com/documentation/apple_pay_on_the_web/applepaysession/1778027-canmakepayments>`_ method.

#. Create an `Apple Pay Session <https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api/creating_an_apple_pay_session>`_.

#. `Provide merchant validation <https://developer.apple.com/documentation/apple_pay_on_the_web/apple_pay_js_api/providing_merchant_validation>`_.
   Instead of using Apple's API, you can use a :doc:`dedicated endpoint in the  Mollie API </reference/v2/miscellaneous/request-apple-pay-payment-session>`.

#. One the shopper has authorized the payment, you will receive the `Apple Pay Payment object <https://developer.apple.com/documentation/apple_pay_on_the_web/applepaypayment>`_.
   You must then add the `token <https://developer.apple.com/documentation/apple_pay_on_the_web/applepaypaymenttoken>`_
   when invoking the :doc:`Create Payment API </reference/v1/payments-api/create-payment>`.
