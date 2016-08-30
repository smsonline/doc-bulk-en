SMS Bulk Service
================

Sending of SMS messages
-----------------------

To send messages to the end-user, partners need to send POST or GET request to the server of «Bulk» system on the following address https://bulk.sms-online.com/.

.. |nbsp| unicode:: 0xA0
   :trim:

==================== ======================== ================================================================================
Parameter            Type                     Description
==================== ======================== ================================================================================
**Required parameters**
------------------------------------------------------------------------------------------------------------------------------
txt                  String |nbsp| (2048)     Text of SMS to be sent (default encoding is UTF-8).
                                              
                                              When sending parameter txt with special characters (including «space»),
                                              it should be URI encoded using the function rawurlencode() or similar.
user                 String                   Partner login
from                 String |nbsp| (11)       End user`s alpha name – figures or Latin characters
phone[1..n]          Number                   End  user’s  phone  number  in  international  format, digits  only,
                                              without  the  plus  sign:  country  code, operator code and the phone number.
                                              If you  want to send the  message to more phone numbers, set the ‘phone’
                                              parameter for  each  of  them, so there will  be  several  ‘phone’
                                              parameters  in  your request* 
sign                 String                   `Digital signature`_
**Optional parameters**
------------------------------------------------------------------------------------------------------------------------------
pref                 String                   Prefix for sending response SMS to the service of a partner
                                              :ref:`session mode <ref-session>`
sending_method       String                   Only value «sms» is available for sending SMS messages
p_transaction_id     String                   Transaction id in partner system, which is used to check statistics and is sent
                                              to the partner with delivery report
charset              String                   Txt parameter encoding:

                                              * UTF8 or UTF-8 (on default) – clear indication of Unicode UTF-8 encoding
                                              * UTF-16BE or UCS-2 – Unicode UTF-16 Big Endian encoding
                                              * CP1251 – clear indication of Windows-1251 encoding
delay                Number                   To delay sending of a text message to the end-user for stated number of minutes.

                                              Maximum value – 10080 (a week).
dlr                  Number                   Message delivery report:

                                              * 0 (on default) – without report
                                              * 1 – to request report on message delivery (ref. p.5)
==================== ======================== ================================================================================

In  the  “from”  parameter  you  can  send  alphanumeric  value  (Latin  letters,  numbers,  space  and  some special  characters; up to 11 characters).  The end user’s phone will display this  value in  the  “From:” field (it  depends  on  the phone model).  For  security purposes,  SMS Online doesn’t deliver messages with any text  in  the  «From» field. When you finish testing, you will need to provide the SMS Online manager with the chosen text for the «From» field or do it via contact form.

If the message is up to 160  characters long  (symbols from the  GSM 03.38  encoding  only), it is paid as one MT message. Otherwise, the number of MT messages will be increased (the long one will be divided into parts of up to 153 characters long each).

If there is at least one character in the message that is not presented  in  the GSM  03.38, the message maximum length is reduced to 70 characters. If the message is up to 70 characters long, it is paid as one MT message. Otherwise, the number of MT messages will be increased (the long one will be divided into parts of up to 67 characters length each).

Partner can send a symbol of a new line in a parameter «txt» in the most suitable form: in the form of «n» or «r» or «<br>». Bulk system identifies all the variants and will send SMS with line translation to the end-user.

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

Pay attention that error-free response («code=0») of the script doesn’t mean the message will be delivered, it means that the request processing has been accepted.

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

=== ================== =========================================================
Код Значение           Описание                                                 
=== ================== =========================================================
0   delivered          The message has been successfully delivered
-1  not delivered      The message hasn’t been delivered (delivery error)                
-2  expired            Expired and deleted from SMSC (end-user has been 
                       unavailable for 24 hours)     
-3  rejected           The message has been rejected by operator (incorrect 
                       number, end-user has blocked message reception and etc.)
=== ================== =========================================================

.. _ref-session:

Session mode
------------

«SMS Online» system can work in a session mode: when sending SMS to the end-user, you can state session prefix (parameter «pref») to be able to send response messages  (by pressing «Respond» button in your phone) to your service via «SMS Payment»  system (if required, to get documentation for this system, you should contact manager of «SMS Online»).

.. note:: Prefix should be connected to the «SMS Payment» system.


**Example:** partner sends to the end-user a message with the following parameters:

.. code-block:: none

   from = 1320
   txt  = To take part in the trivia, please send your age in response SMS
   pref = VIKTORINA


The end-user will have to simply answer this SMS (by pressing «respond» button in the phone). For example, he wrote «26 years». Then partner script will receive this message in the following form:

.. code-block:: none

   pref=VIKTORINA   txt=26years

Session mode will be on for a period of 48 hours. Apart from contests and trivias, this function can be used to send confirmations, dialogues, chats and so on.
