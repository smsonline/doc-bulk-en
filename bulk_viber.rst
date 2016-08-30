Viber Bulk SMS
==============

**Operating principles**
---------------

System of Viber Bulk SMS supports several types of sending messages:

* **Only text** – on default
* **Only image** – the end-user receives an image
* **Text + button** – the end-user receives a text message with a button under it. When pressing a button, the end-user gets redirected to the stated link. 
* **Text + button + image** – the end-user receives a text message with a picture and a button under it. 
If the end-user doesn’t have a messenger, the system can automatically send SMS to the end-user (this option is connected by a manager). Also there is an option of sending automatic SMS if a message via Viber hasn’t been delivered within a certain period.


**Peculiarities:**

* The way the final message will look is defined automatically according to sent parameters 
* If a parameter has a blank value, then it is considered to be not sent
* If button_text is set, but button_link isn’t set (or vice versa), the button won`t be sent



Sending Viber messages
------------------------

To send messages to the end-user, partners need to send POST or GET request to the server of «Bulk» system on the following address https://bulk.sms-online.com/.

.. |nbsp| unicode:: 0xA0
   :trim:

Text message
^^^^^^^^^^^^^^^^^^^

==================== ======================== ================================================================================
Parameter            Type                     Description
==================== ======================== ================================================================================
**Required parameters**
------------------------------------------------------------------------------------------------------------------------------
txt                  String |nbsp| (2048)     Text of SMS to be sent (default encoding is UTF-8).
{% include _templates/bulk_viber_params.rst %}
==================== ======================== ================================================================================


Image
^^^^^^^^^^^

==================== ======================== ================================================================================
Parameter            Type                     Description
==================== ======================== ================================================================================
**Required parameters**
------------------------------------------------------------------------------------------------------------------------------
image_id             String |nbsp| (64)       ID of the uploaded image. More details about uploading images – ref.
                                              :ref:`uploading media files <ref-media>`
{% include _templates/bulk_viber_params.rst %}
==================== ======================== ================================================================================


Text and button
^^^^^^^^^^^^^^

==================== ======================== ================================================================================
Parameter            Type                     Description
==================== ======================== ================================================================================
**Required parameters**
------------------------------------------------------------------------------------------------------------------------------
txt                  String |nbsp| (2048)     Text of message to be sent in UTF-8 encoding.
button_text          String |nbsp| (30)       Button text
button_link          String |nbsp| (2048)     Button link
{% include _templates/bulk_viber_params.rst %}
==================== ======================== ================================================================================

Text, button and image
^^^^^^^^^^^^^^^^^^^^^^^^^^^

==================== ======================== ================================================================================
Parameter            Type                     Description
==================== ======================== ================================================================================
**Required parameters**
------------------------------------------------------------------------------------------------------------------------------
txt                  String |nbsp| (2048)     Text of message to be sent in UTF-8 encoding.
image_id             String |nbsp| (64)       ID of the uploaded image. More details about uploading images – ref.
                                              :ref:`uploading media files <ref-media>`
button_text          String |nbsp| (30)       Button text
button_link          String |nbsp| (2048)     Button link
{% include _templates/bulk_viber_params.rst %}
==================== ======================== ================================================================================


Digital signature
^^^^^^^^^^^^^^^^
.. include:: includes/bulk_sign.rst


Response codes of Bulk system
------------------------------

Bulk system responds to requests on sending SMS messages in XML.

**Examples of system responses:**

.. code-block:: xml

    <!-- If a request corresponds with the protocol, then the script response will be the following -->
    <?xml version="1.0" encoding="utf-8"?>
    <response>
      <code>0</code>
      <tech_message>OK</tech_message>
    </response>

.. code-block:: xml

    <!-- In case of error, parameter «code» will be less than 0. In this case «tech_message» field contains text description of the error. -->
    <?xml version="1.0" encoding="utf-8"?>
    <response>
      <code>-1</code>
      <tech_message>PARAM ERROR (phone)</tech_message>
    </response>

On default Bulk SMS is limited to 10 SMS/second. If you need higher speed – contact your manager.

Pay attention that error-free response («code=0») of the script doesn’t mean the message will be delivered, it means that the request processing has been accepted

**Decoding system responses:**

===== ============================================ ===============
Code  Description                                  Repeat
===== ============================================ ===============
0     The request has been successfully processed  No
-1    Incorrect input data                         No
-2    Authentication error                         No
-3    The request processing is rejected           No
-4    Temporary technical error                    Yes
-5    You have no more SMS on your account         No
===== ============================================ ===============


Delivery reports
-----------------

The system can send partners information on status delivery of the message (DLR) to the end-user. If request on message sending contains parameter «dlr=1», then response XML, will have not only status but id of sent message as well («msg_id»). There can be more than one id in case messages were sent to several end-users in one request.


**Example of system response with msg_id:**

.. code-block:: xml

    <?xml version="1.0"?>
    <response>
        <tech_message>OK</tech_message>
        <code>0</code>
        <msg_id phone="79031234567">550e8400-e29b-41d4-a716-446655440000</msg_id>
        <msg_id phone="79165557755">550e8400-e29b-41d4-a716-446655440001</msg_id>
    </response>


To get report, partners need:

* To develop script, which receives delivery reports (via HTTPS) in accordance with the protocol described below and send its address to manager of «SMS Online».
* To allow script run from ip-addresses, sent by manager of «SMS Online».
* Inform your manager whether interim statuses should be delivered or only final ones.



Standard delivery reports
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When updating delivery status in Bulk system, partner`s script will receive three parameters:

