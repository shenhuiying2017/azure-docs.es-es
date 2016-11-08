---
title: Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager | Microsoft Docs
description: Asegúrese de que el entorno está preparado para la copia de seguridad de máquinas virtuales en Azure
services: backup
documentationcenter: ''
author: markgalioto
manager: cfreeman
editor: ''
keywords: copias de seguridad; realizar copia de seguridad

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2016
ms.author: trinadhk; jimpark; markgal;

---
# Preparación del entorno para la copia de seguridad de máquinas virtuales implementadas según el modelo de Resource Manager
> [!div class="op_single_selector"]
> * [Modelo de Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modelo clásico](backup-azure-vms-prepare.md)
> 
> 

En este artículo se proporcionan los pasos necesarios para preparar el entorno de copia de seguridad de una máquina virtual (VM) implementada según el modelo de Resource Manager. Los pasos que se muestran en los procedimientos utilizan el Portal de Azure.

El servicio Copia de seguridad de Azure dispone de dos tipos de almacenes (almacenes de copia de seguridad y almacenes de servicios de recuperación) para proteger las máquinas virtuales. Un almacén de copia de seguridad protege las máquinas virtuales implementadas mediante el modelo de implementación clásica. Un almacén de servicios de recuperación protege **tanto máquinas virtuales implementadas con el modelo clásico como las implementadas mediante Resource Manager**. Para proteger las máquinas virtuales implementadas según el modelo de Resource Manager, debe usar un almacén de Servicios de recuperación.

> [!NOTE]
> Azure cuenta con dos modelos de implementación para crear recursos y trabajar con ellos: [Resource Manager y el modelo clásico](../resource-manager-deployment-model.md). Consulte [Preparación del entorno de copia de seguridad de máquinas virtuales de Azure](backup-azure-vms-prepare.md) para más información sobre cómo trabajar con las máquinas virtuales del modelo de implementación clásica.
> 
> 

Para proteger una máquina virtual implementada según el modelo de Resource Manager o realizar una copia de seguridad de una de estas máquinas, asegúrese de cumplir los siguientes requisitos previos:

* Ha creado un almacén de Servicios de recuperación (o ha identificado uno existente) *en la misma ubicación que la VM*.
* Ha seleccionado un escenario, ha definido la directiva de copia de seguridad y ha definido los elementos que quiere proteger.
* Ha comprobado la instalación del agente de máquina virtual en la máquina virtual.
* Ha comprobado la conectividad de la red

Si sabe que estas condiciones ya existen en su entorno, vaya al artículo [Copia de seguridad de máquinas virtuales de Azure](backup-azure-vms.md). Si necesita configurar o comprobar cualquiera de estos requisitos previos, este artículo le guía por los pasos para prepararlos.

## Limitaciones al realizar copias de seguridad y restaurar una máquina virtual
Antes de preparar el entorno, tenga en cuenta las limitaciones.

