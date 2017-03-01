---
title: "Preparación del entorno para realizar copias de seguridad de Azure Virtual Machines | Microsoft Docs"
description: "Asegúrese de que el entorno está preparado para la copia de seguridad de máquinas virtuales en Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonn
editor: 
keywords: copias de seguridad; realizar copia de seguridad
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: f517a649a6c6aa65b350767bc66cf4d60c7988b5
ms.openlocfilehash: 9a114e954d59dcecaf3310e024428770bc4a2349
ms.lasthandoff: 02/24/2017


---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Preparación del entorno de copia de seguridad de máquinas virtuales de Azure
> [!div class="op_single_selector"]
> * [Modelo de Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modelo clásico](backup-azure-vms-prepare.md)
>
>

Para hacer copias de seguridad de una máquina virtual (VM) de Azure, deben darse tres condiciones.

* Tiene que crear un almacén de copia de seguridad o identificar un almacén de copia de seguridad existente *en la misma región que la máquina virtual*.
* Establecer la conectividad de red entre las direcciones de Internet públicas Azure y los puntos de conexión de almacenamiento de Azure.
* Instalar el agente de máquina virtual en la VM.

Si sabe que estas condiciones ya existen en su entorno, vaya al artículo [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms.md). De lo contrario, continúe leyendo, este artículo le guiará por los pasos para preparar el entorno para realizar la copia de una VM de Azure.

##<a name="supported-operating-system-for-backup"></a>Sistemas operativos compatibles para copia de seguridad
 * **Linux**: Copia de seguridad de Azure admite [una lista de distribuciones aprobadas por Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , con la excepción de CoreOS Linux. _Otras distribuciones con la iniciativa "traiga su propio Linux" también podrían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual y haya compatibilidad con Python. Sin embargo, no respaldamos esas distribuciones para copia de seguridad._
 * **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitaciones al realizar copias de seguridad y restaurar una máquina virtual
> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../azure-resource-manager/resource-manager-deployment-model.md). La siguiente lista proporciona las limitaciones cuando se implementa en el modelo clásico.
>
>

