<!-- Output copied to clipboard! -->

<!-----
NEW: Check the "Suppress top comment" option to remove this info from the output.

Conversion time: 2.537 seconds.


Using this Markdown file:

1. Paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β29
* Wed Dec 30 2020 03:51:34 GMT-0800 (PST)
* Source doc: Tratamiento de Errores Plataforma OpenPass
* Tables are currently converted to HTML tables.
* This document has images: check for >>>>>  gd2md-html alert:  inline image link in generated source and store images to your server. NOTE: Images in exported zip file from Google Docs may not appear in  the same order as they do in your doc. Please check the images!


WARNING:
You have 2 H1 headings. You may want to use the "H1 -> H2" option to demote all headings by one level.

----->


<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 1; ALERTS: 3.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>
<a href="#gdcalert2">alert2</a>
<a href="#gdcalert3">alert3</a>

<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>



# Plataforma OpenPass


# Tratamiento de Errores


### Esquema de secuencias tratamiento de errores por componente



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.png "image_tooltip")


**Ante la falta de respuesta (Time Out), o la respuesta de un estado pendiente se recomienda inmediatamente accionar con el Get Transaction correspondiente y por intervalos de 5/10 segundos hasta obtener un estado final de transacción.**


### La plataforma OpenPass trata los errores de dos formas



*   Errores de validación de plataforma
*   Errores de mensajería - Transaccional anidada


### Errores de validación - Plataforma

OpenPass como plataforma transaccional tiene responsabilidades sobre los datos que administra, en tal sentido cuando se produce la activación de una validación de la plataforma OpenPass se genera la devolución de un error. 

Estos casos donde el informe del error es debido a un control de la plataforma OpenPass es posible identificar el error con la estructura que se detalla a continuación, estos errores se informan como HTTP 400.

Algunos errores que son responsabilidad de la plataforma OpenPass son: 



*   validación de usuario habilitada 			→ errorCode 105 
*   validación de saldo de la cuenta 		→ errorCode 301

Estos errores, como el resto de los errores identificados por la plataforma OpenPass siempre están tipificados por un errorType y un errorCode, algunos ejemplos: (**Ver anexo de tabla de códigos de error de validación de la plataforma OpenPass**)

HTTP Status 400

{

    "code": "2",

    "message": "request error",

    "data": {

        "messageText": "Usuario no habilitado",

        "errorCode": 105,

        "errorType": "Security"

    }

}

HTTP Status 400

{

    "code": "2",

    "message": "request error",

    "data": {

        "messageText": "Saldo virtual insuficiente.",

        "errorCode": 310,

        "errorType": "Business"

    }

}


### Errores de mensajería / Transacciones Anidadas

Si las validaciones iniciales realizadas por la plataforma OpenPass son superadas, se procede a realizar la petición a los distintos  productos. Los productos pueden ser ofrecidos por  agrupadores (hubs de proveedores) o en forma directa por cada proveedor en particular.

Dentro de la mensajería lo primero que se debe evaluar es el estado general de la transacción. Estos estados pueden ser :


    **approved **(operación exitosa)  y corresponde a un estado final de transacción. Ejemplificando en la primer secuencia del diagrama 


    **rejected **(operación cancelada) y corresponde a un estado final de transacción. Ejemplificado en la segunda secuencia del diagrama. 


    	**pending **(operación pendiente) y corresponde a un estado intermedio de transacción que finalmente derivará en un approved o rejected. El tiempo en que una transacción permanece en este estado es variable y depende del tipo de producto transaccionado. Ejemplificado en la segunda secuencia del diagrama.

**Ejemplo**

HTTP Status 200

    "state": "pending",

    "stateDetail": {

        "message": "Error de timeout [45]",

        "transactionType": "Sale"

    },

    "id": "2D060010153722F792D7",

    "number": 675870517,

    "created": "2020-09-18T14:04:57-03:00",

    "amount": 10.00000000,

    "description": "Recarga - 675870517",

    "state": "pending",

    "stateDetail": {

        "message": "Error de timeout [45]",

        "transactionType":** "Sale"**

    },




### Estados Transaccionales

Los estados  transaccionales están determinados por el proveedor ya sea éste un agrupador o el propio proveedor del producto . Dado que no existe un nomenclador general de códigos de estado acordado por todos los proveedores / producto y al ser este punto completamente heterogéneo y fuera del alcance de la plataforma  no existe un mapping de códigos cross provider por lo cual la plataforma OpenPass resolvió esta situación particular incluyendo el “mensaje o texto” tal cual fue detallado por el proveedor, agregándole la tipificación del tipo de transacción correspondiente a nivel de detalle de la transacción.

HTTP Status 200

{

"state": "rejected",

"stateDetail": {

        "message": "Error de timeout [45]",

        "transactionType":** "Sale"**

    },

}


```
Toda transacción que sea capaz de ser validada y procesada por la plataforma OpenPass será devuelta con Status 200 independientemente del estado transaccional de negocio.
```


Otra consideración que realiza la plataforma OpenPass para devolver el estado es al trabajar con transacciones anidadas, como puede ser el pago de un producto a través de una tarjeta de crédito. 

En este supuesto la petición original de la billetera determinará dos transacciones, una para realizar el débito de la tarjeta y otra para notificar el pago al producto. Esta situación puede provocar que alguna o ambas transacciones abiertas pueda sufrir un error, en ese supuesto la plataforma OpenPass tomará el primer error devuelvo y será éste el que finalmente sea devuelto al middleware.

Se aclara especialmente este punto dado que no se devuelve una colección o array de los errores transaccionales.




### Anexo de tabla de códigos de error de validación de la plataforma OpenPass



<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image2.png "image_tooltip")




<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image3.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image3.png "image_tooltip")

# Errores

The beginning of an awesome article...
