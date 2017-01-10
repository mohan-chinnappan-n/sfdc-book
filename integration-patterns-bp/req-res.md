# Remote Process Invocation—Request and Reply

![Request Response](https://developer.salesforce.com/docs/resources/img/en-us/204.0?doc_id=dev_guides%2Fintegration_patterns%2Fimages%2Fremote_process_invocation_state.png&folder=integration_patterns_and_practices)


- The user initiates an action on the Visualforce page (for example, clicks a button).
- The browser performs an HTTP POST that in turn performs an **action** on the corresponding Apex controller.
- The controller calls a previously-generated Apex Web service proxy class.
- The proxy class performs the actual call to the remote Web service.
- The **response** from the **remote system** is returned to the Apex controller, which then processes the response, updates any data in Salesforce as required, and re-renders the page.


### Idempotent

**Idempotent** capabilities guarantee that **repeated invocations** are safe and will have no negative effect

### Note

It’s almost impossible to guarantee that Salesforce only makes the call once (especially if the call is triggered from a user interface event)


#### How to build a Idempotent receiver:
The most typical method of building an **idempotent receiver** is for it to track duplicates based on unique message identifiers sent by the consumer. Apex Web service or REST calls should be **customized to send a unique message ID**.

In addition, operations that create records in the **remote system should always check for duplicates** before inserting.

If he record exists in the remote system, the record should be updated. In most systems, this is termed an upsert operation.


#### Example
A utility company uses Salesforce and has a separate system that contains **customer billing information**. They want to display the billing history for a customer account without having to store that data in Salesforce. They have an existing **Web service** that can return a **list of bills and their details for a given account number**, but cannot otherwise display this data in a browser.

This requirement can be accomplished with the following:
  - Salesforce consumes the billing history service WSDL from an Apex proxy class.
  - Create a Visualforce page and custom controller to **execute this Apex proxy class** with the account number as the unique identifier.
  - The custom controller then **parses the return values from the Apex callout** and the Visualforce page  subsequently renders the billing info to the user.

This example demonstrates the following:

- The state of the customer is tracked with an account number stored on the Salesforce account object.

- Subsequent processing of the reply message by the caller (SFDC).



###Governor Limits

Due to the multi-tenant nature of the Salesforce platform, there are limits to Apex callouts.
- Only **10** callouts can be made in a given **execution context**
- A **maximum of 60 seconds invocation time** for a **given callout**

    -  **120 seconds** of invocation time for **all callouts in a given execution context**

  <progress value="60" max="120">Single callout</progress>  60s for single callout

  <progress value="120" max="120">Single callout</progress> 120s for all callout in the context

- A **maximum message size of 3 MB** for a given callout request or response


###Timeliness

Timeliness is of significant importance in this pattern.

 In most cases:
 - The request is typically invoked from the **user interface**, therefore, the process shouldn't keep the user waiting.
 - Salesforce has a **configurable timeout** of up to **60 seconds** for calls from Apex.
 - Completion of the remote process should be executed in a timely manner to conclude within the Salesforce timeout limit and/or within user expectations.


### Security



Any call to a remote system must maintain :
  - confidentiality
  -  integrity
  - availability
  of the request.

  The following security considerations are specific to using Apex SOAP and HTTP calls in this pattern:

  - **One-way SSL** is enabled by default, but **two-way SSL** is supported with both **self-signed** and **CA-signed certificates** to maintain authenticity of both the client and server.

  - ***WS-Security is not currently supported by Salesforce.***

  - Where necessary, consider using **one-way hashes or digital signatures** using the Apex Crypto class methods to ensure **request integrity**.

  - Implement the appropriate **firewall** mechanisms to protect the remote system.




| Header One     | Header Two     |
| :------------- | :------------- |
| Apex **SOAP** callouts      | The endpoint must be capable of receiving a Web service call via HTTP. The endpoint must be accessible over the **public Internet** by Salesforce. Web service standards supported by Salesforce for Apex SOAP callouts are: **WSDL 1.1,  SOAP 1.1,  WSI-Basic Profile 1.1 - HTTP **     |
|Apex **HTTP** callouts|The endpoint must be capable of receiving HTTP calls. The endpoint must be accessible over the public Internet by Salesforce. Apex HTTP callouts can be used to call **RESTful** services using the standard **GET, POST, PUT, and DELETE** methods.|
