Create profile
==================

.. api-name:: Reseller API
   :version: 1

.. endpoint::
   :method: POST
   :url: https://www.mollie.com/api/reseller/v1/create-profile

This method allows you to create a website profile for a merchant.

Parameters
----------
Make sure to add the :ref:`obligatory parameters <secret-keys>` always. Besides that, add the following
parameters:

.. note:: It is not necessary to set ``username`` and ``password`` if you are using ``partner_id_customer``. Otherwise
          both are required to set.

.. list-table::
   :widths: auto

   * - ``username``

       .. type:: string

     - The username of the account you would like to create a profile for.

   * - ``password``

       .. type:: string

     - The password of the account you would like to create a profile for.

   * - ``partner_id_customer``

       .. type:: string

     -  The partner ID of the account you would like to create a profile for. It can be used instead of the parameters
        ``username`` and ``password``.

   * - ``name``

       .. type:: string
          :required: true

     - The name of the website profile.

   * - ``website``

       .. type:: URL
          :required: true

     - The url of the website profile

   * - ``email``

       .. type:: string
          :required: true

     - The e-mail address at which customers can reach the merchant.

   * - ``phone``

       .. type:: string
          :required: true

     - The phone number at which customers can reach the merchant.

   * - ``category``

       .. type:: string
          :required: false

     - The category in which the merchant is active. The value is a merchant category code. Must be one of the following
       values:

       * ``4121`` Travel, rental and transportation
       * ``5192`` Books, magazines and newspapers
       * ``5399`` General merchandise
       * ``5499`` Food and drinks
       * ``5533`` Automotive Products
       * ``5641`` Children Products
       * ``5651`` Clothing & Shoes
       * ``5732`` Electronics, computers and software
       * ``5735`` Entertainment
       * ``5815`` Digital services
       * ``5944`` Jewelry & Accessories
       * ``5977`` Health & Beauty products
       * ``6012`` Financial services
       * ``7299`` Personal services
       * ``7999`` Events, festivals and recreation
       * ``8398`` Charity and donations
       * ``0`` Other

Response
--------
.. code-block:: http
   :linenos:

   HTTP/1.1 200 OK
   Content-Type: application/xml; charset=utf-8

   <?xml version="1.0" encoding="UTF-8"?>
   <response version="v1">
        <success>true</success>
        <resultcode>10</resultcode>
        <resultmessage>Profile created successfully</resultmessage>
        <profile>
            <name>Snoep.nl</name>
            <hash>9C696E36</hash>
            <website>http://snoep.nl/</website>
            <sector>6</sector>
            <category>5399</category>
            <verified>false</verified>
            <phone>0201234567</phone>
            <email>info@snoep.nl</email>
            <api_keys>
                <test>test_ImXWtEB4alZ149cxDrLxr1XDt8kbI9</test>
                <live>live_DjymcBSCZX4MijQ2RKHGTmAvB4J4xw</live>
            </api_keys>
        </profile>
   </response>
