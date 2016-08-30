Description of Bulk system
===========================

The scheme of Bulk system operation: partner software via protocol HTTPS calls script «SMS Online» and sends it the end-user`s phone number, text of the message and other parameters. This information will be send to the end-user in a text message. 

We remind you that unauthorized bulk (SPAM) is strictly forbidden. In case of a disputable situation (complaints from end-user and/or operator), partner has to provide confirmation within one work day that end-user was legitimately added to the bulk base.



System description
------------------

To send messages to the end-user, partners need to send POST or GET request to the server of «Bulk» system on the following address https://bulk.sms-online.com/.

Bulk system supports the following methods of message sending: 

* **SMS** – sending of SMS message
* **Viber** – sending of messages via Viber messenger
* **USSD** – sending of USSD notification

.. note:: On default we provide only SMS method, other methods can be connected via your manager.

Terms
-----

* МТ message (Mobile Terminated) - a message from a server to the end user.
* MO message (Mobile Originated) - a message from the end user to a server.
* Short message - a message up to 160 characters long if  the GSM 03.38 encoding is used. If there is at least one character in the message that is not presented  in  the GSM  7-bit table (‘€’, for example),  the message maximum length is reduced to 70 characters
* Long message - a message longer than 160 (or 70, see “Short message”) characters.
* SMSC – a network element in the mobile phone network which delivers SMS messages.
* Alpha name - the “From” parameter of the SMS. It can contain numbers or Latin letters (up to 11).
* SMS Bulk – SMS mailing service that sends MT messages to the end users.
* DLR (delivery report) — report about SMS being delivered.


Содержание
------------------
.. toctree::
   :maxdepth: 2

   self
   access
   bulk_sms
   bulk_viber
   examples
   faq
   changelog
   contacts
