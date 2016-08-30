
.. _`Digital signature`:

**A  digital  signature** is  required  to  verify  the  transferred  data.  To  get  the  digital  signature  you  need  to concatenate the values of the parameters in the order given below.  The last parameter in the sequence is  the  private  key that is only known to you  and  SMS Online  (“secret_key”).  After concatenating all the parameters,  the MD5 hash  in  hex  format  must be calculated.  The result of the calculation is the digital signature.

Parameters sequence: user, from, phone, txt, secret_key.

.. code-block:: none

   sign = md5( user + from + phone + txt + secret_key )

The plus sign is the concatenation operator here. If there are more than one phone number, to calculate MD5 you must concatenate them all in the same order as they are sent in the request.

If txt parameter lacks in the request, digital signature should be perceived the following way: 

.. code-block:: none

    sign = md5( user + from + phone + secret_key )