* No se admite la copia de seguridad de máquinas virtuales con más de 16 discos de datos.
* No se admite la copia de seguridad de máquinas virtuales con una dirección IP reservada y sin puntos de conexión definidos.
* No se admite la copia de seguridad de máquinas virtuales de Linux con la extensión de Docker.
* Los datos de copia de seguridad no incluyen unidades montadas de red conectadas a la máquina virtual.
* No se admite el reemplazo de una máquina virtual existente durante la restauración. Si intenta restaurar la máquina virtual cuando ya existe, la operación de restauración dará error.
* No se admite la restauración y copia de seguridad entre regiones.
* Puede realizar copias de seguridad de máquinas virtuales en todas las regiones públicas de Azure (consulte la [lista de comprobación](https://azure.microsoft.com/regions/#services) de las regiones admitidas). Si la región que está buscando no es compatible actualmente, no aparecerá en la lista desplegable durante la creación del almacén.
* Solo puede realizar copias de seguridad de máquinas virtuales que tengan alguna de las siguientes versiones de sistema operativo:
  * **Linux**: Copia de seguridad de Azure admite [una lista de distribuciones aprobadas por Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md), con la excepción de CoreOS Linux. Otras distribuciones con la iniciativa "traiga su propio Linux" también podrían funcionar, siempre que el agente de máquina virtual esté disponible en la máquina virtual y haya compatibilidad con Python.
  * **Windows Server**: no se admiten las versiones anteriores a Windows Server 2008 R2.
* La restauración de una máquina virtual de controlador de dominio que forma parte de una configuración de varios controladores de dominio solo se admite a través de PowerShell. Más información sobre cómo [restaurar un controlador de dominio de varios controladores de dominio](backup-azure-restore-vms.md#restoring-domain-controller-vms)
* Solo se admite la restauración de las máquinas virtuales que tienen las siguientes configuraciones especiales de red a través de PowerShell. Las máquinas virtuales que se crean con el flujo de trabajo de restauración en la interfaz de usuario no tendrán estas configuraciones de red cuando se complete la operación de restauración. Si desea obtener más información, consulte [Restauración de máquinas virtuales con configuraciones de red especiales](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
  * Máquinas virtuales con la configuración del equilibrador de carga (interna y externa).
  * Máquinas virtuales con varias direcciones IP reservadas.
  * Máquinas virtuales con varios adaptadores de red.

## Creación de un almacén de Servicios de recuperación para una máquina virtual
Un almacén de Servicios de recuperación es una entidad que almacena las copias de seguridad y los puntos de recuperación creados con el tiempo. También contiene las directivas de copia de seguridad asociadas a las máquinas virtuales protegidas.

Para crear un almacén de Servicios de recuperación:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. En el menú del centro, haga clic en **Examinar** y, en la lista de recursos, escriba **Servicios de recuperación**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacén de Servicios de recuperación**.
   
    ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>
   
    Se muestra la lista de almacenes de Servicios de recuperación.
3. En el menú **Almacenes de Servicios de recuperación**, haga clic en **Agregar**.
   
    ![Creación del almacén de Servicios de recuperación, paso 2](./media/backup-azure-vms-first-look-arm/rs-vault-menu.png)
   
    Se abre la hoja del almacén de Servicios de recuperación, donde se le pide que proporcione los valores de **Nombre**, **Suscripción**, **Grupo de recursos** y **Ubicación**.
   
    ![Creación del almacén de Servicios de recuperación, paso 5](./media/backup-azure-vms-first-look-arm/rs-vault-attributes.png)
4. En **Nombre**, escriba un nombre descriptivo que identifique el almacén. El nombre debe ser único para la suscripción de Azure. Escriba un nombre que tenga entre 2 y 50 caracteres. Debe comenzar por una letra y solo puede contener letras, números y guiones.
5. Haga clic en **Suscripción** para ver la lista de suscripciones disponibles. Si no está seguro de la suscripción que desea utilizar, use la suscripción predeterminada (o sugerida). Solo habrá varias opciones si la cuenta de su organización está asociada a varias suscripciones de Azure.
6. Haga clic en **Grupo de recursos** para ver la lista de grupos de recursos disponibles o haga clic en **Nuevo** para crear uno. Para una información completa sobre los grupos de recursos, consulte [Información general de Azure Resource Manager](../resource-group-overview.md).
7. Haga clic en **Ubicación** para seleccionar la región geográfica del almacén. El almacén **debe** estar en la misma región que las máquinas virtuales que desea proteger.
   
   > [!IMPORTANT]
   > Si no está seguro de en qué ubicación existe la máquina virtual, cierre el cuadro de diálogo de creación del almacén y vaya a la lista de máquinas virtuales del portal. Si tiene máquinas virtuales en varias regiones, debe crear un almacén de Servicios de recuperación en cada región. Cree el almacén en la primera ubicación antes de pasar a la siguiente ubicación. No es necesario especificar cuentas de almacenamiento para almacenar los datos de copia de seguridad: el almacén de Servicios de recuperación y el servicio Copia de seguridad de Azure lo controlarán automáticamente.
   > 
   > 
8. Haga clic en **Crear**. La creación del almacén de Servicios de recuperación puede tardar unos minutos. Supervise las notificaciones de estado en la parte superior derecha del portal. Una vez creado el almacén, aparece en la lista de almacenes de servicios de recuperación.
   
    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look-arm/rs-list-of-vaults.png)
   
    Ahora que ha creado el almacén, aprenda a configurar la replicación de almacenamiento.

## Configuración de la replicación de almacenamiento
La opción de replicación de almacenamiento permite elegir entre almacenamiento con redundancia geográfica y almacenamiento con redundancia local. De forma predeterminada, el almacén tiene almacenamiento con redundancia geográfica. Si esta es su copia de seguridad principal, elija el almacenamiento con redundancia geográfica. Elija el almacenamiento con redundancia local si desea una opción más económica que no sea tan duradera. Para más información sobre las opciones de almacenamiento con [redundancia geográfica](../storage/storage-redundancy.md#geo-redundant-storage) y [local](../storage/storage-redundancy.md#locally-redundant-storage) consulte la [información general de replicación de Almacenamiento de Azure](../storage/storage-redundancy.md).

Para editar la configuración de replicación de almacenamiento:

1. Seleccione el almacén para abrir su panel y la hoja de configuración. Si la hoja **Configuración** no se abre, haga clic en **Toda la configuración** en el panel del almacén.
2. En la hoja **Configuración**, haga clic en **Infraestructura de copia de seguridad** > **Configuración de copia de seguridad** para abrir la hoja **Configuración de copia de seguridad**. En la hoja **Configuración de copia de seguridad**, elija la opción de replicación de almacenamiento para su almacén.
   
    ![Lista de copias de seguridad](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)
   
    Tras elegir la opción de almacenamiento del almacén, está listo para asociar la máquina virtual con el almacén. Para comenzar la asociación, es preciso detectar y registrar las máquinas virtuales de Azure.

## Selección de un objetivo de copia de seguridad, establecimiento de la directiva y definición de los elementos para proteger
Antes de registrar una máquina virtual en un almacén, ejecute el proceso de detección para asegurarse de que se identifican todas las nuevas máquinas virtuales que se hayan agregado a la suscripción. El proceso consulta a Azure para recibir la lista de máquinas virtuales incluidas en la suscripción, junto con información adicional, como el nombre del servicio en la nube y la región. En el Portal de Azure, el escenario se refiere a lo que va a colocar en el almacén de servicios de recuperación. Directiva es la programación de la frecuencia y de cuándo se eligen los puntos de recuperación. La directiva también incluye el intervalo de retención de los puntos de recuperación.

1. Si ya tiene abierto un almacén de Servicios de recuperación, vaya al paso 2. Si no tiene abierto un almacén de Servicios de recuperación, pero está en el Portal de Azure, en el menú del concentrador, haga clic en **Examinar**.
   
   * En la lista de recursos, escriba **Servicios de recuperación**.
   * Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Almacenes de servicios de recuperación** cuando lo vea.
     
     ![Creación del almacén de Servicios de recuperación, paso 1](./media/backup-azure-vms-first-look-arm/browse-to-rs-vaults.png) <br/>
     
     Aparece la lista de almacenes de Servicios de recuperación.
   * En la lista de almacenes de Servicios de recuperación, seleccione un almacén.
     
     Se abre el panel del almacén seleccionado.
     
     ![Hoja del almacén abierta](./media/backup-azure-vms-first-look-arm/vault-settings.png)
2. En el menú del panel del almacén, haga clic en **Copia de seguridad** para abrir la hoja Copia de seguridad.
   
    ![Hoja Copia de seguridad abierta](./media/backup-azure-vms-first-look-arm/backup-button.png)
   
    Cuando se abre la hoja, el servicio Copia de seguridad busca las máquinas virtuales nuevas en la suscripción.
   
    ![Detectar máquinas virtuales](./media/backup-azure-vms-first-look-arm/discovering-new-vms.png)
3. En la hoja Copia de seguridad, haga clic en **Objetivo de copia de seguridad** para abrir la hoja del mismo nombre.
   
    ![Hoja Escenario abierta](./media/backup-azure-vms-first-look-arm/select-backup-goal-one.png)
4. En la hoja Objetivo de copia de seguridad, establezca **¿Dónde se ejecuta su carga de trabajo?** en Azure y **¿De qué quiere realizar una copia de seguridad?** en Máquina virtual, y haga clic en **Aceptar**.
   
    La hoja Backup Goal (Objetivo de la copia de seguridad) se cierra y se abre la hoja Directiva de copia de seguridad.
   
    ![Hoja Escenario abierta](./media/backup-azure-vms-first-look-arm/select-backup-goal-two.png)
5. En la hoja Directiva de copia de seguridad, seleccione la que quiera aplicar al almacén y haga clic en **Aceptar**.
   
    ![Seleccionar directiva de copia de seguridad](./media/backup-azure-vms-first-look-arm/setting-rs-backup-policy-new.png)
   
    En los detalles se muestran los datos de la directiva predeterminada. Si quiere crear una directiva, seleccione **Crear nuevo** en el menú desplegable. El menú desplegable también proporciona una opción para cambiar la hora a la que se tomó la instantánea, a las 19:00. Si quiere instrucciones para definir una directiva de copia de seguridad, consulte [Definición de una directiva de copia de seguridad](backup-azure-vms-first-look-arm.md#defining-a-backup-policy). Al hacer clic en **Aceptar**, la directiva de copia de seguridad se asocia con el almacén.
   
    A continuación, elija las máquinas virtuales que se asociarán con el almacén.
6. Elija las máquinas virtuales que se asociarán con la directiva especificada y haga clic en **Seleccionar**.
   
    ![Seleccionar carga de trabajo](./media/backup-azure-vms-first-look-arm/select-vms-to-backup-new.png)
   
    Si aún no ve la máquina virtual, compruebe que existe en la misma ubicación de Azure que el almacén de Servicios de recuperación.
7. Ahora que ha definido toda la configuración del almacén, en la hoja Copia de seguridad, haga clic en **Habilitar copia de seguridad** en la parte inferior de la página. La directiva se implementa en el almacén y en las máquinas virtuales.
   
    ![Habilitar copia de seguridad](./media/backup-azure-vms-first-look-arm/enable-backup-settings-new.png)

La siguiente fase de la preparación es instalar el agente de máquina virtual o comprobar que esté instalado.

## Instale el agente de máquina virtual en la máquina virtual.
El agente de máquina virtual de Azure se debe instalar en la máquina virtual de Azure para que funcione la extensión de copia de seguridad. Si la máquina virtual se creó desde la galería de Azure, el agente de máquina virtual ya está presente en la máquina virtual. Esta información se proporciona para las situaciones donde *no* se utiliza una máquina virtual creada desde la galería de Azure; por ejemplo, si ha migrado una máquina virtual desde un centro de datos local. En tal caso, debe instalarse el agente de máquina virtual con el fin de proteger la máquina virtual.

Obtenga más información sobre el [agente de máquina virtual](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) y [cómo instalarlo](../virtual-machines/virtual-machines-windows-classic-manage-extensions.md).

Si tiene problemas para realizar una copia de seguridad de la máquina virtual de Azure, asegúrese de que el agente de máquina virtual de Azure está instalado correctamente en la máquina virtual (consulte la tabla siguiente). Si ha creado una máquina virtual personalizada, [asegúrese de que la casilla **Instalar el agente de máquina virtual** esté activada](../virtual-machines/virtual-machines-windows-classic-agents-and-extensions.md) antes de aprovisionar la máquina virtual.

La tabla siguiente proporciona información adicional acerca del agente de máquina virtual para las máquinas virtuales de Windows y Linux.

| **Operación** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalación del agente de la máquina virtual |<li>Descargue e instale el [MSI del agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necesitará privilegios de administrador para efectuar la instalación. <li>[Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |<li> Instale el [agente de Linux](https://github.com/Azure/WALinuxAgent) más reciente desde GitHub. Necesitará privilegios de administrador para efectuar la instalación. <li> [Actualice la propiedad de la máquina virtual](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que el agente está instalado. |
| Actualización del agente de la máquina virtual |Actualizar el agente de la máquina virtual es tan sencillo como volver a instalar los [archivos binarios del agente de la máquina virtual](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |Siga las instrucciones para [actualizar el agente de máquina virtual Linux](../virtual-machines/virtual-machines-linux-update-agent.md). <br>Asegúrese de que no se está ejecutando ninguna operación de copia de seguridad mientras se actualiza el agente de la máquina virtual. |
| Validación de la instalación del agente de máquina virtual |<li>Vaya a la carpeta *C:\\WindowsAzure\\Packages* de la máquina virtual de Azure. <li>El archivo WaAppAgent.exe debe estar ahí.<li> Haga clic con el botón derecho en el archivo, vaya a **Propiedades** y, luego, seleccione la pestaña **Detalles**. En el campo de versión del producto, debe aparecer el valor 2.6.1198.718 o uno superior. |N/D |

### Extensión de copia de seguridad
Una vez que el agente de máquina virtual está instalado en la máquina virtual, el servicio Copia de seguridad de Azure instala la extensión de copia de seguridad en el agente de máquina virtual. El servicio Copia de seguridad de Azure actualiza la extensión de copia de seguridad y le aplica revisiones en un proceso que transcurre de forma fluida.

El servicio Copia de seguridad instala la extensión de copia de seguridad tanto si la máquina virtual está en ejecución como si no lo está. Una máquina virtual en ejecución ofrece más probabilidad de obtener un punto de recuperación coherente con la aplicación. Sin embargo, el servicio Copia de seguridad de Azure sigue realizando la copia de seguridad de la máquina virtual aunque esté apagada y no haya sido posible instalar la extensión. Esto se conoce como máquina virtual sin conexión. En este caso, el punto de recuperación será *coherente frente a bloqueos*.

## Conectividad de red
Para administrar las instantáneas de máquina virtual, la extensión de copia de seguridad necesita conectividad a las direcciones IP públicas de Azure. Sin la conectividad a Internet adecuada, el tiempo de espera de las solicitudes HTTP de la máquina virtual se agota y se produce un error en la operación de copia de seguridad. Si la implementación tiene restricciones de acceso establecidas (a través de un grupo de seguridad de red (NSG), por ejemplo), elija entonces una de estas opciones para proporcionar una ruta de acceso clara para el tráfico de copia de seguridad:

* [Whitelist the Azure datacenter IP ranges](http://www.microsoft.com/es-ES/download/details.aspx?id=41653) (Creación de listas blancas con intervalos de direcciones IP de centro de datos de Azure): consulte este artículo para obtener instrucciones sobre cómo crear una lista blanca con las direcciones IP.
* Implementación de un servidor proxy HTTP para enrutar el tráfico.

Al decidir qué opción utilizar, busque el equilibrio entre costo, control granular y facilidad de uso.

| Opción | Ventajas | Desventajas |
| --- | --- | --- |
| Creación de una lista blanca con intervalos IP |Sin costos adicionales.<br><br>Para abrir el acceso en un grupo de seguridad de red, use el cmdlet <i>Set-AzureNetworkSecurityRule</i>. |Es complejo de administrar, ya que los intervalos de IP afectados cambian con el tiempo.<br><br>Proporciona acceso a la totalidad de Azure y no solo al almacenamiento. |
| Proxy HTTP |Se permite un control detallado en el proxy sobre las direcciones URL de almacenamiento.<br>Un único punto de acceso a Internet a las máquinas virtuales.<br>No están sujetas a cambios de direcciones IP de Azure. |Costes adicionales de ejecutar una máquina virtual con el software de proxy. |

### Preparar una lista blanca con los intervalos IP del centro de datos de Azure.
Para crear una lista blanca con los intervalos de IP de centro de datos de Azure, visite el [sitio web de Azure](http://www.microsoft.com/es-ES/download/details.aspx?id=41653). Ahí encontrará información detallada sobre los intervalos de IP, junto con instrucciones.

### Uso de un proxy HTTP para las copias de seguridad de máquinas virtuales
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

#### Paso 1. Configuración de las conexiones de red salientes
###### Para máquinas Windows
En este paso se configura el servidor proxy para la cuenta de sistema local.

1. Descargue [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
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

###### Para máquinas Linux
Agregue la siguiente línea al archivo ```/etc/environment```:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Agregue las líneas siguientes al archivo ```/etc/waagent.conf```:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### Paso 2: Aceptación de conexiones entrantes en el servidor proxy:
1. En el servidor proxy, abra Firewall de Windows. La manera más fácil de acceder al firewall es buscar Firewall de Windows con seguridad avanzada.
   
    ![Abrir el firewall](./media/backup-azure-vms-prepare/firewall-01.png)
2. En el cuadro de diálogo Firewall de Windows, haga clic con el botón derecho en **Reglas de entrada** y haga clic en **Nueva regla**.
   
    ![Crear una nueva regla](./media/backup-azure-vms-prepare/firewall-02.png)
3. En **Asistente para nueva regla de entrada**, elija la opción **Personalizada** para el **Tipo de regla** y haga clic en **Siguiente**.
4. En la pantalla para seleccionar el **programa**, elija **Todos los programas** y haga clic en **Siguiente**.
5. En la página **Protocolo y puertos**, escriba la información siguiente y haga clic en **Siguiente**:
   
    ![Crear una nueva regla](./media/backup-azure-vms-prepare/firewall-03.png)
   
   * n *Tipo de protocolo*, elija *TCP*.
   * En *Puerto Local*, elija *Puertos específicos* y, en el campo siguiente, especifique el valor de ```<Proxy Port>``` configurado.
   * En *Puerto remoto*, seleccione *Todos los puertos*.
     
     Durante el resto del asistente, haga clic en todas las pantallas hasta el final y asigne un nombre a esta regla.

#### Paso 3: Adición de una regla de excepción al grupo de seguridad de red
En un símbolo del sistema de Azure PowerShell, escriba el siguiente comando:

El siguiente comando agrega una excepción al NSG. Esta excepción permite el tráfico TCP de cualquier puerto en la dirección IP 10.0.0.5 a cualquier dirección de Internet en el puerto 80 (HTTP) o 443 (HTTPS). Si necesita un puerto específico en la red pública de Internet, asegúrese de agregarlo también a ```-DestinationPortRange```.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```


*En estos pasos se utilizan valores y nombres específicos para este ejemplo. Cuando especifique detalles en el código, o los corte o pegue, use los nombres y los valores de su implementación.*

Ahora que sabe que tiene conectividad de red, está listo para realizar la copia de seguridad de la máquina virtual. Consulte [Copia de seguridad de máquinas virtuales de Azure en un almacén de Servicios de recuperación](backup-azure-arm-vms.md).

## ¿Tiene preguntas?
Si tiene alguna pregunta o hay alguna característica que le gustaría que se incluyera, [envíenos sus comentarios](http://aka.ms/azurebackup_feedback).

## Pasos siguientes
Ahora que ha preparado el entorno para realizar la copia de seguridad de la máquina virtual, el siguiente paso lógico es crear una copia de seguridad. El artículo de planeamiento ofrece información más detallada sobre la realización de copias de seguridad de máquinas virtuales.

* [Copia de seguridad de máquinas virtuales](backup-azure-vms.md)
* [Planeación de la infraestructura de copia de seguridad de máquinas virtuales](backup-azure-vms-introduction.md)
* [Administración de copias de seguridad de máquinas virtuales](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0831_2016-->