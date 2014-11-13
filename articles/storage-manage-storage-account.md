<properties urlDisplayName="How to manage" pageTitle="C&oacute;mo administrar cuentas de almacenamiento | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Aprenda a administrar cuentas de almacenamiento en Azure usando el Portal de administraci&oacute;n de Azure." metaCanonical="" services="storage" documentationCenter="" title="Administraci&oacute;n de cuentas de almacenamiento" authors="tamram" solutions="" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="managestorageaccounts"></span></a>Administración de cuentas de almacenamiento

## Tabla de contenido

-   [Direccionamiento del replicación de cuenta de almacenamiento][Direccionamiento del replicación de cuenta de almacenamiento]
-   [Direccionamiento del copiado y regeneración de claves de acceso de almacenamiento][Direccionamiento del copiado y regeneración de claves de acceso de almacenamiento]
-   [Direccionamiento del una cuenta de almacenamiento][Direccionamiento del una cuenta de almacenamiento]

## <span id="georeplication"></span></a>Direccionamiento del de los datos de la cuenta de almacenamiento para obtener durabilidad y alta disponibilidad

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### Para especificar la configuración de replicación de una cuenta de almacenamiento

1.  En el [Portal de administración de Azure][Portal de administración de Azure], haga clic en **Almacenamiento** y, a continuación, haga clic en el nombre de su cuenta de almacenamiento para ver el panel.

2.  Haga clic en **Configure**.

3.  En el campo **Replication**, seleccione el tipo de replicación que desea para su cuenta de almacenamiento.

4.  Haga clic en **Save** y confirme su selección si se le solicita.

## <span id="regeneratestoragekeys"></span></a>Direccionamiento del copiado y regeneración de claves de acceso de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio.

En el [Portal de administración][Portal de administración], use **Manage Keys** en el panel o la página **Almacenamiento** para ver, copiar y regenerar las claves de acceso de almacenamiento que se usan para tener acceso a los servicios de Blob, Tabla y Cola.

### Copia de una clave de acceso de almacenamiento

Puede usar **Manage Keys** para copiar una clave de acceso de almacenamiento para usarla en una cadena de conexión. La cadena de conexión requiere el uso del nombre de la cuenta de almacenamiento y una clave en la autenticación. Para obtener información sobre la configuración de cadenas de conexión para tener acceso a los servicios de almacenamiento de Azure, consulte [Configurar las cadenas de conexión de Windows Azure][Configurar las cadenas de conexión de Windows Azure].

1.  En el [Portal de administración][Portal de administración], haga clic en **Almacenamiento** y, a continuación, haga clic en el nombre de la cuenta de almacenamiento para abrir el panel.

2.  Haga clic en **Manage Keys**.

    Se abre **Manage Access Keys**.

    ![Managekeys][Managekeys]

3.  Para copiar una clave de acceso de almacenamiento, seleccione el texto de la clave. Posteriormente, haga clic con el botón secundario y clic en **Copy**.

### Nueva generación de las claves de acceso de almacenamiento

Debe cambiar las claves de acceso de su cuenta de almacenamiento periódicamente para ayudar a mantener más seguras las conexiones de almacenamiento. Se asignan dos claves de acceso para que pueda mantener las conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso.

<div class="dev-callout"> 
    <b>Advertencia</b> 
    <p>La nueva generaci&oacute;n de sus claves de acceso afecta a las m&aacute;quinas virtuales, los servicios multimedia y cualquier aplicaci&oacute;n que dependa de la cuenta de almacenamiento. Todos los clientes que usan la clave de acceso para acceder a la cuenta de almacenamiento deben estar actualizados para usar la nueva clave.
    </p> 
    </div>

**Máquinas virtuales** - Si su cuenta de almacenamiento contiene una máquina virtual que se está ejecutando, tendrá que volver a implementar todas las máquinas virtuales después de generar de nuevo las claves de acceso. Para evitar una nueva implementación, apague las máquinas virtuales antes de generar de nuevo las claves de acceso.

**Servicios multimedia** - Si tiene servicios multimedia que dependen de su cuenta de almacenamiento, debe volver a sincronizar las claves de acceso con los servicios multimedia después de generar de nuevo las claves.

**Aplicaciones** - Si tiene aplicaciones web o servicios en la nube que usan la cuenta de almacenamiento, perderá las conexiones si genera de nuevo las claves, a menos que las convierta. A continuación se muestra el proceso:

1.  Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.

2.  Vuelva a generar la clave de acceso primaria para su cuenta de almacenamiento. En el [Portal de administración][Portal de administración], desde el panel o la página **Configure**, haga clic en **Manage Keys**. Haga clic en **Regenerate** debajo de la clave de acceso primaria y, a continuación, haga clic en **Yes** para confirmar que desea generar una clave nueva.

3.  Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

4.  Vuelva a generar la clave de acceso secundaria.

## <span id="deletestorageaccount"></span></a>Direccionamiento del una cuenta de almacenamiento

Para quitar una cuenta de almacenamiento que ya no utiliza, use **Delete** en el panel o la página **Configure**. **Delete** elimina toda la cuenta de almacenamiento, incluidos todos los blobs, tablas y colas de la cuenta.

<div class="dev-callout">
    <b>Advertencia</b>
    <p>No hay forma de restaurar el contenido de una cuenta de almacenamiento eliminada. Aseg&uacute;rese 
    de hacer una copia de seguridad de cualquier contenido que desee guardar antes de eliminar la cuenta.
    </p>
    <p>
    Si su cuenta de almacenamiento contiene archivos o discos VHD para una m&aacute;quina virtual de Azure 
    , debe eliminar entonces las im&aacute;genes y los discos que usen esos archivos VHD 
    antes de eliminar la cuenta de almacenamiento. Primero, detenga la m&aacute;quina virtual si se est&aacute; ejecutando y, a continuaci&oacute;n, elim&iacute;nela. Para eliminar los discos, dir&iacute;jase a la pesta&ntilde;a Discos y elimine todos los discos que se encuentran en la cuenta de almacenamiento. Para eliminar im&aacute;genes, dir&iacute;jase a la pesta&ntilde;a Images y elimine todas las im&aacute;genes almacenadas en la cuenta.
    </p>
</div>

1.  En el [Portal de administración][Portal de administración], haga clic en **Almacenamiento**.

2.  Haga clic en cualquier lugar de la entrada de la cuenta de almacenamiento excepto en el nombre y, a continuación, haga clic en **Delete**.

    -O bien-

    Haga clic en el nombre de la cuenta de almacenamiento para abrir el panel y, a continuación, haga clic en **Delete**.

3.  Haga clic en **Yes** para confirmar que desea eliminar la cuenta de almacenamiento.

  [Direccionamiento del replicación de cuenta de almacenamiento]: #georeplication
  [Direccionamiento del copiado y regeneración de claves de acceso de almacenamiento]: #regeneratestoragekeys
  [Direccionamiento del una cuenta de almacenamiento]: #deletestorageaccount
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Portal de administración]: http://manage.windowsazure.com
  [Configurar las cadenas de conexión de Windows Azure]: http://msdn.microsoft.com/es-es/library/ee758697.aspx
  [Managekeys]: ./media/storage-manage-storage-account/Storage_ManageKeys.png
