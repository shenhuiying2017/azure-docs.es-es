---
title: "Azure Backup: Preparación para la copia de seguridad de máquinas virtuales | Microsoft Docs"
description: "Asegúrese de que el entorno esté preparado para hacer la copia de seguridad de máquinas virtuales en Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: copias de seguridad; realizar copia de seguridad
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/7/2017
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 27d8da9df87dc5e93272be0bb6584e90971fb461
ms.lasthandoff: 03/22/2017


---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager
> [!div class="op_single_selector"]
> * [Modelo de Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modelo clásico](backup-azure-vms-prepare.md)
>
>

En este artículo se proporcionan los pasos necesarios para preparar el entorno de copia de seguridad de una máquina virtual (VM) implementada según el modelo de Resource Manager. Los pasos que se muestran en los procedimientos utilizan el Portal de Azure.  

El servicio Copia de seguridad de Azure dispone de dos tipos de almacenes (almacenes de copia de seguridad y almacenes de servicios de recuperación) para proteger las máquinas virtuales. Un almacén de copia de seguridad protege las máquinas virtuales implementadas mediante el modelo de implementación clásica. Un almacén de Recovery Services protege **tanto las máquinas virtuales implementadas con el modelo clásico como las implementadas mediante Resource Manager**. Para proteger las máquinas virtuales implementadas según el modelo de Resource Manager, debe usar un almacén de Servicios de recuperación.

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). Consulte [Preparación del entorno de copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-prepare.md) para más información sobre cómo trabajar con las máquinas virtuales del modelo de implementación clásica.
>
>

Para proteger una máquina virtual implementada según el modelo de Resource Manager o realizar una copia de seguridad de una de estas máquinas, asegúrese de cumplir los siguientes requisitos previos:

