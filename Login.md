# Login

Authentication to The Collective is through WebAuthN (https://developer.mozilla.org/en-US/docs/Web/API/Web_Authentication_API). This allows the service to store a 
private key with an Authenticator device (Windows Hello, Touch ID, Android Fingerprint, Yubi Key) and a public key on the service to authenticate users. 

At a high level, here is how this works - the service will generate a random string and request a userid using WebAuthN APIs to sign it with the private key stored 
on the Authenticator device. A signed string is returned that can be validated with the public key the service knows thus authenticating the user. The completion of the 
Authentication process will result in the user getting a user token signed by the service. The public key for the service will be declared, allowing any system to 
validate a user token as having originated from the service. Henceforth the user token identifies the user.

## Requirements on the Service

- Declare public key
- Internal Table #1 UID table

|Column| Details|
|------|--------|
|UID   | 32 byte identifier - 1 byte should be used to indicate the kind of identified this is which is User ID in this case |
|Storage Root URI| URL representing the storage root for this user |
|Email| Email address, used to reset the account in case all registered devices are lost. See section about Account Reset, for caveats| 
|DOM local storage Symmetric Encryption Key| Key provided by the service used to encrypt data stored in local dom storage. |

- Internal Table #2 UID Device table

|Column| Details|
|------|--------|
|UID   | 32 byte identifier - 1 byte should be used to indicate the kind of identified this is which is User ID in this case |
|Device Public Key | A registered public key for the UID |
|User Directory Private Key Encryption Challenge string | Challenge string used by the Device to retrieve the symmetric key for encrypting the User Private Key|
|Encrypted User Directory Private Key | User private key encrypted by this device with symmetric encryption. Not decryptable by the service. |

Note, each UID can have multiple rows since a user can register multiple devices.

- Rest API that provided a user token can return the DOM storage Symmetric Encryption Key.


## Account Registration
During new user registration we ask for Email Id, Name and a register button that initiates the WebAuthN ceremony. Service will provide for the ceremony a unique UID. 
Post WebAuthN registration ceremony we have a public key for the Authenticator device. We ask the user for a storage provider URI or let them choose The Collective as the 
default storage provider. UID table and UID device table entries are populated.

For a new account, a next important step is to generate the user folder public key / private key. Firstly what are these keys ? 
Storage in The collective is all based on folders and files, like linux everything is a file. Folders are encrypted with a public key, the public key to encrypt contents of a folder are stored in a .keys file within each folder. Creating a UID will create a user directory in the chosen storage service, the folder public key/ private key are needed to encrypt content in that folder. The client side code initiates this process to create a user folder public/private key pair using the Web Cypto API https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API. The Public key is written into the .keys file of the user home directory. The private key is stored locally using dom storage and encrypted with an authenticator device specific symmetric key and sent to the service.

Key generation for the device specific symmetric key. WebAuthN APIs only allow for requesting the Authenticator devices to sign strings, there is a large blob extension that can allow for storing device specific symmetric key on the device but its support across devices is not clear. The solution at this point is to request webauthn to sign a known
challenge string and use the signature string as the symmetric key. The User Private Key Encryption Challenge string will be saved on the service as well. Note: This second WebAuthN request will result in a second user prompt, however this is a one time affair as the generated symmetric key will be stored in the user local DOM storage until sign out.

Storing the private key with local Dom storage https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage, content in DOM storage is encrypted with a symmetric key. This symmetric key is can be acquired from the service by calling a REST API with the user token.
