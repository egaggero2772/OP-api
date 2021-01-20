# Recargas


## Introducción

 

El presente documento describe el uso de las APIs para poder realizar distintos tipos de Recargas desde la Plataforma OpenPass. 

Todos los servicios expuestos están creados con la finalidad de que se puedan acceder de una manera simple, ágil y unificada independientemente del proveedor con el que se procese la transacción según las reglas de negocio definidas. 

Dado que la Plataforma OpenPass se conecta con diferentes proveedores de servicios de pago y recargas (Rapipago, Pagomiscuentas, CobreExpress, Telerecargas, etc), y cada uno de ellos tiene diferentes procesos, servicios, flujos, criterios y circuitos para realizar las operaciones correspondientes, lo que se hace es unificar y consolidar todos esas diferencias entre los proveedores dentro de la Plataforma OpenPass y se exponen servicios propios del Plataforma. 

Es decir, quien se integra con la Plataforma OpenPass no debe preocuparse por las diferencias entre proveedores, eso lo resuelve todo la Plataforma internamente. Solo debe integrarse con los servicios expuestos por OpenPass y la misma plataforma resuelve cómo interactuar con cada proveedor según sea definido en las reglas de negocio.

Debido a que la Plataforma OpenPass se encuentra en constante evolución, ya sea por mejoras incorporadas, nuevas funcionalidades, cambios normativos y/o cambios en los proveedores y con el afán de lograr una documentación actualizada, los detalles técnicos de cada uno de los métodos y sus atributos se encuentran en la herramienta swagger.

 

**_ https://app.swaggerhub.com/apis/OpenPass-AR/Bhub-FinTech3.0/1.0.0#/_**

 



Resumen

 

_Recargas_

 

La Plataforma OpenPass ofrece la posibilidad de realizar, a través de servicios (APIs), distintos tipos de Recargas. Por ej::

 



*   Recargas de Telefonía Celular
*   Servicios de Transporte (SUBE, RedBus, etc)
*   Etc.

 

Tal como mencionamos antes, el objetivo de la Plataforma OpenPass es unificar los diferentes servicios de los distintos proveedores brindando un único método mediante el cual se pueden realizar los diferentes tipos de Recargas.

Lo que va a cambiar entre una Recarga y otra, son los datos particulares de cada una de ellas, los cuales deberán ser incluídos dentro del atributo _transactionData._

Algo importante a tener en cuenta es que previo a llamar al método para realizar Recargas, es necesario identificar el Código de Producto al cual se le quiere realizar una recarga.

La lista de Productos disponibles para poder realizarles recargas, se obtiene mediante el método:

**_GET /sales/topup/products_**

Una vez obtenido e identificado el Producto a cargar, se debe utilizar el Código del Producto para llamar al método mediante el cual se realizan las Transacciones de Recarga: **_POST /sales/topup_**

En el request se deberá indicar, el Importe a Recargar, el Id de Producto y el contenido del atributo _transactionData _el cual variará dependiendo el Producto a Recargar.



*   **Recarga de Telefonía Celular**: Para recargas de abonados de números telefónicos (Ej: Movistar, Claro, Personal, Tuenti y Nextel): 

    Se informa el país, el código de área, sin incluir el 0 y el número de línea. (Si no se informa el prefijo de país, se asume +54, Argentina) 


        Ej 1: Área 011 y número 12345678: se informa: _transactionData_:1112345678 


        Ej 2: Área 02965 y número 123456: se informa: _transactionData_: 2965123456 


    

*   **Recargas por Documento**. Para recargas de productos que requieren tipo y número de documento, (Ej: GranDT y Ole) 

    Para la venta de estos productos se debe informar el código de tipo de documento (DNI = 1, LC = 2 y LE = 3) con el número de documento. 


        Ejemplo: 


        DNI 12.345.678: se informa: _transactionData: _112345678 


        LE 123.456: se informa: _transactionData_: 3123456 

*   **Recargas por Código**:  Para recargas que requieren un código asociado al abonado (Ej: DirectTv Prepago, Edenor Prepago, SUBE Diferido) 

    Para la venta de estos productos se debe informar el código del abonado sin ningún tipo de formato. 


        Ejemplo para DirectV: 


        123456789123-456789: se informa: _transactionData_: 123456789123456789 


        Ejemplo para Edenor Prepago: 


        07012345678: se informa: _transactionData_: 07012345678 (corresponde al nro. de medidor) 


        Ejemplo para SUBE Diferido: 


        1234567891234567: se informa: _transactionData_: 1234567891234567 (corresponde al nro. de tarjeta de 16 dígitos) 


 <span style="text-decoration:underline;">Consideraciones</span>:


    En el atributo _clientTransactionId_ se debe enviar el ID de la Transacción de quien esté llamando al método. De esta manera se podrá utilizar para identificar la Transacción dentro de la Plataforma OpenPass cuando sea necesario. 


    Por ej: en caso que se corte la comunicación en la respuesta del llamado inicial. en este caso, no se podrá saber si la transacción fue exitosa o no, ya que no se tuvo respuesta y por ende no se obtuvo en_ confirmationId._


    En este caso se debe consultar el estado de la Transacción mediante el método_ **GET** /sale/{id}_ donde id = _clientTransactionId_

 

_Estados de respuesta posibles de una Transacción _

Al igual que el resto de las transacciones gestionadas por la Plataforma OpenPass, los criterio utilizados para el Tratamiento de Errores corresponden a los detallados en la sección **_<span style="text-decoration:underline;">"Tratamiento de Errores Plataforma OpenPass"</span>_**
