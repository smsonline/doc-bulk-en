Examples of operating with Bulk system 
======================================


Examples of operating with API (for Perl and PHP) you can find at github.com:  https://github.com/smsonline/samples/tree/master/Bulk

Example of GET-request:

.. code-block:: none

    https://bulk.sms-online.com/?user=hitfm&from=HitFM&phone=79031234567&txt=%D0%A1%D1%8A%D0%B5%D1%88%D1%8C%20%D0%B5%D1%89%D0%B5%20%D1%8D%D1%82%D0%B8%D1%85%20%D1%84%D1%80%D0%B0%D0%BD%D1%86%D1%83%D0%B7%D1%81%D0%BA%D0%B8%D1%85%20%D0%B1%D1%83%D0%BB%D0%BE%D0%BA&sign=29def94e0f987855bbf4407e93b088d7

When sending parameter txt with special characters (including «space»), it should be URI encoded using the function rawurlencode() or similar. 

In the abovementioned example parameter «txt» contains a line «S`esh eshe etih frantsuzkih bulok».

If the request corresponds to the protocol, then script response will be the following:

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <response>
      <code>0</code>
      <tech_message>OK</tech_message>
    </response>

In case of error, parameter «code» will be less than zero (ref. p. 7.1). In this event, field «tech_message» contains text description of the error. Example:

.. code-block:: xml

    <?xml version="1.0" encoding="utf-8"?>
    <response>
      <code>-1</code>
      <tech_message>PARAM ERROR (phone)</tech_message>
    </response>

On default Bulk SMS is limited to 10 SMS/second. If you need higher speed – contact your manager.