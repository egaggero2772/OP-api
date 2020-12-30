#Register – CVU - ALIAS**

Register.

** **

Mediante el método “_Register_” _(POST - /account/finalConsumerAccount/register_) se dan de alta cuentas en la Plataforma OP.

** **

El registro de cuentas se complementa con todas sus alternativas de agregado y modificación de datos mediante una serie de métodos asociados en caso de que se quiera realizar una carga de información del usuario de manera progresiva, es decir, realizar un alta de cuenta inicial con los datos mínimos y luego, según el cliente vaya necesitando/queriendo, irá agregando el resto de sus datos nominativos.

 

Por ej: email, phone, tributary-info, etc.

 

El método _Register_ es el método principal de la Plataforma OpenPass ya que el resto de los métodos y funciones de la plataforma necesitan contar con una cuenta creada en la Plataforma.

** **

**NOTA**: Siempre que el método “_Register_” devuelva un **OK**, significa que la cuenta **YA** fue creada en la Plataforma OP, con lo cual, en caso de volver a llamar al método con los mismos parámetros, el proceso devolverá un error por “_Cuenta Existente”_

Si bien la Plataforma OP permite manejar un ID de Cliente Externo (sería el ID mediante el cual quien consume los servicios de OP identifica sus cuentas), se recomienda de todas maneras persistir el ID de Cuenta de OP con el objetivo de mantener una mejor sincronización de datos entre plataformas, permitir un mejor troubleshooting, etc.



<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image1.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image1.png "image_tooltip")


**_ _**

**_ _**

**_ _**

**Proceso de Alta/Consulta de CVU y ALIAS**

** **

La plataforma OP cuenta con varios métodos relacionados al CVU de una cuenta.

 


        ·   	_POST - /bank-services/cvu_


        ·   	_GET - /bank-services/cvu_


        ·   	_PUT - /bank-services/cvu/alias_

 **_<span style="text-decoration:underline;"> </span>_**

**_<span style="text-decoration:underline;">POST - /bank-services/cvu</span>_**

** **


    Mediante este método se da de Alta el CVU asociado a una cuenta. Cabe aclarar que es requisito que la cuenta **EXISTA** en la Plataforma OpenPass y que el usuario ya cuente con el CUIT registrado en la plataforma.


     


    El proceso creará el CVU e intentará crear el ALIAS como un proceso orquestado dentro de la Plataforma OpenPass (siempre con el concepto de Best Effort) y basado en el patrón definido (ver detalle más abajo).


     


    Se pueden dar 3 situaciones diferentes:


     


            -    	Se crea el CVU y ALIAS: El método devolverá un **OK**, el valor del CVU y el ALIAS.


     


            -    	Se crea el CVU y ALIAS **NO**: Si por algún motivo se encontrara un error en el proceso de creación del Alias (de comunicación, falta de respuesta del BIND o cualquier otro tipo de error que imposibilite la creación del Alias) el mismo **NO** será devuelto en el response del método (el campo **_“{label}”_ NO** será devuelto).


         


        **IMPORTANTE**: En este caso, como el CVU **SI** pudo ser creado, el método devolverá un OK y el valor del CVU. Es decir, el hecho de poder o no generar el ALIAS, **NO** interfiere con la creación del CVU.


         


            -    	No se crea el CVU ni el ALIAS: El método devolverá **ERROR**.

 


    _Independientemente que es “recomendable” que el método se utilice solo para dar de Alta CVUs, el proceso de todas maneras valida, previamente a la creación del CVU, que la cuenta **NO** tenga uno previamente creado y asociado en la base de datos de la Plataforma OpenPass pudiendo darse 3 situaciones diferentes:_


     


            -    	CVU y ALIAS **NO** existen: El proceso funcionará según lo descrito anteriormente.


     


            -    	CVU existe y ALIAS **NO** existe: Por una cuestión de mantener constantemente una sincronización entre los diferentes repositorios de datos, el proceso en este caso funcionará igual que el proceso anterior validando con el API Bank correspondiente la creación del CVU y volverá a intentar dar de alta el ALIAS con las mismas consideraciones antes descritas.


         


            -    	CVU y ALIAS existen: En este caso, el proceso devuelve la información que tiene almacenada en las bases de datos de la Plataforma OP.


     

 


    Es altamente recomendado **NO** utilizar este método como “consulta” del CVU asociado a una cuenta. En ese caso se debe/recomienda utilizar el método.

