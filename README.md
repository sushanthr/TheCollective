# The Collective

The collective is a prototype for an open social network, with interesting properties: 
- End to End encryption of all content. Safeguarding users from profiling, selling of user data.
- Explicit data access control.
  At two levels:
    - Data can be classified as accessible to public/ signed in users (prevents crawlers from access)/ groups of users / individual users.
    - Any compute in this social network is plug and play and can be replaced by the end user with their own. (More on this later).
- Fully inspectable source code. 
  The entire service/ app will be built using client side javascript - which makes everything the site does auditable. 
- Near 0 backend code.
  Storage is primarily the API that is exposed to the client side code - and that too will be pluggable, with the users having freedom to plugin any storage provider.
  As the feature set evolves there may be a need for explicit services for things like image/video format conversion these will be pluggable as well.
- WebAuthN based accounts and content encryption key
  The goal is to not require yet another password, sign up. All sign ins will be via WebAuthN and the WebAuthN keys will also guard user content encryption, leaving the service with no decryption keys for the users content.

Other Salient features
- Content moderation / Fake News policy
   - Apart from typical report content buttons. Each content can be ranked for truthfulness. Truthfulness of content will be explicitly surfaced along with likes/dislikes.
   - Content reporting will be community driven. Reported content will be removed through a process that leaverages the viewers and asks them if the content is offensive.
- Compliance with local laws
   - Protection from nation state or law enforcement is an explicit non goal. This network should not be a haven for illegal activity. 
   - Though the content is completely encrypted, the system design should allow for
      - law enforcement to remove content. (Easy to do, should not require decryption)
      - Inspect content. (More difficult to achieve since the service does not have decryption keys).
- Ads
  - Ads are going to be a necessary evil to fund a storage service. Each storage provider would be allowed to declare an advertiser id and ads would be placed, so that storage providers can pay for the service. Users brining their own storage will ofcourse not show ads.
 
 Flexiblity with Content

 Social networks can be about images, blogs, videos, professional networks, real time communication - with the collective the goal is not to contrain the network to any particular genre. These genres are viewed like themes (declarative HTML/CSS) applied on a foundation of data access primitives. The eventual goal is to support sharing images, video, blogs, posts, chat, video conferencing.
 
 The idea is to build all of the above using web tech and web apps, have all the code inspectable and in Javascript even if this boils down to leaving out less capable devices or makes the experience quirky.
