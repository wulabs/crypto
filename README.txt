Madhukar Krishnarao
Jack Wu

Password Management:
====================
When the user first logs in, the password behavior depends on whether there
are entries in the Group->Key table.

  Case (1): cs255.localStorage is empty
    - User is free to access the group page and other facebook book pages.
    - Messages cannot be encrypted or decrypted. If encryption is tried, an
      Alert() is provided to notify the user that encryption key is missing.
      If decryption is tried, an invalid exception is thrown and a suitable
      message is provided to the user.
    - Password is set when the user generates a new Key for a Group the
      very first time.

  Case (2): cs255.localStorage is not empty
    - Any time the user tries to access a page where the script is loaded, a
      prompt for the password appears.
    - Password Validation:
      When a user enters a password, a key is generated using the salt in the
      localStorage. Decryption is then attempted with this key. If the
      key table is returned, then password should be valid. Else, the password
      is assumed to be wrong. At this point, an Alert() is provided to the
      user and the password prompt appears again.

      Note that there is no check on the number of attempts a user gets till
      the right password is accepted. Without such a check, a brute force
      attack can be done on the specific users password. The prevention of
      such an attack is not covered in the scope of this project.

  Password security: The password is stored on sessionStorage. Contents in
  the sessionStorage is considered secure since the information is lost once
  the user logs out and closes the tab. However, sessionStorage is on a per
  tab. Consider a case where a user logs into FB on one tab. Then opens up
  the account page in a different tab. The browser lets you do this. Now, the
  user logs off from the second tab and closes it. At this time, although
  the user is logged out, the sessionStorage still persists from the first tab.
  This could potentially lead to an attack. One possible solution against this
  is to use cookie information while reading and writing data from/to
  sessionStorage. We can allow information in the sessionStorage to be
  accessible only if the cookie (with the user login information) present.

  The password is the weakest link in the system. We assume that the user
  chooses a sufficiently strong password.


Milestone 1:
============

* Message Encrypt/Decrypt Key:
------------------------------

  - Message encryption and decryption is done using 256 bit keys.
  - The key is derived from the user password and a random salt.
  - The salt is 4 random 32 bit int. 128 bits in total.
  
  Why is it secure?
    - To brute force a 256 bit key, 2^128 attempts are required. This is
      negligible. Hence the advantage of an attacker trying to iteratively
      guess the key is low.
    - AES is used to encrypt decrypt the messages. AES uses 128 bit block
      size. So after 2^64 blocks, we are expected to change the key. This
      number is pretty huge.
    - Although 2^64 block of data is pretty big, theoretically, this limit
      can be reached. Our code does not consider this. So after this limit,
      our code is not considered secure.
  

* Message Encryption/Decryption:
--------------------------------

  - Uses the Message Encrypt/Decrypt Key.
  - Uses CBC mode encryption with AES as the block cipher primitive
  - Ues a random IV (4 * 32 bit int)
  - Padding ensures correct block size. Block size is 128 bits.
  - N bits of value N are appended. In case of a message with correct block
    size multiple, a dummy block of 128 bits is introduced.
  
  Why is it secure?
    - CBC encryption with random IV is secure
    - Even if the message is just one block size in length, random IV
      guarantees that no two messages return the same ciphertext.


* Group->Key table:
-------------------

  - A mapping of group name to its 256 bit key.
  - Encrypted and saved in localStorage
  - The table is encrypted using a different key than the message key
    using CBC mode encryption with AES as the block cipher primitive.
  - Uses the password and a random salt (4 * 32 bits) to generate the key.
    This ensures that the key is different from the message key.
  
  Why is it secure?
    - Entire table is encrypted and stored on disk. Not just induvidual keys
      Due to this, no information about the number of groups or the group
      names is visible. However, based on the number of bytes of encrypted
      data, it is possible to know the size of the table.
    - A database key is derived using user password and random salt (4 * 32
      bit int)
    - The salt is stored along with the encrypted table. Since the salt is
      chosen at random, it is safe to store it on the disk.
    - If for some reason, the salt on the localStorage is missing, we throw
      an exception at that time and do not continue any processing. This is
      to safeguard against any active attacks on the salt.


* Issues with cryptography in a browser
---------------------------------------
  - Securely delivering javascript to a browser is a chiken-egg problem.
  - Perfect security could be slow (esp for page refresh/responsiveness)
  - Unreliable runtime environment.


* Side channel attacks
----------------------
  - Once attacker controls web requests, all security is lost since attacker
    can inject <script> tags to fetch any unencrypted data.
  - Get data stored on session storage
  - Get data stored in browser memory
  - All encryption is based on password. Although attempt is made in the
    code to leak information about the keys, an attacker can always guess
    the password using social engineering attacks or dupe the user into
    entering the password using pishing attacks.
  

* Assumptions in the code
-------------------------
  - Random number generator is truly (Pseudo) random
  - No bugs in AES primitives.
  - Implicit assumption that data in the disk is not tampered with.