**_<span style="text-decoration:underline;">GET - /bank-services/cvu</span>_**

** **


    Mediante este método se consulta el CVU y ALIAS asociado a una cuenta, siempre y cuando este haya sido creado previamente. La información se toma de las bases de datos de la Plataforma OpenPass.


     


    Como parte del proceso, se pueden dar diferentes situaciones en las cuales el método trabajará según se detalla a continuación:


     


        -    	CVU y ALIAS existentes: El método devolverá ambos valores en su response.

 


        -    	CVU existe y ALIAS **NO** existe: El proceso intentará crear el ALIAS como un proceso orquestado dentro de la Plataforma OpenPass (siempre con el concepto de Best Effort) y basado en el patrón definido (ver detalle más abajo).


         


        Si por algún motivo se encontrara un error en el proceso de creación del Alias (de comunicación, falta de respuesta del BIND o cualquier otro tipo de error que imposibilite la creación del Alias) el mismo **NO** será devuelto en el response del método (el campo **_“{label}”_** **NO** será devuelto)


     


        -    	CVU y ALIAS NO existen: El método devolverá “vacío” en su response y **NO** devolverá el atributo **_“{label}”._**


     

 

**_<span style="text-decoration:underline;">NOTA</span>_**: En todos los casos donde los procesos intenten crear un ALIAS asociado al CVU de una cuenta y este **NO** pueda ser generado, la Plataforma OP el campo **_“{label}”_ NO** será devuelto, caso contrario se devolverá el valor del ALIAS generado.

 

** **

** **

**_<span style="text-decoration:underline;">PUT - /bank-services/cvu/alias</span>_**

** **


    Mediante este método se da de alta el ALIAS asociado a un CVU de una cuenta. Cabe aclarar que es requisito que la cuenta y el CVU **EXISTAN** en la Plataforma OpenPass.


     


    Independientemente de que la cuenta tenga un ALIAS previamente generado o no, el proceso modificará el valor del ALIAS asociado a dicha cuenta tanto en sus bases de datos como en el API Bank correspondiente


    ** **


        ** **


        ** **

**_<span style="text-decoration:underline;"> </span>_**

**_<span style="text-decoration:underline;"> </span>_**



**_<span style="text-decoration:underline;">Patrón definido para la creación del ALIAS.</span>_**

** **

A continuación, se detalla el patrón definido de manera conjunta entre OP y FT para la creación del alias.

 

El mismo está basado en un esquema de reemplazo de variables y es configurado desde el backoffice de la plataforma y puede ser cambiado de acuerdo con la necesidad que se presente desde la opción administración de conectores para el correspondiente al BIND, tal como se muestra en la siguiente figura.:



<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image2.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image2.png "image_tooltip")


 

Está conformado por un “aliasTemplate” y un “aliasDomain” opcional

 

El patrón del “aliasTemplate” se podrá formar mediante la combinación de 4 variables opcionales que en su totalidad no deberán sumar los 20 caracteres.

 

Las variables disponibles para el patrón son:

 


    %1 - Primera letra nombre 


    %2 - Primer Nombre Completo 


    %3 - Primer letra apellido 


    %4 - Apellido completo 

 

Las 4 variables obtienen su valor de los datos de la Billetera. En caso de duplicidad, se agregara un número incremental en la última posición antes del aliasDomain (si estuviera definido)

 

A modo de ejemplo les incluyo como sería el patrón (en amarillo) y su aplicación en base a una billetera cuyo titular tiene como nombre **Juan**, apellidos **De los Palotes** y aliasDomain= **bhub**.  Lo primero que haremos antes de aplicar el patrón es eliminar todos los espacios del nombre y apellido y luego reemplazamos todo carácter no admisible en el estándar de CVU/CBU, como la letra ñ, por citar un ejemplo.

 

1. '%1%4.$aliasDomain' => jdelospalotes.bhub

2. '%2.%4.$aliasDomain' => juan.delospalot.bhub (Se auto-recortó el largo, hasta llegar a 20 caracteres)

3. ‘%2.%4.$aliasDomain” => juan.delospalo1.bhub (Alias incrementado por duplicidad)

