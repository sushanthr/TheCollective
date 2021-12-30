# Storage
Storage in The Collective is all based on Folders and Files, everything is a file. Storage system will enforce ACLs with each folder having an ACL 
that controls access to it and seperately each folder having a .key file that declares the public key used to encrypt data stored in that folder.

What data lives in Storage ?
If a kind of user related data is never encrypted, for example user email id / device keys these are stored outside storage system in service internal tables. 
The design pereference is to as much as possible keep data in storage and rarely use hidden internal tables. Data like a post an be encrypted or un encrypted for public posts, these will live in storage since they can be encrypted at times.

Coming back to the topic, what data lives in storage - primarily these are posts which can contain any kind of media. A post will also contain comments, which can also contain any kind of media. Images/Text are embedded directly in a post. Every post lives inside a group, two default groups exist Public / Friends. Users can create additional groups.

```
User HOME folder
  |- .key (the private key for this is available in Encrypted User Directory Private Key in - Internal Table #2 UID Device table)
  |- Group_Public 
      |- .nokey (has a .nokey file which implies its contents are no encrypted)
      |- Post #1
        |- Comment #0 (the primary content of the post)
        |- Comment #1
        |- Comment #2
  |- Saved Keys
    |- Group_Friend #1 private key for Group_FriendId#1\.key
    |- Group_Friend #N
    |- Group_Friend Self private key for Group_#UID\.key
 ```
 
 ```
 Group HOME folder
  |- .key
  |- Post #1
      |- Comment #0 (the primary content of the post)
      |- Comment #1
      |- Comment #2
  |- Post #N
 ```
 
When there is a private conversation between two users a new Group is created for them with a group home folder - and rest of the policy around groups follows for that conversation.
 
 ## .key files
 These are the public keys used to encrypt any files in the subtree. If a folder must not be encrypted it will have a .nokey file.
 
