<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="How to manage" pageTitle="How to manage storage accounts | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Administración de cuentas de almacenamiento
===========================================

Tabla de contenido
------------------

-   [Administración de replicación de cuenta de almacenamiento](#georeplication)
-   [Visualización, copiado y regeneración de claves de acceso de almacenamiento](#regeneratestoragekeys)
-   [Eliminación de una cuenta de almacenamiento](#deletestorageaccount)

Administración de replicación de cuenta de almacenamiento
---------------------------------------------------------

Tiene tres opciones para replicar su cuenta de almacenamiento:

-   **Replicación geográfica redundante.** La replicación geográfica redundante está habilitada para su cuenta de almacenamiento de manera predeterminada. Con la replicación geográfica redundante, sus datos se replican en una ubicación geográfica secundaria para habilitar la conmutación por error en esa ubicación en caso de que se produzca un desastre importante en la ubicación primaria. La ubicación secundaria se encuentra en la misma región, pero está a cientos de kilómetros de la ubicación primaria.

-   **Replicación geográfica redundante de acceso de lectura.** La replicación geográfica redundante de acceso de lectura replica sus datos en una ubicación geográfica secundaria; además, proporciona acceso de lectura a sus datos en la ubicación secundaria. La replicación con redundancia geográfica de acceso de lectura le permite tener acceso a los datos desde la ubicación principal o la secundaria, ante la eventualidad de que alguna de las ubicaciones deje de estar disponible.

-   **Replicación localmente redundante**. Con la replicación localmente redundante, los datos de su cuenta de almacenamiento se replican tres veces en el mismo centro de datos. La replicación localmente redundante se ofrece a tarifas con descuento.

    Tenga en cuenta de que si especifica una replicación localmente redundante para su cuenta de almacenamiento y posteriormente decide habilitar una replicación geográfica redundante, incurrirá en un costo por datos único para replicar sus datos existentes en la ubicación secundaria.

-   **Replicación geográfica redundante de acceso de lectura (solo vista previa).** La replicación geográfica redundante de acceso de lectura replica sus datos en una ubicación geográfica secundaria; además, proporciona acceso de lectura a sus datos en la ubicación secundaria. La replicación con redundancia geográfica de acceso de lectura le permite tener acceso a los datos desde la ubicación principal o la secundaria, ante la eventualidad de que alguna de las ubicaciones deje de estar disponible.

Para obtener información de precios para la replicación de cuentas de almacenamiento, consulte [Detalles de precios de Storage](http://www.windowsazure.com/en-us/pricing/details/storage/).

### Para especificar la configuración de replicación de una cuenta de almacenamiento

1.  En el [Portal de administración de Azure](https://manage.windowsazure.com), haga clic en **Almacenamiento** y, a continuación, haga clic en el nombre de su cuenta de almacenamiento para ver el panel.

2.  Haga clic en **Configure**.

3.  En el campo **Replication**, seleccione el tipo de replicación que desea para su cuenta de almacenamiento.

4.  Haga clic en **Save** y confirme su selección si se le solicita.

Visualización, copiado y regeneración de claves de acceso de almacenamiento
---------------------------------------------------------------------------

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

En el [Portal de administración](http://manage.windowsazure.com), use **Manage Keys** en el panel o la página **Almacenamiento** para ver, copiar y regenerar las claves de acceso de almacenamiento que se usan para tener acceso a los servicios de Blob, Tabla y Cola.

### Copia de una clave de acceso de almacenamiento

Puede usar **Manage Keys** para copiar una clave de acceso de almacenamiento para usarla en una cadena de conexión. La cadena de conexión requiere el uso del nombre de la cuenta de almacenamiento y una clave en la autenticación. Para obtener información sobre la configuración de cadenas de conexión para tener acceso a los servicios de almacenamiento de Azure, consulte [Configurar las cadenas de conexión de Windows Azure](http://msdn.microsoft.com/en-us/library/ee758697.aspx).

1. En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Almacenamiento** y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2. Haga clic en **Manage Keys**.

 	Se abre **Manage Access Keys**.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

3. Para copiar una clave de acceso de almacenamiento, seleccione el texto de la clave. Posteriormente, haga clic con el botón secundario y clic en **Copy**.

### Nueva generación de las claves de acceso de almacenamiento

Debe cambiar las claves de acceso de su cuenta de almacenamiento periódicamente para ayudar a mantener más seguras las conexiones de almacenamiento. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso.

**Advertencia**

La nueva generación de sus claves de acceso afecta a las máquinas virtuales, los servicios multimedia y cualquier aplicación que dependa de la cuenta de almacenamiento.

**Máquinas virtuales** - Si su cuenta de almacenamiento contiene una máquina virtual que se está ejecutando, tendrá que volver a implementar todas las máquinas virtuales después de generar de nuevo las claves de acceso. Para evitar una nueva implementación, apague las máquinas virtuales antes de generar de nuevo las claves de acceso.

**Servicios multimedia** - Si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las claves de acceso con los servicios multimedia después de generar de nuevo las claves.

**Aplicaciones** - Si tiene aplicaciones web o servicios en la nube que usan la cuenta de almacenamiento, perderá las conexiones si genera de nuevo las claves, a menos que las convierta. A continuación se muestra el proceso:

1.  Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.

2.  Vuelva a generar la clave de acceso primaria para su cuenta de almacenamiento. En el [Portal de administración](http://manage.windowsazure.com), desde el panel o la página **Configure**, haga clic en **Manage Keys**. Haga clic en **Regenerate** debajo de la clave de acceso primaria y, a continuación, haga clic en **Yes** para confirmar que desea generar una clave nueva.

3.  Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

4.  Vuelva a generar la clave de acceso secundaria.

Eliminación de una cuenta de almacenamiento
-------------------------------------------

Para quitar una cuenta de almacenamiento que ya no utiliza, use **Delete** en el panel o la página **Configure**. **Delete** elimina toda la cuenta de almacenamiento, incluidos todos los blobs, tablas y colas de la cuenta.

**Advertencia**

No hay forma de restaurar el contenido de una cuenta de almacenamiento eliminada. Asegúrese de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta.

Si su cuenta de almacenamiento contiene archivos VHD o discos para una máquina virtual de Azure, deberá entonces eliminar todas las imágenes y los discos que usan esos archivos VHD para poder eliminar la cuenta de almacenamiento. Primero, detenga la máquina virtual si se está ejecutando y, a continuación, elimínela. Para eliminar los discos, diríjase a la pestaña Discos y elimine todos los discos que se encuentran en la cuenta de almacenamiento. Para eliminar imágenes, diríjase a la pestaña Images y elimine todas las imágenes almacenadas en la cuenta.

1.  En el [Portal de administración](http://manage.windowsazure.com), haga clic en **Almacenamiento**.

2.  Haga clic en cualquier lugar de la entrada de la cuenta de almacenamiento excepto en el nombre y, a continuación, haga clic en **Delete**.

    -O bien-

    Haga clic en el nombre de la cuenta de almacenamiento para abrir el panel y, a continuación, haga clic en **Delete**.

3.  Haga clic en **Yes** para confirmar que desea eliminar la cuenta de almacenamiento.


