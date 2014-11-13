<properties title="C&oacute;mo configurar un conjunto de disponibilidad para m&aacute;quinas virtuales" pageTitle="C&oacute;mo configurar un conjunto de disponibilidad para m&aacute;quinas virtuales" description="Proporciona los pasos para configurar un conjunto de disponibilidad para una m&aacute;quina virtual en Azure" metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="kathydav" />

# Cómo configurar un conjunto de disponibilidad para máquinas virtuales

Un conjunto de disponibilidad ayuda a mantener las máquinas virtuales disponibles durante períodos de inactividad, como por ejemplo mientras se realiza mantenimiento. Colocar dos o más máquinas virtuales similarmente configuradas en un conjunto de disponibilidad crea la redundancia necesaria para mantener la disponibilidad de las aplicaciones o servicios que ejecuta su máquina virtual. Para obtener más detalles, consulte [Administración de la disponibilidad de las máquinas virtuales][Administración de la disponibilidad de las máquinas virtuales].

Es una buena práctica usar una combinación de conjuntos de disponibilidad y extremos de equilibrio de carga para ayudar a asegurar de que su aplicación está siempre disponible y ejecutándose de forma eficaz. Para obtener detalles acerca de extremos de equilibrio de carga, consulte [Equilibrio de carga para Servicios de infraestructura de Azure][Equilibrio de carga para Servicios de infraestructura de Azure].

Puede poner máquinas virtuales en un conjunto de disponibilidad usando una de estas dos opciones:

-   [Opción 1: Creación de una máquina virtual y un conjunto de disponibilidad simultáneamente][Opción 1: Creación de una máquina virtual y un conjunto de disponibilidad simultáneamente]. A continuación, agregue nuevas máquinas virtuales al conjunto cuando cree en dichas máquinas virtuales.
-   [Opción 2: Adición de una máquina virtual existente a un conjunto de disponibilidad][Opción 2: Adición de una máquina virtual existente a un conjunto de disponibilidad].

> [WACOM.NOTE] Las máquinas virtuales que desee poner en el mismo conjunto de disponibilidad deben pertenecer al mismo servicio en la nube.

## <span id="createset"></span> </a>Opción 1: Creación de una máquina virtual y un conjunto de disponibilidad simultáneamente

Puede usar el Portal de administración o cmdlets de Azure PowerShell para hacer esto.

Para utilizar el Portal de administración:

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de administración][Portal de administración] de Azure.

2.  En la barra de comandos, haga clic en **New**.

3.  Haga clic en **Máquina virtual** y, a continuación, haga clic en **From Gallery**.

4.  Utilice las dos primeras pantallas para elegir una imagen, un nombre de usuario, una contraseña, etc. Para obtener más detalles, consulte [Creación de una máquina virtual que ejecuta Windows][Creación de una máquina virtual que ejecuta Windows].

5.  La tercera pantalla permite configurar los recursos relacionados con las redes, el almacenamiento y la disponibilidad. Haga lo siguiente:

    1.  Seleccione la opción apropiada para el servicio la nube. Establézcalo en **Crear un nuevo servicio en la nube** (a menos que esté agregando esta nueva máquina virtual a un servicio en la nube de máquinas virtuales existentes). A continuación, bajo **Nombre DNS de servicio en la nube**, escriba un nombre. El nombre DNS pasará a ser parte del URI que se usa para establecer contacto con la máquina virtual. El servicio en la nube se comporta como un grupo de comunicaciones y aislamiento. Todas las máquinas virtuales del mismo servicio en la nube se pueden comunicar entre sí, se pueden configurar para equilibrio de carga y se pueden colocar en el mismo grupo de disponibilidad.

    2.  En **Región/Grupo de afinidad/Red virtual**, especifique una red virtual si pretende usar una. **Importante**: si desea que una máquina virtual use una red virtual, debe incorporar dicha máquina virtual a la red virtual cuando cree la máquina virtual. No puede incorporar una máquina virtual a una red virtual después de crear la VM. Para obtener más información, consulte [Información general sobre redes virtuales de Azure][Información general sobre redes virtuales de Azure].

    3.  Cree el conjunto de disponibilidad. En **Conjunto de disponibilidad**, déjelo establecido en **Crear un conjunto de disponibilidad**. A continuación, escriba un nombre para el conjunto.
    4.  Cree los extremos predeterminados y agregue más extremos si es necesario. También puede agregar extremos posteriormente.

    ![Crear un conjunto de disponibilidad para una nueva máquina virtual][Crear un conjunto de disponibilidad para una nueva máquina virtual]

6.  En la cuarta pantalla, elija las extensiones que desee instalar. Las extensiones proporcionan características que facilitan la administración de la máquina virtual, como por ejemplo la ejecución de antimalware o el restablecimiento de contraseñas. Para obtener detalles, consulte [Agente de máquina virtual de Azure y extensiones de máquina virtual][Agente de máquina virtual de Azure y extensiones de máquina virtual].