* No se admite la copia de seguridad de máquinas virtuales con más de 16 discos de datos.
* No se admite la copia de seguridad de máquinas virtuales con una dirección IP reservada y sin puntos de conexión definidos.
* Los datos de copia de seguridad no incluyen unidades montadas de red conectadas a la máquina virtual.
* No se admite el reemplazo de una máquina virtual existente durante la restauración. Primero, elimine la máquina virtual existente y los discos asociados y, a continuación, restaure los datos de copia de seguridad.
* No se admite la restauración y copia de seguridad entre regiones.
* Se admite la copia de seguridad de máquinas virtuales mediante el uso del servicio Copia de seguridad de Azure en todas las regiones públicas de Azure (consulte la [lista de comprobación](https://azure.microsoft.com/regions/#services) de las regiones compatibles). Si la región que está buscando no es compatible actualmente, no aparecerá en la lista desplegable durante la creación del almacén.
* La copia de seguridad de máquinas virtuales con el servicio Copia de seguridad de Azure solo se admite en determinadas versiones de sistemas operativos:
* La restauración de una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio solo se admite a través de PowerShell. Más información sobre cómo [restaurar un controlador de dominio de varios controladores de dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms)
* Solo se admite la restauración de las máquinas virtuales que tienen las siguientes configuraciones especiales de red a través de PowerShell. Las máquinas virtuales que se crean con el flujo de trabajo de restauración en la interfaz de usuario no tendrán estas configuraciones de red cuando se complete la operación de restauración. Si desea obtener más información, consulte [Restauración de máquinas virtuales con configuraciones de red especiales](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Máquinas virtuales con la configuración del equilibrador de carga (interna y externa).
  * Máquinas virtuales con varias direcciones IP reservadas.
  * Máquinas virtuales con varios adaptadores de red.

## <a name="create-a-backup-vault-for-a-vm"></a>Creación de un almacén de copia de seguridad para una máquina virtual
Un almacén de copia de seguridad es una entidad que almacena todas las copias de seguridad y los puntos de recuperación creados con el tiempo. El almacén de copia de seguridad contiene también las directivas de copia de seguridad que se aplicarán a las máquinas virtuales cuya copia de seguridad se está realizando.

La siguiente imagen muestra las relaciones entre las diversas entidades de Azure Backup:     ![entidades y relaciones de Azure Backup](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para crear un almacén de copia de seguridad:

1. Inicie sesión en el [Portal de Azure](http://manage.windowsazure.com/).
2. En Azure Portal, haga clic en **Nuevo** > **Integración híbrida** > **Copia de seguridad**. Al hacer clic en **Copia de seguridad**, cambiará automáticamente al portal clásico (que aparece después de la nota).

    ![Portal de Ibiza](./media/backup-azure-vms-prepare/Ibiza-portal-backup01.png)

   > [!NOTE]
   > Si la suscripción se usó por última vez en el portal clásico, es posible que la suscripción se abra en el portal clásico. En este evento, para crear un almacén de copia de seguridad, haga clic en **Nuevo** > **Data Services** > **Recovery Services** > **
Almacén de copia de seguridad** > **Creación rápida** (vea la imagen siguiente).
   >
   >

    ![Crear un almacén de copia de seguridad](./media/backup-azure-vms-prepare/backup_vaultcreate.png)
3. En **Nombre**, escriba un nombre descriptivo para identificar el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.
4. En **Región**, seleccione la región geográfica del almacén. El almacén debe estar en la misma región que las máquinas virtuales que desea proteger. Si tiene máquinas virtuales en varias regiones, debe crear un almacén de copia de seguridad en cada región. No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de copia de seguridad y el servicio Copia de seguridad de Azure lo controlarán automáticamente.
5. En **Suscripción** , seleccione la suscripción que desea asociar al almacén de copia de seguridad. Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.
6. Haga clic en **Crear almacén**. La creación del almacén de credenciales de copia de seguridad puede tardar unos minutos. Supervise las notificaciones de estado en la parte inferior del portal.

    ![Crear la notificación del sistema del almacén](./media/backup-azure-vms-prepare/creating-vault.png)
7. Un mensaje confirmará que el almacén se creó correctamente. En la página de **Recovery Services** se mostrará como **Activo**. Asegúrese de elegir la opción de redundancia de almacenamiento apropiada justo después de crear el almacén. Más información acerca de cómo [establecer la opción de redundancia de almacenamiento en el almacén de copia de seguridad](backup-configure-vault.md#create-a-recovery-services-vault).

    ![Lista de copias de seguridad](./media/backup-azure-vms-prepare/backup_vaultslist.png)
8. Haga clic en el almacén de copia de seguridad para ir a la página **Inicio rápido** , donde se muestran las instrucciones para realizar la copia de seguridad de las máquinas virtuales de Azure.

    ![Instrucciones de copia de seguridad de máquina virtual en la página del Panel](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

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

El siguiente comando agrega una excepción al NSG. Esta excepción permite el tráfico TCP de cualquier puerto en la dirección IP 10.0.0.5 a cualquier dirección de Internet en el puerto 80 (HTTP) o 443 (HTTPS). Si necesita un puerto específico en la red pública de Internet, asegúrese de agregarlo también a ```-DestinationPortRange``` .

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*Asegúrese de sustituir los nombres en el ejemplo con los detalles correspondientes a la implementación.*

## <a name="vm-agent"></a>Agente de máquina virtual
Antes de empezar a realizar la copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure esté instalado correctamente en la máquina virtual. Como el agente de máquina virtual es un componente opcional en el momento de crearse la máquina virtual, asegúrese de que la casilla correspondiente al agente de máquina virtual esté activada antes de aprovisionar la máquina virtual.

### <a name="manual-installation-and-update"></a>Instalación manual y actualización
El agente de la máquina virtual ya está presente en las máquinas virtuales que se crean desde la Galería de Azure. Sin embargo, las máquinas virtuales que se migran desde los centros de datos locales no tienen instalado el agente de la máquina virtual. Para dichas máquinas virtuales, el agente de la máquina virtual debe instalarse explícitamente. Lea más sobre cómo [instalar el agente de la máquina virtual en una máquina virtual existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual |<li>Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación. <li>[Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |<li> Instale el [agente de Linux](https://github.com/Azure/WALinuxAgent) más reciente desde GitHub. Necesitará privilegios de administrador para efectuar la instalación. <li> [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |
| Actualización del agente de la máquina virtual |Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |Siga las instrucciones para [actualizar el agente de máquina virtual Linux ](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br><br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |
| Validación de la instalación del agente de máquina virtual |<li>Acceda a la carpeta *C:\WindowsAzure\Packages* de la máquina virtual de Azure. <li>El archivo WaAppAgent.exe debe estar ahí.<li> Haga clic con el botón derecho en el archivo, desplácese hasta **Propiedades** y seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior. |N/D |

Obtenga información acerca del [agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y [cómo instalarlo](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Extensión de copia de seguridad
Para crear la copia de seguridad de la máquina virtual, el servicio Copia de seguridad de Azure instala una extensión al agente de máquina virtual. El servicio Copia de seguridad de Azure actualiza y aplica revisiones perfectamente a la extensión de copia de seguridad sin la intervención del usuario.

Si se ejecuta la máquina virtual, se instala la extensión de copia de seguridad. Una máquina virtual en ejecución también ofrece la mayor probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Copia de seguridad de Azure continuará realizando una copia de seguridad de la máquina virtual, incluso si está desactivada y la extensión no se ha podido instalar (lo que se conoce también como máquina virtual sin conexión). En este caso, el punto de recuperación será *coherente frente a bloqueos* , como se explicó anteriormente.

## <a name="questions"></a>¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha preparado el entorno para realizar la copia de seguridad de la máquina virtual, el siguiente paso lógico es crear una copia de seguridad. El artículo de planeamiento ofrece información más detallada sobre la realización de copias de seguridad de máquinas virtuales.

* [Copia de seguridad de máquinas virtuales](backup-azure-vms.md)
* [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
* [Administración de copias de seguridad de máquinas virtuales](backup-azure-manage-vms.md)

