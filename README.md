mplane_http_transport
=====================

[![mPlane](http://www.ict-mplane.eu/sites/default/files//public/mplane_final_256x_0.png)](http://www.ict-mplane.eu/)

This library implements a nodejs module for transport of [mPlane](http://www.ict-mplane.eu/) informational elements over HTTPS.
All transaction can be secured using trusted certificates.

#REST API
The implementation has a limited set of  [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) set of messages.
In particular, for any of the mPlane informational elements, it is possible to Create and Read them between mPlane components.


##REGISTER
The register keyword is the Create message for an element.

###Capability
Use this message to register a list of capabilities.

    URL: /register/capability
    METHOD: POST
    BODY: json array containing the capabilities.
    ANSWER:
           + 200 OK. Operations completed. The body can contain a list of capabilityes not registered with some explanation
                    Example: {"label1":{registered:"no", reason:"..."} , "label1":{registered:"ok"}})
           + 400 not a capability/wrong format
           + 401 not authorized
           + 500 server error

###Result
Use this message to register a result.
    
    URL:/register/result
    METHOD:POST
    BODY: result come da to_dict
    ANSWER:
        + 200 OK
        + 400 not a result/wrong format
        + 401 not authorized
        + 403 unexpected result. Usually this is due to no specification known for this result.
        + 500 server error

##Specification
Use this message to register a specification for a component.
  
    URL: /register/specification
    METHOD: POST
    BODY: {DistinguishedName:{specification}}. The distinguished name of the component you need to register a specification for
    ANSWER:
        + 200 OK. The body will contain the receipt of the required specification
        + 400 not a specification/wrong format
        + 401 not authorized
        + 403 unrecognized specification. Usually this is due to no capability known for this specification.
        + 500 server error

##SHOW
These set of messages is implemented to read information from specific mPlane component. 

###Result
Use this message to show result from a receipt.
    
    URL:/show/result/
    METHOD:POST
    BODY: redemption of the specification you need to know results.
    ANSWER:
        + If the result is available
            200 OK + result 
        + If the Result is not available
            200 OK + receipt of the Specification
        + 401 not authorized
        + 403 unexpected redemption. Usually this is due to no Specification kwnon for the redeem provided.
        + 500 server error

###Capability
Use this message to show capability kwnon by a component.

        URL:/show/capability
        METHOD:GET
        BODY: empty
        ANSWER:
               + 200 OK. The body contains the capabilities in the format {DN1:[capability1, capability2],…}
               + 401 not authorized
               + 500 server error

###Specification
This Api is needed to show Specification registered on a component.

        URL:/show/specification
        METHOD:GET
        BODY: empty
        ANSWER:
               + 200 OK. The body contains an array of specifications. Component can filter Specifications from the provided DN.
               + 401 not authorized
               + 428 not registered. This message is returned if the component needs to have at least a registered capability from the Component. 
                    It is a way to inform all probes that a Supervisor lost all the capability (for example after a crash) and ask probes to register again. 
               + 500 server error