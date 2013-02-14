CS255 - Programming Assignment 1
=================================


Students:
==========
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
      When a user enters a password, a key is drrived using this password. 
      Decryption is then attempted with this key. If the
      key table is returned, then password should be valid. Else, the password
      is assumed to be wrong. At this point, an Alert() is provided to the
      user and the password prompt appears again.

      Note that there is no check on the number of attempts a user gets till
      the right password is accepted. Without such a check, a brute force
      attack can be done on the specific users password. The prevention of
      such an attack is not covered in the scope of this project.


Key Generation:
===============
When the user first creats a group, the user is expected to generate a new key
for that group using the "Generate New Key" button under "Account Settings".

Once the key is generated, the key is expected to be sent to all members of the
group in a secure manner. This is not covered in the scope of our project. It
is assumed that group users have a secure channel established to transfer the
keys.