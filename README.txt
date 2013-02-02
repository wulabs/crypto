Madhukar Krishnarao
Jack Wu

Password management:
====================
  - A prompt for the user password appears the first time user logs into the
    account.
  - If the Group->Keys table is empty, then user is free to choose any 
    password.
  - If Group->Keys table already has entries, then the user is expected to 
    enter the password used when creating the keys.
  - In case a wrong password is entered, then the Group->Keys table does not
    decrypt and is not displayed in the UI. At this time, you can retry with
    the right password by restarting the session.
  - Passwords can have a maximum length of 32 characters. Longer passwords
    will be truncated to 32 characters.


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