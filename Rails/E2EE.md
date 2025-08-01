# End To End Encryption

In here we will be learning about the E2EE. Let's take a chat application. You need to keep the user chat messages private. So you cannot store the chats or media without a proper encryption. But encrypting and decrypting a message or media can be a complex process. We will learn about it in a step by step process.

FYI, if you don't want to know how it all started directly go to [Hands On It](#hands_on_it) section.

### How it all started
While I'm creating social media I have created chat feature to my application. After completing my chat application I came to know storing users private conversation is actually not ideal and it is threat to users privacy. So then I wanted to create an E2EE feature for my chat application.

Then I started to brainstorm about how to implement it in my rails application and started surfing net and AI.

Initially i thought it can be a easy process. All I need to do is while creating new message and sending it to user I need to encryt the data and on reciever side user decrypts them and read them. Then I started digging deeper into how I need to encrypt it. So, for that purpose I need to create a public and private key for that user and I can use that public key to encrypt and private key to decrypt. But the problem here is How another user can decrypt the message? Because we are not sharing our private key with other users what we need to do is we need to encrypt in such a way that people to whom a particular message is intented to able to decrypt it with their own private key. For that purpose we will be encrypting the message with all the public keys of the intended users.

Then comes the another issue, what happenes when private key is removed from user's device or user is trying to access from another device? At that point of time user is will pushed to login page where user need to type password, that is stored for a moment in client side, once user is authenticated in backend we send the encrypted private key back to client and using the password that is stored previously in client side we can decrypt the private key. Then that code in saved in the browser securly.

Now let's look into how to code this.

### Hands On It

First let's add `openpgp.js` into our rails application. So let's go to our teminal and type:

```bash
bin/importmap pin openpgp
```
This will add `openpgp.js` to our vendor folder and pin that in our importmap to use it in our JS files.

Now let's create a Helper file for creating keys, encrying and decrypting files.

I've created the file named `crypto_helper.js` in my javascript helpers.

```javascript
// app/javascript/helpers/crypto_helper.js

import * as openpgp from "openpgp";

export default class CryptoHelper {

  // Used to generate public key and private key for a user.
  static async generateKeys(username, email) {
    try {
      const { privateKey, publicKey } = await openpgp.generateKey({
        type: 'ecc',
        curve: 'curve25519',
        userIDs: [{ username: username, email: email }],
      });
      return { publicKey, privateKey };
    } catch (error) {
      console.error("Error generating keys:", error);
      return null;
    }
  }

  /* 
  Used to encrypt a private key with a password so that we can send this to
  store the encypted key in our cloud DB.
  */
  static async encryptPrivateKey(privateKeyArmored, password) {
    try {
      const privateKeyObject = await openpgp.readPrivateKey({ armoredKey: privateKeyArmored });
      const encryptedKey = await openpgp.encryptKey({
        privateKey: privateKeyObject,
        passphrase: password
      });
      return encryptedKey.armor();
    } catch (error) {
      console.error("Error encrypting private key:", error);
      return null;
    }
  }

  /* 
  Used to decrypt an encrypted private key with a password that we used to encrypt
  it incase of user needs his private key and his browser don't have it.
  */
  static async decryptPrivateKey(encryptedPrivateKeyArmored, password) {
    try {
      const encryptedKeyObject = await openpgp.readPrivateKey({ armoredKey: encryptedPrivateKeyArmored });
      const decryptedKeyObject = await openpgp.decryptKey({
        privateKey: encryptedKeyObject,
        passphrase: password
      });
      return decryptedKeyObject.armor();
    } catch (error) {
      console.error("Error decrypting private key (check password):", error);
      return null;
    }
  }

  // Used to encrypt a message with a list of recipient and also self public keys.
  static async encryptMessage(recipientPublicKeysArmored, plainText) {
    try {
      const recipientPublicKeys = await Promise.all(
        recipientPublicKeysArmored.map(armoredKey => openpgp.readKey({ armoredKey }))
      );

      const message = await openpgp.createMessage({ text: plainText });

      const encryptedMessage = await openpgp.encrypt({
        message,
        encryptionKeys: recipientPublicKeys,
      });
      return encryptedMessage;
    } catch (error) {
      console.error("Error encrypting message:", error);
      return null;
    }
  }

  // Used to decrypt a message with a private key.
  static async decryptMessage(privateKeyArmored, encryptedMessageArmored) {
    try {
      const message = await openpgp.readMessage({ armoredMessage: encryptedMessageArmored });
      const privateKey = await openpgp.readPrivateKey({ armoredKey: privateKeyArmored });
      const { data: decryptedText } = await openpgp.decrypt({
        message,
        decryptionKeys: privateKey
      });
      return decryptedText;
    } catch (error) {
      console.error("Error decrypting message:", error);
      return null;
    }
  }

  /*
  Used to encrypt a file(like image, video, audio and files) with a list of
  recipient and also self public keys.
  */
  static async encryptFile(recipientPublicKeysArmored, fileData) {
    try {
      const recipientPublicKeys = await Promise.all(
        recipientPublicKeysArmored.map(armoredKey => openpgp.readKey({ armoredKey }))
      );

      const message = await openpgp.createMessage({ binary: fileData });

      const encryptedFile = await openpgp.encrypt({
        message,
        encryptionKeys: recipientPublicKeys,
        format: 'binary' // Specify binary output
      });

      return encryptedFile;
    } catch (error) {
      console.error("Error encrypting file:", error);
      return null;
    }
  }

  /*
  Used to encrypt an encrypted file(like image, video, audio and files) with a
  private key.
  */
  static async decryptFile(encryptedPrivateKeyArmored, encryptedFileData) {
    try {
      const privateKey = await openpgp.readPrivateKey({ armoredKey: encryptedPrivateKeyArmored });

      const message = await openpgp.readMessage({ binaryMessage: encryptedFileData });

      const { data: decryptedFile } = await openpgp.decrypt({
        message,
        decryptionKeys: privateKey,
        format: 'binary' // Specify binary input
      });

      return decryptedFile;
    } catch (error) {
      console.error("Error decrypting file:", error);
      return null;
    }
  }
}
```