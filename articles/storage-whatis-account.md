<properties 
	pageTitle="Acerca de las cuentas de almacenamiento de Azure | Microsoft Azure" 
	description="Obtenga información acerca de cómo trabajar con cuentas de almacenamiento de Azure." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tamram"/>


# Acerca de las cuentas de almacenamiento de Azure

Una cuenta de almacenamiento de Azure es una cuenta segura que proporciona acceso a los servicios del Almacenamiento de Azure. Las cuentas de almacenamiento ofrecen un espacio de nombres exclusivo para los datos y, de forma predeterminada, se encuentra disponible solo para el usuario, que es el propietario de la cuenta. 

Existen dos tipos de cuentas de almacenamiento:

- Una cuenta de almacenamiento estándar incluye el almacenamiento de blobs, tablas, y colas. El almacenamiento de archivos se encuentra disponible bajo petición a través de la [página de vista previa de Azure](/es-es/services/preview/).
- Una cuenta de almacenamiento premium actualmente solo admite los discos de Máquinas virtuales de Azure. El almacenamiento premium de Azure se encuentra disponible bajo petición a través de la [página de vista previa de Azure](/es-es/services/preview/). Consulte [Almacenamiento premium: Almacenamiento de alto rendimiento para cargas de trabajo de máquinas virtuales de Azure](http://go.microsoft.com/fwlink/?LinkId=521898) para obtener una introducción detallada del almacenamiento Premium.

Se le facturará por el uso del Almacenamiento de Azure en función de la cuenta de almacenamiento. Los costes del almacenamiento se basan en cuatro factores: capacidad de almacenamiento, esquema de replicación, transacciones de almacenamiento y salida de datos. 

- La capacidad de almacenamiento se refiere a cuánto de la asignación correspondiente a cuentas de almacenamiento utiliza para almacenar datos. El coste de simplemente almacenar los datos está determinado por la cantidad de datos que almacena y la manera en que se replican. 
- La replicación determina cuántas copias de los datos se conservan al mismo tiempo, y en qué ubicaciones. 
- Las transacciones se refieren a todas las operaciones de lectura y escritura en Almacenamiento de Azure. 
- La salida de los datos se refiere a los datos transferidos fuera de una región de Azure. Cuando una aplicación que no está en ejecución en la misma región tiene acceso a los datos en su cuenta de almacenamiento, se le cobra por la salida de los datos, independientemente de si la aplicación es un servicio en la nube u otro tipo de aplicación. (En el caso de los servicios de Azure, puede llevar a cabo pasos para agrupar sus datos y servicios en los mismos centros de datos para reducir o eliminar los cargos por concepto de salida de los datos).  

La página [Detalles de precios de Storage](http://azure.microsoft.com/pricing/details/#storage) proporciona información detallada sobre los precios de la capacidad, replicación y transacciones de almacenamiento. La página [Detalles de precios de Transferencias de datos](http://azure.microsoft.com/pricing/details/data-transfers/) proporciona información detallada sobre los precios para la salida de datos.

Para obtener más información acerca de los objetivos de capacidad y rendimiento de la cuenta de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento del almacenamiento de Azure](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx).

> [AZURE.NOTE] Al crear una máquina virtual de Azure, se crea automáticamente una cuenta de almacenamiento en la ubicación de implementación si todavía no tiene una cuenta de almacenamiento en esa ubicación. Por tanto, no es necesario aplicar los pasos descritos a continuación para crear una cuenta de almacenamiento para los discos de máquinas virtuales. El nombre de la cuenta de almacenamiento se basará en el nombre de la máquina virtual. Consulte la [documentación de máquinas virtuales de Azure](/es-es/documentation/services/virtual-machines/) para obtener más detalles. <br />

## Tabla de contenido ##

En este artículo se describe cómo crear una cuenta de almacenamiento estándar, además de plasmar algunas decisiones que hay que tener en cuenta para crearla. También se describe cómo administrar las claves de acceso a la cuenta de almacenamiento y cómo eliminar una cuenta de almacenamiento.


- [Uso de una cuenta de almacenamiento](#create)
- [Uso de copiado y regeneración de claves de acceso de almacenamiento](#regeneratestoragekeys)
- [Uso de una cuenta de almacenamiento](#deletestorageaccount)
* [Pasos siguientes](#next)

## <a id="create"></a>Procedimiento: una cuenta de almacenamiento ##

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Crear nuevo**, en **Almacenamiento** y, a continuación, en **Creación rápida**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. En **Dirección URL**, escriba un nombre para la cuenta de almacenamiento. Consulte [Extremos de la cuenta de almacenamiento](#account-endpoints) a continuación para obtener información detallada sobre la forma en que se utilizará este nombre para dirigir objetos almacenados en el Almacenamiento de Azure.

4. En **Ubicación/Grupo de afinidad**, seleccione una ubicación para la cuenta de almacenamiento que esté próxima a usted o a los clientes. Si otro servicio de Azure va a obtener acceso a los datos de la cuenta de almacenamiento, como un servicio en la nube o una máquina virtual de Azure, debe seleccionar un grupo de afinidad en la lista para agrupar la cuenta de almacenamiento en el mismo centro de datos que otros servicios de Azure que utiliza, a fin de mejorar el rendimiento y reducir los costes. 

	> [AZURE.NOTE] Tenga en cuenta que debe seleccionar un grupo de afinidad al crear la cuenta de almacenamiento; no puede mover una cuenta existente a un grupo de afinidad.

	Para obtener información detallada sobre los grupos de afinidad, consulte [Coubicación de servicios con un grupo de afinidad](#affinity-group) que aparece a continuación.

	
5. Si tiene más de una suscripción a Azure, aparecerá el campo **Subscription**. En **Suscripción**, escriba la suscripción a Azure con la cual desea usar la cuenta de almacenamiento. Puede crear hasta cinco cuentas de almacenamiento para una suscripción.

6. En **Replicación**, seleccione el nivel deseado de replicación para la cuenta de almacenamiento. La opción de replicación recomendada es la replicación con redundancia geográfica, que ofrece la durabilidad máxima de los datos. Para obtener más información sobre las opciones de replicación del Almacenamiento de Azure, consulte [Opciones de replicación de la cuenta de almacenamiento](#replication-options) que aparece a continuación.

6. Haga clic en **Crear cuenta de almacenamiento**.

	Su cuenta de almacenamiento puede tardar unos minutos en crearse. Para revisar el estado, puede supervisar las notificaciones en la parte inferior del portal. Una vez que se ha creado la cuenta de almacenamiento, la nueva cuenta de almacenamiento tiene un estado **Online** y está lista para usarse. 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>Extremos de la cuenta de almacenamiento 

Cada objeto almacenado en el Almacenamiento de Azure tiene una dirección URL exclusiva; el nombre de la cuenta de almacenamiento forma el subdominio de dicha dirección. El subdominio junto con el nombre de dominio, que es específico de cada servicio, forman un  *extremo* para la cuenta de almacenamiento. 

Por ejemplo si la cuenta de almacenamiento se llama  *mystorageaccount*, los extremos predeterminados para la cuenta de almacenamiento son: 

- Servicio BLOB: http://*mystorageaccount*.blob.core.windows.net

- Servicio Tabla: http://*mystorageaccount*.table.core.windows.net

- Servicio Cola: http://*mystorageaccount*.queue.core.windows.net

- Servicio Archivo: http://*mystorageaccount*.file.core.windows.net

Puede ver los extremos de la cuenta de almacenamiento en el Panel de almacenamiento del Portal de administración de Azure después de haber creado la cuenta.

La dirección URL para el acceso a un objeto en una cuenta de almacenamiento se crea anexando la ubicación del objeto en la cuenta de almacenamiento al extremo. Por ejemplo, una dirección de blob podría tener el siguiente formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

También puede configurar un nombre de dominio personalizado para usar con la cuenta de almacenamiento. Consulte [Configuración de un nombre de dominio personalizado para datos Blob en una cuenta de almacenamiento](../storage-custom-domain-name/) para obtener información detallada.

### <a id="affinity-group"></a>Coubicación de servicios con un grupo de afinidad 

Un  *grupo de afinidad* es un grupo geográfico de los servicios de Azure y máquinas virtuales con la cuenta de almacenamiento de Azure. Un grupo de afinidad puede mejorar el rendimiento del servicio al ubicar cargas de trabajo de equipos en el mismo centro de datos o cerca de la audiencia de usuarios de destino. Además, no se aplicarán costes de facturación en la salida cuando el acceso a los datos de una cuenta de almacenamiento tenga lugar desde otro servicio que forma parte del mismo grupo de afinidad.

> [AZURE.NOTE]  Para crear un grupo de afinidad, abra el área <b>Configuración</b> del Portal de administración, haga clic en <b>Grupos de afinidad</b> y, a continuación, haga clic en el botón <b>Agregar un grupo de afinidad</b> o <b>Agregar</b>. Además puede crear y administrar los grupos de afinidad usando la API de administración de servicios de Azure. Consulte <a href="http://msdn.microsoft.com/library/windowsazure/ee460798.aspx">Operaciones en grupos de afinidad</a> para obtener más información.


### <a id="replication-options"></a>Opciones de replicación de la cuenta de almacenamiento

[AZURE.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>Procedimiento: copia y regeneración de claves de acceso de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

> [AZURE.NOTE] Se recomienda no compartir con nadie las claves de acceso de la cuenta de almacenamiento. Para permitir el acceso a los recursos de almacenamiento sin proporcionar sus claves de acceso, puede usar una  *firma de acceso compartido*. Una firma de acceso compartido proporciona acceso a un recurso de su cuenta durante un intervalo que defina y con los permisos que especifique. Consulte el [tutorial sobre las firmas de acceso compartido](../storage-dotnet-shared-access-signature-part-1/) para obtener más información.

En el [Portal de administración](http://manage.windowsazure.com), use **Manage Keys** en el panel o la página **Almacenamiento** para ver, copiar y regenerar las claves de acceso de almacenamiento que se usan para tener acceso a los servicios de Blob, Tabla y Cola. 

### Copia de una clave de acceso de almacenamiento ###

Puede usar **Administrar claves** para copiar una clave de acceso de almacenamiento para usarla en una cadena de conexión. La cadena de conexión requiere el uso del nombre de la cuenta de almacenamiento y una clave en la autenticación. Para obtener información sobre la configuración de cadenas de conexión para tener acceso a los servicios de almacenamiento de Azure, consulte [Configurar las cadenas de conexión de Windows Azure](http://msdn.microsoft.com/library/ee758697.aspx).

1. En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Almacenamiento** y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Administrar claves**.

 	Se abre **Administrar claves de acceso**.

	![Managekeys](./media/storage-create-storage-account/Storage_ManageKeys.png)

 
3. Para copiar una clave de acceso de almacenamiento, seleccione el texto de la clave. A continuación, haga clic con el botón secundario y haga clic en **Copiar**.

### Nueva generación de las claves de acceso de almacenamiento ###
Debe cambiar las claves de acceso de su cuenta de almacenamiento periódicamente para ayudar a mantener más seguras las conexiones de almacenamiento. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso. 

> [AZURE.WARNING] La nueva generación de las claves de acceso afecta a las máquinas virtuales, los servicios multimedia y cualquier aplicación que dependa de la cuenta de almacenamiento. Todos los clientes que usan la clave de acceso para acceder a la cuenta de almacenamiento deben estar actualizados para usar la nueva clave.

**Máquinas virtuales** - Si su cuenta de almacenamiento contiene una máquina virtual que se está ejecutando, tendrá que volver a implementar todas las máquinas virtuales después de generar de nuevo las claves de acceso. Para evitar una nueva implementación, apague las máquinas virtuales antes de generar de nuevo las claves de acceso.
 
**Servicios multimedia** - Si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las claves de acceso con los servicios multimedia después de generar de nuevo las claves.
 
**Aplicaciones** - Si tiene aplicaciones web o servicios en la nube que usan la cuenta de almacenamiento, perderá las conexiones si genera de nuevo las claves, a menos que las convierta. A continuación se muestra el proceso:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento. 

2. Vuelva a generar la clave de acceso primaria para su cuenta de almacenamiento. En el [Portal de administración](http://manage.windowsazure.com), desde el panel o la página **Configure**, haga clic en **Manage Keys**. Haga clic en **Regenerar** debajo de la clave de acceso primaria y, a continuación, haga clic en **Sí** para confirmar que desea generar una clave nueva.

3. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

4. Vuelva a generar la clave de acceso secundaria.

## <a id="deletestorageaccount"></a>Procedimiento: de una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no utiliza, use **Eliminar** en el panel o en la página **Configurar**. **Eliminar** elimina toda la cuenta de almacenamiento, incluidos todos los blobs, tablas y colas de la cuenta. 

> [AZURE.WARNING] No hay forma de restaurar el contenido de una cuenta de almacenamiento eliminada. Asegúrese 
	de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta. <br />
	Si su cuenta de almacenamiento contiene archivos VHD o discos para una máquina virtual de Azure, deberá entonces eliminar todas las imágenes y los discos que usan esos archivos VHD para poder eliminar la cuenta de almacenamiento. Primero, detenga la máquina virtual si se está ejecutando y, a continuación, elimínela. Para eliminar los discos, diríjase a la pestaña Discos y elimine todos los discos que se encuentran en la cuenta de almacenamiento. Para eliminar imágenes, diríjase a la pestaña Images y elimine todas las imágenes almacenadas en la cuenta.


1. En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Almacenamiento**.

2. Haga clic en cualquier lugar de la entrada de la cuenta de almacenamiento excepto en el nombre y, a continuación, haga clic en **Eliminar**.

	 -O-

	Haga clic en el nombre de la cuenta de almacenamiento para abrir el panel y, a continuación, haga clic en **Eliminar**.

3. Haga clic en **Sí** para confirmar que desea eliminar la cuenta de almacenamiento.

## <a id="next"></a>Pasos siguientes

- Para obtener más información acerca del Almacenamiento de Azure, consulte la documentación sobre Almacenamiento de Azure en [azure.com](http://azure.microsoft.com/documentation/services/storage/) y en [MSDN](http://msdn.microsoft.com/library/gg433040.aspx). 

- Visite el [blog del equipo de Almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/).



<!--HONumber=42-->