7.  Haga clic en la fecha para crear la máquina virtual y el conjunto de disponibilidad.

    En el panel de la máquina virtual nueva, puede hacer clic en **Configurar** y ver que la máquina virtual pertenece al conjunto de disponibilidad nuevo.

Para usar cmdlets de Azure:

1.  Abra una sesión de Azure PowerShell y ejecute comandos de forma similar a los siguientes ejemplos. Tenga en cuenta que en estos ejemplos se asume que está creando la máquina virtual, el servicio en la nube y el conjunto de disponibilidad.

2.  Obtenga el nombre de la imagen que desea usar para crear la máquina virtual y almacenarla en una variable. El comando usa el número de índice y la propiedad ImageName del objeto de imagen:

    `C:\PS> $image = (Get-AzureVMImage)[4].ImageName`

    > [WACOM.NOTE] Para obtener una lista de todas las imágenes que se aplican a su suscripción, ejecute `Get-AzureVMImage` sin parámetros.

3.  Especifique la configuración para la nueva máquina virtual y, a continuación, use la canalización para pasar un objeto de configuración al cmdlet que cree la máquina virtual. Asegúrese de sustituir los marcadores de posición por sus propios valores, como por ejemplo \<VmName\> y \<VmSize\>.

    `C:\PS> New-AzureVMConfig -Name "<VmName>" -InstanceSize <VmSize> -AvailabilitySetName "<SetName>" -ImageName $image | Add-AzureProvisioningConfig -Windows -AdminUsername "<UserName>" -Password "<MyPassword>" | Add-AzureDataDisk -CreateNew -DiskSizeInGB 50 -DiskLabel 'datadisk1' -LUN 0 | New-AzureVM –ServiceName "<MySvc1>"`

## <span id="addmachine"></span> </a>Opción 2: Adición de una máquina virtual existente a un conjunto de disponibilidad

En el Portal de administración, puede agregar máquinas virtuales existentes a un conjunto de disponibilidad existente o crear uno nuevo para ellas. (Recuerde que las máquinas virtuales deben pertenecer al mismo servicio en la nube.) Estos pasos son prácticamente los mismos. En Azure PowerShell, puede agregar la máquina virtual a un conjunto de disponibilidad existente.

1.  Si no lo ha hecho todavía, inicie sesión en el [Portal de administración][Portal de administración] de Azure.

2.  En la barra de navegación, haga clic en **Máquinas virtuales**.

3.  En la lista de máquinas virtuales, seleccione una de las máquinas virtuales que desee agregar al conjunto. Haga clic en la fila de la máquina virtual para abrir su panel.

4.  En las pestañas debajo del nombre de la máquina virtual, haga clic en **Configurar**.

5.  En la sección Configuración, busque **Conjunto de disponibilidad**. Realice una de las operaciones siguientes:

    A. Elija **Crear un conjunto de disponibilidad** y, a continuación, escriba un nombre para el conjunto.

    B. Elija **Seleccionar un conjunto de disponibilidad** y, a continuación, seleccione un conjunto de la lista.

    ![Crear un conjunto de disponibilidad para una máquina virtual existente][Crear un conjunto de disponibilidad para una máquina virtual existente]

6.  Haga clic en **Save**.

Para usar cmdlets de Azure:

Abra una sesión de Azure PowerShell y ejecute el comando siguiente. Asegúrese de sustituir los marcadores de posición por sus propios valores, como por ejemplo \<VmCloudServiceName\> y \<VmName\>.

    C:\PS> Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<MyAvSet>" | Update-AzureVM

> [WACOM.NOTE] La máquina virtual podría reiniciarse para terminar de agregarla al conjunto de disponibilidad.

## Recursos adicionales

[Acerca de los ajustes de configuración de máquinas virtuales en Azure][Acerca de los ajustes de configuración de máquinas virtuales en Azure]

<!-- LINKS -->

  [Administración de la disponibilidad de las máquinas virtuales]: ../virtual-machines-manage-availability
  [Equilibrio de carga para Servicios de infraestructura de Azure]: ../virtual-machines-load-balance
  [Opción 1: Creación de una máquina virtual y un conjunto de disponibilidad simultáneamente]: #createset
  [Opción 2: Adición de una máquina virtual existente a un conjunto de disponibilidad]: #addmachine
  [Portal de administración]: http://manage.windowsazure.com
  [Creación de una máquina virtual que ejecuta Windows]: ../virtual-machines-windows-tutorial
  [Información general sobre redes virtuales de Azure]: http://go.microsoft.com/fwlink/p/?linkid=294063
  [Crear un conjunto de disponibilidad para una nueva máquina virtual]: ./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png
  [Agente de máquina virtual de Azure y extensiones de máquina virtual]: http://go.microsoft.com/fwlink/p/?LinkId=XXX
  [Crear un conjunto de disponibilidad para una máquina virtual existente]: ./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png
  [Acerca de los ajustes de configuración de máquinas virtuales en Azure]: http://msdn.microsoft.com/es-es/library/azure/dn763935.aspx
