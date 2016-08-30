<!-- Шаблон с базовыми параметрами для Viber -->
<!--
==================== ======================== ================================================================================
Parameter            Type                     Description
==================== ======================== ================================================================================
-->
user                 String                   Partner login
from                 String |nbsp| (11)       End user`s alpha name – figures or Latin characters
phone[1..n]          Number                   End  user’s  phone  number  in  international  format, digits  only,  without 
                                              the  plus  sign: country  code, operator code and the phone number.
                                              If you  want to send the  message to more phone numbers, set the ‘phone’ 
											  parameter for  each  of  them, so there will  be  several  ‘phone’  parameters 
											  in  your request* 
sign                 String                   `Digital signature`_
sending_method       String                   The value should be: viber

**Optional parameters**
------------------------------------------------------------------------------------------------------------------------------
p_transaction_id     String                   Transaction id in partner system, which is used to check statistics and is sent
                                              to the partner with delivery notification
charset              String                   Txt parameter encoding:

                                              * UTF8 or UTF-8 (on default) – clear indication of Unicode UTF-8 encoding
                                              * UTF-16BE or UCS-2 – Unicode UTF-16 Big Endian encoding
                                              * CP1251 – clear indication of Windows-1251 encoding
delay                Number                   To delay sending of a text message to the end-user for stated number of minutes.

                                              Maximum value – 10080 (a week).
dlr                  Number                   Message delivery report:

                                              * 0 (on default) – without report
                                              * 1 – to request report on message delivery (ref. p.5)
dlr_timeout          Number                   Time in seconds within which a message should be delivered.

                                              Minimal value – 60 (one minute).

                                              Maximum value – 86400 (24 hours). 

                                              In case it is not set, dlr_timeout is considered to be 14 days.
                                              
                                              This parameter is used to quickly send messages via channel, different from
                                              Viber (SMS, USSD or similar)
expiry_txt           String                   Text which is shown to the end-user after dlr_timeout is finished.

                                              Is shown only to the end-users with iOS.

                                              On default: value of txt parameter (if it is has been sent) or 
                                              "This message has expired" (if txt hasn’t been sent).
<!--
==================== ======================== ================================================================================
-->