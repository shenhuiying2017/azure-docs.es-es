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
ms.date: 1/21/2017
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: 7d7b81a585ba8b10c60062c5d5274c45335cab68
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager

En este artículo se indican los pasos necesarios para preparar el entorno para realizar una copia de seguridad de una máquina virtual en que se ha implementado Azure Resource Manager. Los pasos que se muestran en los procedimientos utilizan el Portal de Azure.  

El servicio Azure Backup dispone de dos tipos de almacenes para proteger las máquinas virtuales: almacenes de Backup y almacenes de Recovery Services. Los almacenes de Backup ayudan a proteger las máquinas virtuales implementadas mediante el modelo de implementación clásica. Los almacenes de Recovery Services ayudan a proteger *tanto las máquinas virtuales implementadas con el modelo clásico como las implementadas mediante Resource Manager*. Si desea proteger las máquinas virtuales implementadas mediante Resource Manager, debe usar un almacén de Recovery Services.

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md).

Para proteger o realizar una copia de seguridad de una máquina virtual implementada mediante Resource Manager, asegúrese de cumplir los siguientes requisitos previos:

* Ha creado un almacén de Recovery Services (o ha identificado uno existente) *en la misma ubicación que la máquina virtual*.
* Ha seleccionado un escenario, ha definido la directiva de copia de seguridad y ha definido los elementos que quiere proteger.
* Ha comprobado la instalación de un agente de máquina virtual en la máquina virtual.
* Ha comprobado la conectividad de la red.
* En el caso de las máquinas virtuales Linux, si desea personalizar el entorno de copia de seguridad para realizar copias de seguridad coherentes con la aplicación, siga los [pasos para configurar scripts previos y posteriores a la instantánea](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Si estas condiciones ya existen en su entorno, vaya al artículo [Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services](backup-azure-arm-vms.md). Si necesita configurar o comprobar cualquiera de estos requisitos previos, este artículo le guía por los pasos necesarios para hacerlo.

## <a name="supported-operating-systems-for-backup"></a>Sistemas operativos compatibles para copia de seguridad
 * **Linux**: Azure Backup admite [una lista de distribuciones que Azure aprueba](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), con la excepción de CoreOS Linux. 
 
    > [!NOTE] 
    > Otras distribuciones del tipo "traiga su propio Linux" podrían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual y haya compatibilidad con Python. Sin embargo, este tipo de distribuciones no se aprueban para copia de seguridad.
 * **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitaciones al realizar copias de seguridad y restaurar una máquina virtual
Antes de preparar el entorno, asegúrese de que conoce estas limitaciones:

* No se admite la copia de seguridad de máquinas virtuales con más de 16 discos de datos.
* No se admite la copia de seguridad de máquinas virtuales cuyos discos de datos tengan un tamaño superior a 1023 GB.

  > [!NOTE]
  > Tenemos una versión preliminar privada para admitir las copias de seguridad de las VM con discos de más de 1 TB. Para más información, consulte [Instant recovery point and large disk support for Azure Backup](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a) (Compatibilidad de punto de recuperación instantáneo y discos grandes con Azure Backup).
  >

* No se admite la copia de seguridad de máquinas virtuales con una dirección IP reservada y sin puntos de conexión definidos.
* No se admite la copia de seguridad de máquinas virtuales cifradas a través de una clave de cifrado de unidad BitLocker (BEK). No se admite la copia de seguridad de máquinas virtuales Linux en las que se ha usado el cifrado LUKS (Linux Unified Key Setup).
* No se recomienda realizar copias de seguridad de máquinas virtuales que contengan la configuración de volúmenes compartidos de clúster (CSV) o de Servidor de archivos de escalabilidad horizontal. Requieren que en una tarea de instantánea haya que implicar todas las máquinas virtuales incluidas en la configuración del clúster. Azure Backup no es compatible con la coherencia entre varias VM. 
* Los datos de la copia de seguridad no incluyen unidades montadas de red conectadas a una máquina virtual.
* No se admite el reemplazo de una máquina virtual existente durante la restauración. Si intenta restaurar la máquina virtual cuando ya existe, la operación de restauración dará error.
* No se admiten la restauración y la copia de seguridad entre regiones.
* A partir de ahora, ya no se admiten la copia de seguridad ni la restauración de máquinas virtuales con ACL de almacenamiento. La copia de seguridad de máquinas virtuales no está admitida si se ha habilitado el almacenamiento en la característica de VNET que permite que las cuentas de almacenamiento solo sean accesibles desde determinadas redes virtuales o subredes y direcciones IP.
* Puede realizar copias de seguridad de máquinas virtuales en todas las regiones públicas de Azure (consulte la [lista de comprobación](https://azure.microsoft.com/regions/#services) de las regiones admitidas). Si la región que busca no se admite, no aparecerá en la lista desplegable durante la creación del almacén.
* La restauración de una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio solo se admite a través de PowerShell. Para más información, consulte [Restauración de máquinas virtuales de controlador de dominio](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Solo se admite la restauración de las máquinas virtuales que tienen las siguientes configuraciones especiales de red a través de PowerShell. Las máquinas virtuales que se crean a través del flujo de trabajo de restauración en la interfaz de usuario no tendrán estas configuraciones de red cuando se complete la operación de restauración. Si desea obtener más información, consulte [Restauración de máquinas virtuales con configuraciones de red especiales](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Máquinas virtuales con la configuración del equilibrador de carga (interna y externa).
  * Máquinas virtuales con varias direcciones IP reservadas.
  * Máquinas virtuales con varios adaptadores de red.

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Creación de un almacén de Recovery Services para una máquina virtual
Un almacén de Recovery Services es una entidad que almacena las copias de seguridad y los puntos de recuperación creados a lo largo del tiempo. También contiene las directivas de copia de seguridad asociadas con las máquinas virtuales protegidas.

Para crear un almacén de Recovery Services:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. En el menú de la **izquierda**, seleccione **Más servicios**y, después, escriba **Recovery Services**. En cuanto empiece a escribirlo, la entrada filtra la lista de recursos. Seleccione **Almacenes de Recovery Services**.

    ![Escribir en el cuadro y seleccionar "Almacenes de Recovery Services" en los resultados](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Aparece la lista de almacenes de Recovery Services.
3. En el menú **Almacenes de Recovery Services**, seleccione **Agregar**.

    ![Creación del almacén de Recovery Services, paso 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    Se abre el panel **Almacenes de Recovery Services**. En él se le solicita que rellene los campos **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.

    ![Panel "Almacenes de Recovery Services"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. y tener entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.
5. Seleccione **Suscripción** para ver la lista de suscripciones disponibles. Si no está seguro de la suscripción que va a utilizar, use la predeterminada (o sugerida). Solo hay varias opciones si la cuenta profesional o educativa está asociada a varias suscripciones de Azure.
6. Seleccione **Grupo de recursos** para ver la lista de grupos de recursos disponibles, o bien seleccione **Nuevo** para crear uno. Para más información sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. El almacén *debe* estar en la misma región que las máquinas virtuales que desea proteger.

   > [!IMPORTANT]
   > Si no está seguro de cuál es ubicación en la que se encuentra la máquina virtual, cierre el cuadro de diálogo de creación del almacén y vaya a la lista de máquinas virtuales del portal. Si tiene máquinas virtuales en varias regiones, debe crear un almacén de Recovery Services en cada una de ellas. Cree el almacén en la primera ubicación antes de pasar a la siguiente ubicación. No hay necesidad de especificar cuentas de almacenamiento para almacenar los datos de la copia de seguridad. Tanto el almacén de Recovery Services como el de Azure Backup lo controlan automáticamente.
   >
   >

8. Seleccione **Crear**. La creación del almacén de Recovery Services puede tardar unos minutos. Supervise las notificaciones del estado en la parte superior derecha del portal. Tras crear el almacén, este ya aparece en la lista de almacenes de Recovery Services. Si no lo ve, haga clic en **Actualizar**.

    ![Lista de copias de seguridad](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Ahora que ha creado el almacén, aprenda a configurar la replicación de almacenamiento.

## <a name="set-storage-replication"></a>Configuración de la replicación del almacenamiento
La opción de replicación del almacenamiento permite elegir entre almacenamiento con redundancia geográfica y almacenamiento con redundancia local. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si esta es su copia de seguridad principal, elija el almacenamiento con redundancia geográfica. Elija un almacenamiento con redundancia local si desea una opción más económica que no sea tan duradera.

Para editar la configuración de replicación de almacenamiento:

1. En el panel **Almacenes de Recovery Services**, seleccione el almacén.
    Al hacerlo, se abren el panel **Configuración** (con el nombre del almacén en la parte superior) y el panel de detalles del almacén.

   ![Elegir el almacén en la lista de almacenes de Backup](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. En el panel **Configuración**, desplácese hacia abajo con el control deslizante vertical hasta la sección **Administrar** y haga clic en **Infraestructura de Backup**. En la sección **General**, seleccione **Configuración de copia de seguridad**. En el panel **Configuración de copia de seguridad**, elija la opción de replicación del almacenamiento para su almacén. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica.

   ![Lista de copias de seguridad](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Si usa Azure como punto de conexión del almacenamiento de copia de seguridad principal, siga utilizando el almacenamiento con redundancia geográfica. Si se usa Azure como punto de conexión de almacenamiento de copia de seguridad no principal, elija el almacenamiento con redundancia local. Para más información acerca de las opciones de almacenamiento, consulte [Replicación de Azure Storage](../storage/common/storage-redundancy.md).

3. Si cambia el tipo de replicación de Storage, seleccione **Guardar**.
    
Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Selección de un objetivo de copia de seguridad, establecimiento de una directiva y definición de los elementos que se van a proteger
Antes de registrar una máquina virtual en un almacén, ejecute el proceso de detección para asegurarse de que se identifican las nuevas máquinas virtuales que se hayan agregado a la suscripción. El proceso consulta en Azure la lista de máquinas virtuales de la suscripción, junto con información como el nombre y la región del servicio en la nube. 

En Azure Portal, el término *escenario* hace referencia a lo que va a colocar en el almacén de Recovery Services. *Directiva* es la programación de la frecuencia con que se toman los puntos de recuperación y en qué momentos se hace. La directiva también incluye el intervalo de retención de los puntos de recuperación.

1. Si ya tiene abierto un almacén de Recovery Services vaya al paso 2. Si no tiene ningún almacén de Recovery Services abierto, abra [Azure Portal](https://portal.azure.com/). En el menú **izquierdo**, seleccione **Más servicios**.

   a. En la lista de recursos, escriba **Recovery Services**. En cuanto empiece a escribirlo, la entrada filtra la lista. Cuando vea la opción **Almacenes de Recovery Services**, haga clic en ella.

      ![Escribir en el cuadro y seleccionar "Almacenes de Recovery Services" en los resultados](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Aparece la lista de almacenes de Recovery Services. Si no hay almacenes en la suscripción, esta lista estará vacía.

      ![Visualización de la lista de almacenes de Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. En la lista de almacenes de Recovery Services, seleccione un almacén.

      Se abren el panel **Configuración** y el panel del almacén elegido.

      ![Panel Configuración y panel del almacén](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. En el menú del panel del almacén, seleccione **Backup**.

   ![Botón Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

   Se abren los paneles **Backup** y **Objetivo de Backup**.

3. En el panel **Objetivo de Backup**, en **¿Dónde se ejecuta su carga de trabajo?** seleccione **Azure** y en **¿De qué desea realizar copias de seguridad?**, seleccione **Máquina virtual**. Después seleccione **Aceptar**.

   ![Paneles Backup y Objetivo de Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   En este paso se registra la extensión de la máquina virtual en el almacén. El panel **Objetivo de Backup** se cierra y se abre el panel **Directiva de copia de seguridad**.

   ![Paneles "Backup" y "Directiva de copia de seguridad"](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. En el panel **Directiva de copia de seguridad**, seleccione la que desee aplicar al almacén.

   ![Seleccionar directiva de copia de seguridad](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Los detalles de la directiva predeterminada se muestran en el menú desplegable. Si quiere crear una directiva, seleccione **Crear nuevo** en el menú desplegable. Si quiere instrucciones para definir una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Haga clic en **Aceptar** para asociar la directiva de copia de seguridad con el almacén.

   El panel **Directiva de copia de seguridad** se cierra y se abre el panel **Seleccionar máquinas virtuales**.
5. En el panel **Seleccionar máquinas virtuales**, elija las máquinas virtuales que se asociarán con la directiva especificada y seleccione **Aceptar**.

   ![Panel "Seleccionar máquinas virtuales"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Se valida la máquina virtual seleccionada. Si no aparecen las máquinas virtuales que espera ver, compruebe que existen en la misma ubicación de Azure que el almacén de Recovery Services y que no están protegidas en otro almacén. El panel del almacén muestra la ubicación del almacén de Recovery Services.

6. Ahora que ha definido toda la configuración del almacén, en el panel **Backup**, haga clic en **Habilitar copia de seguridad**. En este paso se implementa la directiva en el almacén y en las máquinas virtuales. Este paso no crea el punto de recuperación inicial de la máquina virtual.

   ![Botón "Habilitar copia de seguridad"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Después de habilitar correctamente la copia de seguridad, la directiva de copia de seguridad se ejecutará según lo programado. Si desea generar un trabajo de copia de seguridad a petición para hacer una copia de seguridad de las máquinas virtuales ahora, consulte [Desencadenamiento del trabajo de copia de seguridad](./backup-azure-arm-vms.md#triggering-the-backup-job).

Si surgen problemas al registrar la máquina virtual, consulte la siguiente información acerca de cómo instalar el agente de máquina virtual y de la conectividad de red. Probablemente no necesite la siguiente información si va a proteger máquinas virtuales creadas en Azure. Sin embargo, si ha migrado las máquinas virtuales a Azure, asegúrese de que ha instalado correctamente el agente de máquina virtual y que la máquina virtual se puede comunicar con la red virtual.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Instalación del agente de máquina virtual en la máquina virtual
Para que la extensión de Backup funcione, el [agente de máquina virtual](../virtual-machines/windows/agent-user-guide.md) de Azure se debe instalar en la máquina virtual de Azure. Si la máquina virtual se ha creado en Microsoft Azure Marketplace, el agente de máquina virtual ya estará presente en la máquina virtual. 

La siguiente información se proporciona para aquellas situaciones en las que *no* se usa una máquina virtual creada en Microsoft Azure Marketplace. Por ejemplo, ha migrado una máquina virtual desde un centro de datos local. En ese caso, debe instalarse el agente de máquina virtual con el fin de proteger la máquina virtual.

Si tiene problemas para realizar una copia de seguridad de la máquina virtual de Azure, use la siguiente tabla para comprobar que el agente de máquina virtual de Azure está instalado correctamente en la máquina virtual. La tabla proporciona información adicional acerca del agente de máquina virtual para las máquinas virtuales Windows y Linux.

| **operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalar el agente de máquina virtual |Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Para completar la instalación, necesita privilegios de administrador. |Instale el [agente de Linux](../virtual-machines/linux/agent-user-guide.md) más reciente. Para completar la instalación, necesita privilegios de administrador. Se recomienda instalar el agente desde el repositorio de distribución. *No se recomienda* instalar el agente de máquina virtual Linux directamente desde GitHub.  |
| Actualizar el agente de máquina virtual |Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |Siga las instrucciones para [actualizar el agente de máquina virtual Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Se recomienda actualizar el agente desde el repositorio de distribución. *No es aconsejable* actualizar el agente de máquina virtual Linux directamente desde GitHub.<br><br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |
| Validar la instalación del agente de máquina virtual |1. Vaya a la carpeta C:\WindowsAzure\Packages de la máquina virtual de Azure. <br><br>2. Busque el archivo WaAppAgent.exe. <br><br>3. Haga clic con el botón derecho en el archivo, desplácese hasta **Propiedades** y seleccione la pestaña **Detalles**. En el campo **Versión del producto**, debe aparecer el valor 2.6.1198.718 o uno superior. |N/D |

### <a name="backup-extension"></a>Extensión de Backup
Después de que el agente de máquina virtual se instale en la máquina virtual, el servicio Azure Backup instala la extensión de copia de seguridad en el agente de máquina virtual. El servicio Backup actualiza la extensión de copia de seguridad y le aplica revisiones en un proceso que transcurre de forma fluida.

El servicio Backup instala la extensión de copia de seguridad tanto si la máquina virtual está en ejecución como si no lo está. Una máquina virtual en ejecución ofrece más probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Azure Backup sigue realizando la copia de seguridad de la máquina virtual aunque esté apagada y la extensión no se haya podido instalar. Esto se conoce como *máquina virtual sin conexión*. En este caso, el punto de recuperación será *coherente frente a bloqueos*.

## <a name="establish-network-connectivity"></a>Establecimiento de conectividad de red
Para administrar las instantáneas de máquina virtual, la extensión de copia de seguridad necesita conectividad con las direcciones IP públicas de Azure. Sin la conectividad a Internet adecuada, las solicitudes HTTP superan el tiempo de espera de la máquina virtual y se produce un error en la operación de copia de seguridad. Si la implementación tiene restricciones de acceso establecidas (por ejemplo, a través de un grupo de seguridad de red [NSG]), elija una de estas opciones para proporcionar una ruta de acceso clara para el tráfico de copia de seguridad:

* [Incluir los intervalos de IP de un centro de datos de Azure en una lista blanca](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Implementación de un servidor proxy HTTP para enrutar el tráfico.

Al decidir qué opción utilizar, hay que buscar el equilibrio entre costo, control pormenorizado y facilidad de uso.

| Opción | Ventajas | Desventajas |
| --- | --- | --- |
| Creación de una lista blanca con intervalos IP |Sin costos adicionales.<br><br>Para abrir el acceso en un grupo de seguridad de red, use el cmdlet **Set-AzureNetworkSecurityRule**. |Es complejo de administrar, ya que los intervalos de IP afectados cambian con el tiempo.<br><br>Proporciona acceso a la totalidad de Azure, no solo al almacenamiento. |
| Usar un servidor proxy HTTP |Se permite un control detallado en el proxy sobre las direcciones URL de almacenamiento.<br><br>Un único punto de acceso a Internet para las máquinas virtuales.<br><br>No están sujetas a cambios de direcciones IP de Azure. |Costes adicionales de ejecutar una máquina virtual con el software de proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Whitelist the Azure datacenter IP ranges
Para incluir los intervalos de IP de un centro de datos de Azure en una lista blanca, visite el [sitio web de Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653), donde encontrará información detallada acerca de los intervalos de IP, junto con instrucciones.

Con las [etiquetas de servicio](../virtual-network/security-overview.md#service-tags) puede permitir conexiones al almacenamiento de la región concreta. Asegúrese de que la regla que permite el acceso a la cuenta de almacenamiento tiene mayor prioridad que la que bloquea el acceso a Internet. 

![NSG con etiquetas de almacenamiento para una región](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

> [!WARNING]
> Las etiquetas del servicio de almacenamiento solo están disponibles en determinadas regiones y en versión preliminar. Para ver la lista de regiones, consulte el apartado [Etiquetas de servicio](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Uso de un proxy HTTP para copias de seguridad de máquinas virtuales
Cuando se realiza una copia de seguridad de una máquina virtual, la extensión de copia de seguridad de dicha máquina envía los comandos de administración de instantáneas a Azure Storage mediante una API de HTTPS. Enrute el tráfico de extensión de copia de seguridad a través del proxy HTTP, ya que es el único componente configurado para acceder a la red pública de Internet.

> [!NOTE]
> No se recomienda usar ningún software proxy específico. Asegúrese de que selecciona un proxy que sea compatible con los siguientes pasos de configuración.
>
>

En la imagen de ejemplo siguiente se muestran los tres pasos de configuración necesarios para utilizar un proxy HTTP:

* La máquina virtual de la aplicación enruta todo el tráfico HTTP enlazado dirigido a la red pública de Internet a través de la máquina virtual de proxy.
* Esta permite que entre el tráfico de las máquinas virtuales en la red virtual.
* El grupo de seguridad de red (NSG) llamado NSF-lockdown necesita una regla de seguridad que permita el tráfico de Internet saliente de la máquina virtual de proxy.

Para usar a un proxy HTTP para comunicarse con la red pública de Internet, siga estos pasos.

> [!NOTE]
> En estos pasos se utilizan nombres y valores concretos de este ejemplo. Cuando escriba (o pegue) los detalles en su código, use los nombres y valores de su implementación.

#### <a name="step-1-configure-outgoing-network-connections"></a>Paso 1: Configuración de las conexiones de red salientes
###### <a name="for-windows-machines"></a>Para máquinas Windows
En este procedimiento se establece la configuración del servidor proxy para la cuenta de sistema local.

1. Descargue [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Abra Internet Explorer ejecutando el comando siguiente desde un símbolo del sistema con privilegios elevados:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. En Internet Explorer, vaya a **Herramientas** > **Opciones de Internet** > **Conexiones** > **Configuración de LAN**.
4. Compruebe la configuración del proxy de la cuenta del sistema. Establezca la dirección IP y el puerto del proxy.
5. Cierre Internet Explorer.

El siguiente script establece una configuración del proxy a nivel de todo el equipo y la usa para el tráfico HTTP o HTTPS saliente. Si ha configurado un servidor proxy en una cuenta de usuario actual (no en una cuenta de sistema local), utilice este script para aplicarlo a SYSTEMACCOUNT.

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

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Paso 2: Permiso para las conexiones entrantes en el servidor proxy
1. En el servidor proxy, abra Firewall de Windows. La forma más fácil de acceder al firewall es buscar **Firewall de Windows con seguridad avanzada**.
2. En el cuadro de diálogo **Firewall de Windows con seguridad avanzada**, haga clic en **Reglas de entrada** y seleccione **Nueva regla**.
3. En el Asistente para nueva regla de entrada, en la página **Tipo de regla**, seleccione la **Personalizada** y, después, **Siguiente**.
4. En la página **Programa**, seleccione **Todos los programas** y seleccione **Siguiente**.
5. En la página **Protocolo y puertos**, escriba la siguiente información y seleccione **Siguiente**:
   * En **Tipo de protocolo**, seleccione **TCP**.
   * En **Puerto Local**, seleccione **Puertos específicos**. En el siguiente cuadro, especifique el número del puerto del proxy que se ha configurado.
   * En **Puerto remoto**, seleccione **Todos los puertos**.

En cuanto al resto del asistente, acepte la configuración predeterminada hasta llegar al final. Asigne un nombre a esta regla. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Paso 3: Incorporación de una regla de excepción al grupo de seguridad de red
El siguiente comando agrega una excepción al NSG. Esta excepción permite el tráfico TCP de cualquier puerto en la dirección IP 10.0.0.5 a cualquier dirección de Internet en los puertos 80 (HTTP) o 443 (HTTPS). Si necesita un puerto específico en la red pública de Internet, asegúrese de agregarlo a ```-DestinationPortRange```.

En un símbolo del sistema de Azure PowerShell, escriba el siguiente comando:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que desea que se incluya, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>pasos siguientes
Ahora que ha preparado el entorno para realizar la copia de seguridad de la máquina virtual, el siguiente paso lógico es crear una copia de seguridad. El artículo de planeamiento ofrece información más detallada sobre la realización de copias de seguridad de máquinas virtuales.

* [Copia de seguridad de máquinas virtuales](backup-azure-arm-vms.md)
* [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
* [Administración de copias de seguridad de máquinas virtuales](backup-azure-manage-vms.md)
