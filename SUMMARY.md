# Summary

* [Introduction](README.md)
  * [Integration  Patterns and Best Practices](./integration-patterns-bp/index.md)
    * [Request and Reply](./integration-patterns-bp/req-res.md)


    - The user initiates an action on the Visualforce page (for example, clicks a button).
    - The browser performs an HTTP POST that in turn performs an action on the corresponding Apex controller.
    - The controller calls a previously-generated Apex Web service proxy class.
    - The proxy class performs the actual call to the remote Web service.
    - The response from the remote system is returned to the Apex controller, which then processes the response, updates any data in Salesforce as required, and re-renders the page.
-
    
