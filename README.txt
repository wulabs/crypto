Madhukar Krishnarao
Jack Wu

Password management:
====================
When the user first logs in, the password behavior depends on whether there
are entries in the Group->Key table.

  Case (1): Group->Key table is empty
    - User is free to access the group page and other facebook book pages.
    - Messages cannot be encrypted or decrypted. If encryption is tried, an
      Alert() is provided to notifiy the user that encryption key is missing.
      If decryption is tried, an invalid exception is thrown and a suitable
      message is provided to the user.
    - Password is set when the user generates a new Key for a Group the
      very first time.

  Case (2): Group->Key table is not empty
    - Any time the user tries to access a page where the script is loaded, a
      prompt for the password appears.
    - The user is expected to enter the right password to decrypt the table.
    - In case of a wrong password, an alert() is provided and the prompt
      appears again.


Milestone 1:
============

* Message Encrypt/Decrypt Key:

  - 256 bit keys
  - Derived using user password and a salt (4 random 32 bit int)
  
  Why is it secure?
  -----------------
    - To brute force a 256 bit key, 2^128 attempts are required. This is
      negligible.
    - ??? number of messages can be encrypted using the key without hitting
      birthday paradox.
  

* Message Encryption/Decryption:

  - Uses the Message Encrypt/Decrypt Key.
  - Uses CBC mode encryption with AES as the block cipher primitive
  - Ues a random IV (4 * 32 bit int)
  - Padding ensures correct block size. Block size is ?? bits.
  - N bits of value N are appended
  
  Why is it secure?
  -----------------
    - CBC encryption with random IV is secure
    - Random IV gurantees no two messags return the same ciphertext.


* Group->Key table:

  - A mapping of group name to its 256 bit key.
  - Saved in localStorage
  
  Why is it secure?
  -----------------
    - Entire table is encrypted and stored on disk. Not just induvidual keys
    - A database key is derived using user password and random salt (4 * 32
      bit int)
    - The salt is stored along with the encrypted table. Since the salt is
      chosen at random, it is safe to store it on the disk.

* Issues with cryptography in a browser
  - Securly delivering javascript to a browser is a chiken-egg problem.
  - Perfect security could be slow (esp for page refresh/responsiveness)
  - Unreliable runtime environment.

* Side channel attacks
  - Once attacker controls web requests, all security is lost since attacker
    can inject <script> tags to fetch any unencrypted data.
  - Get data stored on session storage
  - Get data stored in browser memory
  - All encryption is based on password. Trick the user into entering the
    password on a different site (there is a name for this...)
  - Guess password (social engineering attacks)
  
* Assumptions for a secure implementation:
  - Randome number generator is truely (Pseudo) random
  - No bugs in AES primitives.
