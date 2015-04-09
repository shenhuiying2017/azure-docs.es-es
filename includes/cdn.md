# Uso de la red CDN en Azure

La Red de entrega de contenido (CDN) de Azure ofrece a los desarrolladores una
solución global para entregar contenido de ancho de banda alto mediante el almacenamiento en caché de los blobs
y del contenido estático de las instancias de proceso de los nodos físicos de Estados
Unidos, Europa, Asia, Australia y Sudamérica. Para obtener una lista actualizada de
las ubicaciones de nodos de CDN, consulte [Ubicaciones POP de la Red de entrega de contenido de Azure (CDN)].

Esta tarea incluye los siguientes pasos:

* [Paso 1: Crear una cuenta de almacenamiento](#Step1)
* [Paso 2: Crear un nuevo extremo de la red CDN para una cuenta de almacenamiento](#Step2)
* [Paso 3: Obtener acceso a su contenido de la red CDN](#Step3)
* [Paso 4: Quitar contenido de la red CDN](#Step4)

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los datos de Azure se incluyen:

-   Mejor rendimiento y experiencia del usuario para los usuarios finales que están lejos de un origen de contenido y utilizan aplicaciones donde son necesarias muchas 'internet trips' para cargar el contenido
-   Gran distribución para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un acontecimiento como el lanzamiento de un producto.

Los clientes existentes de la red CDN ahora pueden usar la red CDN de Azure a través del [Portal de administración de Azure]. La red CDN es una característica complementaria a su suscripción y tiene un [plan de facturación] independiente.

<a id="Step1"> </a>
<h2>Paso 1: Crear una cuenta de almacenamiento</h2>

Use el siguiente procedimiento para crear una nueva cuenta de almacenamiento para una
suscripción de Azure. Las cuentas de almacenamiento proporcionan acceso a 
los servicios de almacenamiento de Azure. La cuenta de almacenamiento representa el nivel superior
del espacio de nombres para obtener acceso a todos los componentes servicios de almacenamiento de
Azure: servicios BLOB, servicios Cola y servicios Tabla. Para obtener más
información acerca de los servicios de almacenamiento de Azure, consulte [Almacenamiento
de Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx).

Para crear una cuenta de almacenamiento es preciso ser administrador
o coadministrador de servicios en la suscripción asociada.

> [AZURE.NOTE] Para obtener información acerca de la realización de esta operación mediante la
API de administración de servicios de Azure, consulte el tema [Crear cuenta de almacenamiento](http://msdn.microsoft.com/library/windowsazure/hh264518.aspx).

**Para crear una cuenta de almacenamiento para una suscripción de Azure**

1.  Inicie sesión en el [Portal de administración de Azure].
2.  En la esquina inferior izquierda, haga clic en **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **Servicios de datos**, haga clic en **Almacenamiento** y, a continuación en **Creación rápida**.

    Aparece el cuadro de diálogo **Crear cuenta de almacenamiento**.

    ![Create Storage Account][create-new-storage-account]

4. En el campo **URL**, escriba un nombre de subdominio. Esta entrada puede contener de 3 a 24 letras minúsculas y números.

    Este valor se convierte en el nombre de host del URI que se usa para
    tratar recursos de Blob, Cola o Tabla para la suscripción. Para
    direccionar un recurso contenedor en el servicio BLOB, se debe usar un
    URI con el siguiente formato, donde *&lt;StorageAccountLabel&gt;* indica
    el valor que se escribió en **Escriba la dirección URL**:

    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*

    **Importante:** la etiqueta de la dirección URL forma el subdominio del URI de
    la cuenta de almacenamiento y debe ser único en todos los servicios hospedados de 
    Azure.

	Este valor también se utiliza como nombre de esta cuenta de almacenamiento en el portal o en el acceso a esta cuenta mediante programación.

5.  En la lista desplegable **Región/grupo de afinidad**, seleccione una región o grupo de afinidad para la cuenta de almacenamiento. Seleccione un grupo de afinidad en lugar de una región si desea que sus servicios de almacenamiento estén en el mismo centro de datos con otros servicios de Microsoft Azure que utilice. Con esto se puede mejorar el rendimiento y no se generan cargos por concepto de salida.  

    **Nota:** para crear un grupo de afinidad, abra el área **Configuración** del Portal de administración, haga clic en **Grupos de afinidad** y haga clic en **Agregar un grupo de afinidad** o en **Agregar**. Además puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Microsoft Azure. Para obtener más información, consulte [Operaciones en grupos de afinidad].

6. En la lista desplegable **Suscripción**, seleccione la suscripción que se usará con la cuenta de almacenamiento.
7.  Haga clic en **Crear cuenta de almacenamiento**. El proceso de creación de la cuenta de almacenamiento podría tardar varios minutos en completarse.
8.  Para comprobar que la cuenta de almacenamiento se ha creado correctamente, verifique que la cuenta aparece en la lista de elementos de **Almacenamiento** con el estado **Conectado**.

<a id="Step2"> </a>
<h2>Paso 2: Crear un nuevo extremo de la red CDN para una cuenta de almacenamiento</h2>

Tras habilitar el acceso de la red CDN a una cuenta de almacenamiento o servicio hospedado todos
los objetos disponibles públicamente son aptos para el almacenamiento en caché perimetral de la red CDN. Si
modifica que esté almacenado actualmente en la memoria caché de la red CDN, el contenido nuevo
no estará disponible a través de la red CDN hasta que esta actualice su contenido
cuando expire el periodo de vida del contenido almacenado en la memoria caché.

**Para crear un nuevo extremo de una red CDN para una cuenta de almacenamiento**

1. En el [Portal de administración de Azure], en el panel de navegación, haga clic en **CDN**.

2. En la cinta, haga clic en **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **Servicios de aplicaciones**, a continuación **CDN** y, finalmente, **Creación rápida**.

3. En la lista desplegable **Dominio de origen**, seleccione en la lista de cuentas de almacenamiento disponibles la que creó en la sección anterior. 

4. Haga clic en el botón **Crear** para crear el nuevo extremo.

5. Una vez creado el extremo, aparecerá en la lista de extremos de la suscripción. La visualización de la lista muestra la URL que se debe utilizar para tener acceso al contenido en caché, así como al dominio de origen. 

	El dominio de origen es la ubicación desde la que la red CDN almacena el contenido
    en la caché. El dominio de origen puede ser una cuenta de almacenamiento o un servicio en la nube; en este ejemplo se usa una cuenta de almacenamiento. El contenido de almacenamiento se almacena en la memoria caché de los servidores perimetrales conforme a la configuración de control de la memoria caché que se haya especificado o a la heurística predeterminada de la red de almacenamiento en caché. Para obtener más información, consulte [Cómo administrar la caducidad del contenido de blobs en la Red de entrega de contenido (CDN) de Azure](http://msdn.microsoft.com/library/gg680306.aspx). 


    > [AZURE.NOTE] La configuración creada para el extremo no
    estará disponible de inmediato; el registro puede tardar hasta 60 minutos
    en propagarse por la red CDN. Es posible que los usuarios que intenten
    usar el nombre de dominio de la red CDN de forma inmediata reciban el código de estado 400
    (Solicitud incorrecta) hasta que el contenido esté disponible a través de la red CDN.

<a id="Step3"> </a>
<h2>Paso 3: Obtener acceso a su contenido de la red CDN</h2> 

Para obtener acceso al contenido almacenado en la memoria caché de la red CDN, use la URL de la red CDN que se le ha proporcionado en el portal. La dirección del blob en caché será similar a la siguiente:

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Paso 4: Eliminar su contenido de la red CDN</h2>

Si ya no desea almacenar en la memoria caché un objeto en la Red
de entrega de contenido (CDN) de Microsoft Azure, puede realizar alguno de los pasos siguientes:

-   En el caso de un blob de Azure, puede eliminar el blob del contenedor
    público.
-   Puede crear un contenedor privado en vez de público. Para obtener más información, consulte [Acceso restringido a contenedores y blobs](http://msdn.microsoft.com/library/dd179354.aspx).
-   Puede deshabilitar o eliminar el extremo de la red CDN con el Portal de
    administración.
-   Puede modificar su servicio hospedado para que deje de responder las solicitudes del
    objeto.

Los objetos almacenados en la memoria caché de la red permanecerán en la caché hasta que expire
el período de vida del objeto. Cuando expire el período de vida,
la red CDN comprobará si el extremo de la red CDN sigue siendo
válido y si es posible obtener acceso de forma anónima al objeto. Si no lo es,
el objeto dejará de almacenarse en caché.

El Portal de administración de Azure no admite actualmente la posibilidad de purgar contenido. Póngase en contacto con el [soporte técnico de Azure](http://azure.microsoft.com/support/options/) si necesita purgar inmediatamente el contenido. 

## Recursos adicionales

-   [Creación de un grupo de afinidad en Azure]
-   [Procedimiento: Administración de cuentas de almacenamiento en una suscripción de Azure]
-   [Acerca de la API de administración de servicios]
-   [Asignación del contenido de la red CDN a un dominio personalizado]

  [Crear una cuenta de almacenamiento]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
  [Ubicaciones de los nodos de la red CDN de Azure]: http://msdn.microsoft.com/library/windowsazure/gg680302.aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [plan de facturación]: /pricing/calculator/?scenario=full
  [Registro de un nombre de subdominio personalizado para obtener acceso a blobs en Azure]: http://msdn.microsoft.com/library/windowsazure/ee795179.aspx
  [Creación de un grupo de afinidad en Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Información general de la red CDN de Azure]: http://msdn.microsoft.com/library/windowsazure/ff919703.aspx
  [Procedimiento: Administración de cuentas de almacenamiento en una suscripción de Azure]: http://msdn.microsoft.com/library/windowsazure/hh531567.aspx
  [Acerca de la API de administración de servicios]: http://msdn.microsoft.com/library/windowsazure/ee460807.aspx
  [Asignación del contenido de la red CDN a un dominio personalizado]: http://msdn.microsoft.com/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Portal de administración anterior]: ../../Shared/Media/previous-portal.png

<!--HONumber=49-->