* Ha creado un almacén de Servicios de recuperación (o ha identificado uno existente) *en la misma ubicación que la VM*.
* Ha seleccionado un escenario, ha definido la directiva de copia de seguridad y ha definido los elementos que quiere proteger.
* Ha comprobado la instalación del agente de máquina virtual en la máquina virtual.
* Ha comprobado la conectividad de la red
* Para las máquinas virtuales Linux, en caso de que desee personalizar el entorno de copia de seguridad para las copias de seguridad coherentes con la aplicación, consulte los [pasos para configurar los scripts anteriores y posteriores a la instantánea](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Si sabe que estas condiciones ya existen en su entorno, vaya al artículo [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms.md). Si necesita configurar o comprobar cualquiera de estos requisitos previos, este artículo le guía por los pasos para prepararlos.

##<a name="supported-operating-system-for-backup"></a>Sistemas operativos compatibles para copia de seguridad
 * **Linux**: Copia de seguridad de Azure admite [una lista de distribuciones aprobadas por Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , con la excepción de CoreOS Linux. _Otras distribuciones con la iniciativa "traiga su propio Linux" también podrían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual y haya compatibilidad con Python. Sin embargo, no respaldamos esas distribuciones para copia de seguridad._
 * **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitaciones al realizar copias de seguridad y restaurar una máquina virtual
Antes de preparar el entorno, tenga en cuenta las limitaciones.

* No se admite la copia de seguridad de máquinas virtuales con más de 16 discos de datos.
* No se admite la copia de seguridad de máquinas virtuales con una dirección IP reservada y sin puntos de conexión definidos.
* No se admite la copia de seguridad de máquinas virtuales cifradas simplemente mediante BEK. No se admite la copia de seguridad de máquinas virtuales Linux cifradas mediante el cifrado LUKS.
* No se admite la copia de seguridad de máquinas virtuales de Linux con la extensión de Docker.
* Los datos de copia de seguridad no incluyen unidades montadas de red conectadas a la máquina virtual.
* No se admite el reemplazo de una máquina virtual existente durante la restauración. Si intenta restaurar la máquina virtual cuando ya existe, la operación de restauración dará error.
* No se admiten la restauración y la copia de seguridad entre regiones.
* Puede realizar copias de seguridad de máquinas virtuales en todas las regiones públicas de Azure (consulte la [lista de comprobación](https://azure.microsoft.com/regions/#services) de las regiones admitidas). Si la región que está buscando no es compatible actualmente, no aparecerá en la lista desplegable durante la creación del almacén.
* La restauración de una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio solo se admite a través de PowerShell. Más información sobre cómo [restaurar un controlador de dominio de varios controladores de dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms)
* Solo se admite la restauración de las máquinas virtuales que tienen las siguientes configuraciones especiales de red a través de PowerShell. Las máquinas virtuales que se crean con el flujo de trabajo de restauración en la interfaz de usuario no tendrán estas configuraciones de red cuando se complete la operación de restauración. Si desea obtener más información, consulte [Restauración de máquinas virtuales con configuraciones de red especiales](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Máquinas virtuales con la configuración del equilibrador de carga (interna y externa).
  * Máquinas virtuales con varias direcciones IP reservadas.
  * Máquinas virtuales con varios adaptadores de red.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Creación de un almacén de Servicios de recuperación para una máquina virtual
Un almacén de Servicios de recuperación es una entidad que almacena las copias de seguridad y los puntos de recuperación creados con el tiempo. También contiene las directivas de copia de seguridad asociadas a las máquinas virtuales protegidas.

Para crear un almacén de Servicios de recuperación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del centro, haga clic en **Examinar** y, en la lista de recursos, escriba **Recovery Services**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacén de Servicios de recuperación**.

    ![Haga clic en el botón Examinar y escriba Recovery Services. Cuando vea la opción Almacén de Recovery Services, haga clic en ella para abrir la hoja Almacén de Recovery Services.](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Se muestra la lista de almacenes de Servicios de recuperación.
3. En el menú **Almacenes de servicios de recuperación**, haga clic en **Agregar**.

    ![Creación del almacén de Servicios de recuperación, paso 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Se abre la hoja del almacén de Recovery Services, donde se le pide que especifique los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.
5. Haga clic en **Suscripción** para ver la lista de suscripciones disponibles. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.
6. Haga clic en **Grupo de recursos** para ver la lista de grupos de recursos disponibles o haga clic en **Nuevo** para crear uno. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. El almacén **debe** estar en la misma región que las máquinas virtuales que desea proteger.

   > [!IMPORTANT]
   > Si no está seguro de en qué ubicación existe la máquina virtual, cierre el cuadro de diálogo de creación del almacén y vaya a la lista de máquinas virtuales del portal. Si tiene máquinas virtuales en varias regiones, debe crear un almacén de Servicios de recuperación en cada región. Cree el almacén en la primera ubicación antes de pasar a la siguiente ubicación. No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de Servicios de recuperación y el servicio Copia de seguridad de Azure lo controlarán automáticamente.
   >
   >

8. Haga clic en **Crear**. La creación del almacén de Servicios de recuperación puede tardar unos minutos. Supervise las notificaciones de estado en la parte superior derecha del portal. Una vez creado el almacén, aparece en la lista de almacenes de servicios de recuperación. Si no ve el almacén, haga clic en **Actualizar**.

    ![Lista de copias de seguridad](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

    Ahora que ha creado el almacén, aprenda a configurar la replicación de almacenamiento.

## <a name="set-storage-replication"></a>Configuración de la replicación de almacenamiento
La opción de replicación de almacenamiento permite elegir entre almacenamiento con redundancia geográfica y almacenamiento con redundancia local. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si esta es su copia de seguridad principal, elija el almacenamiento con redundancia geográfica. Elija el almacenamiento con redundancia local si desea una opción más económica que no sea tan duradera.

Para editar la configuración de replicación de almacenamiento:

1. En la hoja **Almacenes de Recovery Services**, seleccione el almacén.
    Al hacer clic en el almacén, la hoja Configuración (*con el nombre del almacén en la parte superior*) y la hoja de detalles del almacén se abren.

    ![elegir el almacén de la lista de almacenes de copia de seguridad](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. En la hoja **Configuración**, utilice el control deslizante vertical para desplazarse hacia abajo hasta la sección **Administrar**. Haga clic en **Infraestructura de copia de seguridad** para su la hoja. En la sección **General**, haga clic **Configuración de copia de seguridad** para abrir su hoja. En la hoja **Configuración de copia de seguridad** , elija la opción de replicación de almacenamiento para su almacén. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si cambia el tipo de replicación de Storage, haga clic en **Guardar**.

    ![Lista de copias de seguridad](./media/backup-azure-arm-vms-prepare/full-blade.png)

     Si usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, siga utilizando el almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión de almacenamiento de copia de seguridad no principal, elija entonces el almacenamiento con redundancia local. Para más información sobre las opciones de almacenamiento [con redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y [con redundancia local](../storage/storage-redundancy.md#locally-redundant-storage), consulte [Replicación de Azure Storage](../storage/storage-redundancy.md).
    Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selección de un objetivo de copia de seguridad, establecimiento de la directiva y definición de los elementos para proteger
Antes de registrar una máquina virtual en un almacén, ejecute el proceso de detección para asegurarse de que se identifican todas las nuevas máquinas virtuales que se hayan agregado a la suscripción. El proceso consulta a Azure para recibir la lista de máquinas virtuales incluidas en la suscripción, junto con información adicional, como el nombre del servicio en la nube y la región. En el Portal de Azure, el escenario se refiere a lo que va a colocar en el almacén de servicios de recuperación. Directiva es la programación de la frecuencia y de cuándo se eligen los puntos de recuperación. La directiva también incluye el intervalo de retención de los puntos de recuperación.

1. Si ya tiene abierto un almacén de Servicios de recuperación, vaya al paso 2. Si no tiene abierto un almacén de Recovery Services, entonces abra [Azure Portal](https://portal.azure.com/), en el menú del concentrador y haga clic en **Más servicios**.

   * En la lista de recursos, escriba **Servicios de recuperación**.
   * Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de Servicios de recuperación**cuando lo vea.

     ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

     Aparece la lista de almacenes de Servicios de recuperación. Si no hay almacenes en su suscripción, esta lista estará vacía.

    ![Visualización de la lista de almacenes de Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   * En la lista de almacenes de Recovery Services, seleccione un almacén para abrir su panel.

     La hoja Configuración y el panel de almacén correspondientes al almacén elegido se abrirán.

     ![Hoja del almacén abierta](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. En el menú del panel del almacén, haga clic en **Copia de seguridad** para abrir la hoja Copia de seguridad.

    ![Hoja Copia de seguridad abierta](./media/backup-azure-arm-vms-prepare/backup-button.png)

    Se abren las hojas Copia de seguridad y Objetivo de copia de seguridad.

    ![Hoja Escenario abierta](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. En la hoja Objetivo de copia de seguridad, establezca **¿Dónde se ejecuta su carga de trabajo?** en Azure y **What do you want to backup** (¿De qué desea realizar copias de seguridad?) en Máquina virtual. A continuación, haga clic en **Aceptar**.

    Esto registra la extensión de VM con el almacén. La hoja Objetivo de copia de seguridad se cierra y se abre la hoja **Directiva de copia de seguridad**.

    ![Hoja Escenario abierta](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. En la hoja Directiva de copia de seguridad, seleccione la que quiera aplicar al almacén.

    ![Seleccionar directiva de copia de seguridad](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

    Los detalles de la directiva predeterminada se muestran en el menú desplegable. Si quiere crear una directiva, seleccione **Crear nuevo** en el menú desplegable. Si quiere instrucciones para definir una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Haga clic en **Aceptar** para asociar la directiva de copia de seguridad con el almacén.

    La hoja Objetivo de copia de seguridad se cierra y se abre la hoja **Seleccionar máquinas virtuales**.
5. En la hoja **Seleccionar máquinas virtuales**, elija las máquinas virtuales que se asociarán con la directiva especificada y haga clic en **Aceptar**.

    ![Seleccionar carga de trabajo](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

    Se valida la máquina virtual seleccionada. Si no ve las máquinas virtuales que espera ver, compruebe que existen en la misma ubicación de Azure que el almacén de Recovery Services y que no estén ya protegidas en otro almacén. La ubicación del almacén de Recovery Services se muestra en el panel del almacén.

6. Ahora que ha definido toda la configuración del almacén, en la hoja Copia de seguridad, haga clic en **Habilitar copia de seguridad**. La directiva se implementa en el almacén y en las máquinas virtuales. Estro no crea el punto de recuperación inicial para la máquina virtual.

    ![Habilitar copia de seguridad](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Después de habilitar correctamente la copia de seguridad, la directiva de copia de seguridad se ejecutará en la programación. Si desea generar un trabajo de copia de seguridad a petición para hacer una copia de seguridad de las máquinas virtuales ahora, consulte [Desencadenamiento del trabajo de copia de seguridad](./backup-azure-arm-vms.md#triggering-the-backup-job).

Si tiene problemas al registrar la máquina virtual, consulte la siguiente información sobre cómo instalar el agente de VM y sobre conectividad de red. Probablemente no necesite la siguiente información si va a proteger máquinas virtuales creadas en Azure. Sin embargo, si migró las máquinas virtuales a Azure, asegúrese entonces de que ha instalado correctamente el agente de VM y que la máquina virtual se puede comunicar con la red virtual.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instale el agente de máquina virtual en la máquina virtual.
El agente de máquina virtual de Azure se debe instalar en la máquina virtual de Azure para que funcione la extensión de copia de seguridad. Si la máquina virtual se creó desde la galería de Azure, el agente de máquina virtual ya está presente en la máquina virtual. Esta información se proporciona para las situaciones donde *no* se utiliza una máquina virtual creada desde la galería de Azure; por ejemplo, si ha migrado una máquina virtual desde un centro de datos local. En tal caso, debe instalarse el agente de máquina virtual con el fin de proteger la máquina virtual. Más información sobre el [agente de máquina virtual](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md#azure-vm-agents-for-windows-and-linux).

Si tiene problemas para realizar una copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure está instalado correctamente en la máquina virtual (consulte la tabla siguiente). La tabla siguiente proporciona información adicional acerca del agente de máquina virtual para las máquinas virtuales de Windows y Linux.

| **Operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual |Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación. |<li> Instale el [agente de Linux](../virtual-machines/virtual-machines-linux-agent-user-guide.md) más reciente. Necesitará privilegios de administrador para efectuar la instalación. Se recomienda instalar el agente desde el repositorio de distribución. **No se recomienda** instalar el agente de máquina virtual Linux directamente desde github.  |
| Actualización del agente de la máquina virtual |Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |Siga las instrucciones para [actualizar el agente de máquina virtual Linux](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se recomienda actualizar el agente desde el repositorio de distribución. **No se recomienda** actualizar el agente de máquina virtual Linux directamente desde github.<br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |
| Validación de la instalación del agente de máquina virtual |<li>Acceda a la carpeta *C:\WindowsAzure\Packages* de la máquina virtual de Azure. <li>El archivo WaAppAgent.exe debe estar ahí.<li> Haga clic con el botón derecho en el archivo, desplácese hasta **Propiedades** y seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior. |N/D |

### <a name="backup-extension"></a>Extensión de copia de seguridad
Una vez que el agente de máquina virtual está instalado en la máquina virtual, el servicio Copia de seguridad de Azure instala la extensión de copia de seguridad en el agente de máquina virtual. El servicio Copia de seguridad de Azure actualiza la extensión de copia de seguridad y le aplica revisiones en un proceso que transcurre de forma fluida.

El servicio Copia de seguridad instala la extensión de copia de seguridad tanto si la máquina virtual está en ejecución como si no lo está. Una máquina virtual en ejecución ofrece más probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Copia de seguridad de Azure sigue realizando la copia de seguridad de la máquina virtual aunque esté apagada y no haya sido posible instalar la extensión. Esto se conoce como máquina virtual sin conexión. En este caso, el punto de recuperación será *coherente frente a bloqueos*.

## <a name="network-connectivity"></a>Conectividad de red
Para administrar las instantáneas de máquina virtual, la extensión de copia de seguridad necesita conectividad a las direcciones IP públicas de Azure. Sin la conectividad a Internet adecuada, el tiempo de espera de las solicitudes HTTP de la máquina virtual se agota y se produce un error en la operación de copia de seguridad. Si la implementación tiene restricciones de acceso establecidas (a través de un grupo de seguridad de red (NSG), por ejemplo), elija entonces una de estas opciones para proporcionar una ruta de acceso clara para el tráfico de copia de seguridad:

* [Whitelist the Azure datacenter IP ranges](http://www.microsoft.com/en-us/download/details.aspx?id=41653) (Creación de listas blancas con intervalos de direcciones IP de centro de datos de Azure): consulte este artículo para obtener instrucciones sobre cómo crear una lista blanca con las direcciones IP.
* Implementación de un servidor proxy HTTP para enrutar el tráfico.

Al decidir qué opción utilizar, busque el equilibrio entre costo, control granular y facilidad de uso.

| Opción | Ventajas | Desventajas |
| --- | --- | --- |
| Creación de una lista blanca con intervalos IP |Sin costos adicionales.<br><br>Para abrir el acceso en un grupo de seguridad de red, use el cmdlet <i>Set-AzureNetworkSecurityRule</i>. |Es complejo de administrar, ya que los intervalos de IP afectados cambian con el tiempo.<br><br>Proporciona acceso a la totalidad de Azure y no solo al almacenamiento. |
| Proxy HTTP |Se permite un control detallado en el proxy sobre las direcciones URL de almacenamiento.<br>Un único punto de acceso a Internet para las máquinas virtuales.<br>No están sujetas a cambios de direcciones IP de Azure. |Costes adicionales de ejecutar una máquina virtual con el software de proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Whitelist the Azure datacenter IP ranges
Para crear una lista blanca con los intervalos de IP de centro de datos de Azure, visite el [sitio web de Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653). Ahí encontrará información detallada sobre los intervalos de IP, junto con instrucciones.

### <a name="using-an-http-proxy-for-vm-backups"></a>Uso de un proxy HTTP para las copias de seguridad de máquinas virtuales
Cuando se realiza una copia de seguridad de una máquina virtual, la extensión de copia de seguridad de dicha máquina envía los comandos de administración de instantáneas a Almacenamiento de Azure mediante una API de HTTPS. Enrute el tráfico de extensión de copia de seguridad a través del proxy HTTP, ya que es el único componente configurado para el acceso a la red pública de Internet.

> [!NOTE]
> No hay ninguna recomendación que deba usarse para el software de proxy. Asegúrese de que selecciona un proxy que sea compatible con los pasos de configuración que se mencionan a continuación.
>
>

En la imagen de ejemplo siguiente se muestran los tres pasos de configuración necesarios para utilizar un proxy HTTP:

* La máquina virtual de la aplicación enruta todo el tráfico HTTP enlazado para la red pública de Internet a través de la máquina virtual de proxy.
* La máquina virtual de proxy permite el tráfico entrante de máquinas virtuales en la red virtual.
* El grupo de seguridad de red (NSG) llamado NSF-lockdown necesita una regla de seguridad que permita el tráfico de Internet saliente de la máquina virtual de proxy.

![Grupo de seguridad de red con el diagrama de implementación de proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Para usar a un proxy HTTP para la comunicación con la red pública de Internet, siga estos pasos:

#### <a name="step-1-configure-outgoing-network-connections"></a>Paso 1. Configuración de las conexiones de red salientes
###### <a name="for-windows-machines"></a>Para máquinas Windows
En este paso se configura el servidor proxy para la cuenta de sistema local.

1. Descargue [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Ejecute el comando siguiente desde un símbolo del sistema con privilegios elevados.

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Se abrirá la ventana de Internet Explorer.
3. Vaya a Herramientas -> Opciones de Internet -> Conexiones -> Configuración de LAN.
4. Compruebe la configuración de proxy de la cuenta del sistema. Configure la dirección IP de proxy y el puerto.
5. Cierre Internet Explorer.

Se configurará el proxy en todo el equipo y se usará para el tráfico saliente de HTTP/HTTPS.

Si ha configurado un servidor proxy en una cuenta de usuario actual (no en una cuenta de sistema local), use el siguiente script para aplicarlo a SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Si ve el mensaje "(407) Se requiere autenticación del proxy" en el registro del servidor proxy, compruebe que su autenticación está configurada correctamente.
>
>

###### <a name="for-linux-machines"></a>Para máquinas Linux
Agregue la siguiente línea al archivo ```/etc/environment``` :

```
http_proxy=http://<proxy IP>:<proxy port>
```

Agregue las líneas siguientes al archivo ```/etc/waagent.conf```:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Paso 2: Aceptación de conexiones entrantes en el servidor proxy:
1. En el servidor proxy, abra Firewall de Windows. La manera más fácil de acceder al firewall es buscar Firewall de Windows con seguridad avanzada.

    ![Abrir el firewall](./media/backup-azure-vms-prepare/firewall-01.png)
2. En el cuadro de diálogo Firewall de Windows, haga clic con el botón derecho en **Reglas de entrada** y haga clic en **Nueva regla...**.

    ![Crear una nueva regla](./media/backup-azure-vms-prepare/firewall-02.png)
3. En **Asistente para nueva regla de entrada**, elija la opción **Personalizada** para el **Tipo de regla** y haga clic en **Siguiente**.
4. En la pantalla para seleccionar el **programa**, elija **Todos los programas** y haga clic en **Siguiente**.
5. En la página **Protocolo y puertos**, escriba la información siguiente y haga clic en **Siguiente**:

    ![Crear una nueva regla](./media/backup-azure-vms-prepare/firewall-03.png)

   * en *Tipo de protocolo*, elija *TCP*.
   * En *Puerto Local*, elija *Puertos específicos* y, en el campo siguiente, especifique el valor de ```<Proxy Port>``` configurado.
   * En *Puerto remoto*, seleccione *Todos los puertos*.

     Durante el resto del asistente, haga clic en todas las pantallas hasta el final y asigne un nombre a esta regla.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Paso 3: Adición de una regla de excepción al grupo de seguridad de red
En un símbolo del sistema de Azure PowerShell, escriba el siguiente comando:

El siguiente comando agrega una excepción al NSG. Esta excepción permite el tráfico TCP de cualquier puerto en la dirección IP 10.0.0.5 a cualquier dirección de Internet en el puerto 80 (HTTP) o 443 (HTTPS). Si necesita un puerto específico en la red pública de Internet, asegúrese de agregarlo también a ```-DestinationPortRange```.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*En estos pasos se utilizan valores y nombres específicos para este ejemplo. Cuando especifique detalles en el código, o los corte o pegue, use los nombres y los valores de su implementación.*

Ahora que sabe que tiene conectividad de red, está listo para realizar la copia de seguridad de la máquina virtual. Consulte [Copia de seguridad de máquinas virtuales de Azure en un almacén de Servicios de recuperación](backup-azure-arm-vms.md).

## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha preparado el entorno para realizar la copia de seguridad de la máquina virtual, el siguiente paso lógico es crear una copia de seguridad. El artículo de planeamiento ofrece información más detallada sobre la realización de copias de seguridad de máquinas virtuales.

* [Copia de seguridad de máquinas virtuales](backup-azure-vms.md)
* [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
* [Administración de copias de seguridad de máquinas virtuales](backup-azure-manage-vms.md)

