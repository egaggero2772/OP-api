# Tratamiento de Errores


### Esquema de secuencias tratamiento de errores por componente



![](../assets/images/secuencias_tratamiento_de_errores_por_componente.png)


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




![alt_text](../assets/images/tabla_de_codigos_de_error_1.png)
![alt_text](../assets/images/tabla_de_codigos_de_error_2.png)
