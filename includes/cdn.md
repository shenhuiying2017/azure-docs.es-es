# Uso de la red CDN en Azure

La Red de entrega de contenido de Azure (CDN) ofrece a los desarrolladores una solución global de entrega de contenido con alto ancho de banda; para ello, almacena en memoria caché los blobs y los contenidos estáticos de las instancias de proceso en nodos físicos ubicados en Estados Unidos, Europa, Asia, Australia y Sudamérica. Para obtener una lista actualizada de las ubicaciones de nodos de la red CDN, consulte [Ubicaciones del nodo red CDN de Azure].

Esta tarea incluye los siguientes pasos:

* [Paso 1: Crear una cuenta de almacenamiento](#Step1)
* [Paso 2: Crear un nuevo extremo de la red CDN para su cuenta de almacenamiento](#Step2)
* [Paso 3: Obtener acceso a su contenido de la red CDN](#Step3)
* [Paso 4: Eliminar contenido de la red CDN](#Step4)

Entre las ventajas de utilizar la red CDN para almacenar en memoria caché los datos de Azure se incluyen:

-   Mejor rendimiento y experiencia de usuario para aquellos usuarios finales que están lejos de un origen de contenido y utilicen aplicaciones donde son necesarias muchas conexiones a Internet para cargar el contenido.
-   Gran distribución para mejorar la administración de cargas instantáneas pesadas, por ejemplo, al comienzo de un acontecimiento como el lanzamiento de un producto.

Los clientes existentes de la red CDN ahora pueden usar la red CDN de Azure en el [Portal de administración de Azure]. La red CDN es una solución complementaria a su suscripción y tiene un [plan de facturación] independiente.

<a id="Step1"> </a>
<h2>Paso 1: Crear una cuenta de almacenamiento</h2>

Use el siguiente procedimiento para crear una nueva cuenta de almacenamiento para una suscripción de Azure. Una cuenta de almacenamiento proporciona acceso a los servicios de almacenamiento de Azure. La cuenta de almacenamiento representa el máximo nivel de espacio de nombres para tener acceso a todos los componentes del servicio de almacenamiento de Azure: servicios Blob, servicios Cola y servicios Tabla. Para obtener más información acerca de los servicios de almacenamiento de Azure, consulte [Uso de los servicios de almacenamiento de Azure](http://msdn.microsoft.com/en-us/library/azure/gg433040.aspx).

Para crear una cuenta de almacenamiento, debe ser administrador del servicio o coadministrador de la suscripción correspondiente.

<div class="dev-callout">
<strong>Nota:</strong>
<p>Para obtener información acerca de la realización de esta operación mediante la API de administración de servicios de Azure, consulte el tema de referencia <a href="http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx">Crear una cuenta de</a> almacenamiento.</p>
</div>

**Para crear una cuenta de almacenamiento para una suscripción de Azure**

1.  Inicie sesión en el [Portal de administración de Azure].
2.  En la esquina inferior izquierda, haga clic en **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **Servicios de datos**, haga clic en **Almacenamiento** y, a continuación, en **Creación rápida**.

    Se abre el cuadro de diálogo **Crear cuenta de almacenamiento**.

    ![Create Storage Account][create-new-storage-account]

4. En el campo **URL**, escriba el nombre de un subdominio. Esta entrada puede contener de 3 a 24 letras minúsculas y números.

    Este valor se convierte en el nombre del host dentro del URI que se ha usado para direccionar los recursos Blob, Cola o Tabla de la suscripción. Para dirigir un recurso contenedor en el servicio BLOB, debería usar un URI en el siguiente formato, en el que *&lt;StorageAccountLabel&gt;* hace referencia al valor que ha escrito en **Escriba una dirección URL**:

    http://*<StorageAcountLabel>*.blob.core.windows.net/*<mycontainer>*

    **Importante:** La etiqueta de URL forma el subdominio del URI de la cuenta de almacenamiento y debe ser único entre todos los servicios hospedados en Azure.

	Este valor también se utiliza como nombre de esta cuenta de almacenamiento en el portal o en el acceso a esta cuenta mediante programación.

5.  En la lista desplegable **Región/grupo de afinidad**, seleccione una región o grupo de afinidad para la cuenta de almacenamiento. Seleccione un grupo de afinidad en lugar de una región si desea que sus servicios de almacenamiento estén en el mismo centro de datos con otros servicios de Windows Azure que utilice. Con esto se puede mejorar el rendimiento y no se generan cargos por concepto de salida.  

    **Nota:** Para crear un grupo de afinidad, abra el área **Configuración** del Portal de administración, haga clic en **Grupos de afinidad** y, a continuación, haga clic en **Agregar un grupo de afinidad** o **Agregar**. Además puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Windows Azure. Para obtener más información, consulte [Operaciones en grupos de afinidad].

6. En la lista desplegable **Suscripción**, seleccione la suscripción con la que se usará la cuenta de almacenamiento.
7.  Haga clic en **Crear cuenta de almacenamiento**. El proceso de creación de la cuenta de almacenamiento podría tardar varios minutos en completarse.
8.  Para comprobar que la cuenta de almacenamiento se ha creado correctamente, verifique que la cuenta aparece en la lista de elementos de **Almacenamiento** con el estado de **Online**.

<a id="Step2"> </a>
<h2>Paso 2: Crear un nuevo extremo de la red CDN para su cuenta de almacenamiento</h2>

Una vez que haya habilitado el acceso de la red CDN a una cuenta de almacenamiento o servicio hospedado, todos los objetos disponibles de forma pública se pueden almacenar en la memoria caché perimetral de la red CDN. Si modifica un objeto que está almacenado en la memoria caché de la red CDN actualmente, el nuevo contenido no estará disponible a través de la red CDN hasta que la red CDN actualice su contenido al cumplir el período de vida del contenido almacenado en caché.

**Para crear un nuevo extremo de la red CDN para la cuenta de almacenamiento**

1. En el [Portal de administración de Azure], en el panel de navegación, haga clic en **CDN**.

2. En la cinta de opciones, haga clic en **Nuevo**. En el cuadro de diálogo **Nuevo**, seleccione **Servicios de aplicaciones**, **CDN** y, a continuación, **Creación rápida**.

3. En la lista desplegable **Dominio de origen**, seleccione la cuenta de almacenamiento que ha creado en la sección anterior a partir de la lista de cuentas de almacenamiento disponibles. 

4. Haga clic en el botón **Crear** para crear un extremo nuevo.

5. Una vez creado el extremo, aparecerá en la lista de extremos de la suscripción. La visualización de la lista muestra la URL que se debe utilizar para tener acceso al contenido en caché, así como al dominio de origen. 

	El dominio de origen es la ubicación desde la que la red CDN almacena el contenido en la memoria caché. El dominio de origen puede ser una cuenta de almacenamiento o un servicio en la nube; en este ejemplo se usa una cuenta de almacenamiento. El contenido de almacenamiento se almacena en la memoria caché de los servidores perimetrales conforme a la configuración de control de la memoria caché que se haya especificado o a la heurística predeterminada de la red de almacenamiento en caché. Consulte [Administración de la caducidad del contenido de blobs](http://msdn.microsoft.com/en-us/library/gg680306.aspx) para obtener más información 


    <div class="dev-callout">
    <strong>Note</strong>
    <p>The configuration created for the endpoint will not
    immediately be available; it can take up to 60 minutes for the
    registration to propagate through the CDN network. Users who try to
    use the CDN domain name immediately may receive status code 400
    (Bad Request) until the content is available via the CDN.</p>
    </div>

<a id="Step3"> </a>
<h2>Paso 3: Obtener acceso a su contenido de la red CDN</h2> 

Para obtener acceso al contenido almacenado en la memoria caché de la red CDN, use la URL de la red CDN que se le ha proporcionado en el portal. La dirección del blob en caché será similar a la siguiente:

http://<*CDNNamespace*\>.vo.msecnd.net/<*myPublicContainer*\>/<*BlobName*\>

<a id="Step4"> </a>
<h2>Paso 4: Eliminar su contenido de la red CDN</h2>

Si ya no desea almacenar en la memoria caché un objeto en la Red
de entrega de contenido (CDN) de Microsoft Azure, puede realizar alguno de los pasos siguientes:

-   Para un blob de Azure, puede eliminar el blob desde el contenedor público. -   Puede crear un contenedor privado en vez de público. Consulte [Acceso restringido a contenedores y blobs](http://msdn.microsoft.com/en-us/library/dd179354.aspx) para obtener más información
-   Puede deshabilitar o eliminar el extremo de la red CDN con el Portal de administración.
-   Puede modificar su servicio hospedado para no seguir respondiendo las solicitudes del objeto.

Un objeto que ya está almacenado en la memoria caché de la red CDN permanecerá almacenado en caché hasta que cumpla el período de vida del objeto. Al cumplir el período de vida, la red CDN comprobará si el extremo de la red CDN sigue siendo válido y si el objeto sigue siendo accesible de forma anónima. Si no lo es, el objeto dejará de estar almacenado en caché.

El Portal de administración de Azure no admite actualmente la posibilidad de purgar contenido. Póngase en contacto con el [soporte técnico de Azure](http://azure.microsoft.com/en-us/support/options/)  si necesita purgar contenido inmediatamente. 

## Recursos adicionales

-   [Creación de un grupo de afinidad en Azure]
-   [Administración de cuentas de almacenamiento para una suscripción de Azure]
-   [Acerca de la API de administración de servicios]
-   [Asignación del contenido de la red CDN a un dominio personalizado]

  [Crear una cuenta de almacenamiento]: http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx
  [Ubicaciones del nodo red CDN de Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680302.aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [plan de facturación]: /en-us/pricing/calculator/?scenario=full
  [Registro de un nombre de dominio personalizado para obtener acceso a blobs en Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/ee795179.aspx
  [Creación de un grupo de afinidad en Azure]: http://msdn.microsoft.com/library/azure/ee460798.aspx
  [Información general de la Red de entrega de contenido (CDN) de Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/ff919703.aspx
  [Administración de cuentas de almacenamiento para una suscripción de Azure]: http://msdn.microsoft.com/en-us/library/windowsazure/hh531567.aspx
  [Acerca de la API de administración de servicios]: http://msdn.microsoft.com/en-us/library/windowsazure/ee460807.aspx
  [Asignación del contenido de la red CDN a un dominio personalizado]: http://msdn.microsoft.com/en-us/library/windowsazure/gg680307.aspx


[create-new-storage-account]: ./media/cdn/CDN_CreateNewStorageAcct.png
[Portal de administración anterior]: ../../Shared/Media/previous-portal.png

<!--HONumber=35_1-->
