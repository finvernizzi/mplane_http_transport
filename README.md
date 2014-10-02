mplane_http_transport
=====================

[![mPlane](http://www.ict-mplane.eu/sites/default/files//public/mplane_final_256x_0.png)](http://www.ict-mplane.eu/)

This library implements a nodejs module for transport of [mPlane](http://www.ict-mplane.eu/) informational elements over HTTPS.
All transaction can be secured using trusted certificates.

#REST API
The implementation has a limited set of  [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) set of messages.
In particular, for any of the mPlane informational elements, it is possible to Create and Read them between mPlane components.


#REGISTER
The register keyword is the Create message for an element.

##Capability
 > URL: /register/capability
 > METHOD: POST
 > BODY: Array json di capability (ogni capability [ output del to_dict della libreria])
 > ANSWER:
                               + 200 OK (nel body della risposta array json delle label effettivamente registrate. ES: {"label1":{registered:"no", reason:"..."} , "label1":{registered:"ok"}})
                               + 400 not a capability/wrong format                      /// SE HO PIU CAPABILITY DI CUI UNA NON VALIDA? mando il 200 ok con l’elenco???
                               + 401 not authorized
                               + 500 server error


Registrazione di un result. Fatto da un component
  URL:/register/result
                METHOD:POST
                BODY: result come da to_dict
                ANSWER:
                               + 200 OK
                               + 400 not a result/wrong format
                               + 401 not authorized
                               + 403 unexpected result (non esiste una specification per questo result)
                               + 500 server error


Registrazione di una specification. Fatto dal client
  URL: /register/specification
  METHOD: POST
  BODY: {DistinguishedName:{specification}}
  ANSWER:
        + 200 OK + receipt relativa alla specification richiesta
        + 400 not a specification/wrong format
        + 401 not authorized
        + 403 unrecognized specification (non esiste la capability richiesta)
        + 500 server error


Richiesta di result (fatto da un client).
    URL:/show/result/
    METHOD:POST
    BODY: redemption relativa al result che si vuole ottenere
    ANSWER:
        + Se la misurazione è completa:
            200 OK + result richiesto
        + Se la misurazione è ancora in corso:
            200 OK + receipt (la stessa inviata quando la specification è stata richiesta)
        + 401 not authorized
        + 403 unexpected redemption (non esiste una misura in corso per questa redemption)
        + 500 server error

Richiesta di capability (fatto da un client).
                URL:/show/capability
                METHOD:GET
                BODY: - vuota
                ANSWER:
                               + 200 OK + {DN1:[capability1, capability2],…}
                               + 401 not authorized
                               + 500 server error

Richiesta di specification (fatto da un client e da component).
                URL:/show/specification
                METHOD:GET
                BODY: - vuota
                ANSWER:
                               + 200 OK + array di specification (filtro sulla base del DN richiedente: solo le psecification relative alle sue capability)
                               + 401 not authorized
                               + 428 not registered
                               + 500 server error

Richiesta della coda di specification (fatto da un client).
                URL:/show/queue
                METHOD:GET
                BODY: - vuota
                ANSWER:
                               + 200 OK + array di specification. ES: {dn1:[specifications], dn2:[specifications]}
                               + 401 not authorized
                               + 500 server error




