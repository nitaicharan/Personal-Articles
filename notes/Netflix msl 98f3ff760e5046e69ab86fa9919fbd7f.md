# Netflix/msl

Created: July 20, 2022 4:00 PM
Finished: No
Source: https://github.com/Netflix/msl/wiki/MDX-User-Authentication
Tags: #tool

MDX is the Netflix Multiple-Device Experience and allows a controller client to issue commands to a target client. The MDX user authentication scheme is used by an MDX target to assume the user identity of an MDX controller using data provided by the controller. An example is a Playstation 3 target being controlled by an iOS controller.

There are three parties involved: controller, target, and authentication server. One communication channel exists between the controller and target, and a MSL communication channel exists between the target and authentication server. The controller must have already authenticated itself and its user against the authentication server. The target may be unauthenticated against the authentication server.

There are two operations involved when an MDX session is established between the controller and target: user authentication and controller-target pairing. The described MDX user authentication scheme is used to perform user authentication. Controller-target pairing occurs at the application layer and is subject to authorization and other business logic.

To ensure the target is authorized to assume the user identity of the controller, a PIN is displayed by the target. A user must enter this PIN onto the controller and is included in the MDX authentication data sent by the controller to the target. The target also includes its PIN in the user authentication data sent by the target to the authentication server using MSL.

This scheme is identified by the string `MDX`.

The user authentication data is constructed by the target and sent to the authentication server to assume the identity of the controller user.

```
authdata = {
  "#mandatory" : [ "pin", "mdxauthdata", "signature" ],
  "#conditions" : [ mastertoken xor cticket ],
  "mastertoken" : mastertoken,
  "cticket" : "binary",
  "pin" : "string",
  "mdxauthdata" : "binary",
  "signature" : "binary"
}
```

[Untitled](Netflix%20msl%2098f3ff760e5046e69ab86fa9919fbd7f/Untitled%20Database%2021b0801891c845598f4b8b5125622420.csv)

MSL-based controllers may choose to include their master token or include a MSL token construct. Legacy NTBA-based controllers will always include their CTicket. (A CTicket is an encrypted token containing AES-128-CBC and HMAC-SHA256 session keys similarly to a master token, plus a user identity similarly to a user ID token.)

The MSL token construct is defined as follows:

```
1,mastertoken,useridtoken
```

or

```
1=mastertoken=useridtoken
```

where the master token and user ID token encoded representations are Base64-encoded. The existence of commas can be used to differentiate a MSL token construct from a Base64-encoded CTicket as Base64-encoded data does not include commas. When equal signs are used instead the Base64-encoded master token and user ID token can still be properly extracted.

The MDX authentication data is constructed by the controller and sent to the target to authorize the target to assume the controller’s user identity. See below for details regarding the MDX authentication data.

![Netflix%20msl%2098f3ff760e5046e69ab86fa9919fbd7f/mdx.png](Netflix%20msl%2098f3ff760e5046e69ab86fa9919fbd7f/mdx.png)

The signature algorithm is HmacSHA256 and is computed over the binary representation of the MDX authentication data. The master token or CTicket HMAC-SHA256 session key is used.

MSL-based controllers that include their master token represent the MDX authentication data as JSON.

```
mdxauthdata = {
  "#mandatory" : [ "useridtoken", "action", "nonce", "pin" ],
  "useridtoken" : useridtoken,
  "action" : "string",
  "nonce" : "int32(0,-)"
  "pin" : "binary",
}
```

[Untitled](Netflix%20msl%2098f3ff760e5046e69ab86fa9919fbd7f/Untitled%20Database%20c64db28badc14bc8b927a5e58c5a0d24.csv)

The target will assume the user identity of the controller user ID token.

An unused legacy value.

The controller PIN is encrypted using the controller’s master token AES-128-CBC session key. If the MDX authentication data controller PIN does not match the target PIN then user authentication fails.

NTBA-based controllers and MSL-based controllers that include the MSL token construct represent the MDX authentication data as XML.

```
mdxauthdata =
<registerdata>
  <action>regpairrequest</action>
  <nonce>531151</nonce>
  <pin>aWtza21...</pin>
</registerdata>
```

An unused legacy value.

The controller PIN is encrypted using the controller’s master token AES-128-CBC session key. If the MDX authentication data controller PIN does not match the target PIN then user authentication fails.