**_<span style="text-decoration:underline;"> </span>_**



**_<span style="text-decoration:underline;">Posible caso de uso.</span>_**

**_<span style="text-decoration:underline;"> </span>_**

Si bien pueden existir varios flujos y/o casos de uso según se defina durante la etapa de creación del Producto, aquí detallamos unos de ellos (el más común) a modo de ejemplo.

 

En este caso se describe un posible “**_proceso orquestado”_** <span style="text-decoration:underline;">del lado de quien consume los servicios de OP</span> con la intención de dar de Alta la CUENTA, el CVU y el ALIAS dentro del mismo proceso.

 


        -    	**Alta de CUENTA**: _POST - /account/finalConsumerAccount/register_) para dar de alta una nueva cuenta.


        -    	**Alta de CVU**: _POST - /bank-services/cvu_ para dar de alta CVU y ALIAS.

 

En este caso, pueden darse las siguientes 3 situaciones diferentes:

 


        1. 	Exitosa y completa: Cuenta + CVU + Alias


        2. 	Exitosa y parcial (1): Cuenta + CVU (~~Alias~~)


        3. 	Exitosa y parcial (2): Cuenta (~~CVU~~ - ~~Alias~~)

                                                                                                                              

**NOTA**: En **TODOS** los casos, si el método “_Register_” termina **OK**, la cuenta **SERÁ** creada en la Plataforma OP. Más allá de que quien consuma los servicios lo trabaje de manera orquestada, para la Plataforma OP los métodos “_Registrer”_ y “_CVU”_ con procesos completamente independientes.

 


        **1.	Exitosa y completa: Cuenta + CVU + Alias**

 


    En este caso no es necesario tomar ninguna acción desde lo funcional y/o flujo.


    ** **

** **

<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image3.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image3.png "image_tooltip")


** **

** **

** **


        **2.**	**Exitosa y parcial (1): Cuenta + CVU + (~~Alias~~)**

** **


    En este caso no es necesario tomar ninguna acción desde lo funcional y/o flujo al momento del Alta de Cuenta dado que el Cliente puede operar con su Billetera ya que para eso solo es necesario contar con CVU.


     


    No tendría sentido penalizar el Alta de la Cuenta por no haber podido generar el Alias.


     


    De manera asincrónica, es decir, en cualquier otro momento, se puede volver a intentar generar el Alias mediante el método _(GET - /bank-services/cvu_) tal como se explicó previamente.


     


    Por ej: Dentro de una sección de Perfil de Usuario o Datos Financieros, al momento de mostrar el CVU, se debe llamar a dicho método y el mismo proceso intentará volver a crear el Alias en caso de no encontrarlo en la base de la Plataforma OP.


     


     

<p id="gdcalert4" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image4.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert5">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image4.png "image_tooltip")



     


    **NOTA**: SI bien el ALIAS **NO** pudo ser generado en el momento del Alta de Cuenta y CVU, **NO** es necesario ni recomendable volver a realizar un POST de CVU, sino que se DEBE realizar un GET de CVU y nunca volver a llamar al “_Register”_ ya que la cuenta **EXISTE** en la Plataforma OP.


        **3.**	**Exitosa y parcial (2): Cuenta (~~CVU~~ - ~~Alias~~)**

** **


    En este caso se debe tener en cuenta que el Cliente **NO** puede operar con su Billetera ya que en este caso falló la creación del CVU, pero **SÍ es** importante tener en cuenta que la Cuenta **SI** fue creada en la Plataforma OP.


     


    NOTA: Si bien el CVU y ALIAS **NO** pudieron ser generados en el momento del Alta de Cuenta, **NO** se debe volver a llamar al “_Register”_ ya que la cuenta **EXISTE** en la Plataforma OP.


     


    En este punto se deberá evaluar desde Producto y/o UX/UI como se generará el CVU y ALIAS en una instancia posterior y/o desde que opción de menú y/o funcionalidad.


     


    Llegado dicho momento, se debe llamar al método _(POST - /bank-services/cvu_) el cual intentará generar el CVU y el Alias según se describió anteriormente.


    

<p id="gdcalert5" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/image5.png). Store image on your image server and adjust path/filename/extension if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert6">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/image5.png "image_tooltip")

# register_cvu

The beginning of an awesome article...
