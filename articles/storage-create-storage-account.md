<properties urlDisplayName="How to create" pageTitle="Creación, administración o eliminación de una cuenta de almacenamiento | Azure" metaKeywords="" description="Learn how to create, manage, or delete a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="tamram" />


# Acerca de las cuentas de Almacenamiento de Azure

Una cuenta de almacenamiento de Azure es una cuenta segura que le brinda acceso a los servicios de Almacenamiento de Azure. Su cuenta de almacenamiento brinda el espacio de nombres único para los datos y, de manera predeterminada, está disponible solo para usted, el propietario de la cuenta. 

Existen dos tipos de cuentas de almacenamiento:

- Una cuenta de Almacenamiento estándar incluye almacenamiento de Blob, Tabla y Cola. El almacenamiento de archivos está disponible por solicitud mediante la [página Vista previa de Azure](/en-us/services/preview/).
- Una cuenta de Almacenamiento premium actualmente solo admite discos de Máquina virtual de Azure. El Almacenamiento premium de Azure está disponible por solicitud mediante la [página Vista previa de Azure](/en-us/services/preview/). Consulte [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de Máquina virtual de Azure](http://go.microsoft.com/fwlink/?LinkId=521898) para obtener información general detallada del Almacenamiento premium.

El uso de Almacenamiento de Azure se le factura según su cuenta de almacenamiento. Los costos de almacenamiento se basan en cuatro factores: capacidad de almacenamiento, esquema de replicación, transacciones de almacenamiento y salida de datos. 

- La capacidad de almacenamiento se refiere a cuánto de la asignación correspondiente a cuentas de almacenamiento utiliza para almacenar datos. El coste de simplemente almacenar los datos está determinado por la cantidad de datos que almacena y la manera en que se replican. 
- La replicación determina cuántas copias de los datos se mantienen a la vez, además de la ubicación en que se realiza. 
- Las transacciones se refieren a todas las operaciones de lectura y escritura en Almacenamiento de Azure. 
- La salida de los datos se refiere a los datos transferidos fuera de una región de Azure. Cuando una aplicación que no está en ejecución en la misma región tiene acceso a los datos en su cuenta de almacenamiento, se le cobra por la salida de los datos, independientemente de si la aplicación es un servicio en la nube u otro tipo de aplicación. (En el caso de los servicios de Azure, puede llevar a cabo pasos para agrupar sus datos y servicios en los mismos centros de datos para reducir o eliminar los cargos por concepto de salida de los datos).  

La página [Detalles de precios de almacenamiento](http://www.windowsazure.com/en-us/pricing/details/#storage) proporciona información detallada sobre los precios de la capacidad, replicación y transacciones de almacenamiento. La página [Detalles de precios de Transferencia de datos](http://www.windowsazure.com/en-us/pricing/details/data-transfers/) proporciona información detallada de los precios por salida de datos.

Para obtener más información acerca de la capacidad de la cuenta y los objetivos del rendimiento, consulte [Objetivos de escalabilidad y rendimiento de Almacenamiento de Azure](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx).

> [WACOM.NOTE] Cuando crea una máquina virtual de Azure, se crea automáticamente una cuenta de almacenamiento en la ubicación de implementación si todavía no tiene una cuenta de almacenamiento en esa ubicación. Por lo tanto, no es necesario que siga los pasos que aparecen a continuación para crear una cuenta de almacenamiento para los discos de la máquina virtual. El nombre de la cuenta de almacenamiento se basará en el nombre de la máquina virtual. Consulte la [documentación de Máquinas virtuales de Azure](/en-us/documentation/services/virtual-machines/) para obtener más detalles. <br />

## Tabla de contenido ##

Este artículo describe cómo crear una cuenta de almacenamiento estándar y algunas decisiones que debe considerar cuando la crea. También describe cómo administrar las claves de acceso a la cuenta de almacenamiento y cómo eliminar una cuenta de almacenamiento.


- [Creación de una cuenta de almacenamiento](#create)
- [Visualización, copia y regeneración de claves de acceso de almacenamiento](#regeneratestoragekeys)
- [Eliminación de una cuenta de almacenamiento](#deletestorageaccount)
* [Pasos siguientes](#next)

## <a id="create"></a>Reproducción de una cuenta de almacenamiento ##

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Crear nuevo**, haga clic en **Almacenamiento** y, a continuación, en **Creación rápida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. En **Dirección URL**, escriba un nombre para la cuenta de almacenamiento. Consulte [Extremos de la cuenta de almacenamiento](#account-endpoints) que aparece a continuación para ver detalles sobre cómo se usará este nombre para abordar los objetos que se almacenan en Almacenamiento de Azure.

4. En **Ubicación/grupo de afinidad**, seleccione una ubicación para la cuenta de almacenamiento cerca suyo o de sus clientes. Si se tendrá acceso a los datos de la cuenta de almacenamiento desde otro servicio de Azure, como un servicio en la nube o una máquina virtual de Azure, es posible que desee seleccionar un grupo de afinidad desde la lista para agrupar la cuenta de almacenamiento en el mismo centro de datos con otros servicios de Azure que está usando para mejorar el rendimiento y disminuir los costos. 

	> [WACOM.NOTE] Tenga en cuenta que debe seleccionar un grupo de afinidad cuando se crea la cuenta de almacenamiento; no puede mover una cuenta existente a un grupo de afinidad.

	Para obtener detalles acerca de los grupos de afinidad, consulte [Colocalización de servicio con un grupo de afinidad](#affinity-group) que aparece a continuación.

	
5. Si tiene más de una suscripción a Azure, aparecerá el campo **Suscripción**. En **Suscripción**, escriba la suscripción a Azure con la cual desea usar la cuenta de almacenamiento. Puede crear hasta cinco cuentas de almacenamiento para una suscripción.

6. En **Replicación**, seleccione el nivel de replicación deseado para la cuenta de almacenamiento. La opción de replicación recomendada es Replicación con redundancia geográfica, que proporciona el máximo de durabilidad para los datos. Para obtener más detalles sobre las opciones de replicación de Almacenamiento de Azure, consulte [Opciones de replicación de cuentas de almacenamiento](#replication-options) que aparece a continuación.

6. Haga clic en **Crear cuenta de almacenamiento**.

	Su cuenta de almacenamiento puede tardar unos minutos en crearse. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal. Una vez que se ha creado la cuenta de almacenamiento, la nueva cuenta de almacenamiento tiene un estado **En línea** y está lista para usarse. 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>Extremos de la cuenta de almacenamiento 

Cada objeto que almacene en Almacenamiento de Azure tiene una dirección URL única; el nombre de la cuenta de almacenamiento forma el subdominio de esa dirección. El subdominio, en conjunto con el nombre de dominio que es específico para cada servicio, forman un *extremo* para la cuenta de almacenamiento. 

Por ejemplo, si la cuenta de almacenamiento se llama *micuentadealmacenamiento*, los extremos predeterminados de la cuenta de almacenamiento son: 

- Servicio BLOB: http://*mystorageaccount*.blob.core.windows.net

- Servicio Tabla: http://*mystorageaccount*.table.core.windows.net

- Servicio Cola: http://*mystorageaccount*.queue.core.windows.net

- Servicio Archivo: http://*mystorageaccount*.file.core.windows.net

Puede ver los extremos de la cuenta de almacenamiento en el Panel de almacenamiento del Portal de administración de Azure una vez que se crea la cuenta.

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

También puede configurar un nombre de dominio personalizado para usar con la cuenta de almacenamiento. Consulte [Configuración de un nombre de dominio personalizado para datos Blob en una cuenta de almacenamiento](../storage-custom-domain-name/) para obtener detalles.

### <a id="affinity-group"></a>Colocalización de servicios con un grupo de afinidad 

Un *grupo de afinidad* es un grupo geográfico de los servicios de Azure y máquinas virtuales con la cuenta de almacenamiento de Azure. Un grupo de afinidad puede mejorar el rendimiento del servicio al ubicar cargas de trabajo de equipos en el mismo centro de datos o cerca de la audiencia de usuarios de destino. Además, no se aplicarán costes de facturación en la salida cuando el acceso a los datos de una cuenta de almacenamiento tenga lugar desde otro servicio que forma parte del mismo grupo de afinidad.

> [WACOM.NOTE]  Para crear un grupo de afinidad, abra el área <b>Configuración</b> del Portal de administración, haga clic en <b>Grupos de afinidad</b>y, a continuación, haga clic en <b>Agregar un grupo de afinidad</b> o en el botón <b>Agregar</b> . Además puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Azure. Consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx">Operaciones en grupos de afinidad</a> para obtener más información.


### <a id="replication-options"></a>Opciones de replicación de la cuenta de almacenamiento

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>Reproducción de copiado y regeneración de claves de acceso de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

> [WACOM.NOTE] Se recomienda no compartir con nadie las claves de acceso de la cuenta de almacenamiento. Para permitir el acceso a los recursos de almacenamiento sin proporcionar sus claves de acceso, puede usar una *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso de su cuenta durante un intervalo que defina y con los permisos que especifique. Consulte el [tutorial de firma de acceso compartido](../storage-dotnet-shared-access-signature-part-1/) para obtener más información.

En el [Portal de administración](http://manage.windowsazure.com), use **Administrar claves** en el panel o la página **Almacenamiento** para ver, copiar y regenerar las claves de acceso de almacenamiento que se usan para tener acceso a los servicios de Blob, Tabla y Cola. 

### Copia de una clave de acceso de almacenamiento ###

Puede usar **Administrar claves** para copiar una clave de acceso de almacenamiento para usarla en una cadena de conexión. La cadena de conexión requiere el uso del nombre de la cuenta de almacenamiento y una clave en la autenticación. Para obtener información sobre la configuración de cadenas de conexión para tener acceso a los servicios de almacenamiento de Azure, consulte [Configuración de las cadenas de conexión](http://msdn.microsoft.com/en-us/library/ee758697.aspx).

1. En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Almacenamiento** y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Administrar claves**.

 	Se abre **Administrar claves de acceso**.

	![Managekeys](./media/storage-create-storage-account/Storage_ManageKeys.png)

 
3. Para copiar una clave de acceso de almacenamiento, seleccione el texto de la clave. Posteriormente, haga clic en **Copiar**.

### Regeneración de las claves de acceso de almacenamiento ###
Debe cambiar las claves de acceso de su cuenta de almacenamiento periódicamente para ayudar a mantener más seguras las conexiones de almacenamiento. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso. 

<div class="dev-callout"> 
    <b>Advertencia</b> 
    <p>La nueva generación de sus claves de acceso afecta a las máquinas virtuales, los servicios multimedia y cualquier aplicación que dependa de la cuenta de almacenamiento. Todos los clientes que usan la clave de acceso para acceder a la cuenta de almacenamiento deben estar actualizados para usar la nueva clave.
    </p> 
    </div>

**Máquinas virtuales**: si su cuenta de almacenamiento contiene una máquina virtual que se está ejecutando, tendrá que volver a implementar todas las máquinas virtuales después de regenerar las claves de acceso. Para evitar una nueva implementación, apague las máquinas virtuales antes de generar de nuevo las claves de acceso.
 
**Servicios multimedia**: si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las claves de acceso con los servicios multimedia después de regenerar las claves.
 
**Aplicaciones**: si tiene aplicaciones web o servicios en la nube que usan la cuenta de almacenamiento, perderá las conexiones si regenera las claves, a menos que las convierta. A continuación se muestra el proceso:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento. 

2. Vuelva a generar la clave de acceso primaria para su cuenta de almacenamiento. En el [Portal de administración](http://manage.windowsazure.com), en el panel o en la página **Configuración**, haga clic en **Administrar claves**. Haga clic en **Regenerar** bajo la clave de acceso principal y, a continuación, haga clic en **Sí** para confirmar que desea generar una clave nueva.

3. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

4. Vuelva a generar la clave de acceso secundaria.

## <a id="deletestorageaccount"></a>Reproducción de una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no utiliza, use **Eliminar** en el panel o la página **Configurar**. **Eliminar** elimina toda la cuenta de almacenamiento, incluidos todos los blobs, tablas y colas de la cuenta. 

<div class="dev-callout">
	<b>Advertencia</b>
	<p>No hay forma de restaurar el contenido de una cuenta de almacenamiento eliminada. Asegúrese 
	de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta.
	</p>
	<p>
	Si su cuenta de almacenamiento contiene archivos o discos VHD para una máquina virtual de Azure 
	, debe eliminar entonces las imágenes y los discos que usen esos archivos VHD 
	antes de eliminar la cuenta de almacenamiento. Primero, detenga la máquina virtual si se está ejecutando y, a continuación, elimínela. Para eliminar los discos, diríjase a la pestaña Discos y elimine todos los discos que se encuentran en la cuenta de almacenamiento. Para eliminar imágenes, diríjase a la pestaña Images y elimine todas las imágenes almacenadas en la cuenta.
	</p>
</div>


1. En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Almacenamiento**.

2. Haga clic en cualquier lugar de la entrada de la cuenta de almacenamiento, excepto en el nombre y, a continuación, haga clic en **Eliminar**.

	 -O bien-

	Haga clic en el nombre de la cuenta de almacenamiento para abrir el panel y, a continuación, haga clic en **Eliminar**.

3. Haga clic en **Sí** para confirmar que desea eliminar la cuenta de almacenamiento.

## <a id="next"></a>Pasos siguientes

- Para obtener más información acerca del Almacenamiento de Azure, consulte la documentación sobre Almacenamiento de Azure en [azure.com](http://azure.microsoft.com/en-us/documentation/services/storage/) y en [MSDN](http://msdn.microsoft.com/en-us/library/gg433040.aspx). 

- Visite el [blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).


 

<!--HONumber=35_1-->