==================== ===================== ================================================================================
Parameter            Type                  Description
==================== ===================== ================================================================================
msg_id               String |nbsp| (36)    Message id in Bulk system
p_transaction_id     String                Information sent by partner in response to a request of sending a message
status               Number                Status delivery code
==================== ===================== ================================================================================

Delivery statuses can be sent using standard methods POST or GET and protocols  HTTP or HTTPS at partner`s option.

In response to request, partner script should return HTTP response code 200 OK with any text. In case of different HTTP response code, report will be repeated within 24 hours.  

Delivery statuses are sent to partner script, which is stated in Bulk system settings.


**Status delivery codes**

==== ================== ============================================================================
Code Valye              Description                                                 
==== ================== ============================================================================
0    delivered          The message has been successfully delivered
-1   not delivered      The message hasn’t been delivered (delivery error)                
-2   expired            Expired and deleted from SMSC (end-user has been unavailable for 24 hours)     
-3   rejected           The message has been rejected by operator (incorrect number,   
                        end-user has blocked message reception and etc.)
==== ================== ============================================================================

Extended delivery reports
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Bulk system supports delivery of extended statuses for Viber messages.
 
Partner receives the following types of reports:
 
* Report on the message being delivered
* Report on the message being read
* Report on the end-user answering the message
 

**Message delivery report**

==================== ===================== ================================================================================
Parameter            Type                  Description
==================== ===================== ================================================================================
msg_id               String |nbsp| (36)    Message id in Bulk system
p_transaction_id     String                Information sent by partner in response to a request of sending a message
type                 String                Type of report -  delivery
status               String                Possible statuses of the message:

                                           * delivered
                                           * undelivered
                                           * buffered
status_extended      String                Extended reason of delivery failure to the end-user is sent 
                                           when status=undelivered
                                           
                                           Possible values:
                                           
                                           * **VIBER_BLOCKED_BY_USER** – the end-user has blocked the sender`s name
                                           * **VIBER_USER_NOT_FOUND** – Vibe app isn’t installed on the mobile device
                                           * **VIBER_NO_DEVICE** – Viber Bulk messages are not available for this mobile 
                                             device (as a rule it goes for tablets)
                                           * **VIBER_EXPIRED** – DLR wait timeout period is expired
                                             (ref. parameter dlr_timeout)
                                           * **VIBER_UNKNOWN_ERROR** – unknown error (to be specified on request)

                                           *Optional parameter which can be enabled on partner request*
==================== ===================== ================================================================================

**Report on the message being read**

==================== ===================== ================================================================================
Parameter            Type                  Description
==================== ===================== ================================================================================
msg_id               String |nbsp| (36)    Message id in Bulk system
p_transaction_id     String                Information sent by partner in response to a request of sending a message
type                 String                Type of report -  seen
==================== ===================== ================================================================================

**Report on the message being answered**

==================== ===================== ================================================================================
Parameter            Type                  Description
==================== ===================== ================================================================================
msg_id               String |nbsp| (36)    Message id in Bulk system
p_transaction_id     String                Information sent by partner in response to a request of sending a message
type                 String                Type of report – reply
text                 String                Text of end-user response
==================== ===================== ================================================================================

Delivery statuses can be sent using standard methods POST or GET and protocols  HTTP or HTTPS at partner`s option.

In response to request, partner script should return HTTP response code 200 OK with any text. In case of different HTTP response code, report will be repeated within 24 hours.


.. _ref-media:

Uploading of media files
---------------------

File uploading is done by sending the content to the following address with POST-request: : https://media.sms-online.com/upload/ POST-запросом. 

.. note:: Files should be sent in multipart/form-data format.

============ =========== ========== ===============================================================================================
Parameter    Type        Required?  Description
============ =========== ========== ===============================================================================================
login        String      Yes        Partner login
image        File        Yes        Media file.

                                    Acceptable formats: JPG, PNG, GIF.

                                    Maximum size: 10 Mb.
sign         String      Yes        Digital signature is formed on the following algorithm: md5( login + md5(file) + secret_key )
============ =========== ========== ===============================================================================================


In case of successful uploading, you will receive the following response:

.. code::

    {"status": 0, "image_id": "AVESK8...VHUQA8"}

image_id –  is a 64-symbol unique image ID.


In case of error when uploading a file, you will receive the following response:

.. code::

    {"status": 2, error: "Invalid sign"}
    
status – is an error code, error – is a text description of the error.


**Decoding errors**

===== ======================================================================
Code  Description
===== ======================================================================
1     Incorrect data.

      Incorrect data will be specified in the error. 
2     Incorrect signature. 
3     Uploading file exceeds maximum size.
    
      Maximum size – 10 Mb. 
4     Unacceptable format of the file. 

      Acceptable formats: JPG, GIF, PNG.
===== ======================================================================     
