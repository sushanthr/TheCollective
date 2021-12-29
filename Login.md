# Login

Authentication to The Collective is through WebAuthN (https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API). This allows the service to store a 
private key with an Authenticator device (Windows Hello, Touch ID, Android Fingerprint, Yubi Key) and a public key on the service to authenticate users. 

At a high level, here is how this works - the service will generate a random string and request a userid using WebAuthN APIs to sign it with the private key stored 
on the Authenticator device. A signed string is returned that can be validated with the public key the service knows and Authenticating the user. The completion of the 
Authentication process should result in the user getting a user token signed by the service. The public key for the service will be declared, allowing any system to 
validate a user token as having originated from the service.

## Requirements on the Service

- Declare public key
- Internal Table #1 UID table
-
|Column| Details|
|------|--------|
|UID   | 32 byte identifier - 1 byte should be used to indicate the kind of identified this is which is User ID in this case |
|Storage Root URI| URL representing the storage root for this user |
|Email| Email address, user to reset the account in case all registered devices are lost. See section about Account Reset, for caveats| 
|User Public Key| Public key used to encrypt any content written to the user's storage |

- Internal Table #2 UID Device table
-
|Column| Details|
|------|--------|
|UID   | 32 byte identifier - 1 byte should be used to indicate the kind of identified this is which is User ID in this case |
|Device Public Key | A registered public key for the UID |
|User Private Key Encryption Challenge string | Challenge string used by the Device to retrieve the symmetric key for encrypting the User Private Key|
|User Private Key | User private key encrypted by this device with symmetric encryption. Not decryptable by the service. |

Note, each UID can have multiple rows since a user can register multiple devices.

## Account Registration
During new user registration we ask for Email Id, Name and a register button that initiates the WebAuthN ceremony. Service brings to the table a unique UID. 
Post WebAuthN registration ceremony we have a public key for the Authenticator device. We ask the user for a stroage provider URI or let them choose The Collective as the 
default storage provider. UID table and UID device table entries are populated.

For a new account, a next important step is to generate the user public key / private key with which all of the content the user generates will be encrypted. The client side 
code initiates this process and creates a public key / private key pair using the Web Cypto API https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API. The Public key is 
sent back to the service. The private key is stored locally using dom storage and encrypted with an authenticator device specific symmetric key and sent to the service.

Key generation for the device specific symmetric key. WebAuthN APIs only allow for requesting the Authenticator devices to sign strings, there is a large blob extension that could 
allow for storing device specific symmetric key on the device but its support across devices is not clear. The solution at this point is to request webauthn to sign a known
challenge string and use the signature string as the symmetric key. The User Private Key Encryption Challenge string will be saved on the service as well. Note: This second WebAuthN
request will result in a second user prompt, however this is a one time affair as the generated symmetric key will be stored in the user local DOM storage until sign out.
