<properties
	pageTitle="Replicación de máquinas virtuales de Hyper-V en nubes de VMM en Azure mediante Azure Site Recovery con el Portal de Azure | Microsoft Azure"
	description="Describe cómo implementar Azure Site Recovery para orquestar la replicación, ejecutar la conmutación por error y la recuperación de máquinas virtuales de Hyper-V en nubes de VMM en Azure mediante el Portal de Azure"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="05/10/2016"
	ms.author="raynew"/>

# Replicación de máquinas virtuales de Hyper-V en nubes de VMM en Azure mediante Azure Site Recovery con el Portal de Azure | Microsoft Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-azure.md)
- [Azure clásico](site-recovery-vmm-to-azure-classic.md)
- [ARM de PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [PowerShell clásico](site-recovery-deploy-with-powershell.md)

Bienvenido a Azure Site Recovery. Utilice este artículo si desea replicar máquinas virtuales locales de Hyper-V, administradas en nubes de System Center Virtual Machine Manager (VMM), en Azure mediante Azure Site Recovery en el Portal de Azure.

> [AZURE.NOTE] Azure tiene dos [modelos de implementación](../resource-manager-deployment-model) diferentes para crear y trabajar con recursos: el de Azure Resource Manager (ARM) y el clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación.


Azure Site Recovery en el Portal de Azure proporciona una serie de nuevas características:

- En el Portal de Azure los servicios de Copia de seguridad de Azure y Azure Site Recovery se combinan en un único almacén de Servicios de recuperación para que pueda configurar y administrar la continuidad empresarial y la recuperación ante desastres (BCDR) desde una sola ubicación. Un panel unificado permite supervisar y administrar operaciones en los sitios locales y en la nube pública de Azure.
- Los usuarios con suscripciones de Azure aprovisionadas con el programa Proveedor de soluciones en la nube (CSP) pueden administrar ya las operaciones de Site Recovery en el Portal de Azure.
- Site Recovery en el Portal de Azure puede replicar máquinas en cuentas de almacenamiento de ARM. En una conmutación por error, Site Recovery crea máquinas virtuales basadas en ARM en Azure.
- Site Recovery sigue siendo compatible con la replicación en cuentas de almacenamiento clásicas. En una conmutación por error, Site Recovery crea máquinas virtuales mediante el modelo clásico.


Después de leer este artículo, publique los comentarios en la parte inferior de los comentarios de Disqus. Formule cualquier pregunta técnica en el [Foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Información general

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y garanticen que las cargas de trabajo estarán disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Azure Site Recovery?](site-recovery-overview.md)

Este artículo proporciona toda la información que debe replicar en Azure desde las máquinas virtuales locales de Hyper-V en nubes de VMM. Incluye información general sobre la arquitectura, información de planeamiento y pasos de implementación para la configuración de Azure, los servidores locales, la configuración de replicación y el planeamiento de la capacidad. Después de configurar la infraestructura, puede habilitar la replicación en los equipos que desea proteger y comprobar que funciona la conmutación por error.


## Ventajas empresariales

- Site Recovery proporciona protección fuera del sitio para cargas de trabajo empresariales y aplicaciones que se ejecutan en máquinas virtuales de Hyper-V. 
- El portal de Servicios de recuperación proporciona una ubicación única para configurar, administrar y supervisar la replicación, la conmutación por error y la recuperación. 
- Puede ejecutar fácilmente conmutaciones por error desde su infraestructura local a Azure y conmutaciones por recuperación (restauración) desde Azure a servidores host del sitio local. 
- Puede configurar planes de recuperación con varios equipos de modo que las cargas de trabajo de la aplicación en capas experimenten la conmutación por error al mismo tiempo. 


## Arquitectura del escenario


Estos son los componentes del escenario:

- **Servidor VMM**: un servidor VMM local con una o más nubes.
- **Host o clúster de Hyper-V**: servidores host o clústeres de Hyper-V que se administran en las nubes de VMM.
- **Agente de Servicios de recuperación y proveedor de Azure Site Recovery**: durante la implementación, instale el proveedor de Azure Site Recovery en el servidor VMM y el agente de Servicios de recuperación de Azure en los servidores host de Hyper-V. El proveedor del servidor VMM se comunica con Site Recovery mediante HTTPS 443 para replicar la orquestación. El agente en el servidor host de Hyper-V replica los datos en el almacenamiento de Azure mediante HTTPS 443 de forma predeterminada.
- **Azure**: necesita una suscripción, una cuenta de almacenamiento para almacenar los datos replicados y una red virtual de Azure para que las máquinas virtuales se conecten a una red después de la conmutación por error.

![Topología E2A](./media/site-recovery-vmm-to-azure/architecture.png)


## Requisitos previos de Azure

Aquí está lo que necesita en Azure para implementar este escenario.

**Requisito previo** | **Detalles**
--- | ---
**Cuenta de Azure**| Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Obtenga más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. 
**Almacenamiento de Azure** | Necesitará una cuenta de almacenamiento estándar de Azure para almacenar los datos replicados. Puede usar una cuenta de almacenamiento LRS o GRS. Se recomienda GRS para que los datos sean resistentes si se produce una interrupción regional o si no se puede recuperar la región principal. [Más información](../storage/storage-redundancy.md). La cuenta debe estar en la misma región que el almacén de Servicios de recuperación.<br/><br/>No se admite el almacenamiento premium.<br/><br/> Los datos replicados se almacenan en el almacenamiento de Azure y las máquinas virtuales de Azure se crean cuando se produce la conmutación por error. <br/><br/> [Más información sobre](../storage/storage-introduction.md) almacenamiento de Azure.
**Red de Azure** | Necesitará una red virtual de Azure a la que se conectarán las máquinas virtuales de Azure cuando se produzca la conmutación por error. La red virtual de Azure debe estar en la misma región que el almacén de Servicios de recuperación.

## Requisitos previos locales

Esto es lo que se necesita en el entorno local

**Requisito previo** | **Detalles**
--- | ---
**VMM**| Uno o más servidores VMM con System Center 2012 R2. Cada servidor VMM debe tener una o más nubes configuradas. Una nube debe incluir:<br/><br/> uno o más grupos de hosts VMM.<br/><br/> Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts. <br/><br/>[Más información](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html) acerca de cómo configurar las nubes de VMM.
**Hyper-V** | Los servidores host de Hyper-V deben estar ejecutando al menos Windows Server 2012 R2 con el rol de Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/> Un servidor de Hyper-V debe contener una o más máquinas virtuales.<br/><br/> El servidor de host de Hyper-V o clúster que incluye las máquinas virtuales que desea replicar debe administrarse en una nube de VMM.<br/><br/>Los servidores de Hyper-V deben estar conectados a Internet, directamente o a través de un proxy.<br/><br/>Los servidores de Hyper-V deben tener instaladas las correcciones mencionadas en el artículo [2961977](https://support.microsoft.com/kb/2961977).<br/><br/>Los servidores host de Hyper-V necesitan acceso a Internet para la replicación de datos en Azure. 
**Proveedor y agente** | Durante la implementación de Azure Site Recovery, instalará el proveedor de Azure Site Recovery en el servidor VMM y el agente de Servicios de recuperación en los servidores de Hyper-V. El proveedor y el agente deberán conectarse a Azure a través de Internet directamente o a través de un proxy. Tenga en cuenta que no se admite un proxy basado en HTTPS. El servidor proxy del servidor VMM y los hosts de Hyper-V deben permitir el acceso a: <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net <br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/> *.store.core.windows.net<br/><br/>Si tiene reglas de firewall basadas en direcciones IP en el servidor VMM, compruebe que las reglas permitan la comunicación con Azure. Debe permitir los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y el protocolo HTTPS (433).<br/><br/>Permita los intervalos IP para la región de Azure de su suscripción y para el oeste de Estados Unidos.<br/><br/>Además, el servidor proxy en el servidor VMM necesitará acceso a https://www.msftncsi.com/ncsi.txt


## Requisitos previos de equipos protegidos


**Requisito previo** | **Detalles**
--- | ---
**Máquinas virtuales protegidas** | Antes de conmutar por error una máquina virtual debe asegurarse de que el nombre que se asignará a la máquina virtual de Azure cumple con los [requisitos previos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements). Puede modificar el nombre después de habilitar la replicación para la máquina virtual. <br/><br/> La capacidad del disco individual en equipos protegidos no debe ser superior a 1023 GB. Una máquina virtual puede tener hasta 64 discos (es decir, hasta 64 TB).<br/><br/> No se admiten los clústeres invitados de discos compartidos.<br/><br/> No se admite el arranque de Unified Extensible Firmware Interface (UEFI)/Extensible Firmware Interface (EFI).<br/><br/> Si la máquina virtual de origen tiene formación de equipos NIC, se convertirá en una sola NIC después de la conmutación por error en Azure.<br/><br/>No se admite la protección de máquinas virtuales que ejecutan Linux con una dirección IP estática.

## Preparación de la implementación

Para preparar la implementación deberá:

1. [Configurar una red de Azure](#set-up-an-azure-network) en la que las máquinas virtuales de 
2. se ubicarán después de la conmutación por error. 
2. [Configurar una cuenta de almacenamiento de Azure](#set-up-an-azure-storage-account) para datos replicados.
4. [Preparar el servidor VMM](#prepare-the-vmm-server) para la implementación de Azure Site Recovery. 
5. [Preparar la asignación de red](#prepare-for-network-mapping). Configurar redes para que pueda configurar la asignación de red durante la implementación de Site Recovery.

### Configuración de una red de Azure

Se necesita una red de Azure para que las máquinas virtuales de Azure creadas después de la conmutación por error se conecten a ella.

- La red debe estar en la misma región que aquella en la que va a implementar el almacén de Servicios de recuperación.
- Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, va a configurar la red de Azure en el [modo ARM](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) o en el [modo clásico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).
- Es recomendable configurar una red antes de empezar. Si no lo hace, deberá hacerlo durante la implementación de Site Recovery.


### Configuración de una cuenta de almacenamiento de Azure

- Necesitará una cuenta de almacenamiento estándar para almacenar los datos replicados en Azure. La cuenta debe estar en la misma región que el almacén de Servicios de recuperación.
- Según el modelo de recursos que desee usar para las máquinas virtuales de Azure conmutadas por error, configurará una cuenta en el [modo ARM](../storage/storage-create-storage-account.md) o en el [modo clásico](../storage/storage-create-storage-account-classic-portal.md).
- Es recomendable configurar una cuenta antes de empezar. Si no lo hace, deberá hacerlo durante la implementación de Site Recovery.

### Prepare el servidor VMM

- Asegúrese de que el servidor VMM cumpla con los [requisitos previos](#on-premises-prerequisites).
- Durante la implementación de Site Recovery puede especificar que todas las nubes de un servidor VMM deben estar disponibles en el Portal de Azure. Si solo desea que aparezcan nubes concretas en el portal, puede habilitar este valor en la nube en la consola de administrador de VMM. 


### Preparar la asignación de red

Debe configurar la asignación de red durante la implementación de Site Recovery. La asignación de red se produce entre redes de máquinas virtuales de VMM de origen y redes de Azure de destino para permitir lo siguiente:

- Las máquinas que se conmuten por error en la misma red pueden conectarse entre sí, aunque no estén conmutadas de la misma manera o en el mismo plan de recuperación.
- Si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se podrán conectar a máquinas virtuales locales.
- Para configurar una asignación de red, esto es lo que debe preparar:

	- Asegúrese de que las máquinas virtuales del servidor host de Hyper-V de origen estén conectadas a una red de máquinas virtuales de VMM. Esa red debe estar vinculada a una red lógica asociada con la nube.
	- Una red de Azure como la descrita [anteriormente](#set-up-an-azure-network)

- [Más información](site-recovery-network-mapping.md) sobre cómo funciona la asignación de redes.


## Creación de un almacén de Servicios de recuperación

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo** > **Administración** > **Servicios de recuperación**. También puede hacer clic en **Examinar** > **Almacén de Servicios de recuperación** > **Agregar**.

	![Almacén nuevo](./media/site-recovery-vmm-to-azure/new-vault3.png)

3. En **Nombre**, especifique un nombre descriptivo para identificar el almacén. Si tiene más de una suscripción, seleccione una de ellas.
4. [Cree un nuevo grupo de recursos](../resource-group-portal.md#create-resource-group) o seleccione uno existente. Especifique una región de Azure. Las máquinas se replicarán en esta región. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si desea acceder rápidamente al almacén desde el panel haga clic en **Anclar al panel** y, luego, en **Crear almacén**.

	![Almacén nuevo](./media/site-recovery-vmm-to-azure/new-vault-settings.png)

El nuevo almacén aparecerá en el **Panel** > **Todos los recursos**, y en la hoja principal de **Almacenes de Servicios de recuperación**.

## Introducción

Site Recovery proporciona una experiencia introductoria que le ayuda a realizar la implementación lo más rápido posible. Esta introducción comprueba los requisitos previos y le guía a través de los pasos de implementación de Site Recovery en el orden correcto.

En esta introducción puede seleccionar el tipo de máquinas que desea replicar y la ubicación donde desea replicarlas. Puede configurar servidores locales, cuentas de almacenamiento de Azure y redes. Es posible crear directivas de replicación y planear la capacidad. Una vez que haya configurado la infraestructura, puede habilitar la replicación para máquinas virtuales. Puede ejecutar conmutaciones por error para máquinas específicas o crear planes de recuperación para conmutar por error varias máquinas.

Comience la experiencia de introducción decidiendo cómo desea implementar Site Recovery. El flujo descrito en esta introducción cambia ligeramente en función de los requisitos de replicación.



## Paso 1: Seleccione los objetivos de protección

Seleccione aquello que desea replicar y la ubicación en donde se va a realizar la replicación.

1. En la hoja **Almacenes de Servicios de recuperación** seleccione el almacén y haga clic en **Configuración**.
2. En **Introducción** haga clic en **Site Recovery** > **Paso 1: Preparar la infraestructura** > **Objetivo de protección**.

	![Elegir objetivos](./media/site-recovery-vmm-to-azure/choose-goals.png)

3. En **Objetivo de protección** seleccione **En Azure**, y seleccione **Sí, con Hyper-V**. Seleccione **Sí** para confirmar que usa VMM para administrar los hosts de Hyper-V y el sitio de recuperación. y, a continuación, haga clic en **Aceptar**.

	![Elegir objetivos](./media/site-recovery-vmm-to-azure/choose-goals2.png)



## Paso 2: Configuración del entorno de origen

Instale el Proveedor de Azure Site Recovery en el servidor VMM y registre el servidor en el almacén. Instale el agente de los Servicios de recuperación de Azure en servidores host de Hyper-V.

1. Haga clic en **paso 2: Preparar infraestructura** > **Origen**. 

	![Configurar origen](./media/site-recovery-vmm-to-azure/set-source1.png)

2. En **Preparar origen** haga clic en **+ VMM** para agregar un servidor VMM.

	![Configurar origen](./media/site-recovery-vmm-to-azure/set-source2.png)

3. En la hoja **Agregar servidor** compruebe que aparece el **servidor de System Center VMM** en **Tipo de servidor** y que este cumple con los [requisitos previos y los requisitos de direcciones URL](#on-premises-prerequisites).
4. Descargue el archivo de instalación del proveedor de Azure Site Recovery.
5. Descargue la clave de registro. Se le solicitará cuando ejecute el programa de instalación. La clave será válida durante 5 días a partir del momento en que se genera. 

	![Configurar origen](./media/site-recovery-vmm-to-azure/set-source3.png)

6. Instale el proveedor de Azure Site Recovery en el servidor VMM.


### Instalación del proveedor de Azure Site Recovery

1.	Ejecute el archivo de configuración del proveedor.
2. En **Microsoft Update** puede participar en actualizaciones para que las actualizaciones del proveedor se realicen según las directivas de Microsoft Update.
3. En **Instalación** acepte o modifique la ubicación predeterminada de instalación del proveedor y haga clic en **Instalar**.

	![Ubicación de instalación](./media/site-recovery-vmm-to-azure/provider2.png)

4. Una vez finalizada la instalación, haga clic en **Registrar** para registrar el servidor VMM en el almacén.
5. En **Conexión a Internet**, especifique cómo se conecta a Site Recovery el proveedor que se ejecuta en el servidor VMM mediante Internet.

	- Si quiere que el proveedor se conecte directamente, seleccione **Conectarse directamente sin un proxy**.
	- Si quiere conectarse con el proxy configurado actualmente en el servidor, seleccione **Conectarse con la configuración de proxy existente**.
	- Si el proxy existente requiere autenticación, o quiere utilizar un proxy personalizado, seleccione **Conectarse con una configuración de proxy personalizada**.
	- Si utiliza un proxy personalizado, deberá especificar la dirección, el puerto y las credenciales.
	- Si utiliza un servidor proxy, se deberían haber permitido ya las direcciones URL descritas en los [requisitos previos](#on-premises-prerequisites).
	- Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. En **Configuración**, expanda **Seguridad** > **Cuentas de ejecución** y, luego, modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.

	![Internet](./media/site-recovery-vmm-to-azure/provider3.png)

6. En la página **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave. Especifique la suscripción de Azure Site Recovery y el nombre del almacén.

	![Registro de servidor](./media/site-recovery-vmm-to-azure/provider4.png)

7. Puede especificar o modificar la ubicación de un certificado SSL que se genera automáticamente para el cifrado de datos. Este certificado se usa si habilita el cifrado de datos para una nube protegida por Azure en el portal de Azure Site Recovery. Mantenga el certificado en un lugar seguro. Cuando ejecute una conmutación por error en Azure lo necesitará para descifrar si está habilitado el cifrado de datos.

	![Registro de servidor](./media/site-recovery-vmm-to-azure/provider5.png)

8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.
13. Habilite los **metadatos de la nube de sincronización** si desea sincronizar los metadatos de todas las nubes en el servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM. Haga clic en **Register** para finalizar el proceso.

	![Registro de servidor](./media/site-recovery-vmm-to-azure/provider6.png)

9. Con ello, se iniciará el registro. Después de finalizar el registro, el servidor aparecerá en la hoja **Configuración** > **Servidores** del almacén.


#### Instalación mediante la línea de comandos del proveedor de Azure Site Recovery

El proveedor de Azure Site Recovery puede instalarse desde la línea de comandos. Este método se puede usar para instalar el proveedor en un Server Core para Windows Server 2012 R2.

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta. Por ejemplo, C:\\ASR.
2. Desde un símbolo del sistema con privilegios elevados, ejecute estos comandos para extraer el instalador del proveedor.

	    	C:\Windows\System32> CD C:\ASR
	    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. Ejecute este comando para instalar los componentes:

			C:\ASR> setupdr.exe /i

4. Ejecute estos comandos para registrar el servidor en el almacén:

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

Donde:

- **/Credentials**: parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro.  
- **/FriendlyName**: parámetro obligatorio para el nombre del servidor host Hyper-V que aparece en el portal de Azure Site Recovery.
- - **/EncryptionEnabled**: parámetro opcional que solo se usa cuando se está replicando máquinas virtuales de Hyper-V en nubes de VMM en Azure. Especifique si desea cifrar máquinas virtuales en Azure (en cifrado en reposo). Asegúrese de que el nombre del archivo tiene la extensión **.pfx**. El cifrado está desactivado de forma predeterminada.
- **/proxyAddress**: parámetro opcional que especifica la dirección del servidor proxy.
- **/proxyport**: parámetro opcional que especifica el puerto del servidor proxy.
- **/proxyUsername**: parámetro opcional que especifica el nombre de usuario de proxy (si el proxy requiere autenticación).
- **/proxyPassword**: parámetro opcional que especifica la contraseña para autenticarse con el servidor proxy (si el proxy requiere autenticación).


### Instale el agente de los Servicios de recuperación de Azure en servidores host de Hyper-V

1. Después de configurar el proveedor, debe descargar el archivo de instalación del agente de Servicios de recuperación de Azure. Ejecute el programa de instalación en cada servidor de Hyper-V en la nube de VMM. 

	![Sitios de Hyper-V](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)

2. En la página **Comprobación de requisitos previos**, haga clic en **Siguiente**. Todos los requisitos previos que falten se instalarán automáticamente.

	![Prerequisites Recovery Services Agent](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)

3. En la página **Configuración de la instalación**, acepte o modifique la ubicación de instalación y la ubicación de la memoria caché. Puede configurar la memoria caché en una unidad que tenga al menos 5 GB de almacenamiento disponible, pero se recomienda usar una unidad de caché con 600 GB o más de espacio libre. Luego haga clic en **Instalar**.
4. Una vez completada la instalación, haga clic en **Cerrar** para terminar.
	
	![Registro del agente MARS](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

#### Instalación mediante la línea de comandos del agente de Servicios de Azure Site Recovery

Puede instalar el agente de Servicios de recuperación de Microsoft Azure desde la línea de comandos mediante el comando siguiente:

     marsagentinstaller.exe /q /nu

#### Configuración del acceso de proxy a Internet para Site Recovery desde hosts de Hyper-V

El agente de Servicios de recuperación que se ejecuta en los hosts de Hyper-V necesita acceso a Internet para conectarse a Azure para la replicación de máquina virtual. Si va a acceder a Internet a través de un proxy, configúrelo como sigue:

1. Abra el complemento Copia de seguridad de Microsoft Azure en el host de Hyper-V. De manera predeterminada, hay disponible un acceso directo para Copia de seguridad de Microsoft Azure en el escritorio o en la siguiente ruta de acceso: C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.
3. En la pestaña **Configuración de proxy** especifique la información del servidor proxy.

	![Registro del agente MARS](./media/site-recovery-vmm-to-azure/mars-proxy.png)

4. Asegúrese de que el agente puede llegar a las direcciones URL descritas en los [requisitos previos](#on-premises-prerequisites).


## Paso 3: Configuración del entorno de destino

Especifique la cuenta de almacenamiento de Azure que se utilizará para la replicación y la red de Azure a la que se conectarán las máquinas virtuales de Azure después de la conmutación por error.

1.	Haga clic en **Preparar infraestructura** > **Destino** y seleccione la suscripción de Azure que desea utilizar.
2.	Especifique el modelo de implementación que desea usar para las máquinas virtuales después de la conmutación por error.
3.	Site Recovery comprueba que tiene una o más cuentas de almacenamiento y redes compatibles.

	![Almacenamiento](./media/site-recovery-vmm-to-azure/compatible-storage.png)

4.	Si no ha creado una cuenta de almacenamiento y desea crear una con ARM haga clic en **+Cuenta de almacenamiento** para hacerlo directamente. En la hoja **Crear cuenta de almacenamiento** especifique el nombre, el tipo, la suscripción y la ubicación de la cuenta. La cuenta debe estar en la misma ubicación que el almacén de Servicios de recuperación.

	![Almacenamiento](./media/site-recovery-vmm-to-azure/gs-createstorage.png)

	Observe lo siguiente:

	- Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo hará en el Portal de Azure. [Más información](../storage/storage-create-storage-account-classic-portal.md)
	- Si utiliza una cuenta de almacenamiento premium para los datos replicados, deberá configurar una cuenta de almacenamiento estándar adicional para los registros de replicación del almacén que capturan los cambios continuos de los datos locales.

4.	Si no ha creado una red de Azure y desea crear una con ARM haga clic en **+Red** para hacerlo directamente. En la hoja **Crear red virtual** especifique el nombre, el intervalo de direcciones, los detalles de subred, la suscripción y la ubicación de la red. La red debe estar en la misma ubicación que el almacén de Servicios de recuperación.

	![Red](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

	Si desea crear una red mediante el modelo clásico, lo hará en el Portal de Azure. [Más información](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).

### Configurar asignación de red

- [Lea](#prepare-for-network-mapping) una rápida introducción de lo que puede hacer la asignación de redes. [Lea esto](site-recovery-network-mapping.md) para obtener una explicación más detallada.
- Compruebe que las máquinas virtuales del servidor VMM está conectadas a una red de máquinas virtuales y que ha creado al menos una red virtual de Azure. Tenga en cuenta que pueden asignarse varias redes de VM a una sola red de Azure.

Configure la asignación como sigue:

1. En **Configuración** > **Infraestructura de Site Recovery** > **Asignaciones de red** > **Asignación de red** haga clic en el icono de **+Asignación de red**.

	![Asignación de red](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. En **Agregar asignación de red**, seleccione el servidor VMM de origen y **Azure** como destino.
3. Compruebe la suscripción y el modelo de implementación después de la conmutación por error.
4. En **Red de origen**, seleccione la red de máquinas virtuales de origen local que desea asignar en la lista asociada con el servidor VMM. 
5. En **Red de destino** seleccione la red de Azure en la que se ubicarán las máquinas virtuales de Azure replicadas cuando estas se pongan en marcha. y, a continuación, haga clic en **Aceptar**.

	![Asignación de red](./media/site-recovery-vmm-to-azure/network-mapping2.png)

Esto es lo que sucede cuando comienza la asignación de red:

- Las máquinas virtuales existentes en la red de origen se conectarán a la red de destino cuando comience la asignación. Las nuevas máquinas virtuales conectadas a la red de máquinas virtuales de origen se conectarán a la red de Azure asignada cuando se produzca la replicación. 
- Si modifica una asignación de red existente, las máquinas virtuales de réplica se conectarán con la nueva configuración.
- Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error.
- Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.



## Paso 4: Configuración de las opciones de replicación


1. Para crear una nueva directiva de replicación, haga clic en **Preparar infraestructura** > **Configuración de replicación** > **+Crear y asociar**.

	![Red](./media/site-recovery-vmm-to-azure/gs-replication.png)

2. En **Crear y asociar directiva** especifique un nombre de directiva.
3. En **Frecuencia de copia** especifique la frecuencia con la que desea replicar diferencias de datos después de la replicación inicial (cada 30 segundos, 5 o 15 minutos).
4. En **Retención de punto de recuperación**, especifique, en horas, el tiempo que estará disponible el período de retención para cada punto de recuperación. Los equipos protegidos se pueden recuperar en cualquier punto dentro de un período.
6. En **Frecuencia de instantánea coherente con la aplicación** especifique la frecuencia (entre 1 y 12 horas) con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación. Hyper-V usa dos tipos de instantáneas, una instantánea estándar que proporciona una instantánea incremental de toda la máquina virtual y una instantánea coherente con la aplicación que toma una instantánea en un momento concreto de los datos de la aplicación dentro de la máquina virtual. Las instantáneas coherentes con la aplicación utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Tenga en cuenta que si habilita las instantáneas coherentes con la aplicación, se verá afectado el rendimiento de aplicaciones que se ejecutan en las máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados.
3. En **Hora de inicio de la replicación inicial**, especifique cuándo debe comenzar la replicación inicial. La replicación se produce utilizando el ancho de banda de Internet, así que puede que deba programarla fuera del horario de trabajo.
5. En **Cifrar datos almacenados en Azure** especifique si desea cifrar los datos de REST en Almacenamiento de Azure. y, a continuación, haga clic en **Aceptar**.

	![Directiva de replicación](./media/site-recovery-vmm-to-azure/gs-replication2.png)

6. Cuando se crea una nueva directiva se asocia automáticamente con la nube de VMM. Haga clic en **Aceptar**. Puede asociar nubes de VMM adicionales (y las máquinas virtuales que contienen) a esta directiva de replicación en **Configuración** > **Replicación** > Nombre de directiva > **Asociar nube de VMM**.

	![Directiva de replicación](./media/site-recovery-vmm-to-azure/policy-associate.png)

## Paso 5: Pleaneamiento de capacidad

Ahora que tiene la infraestructura básica configurada, puede planear la capacidad y averiguar si necesitará recursos adicionales.

Site Recovery proporciona una herramienta de planeación de capacidad para ayudarle a asignar los recursos adecuados para el entorno de origen, los componentes de recuperación del sitio, las redes y el almacenamiento. Puede ejecutar la herramienta de planeación en modo rápido para obtener resultados basados en un promedio de máquinas virtuales, discos y almacenamiento o en el modo detallado en el que podrá especificar las cifras en el nivel de carga de trabajo. Antes de empezar necesitará:

- Recopilar información sobre su entorno de replicación, incluidas las máquinas virtuales, discos por máquina virtual y almacenamiento por disco.
- Calcular la tasa de cambio (renovación) diaria para los datos replicados. Puede usar la [herramienta Capacity Planner para las réplicas de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) que le ayudarán con ello.

1.	Hacer clic en **Descargar** para descargar la herramienta y, a continuación, ejecútela. [Lea el artículo](site-recovery-capacity-planner.md) que acompaña a la herramienta.
2.	Una vez que haya terminado, seleccione **Sí** en **¿Ha ejecutado la herramienta Capacity Planner**?

	![Planificación de capacidad](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### Consideraciones sobre el ancho de banda de red

Puede utilizar la herramienta de planeación de capacidad para calcular el ancho de banda necesario para la replicación (replicación inicial y, a continuación, la diferencial). Para controlar el uso de ancho de banda de la replicación tiene algunas opciones:

- **Limitar ancho de banda**: el tráfico de Hyper-V que se replica en un sitio secundario pasa a través de un host de Hyper-V específico. Puede limitar el ancho de banda en el servidor host.
- **Retocar el ancho de banda**: puede influir en el ancho de banda utilizado para la replicación mediante un par de claves del registro.

#### Limitar ancho de banda

1. Abra el complemento Copia de seguridad de Microsoft Azure en el servidor host de Hyper-V. De manera predeterminada, hay disponible un acceso directo para Copia de seguridad de Microsoft Azure en el escritorio o en la siguiente ruta de acceso: C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. En el complemento, haga clic en **Cambiar propiedades**.
3. En la pestaña **Limitación**, seleccione **Habilitar límite de uso del ancho de banda de Internet para las operaciones de copia de seguridad** y defina los límites durante las horas de trabajo y las de no trabajo. Los intervalos válidos van de 512 Kbps a 102 Mbps por segundo.

	![Limitar ancho de banda](./media/site-recovery-vmm-to-azure/throttle2.png)

También puede utilizar el cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet para establecer la limitación. Aquí tiene un ejemplo:

    $mon = [System.DayOfWeek]::Monday 
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** indica que no se requiere ninguna limitación.


#### Control del uso de ancho de banda de red

El valor de registro **UploadThreadsPerVM** controla el número de subprocesos que se utilizan para la transferencia de datos (replicación inicial o diferencial) de un disco. Un valor mayor aumenta el ancho de banda de red utilizado para la replicación. El valor de registro **DownloadThreadsPerVM** especifica el número de subprocesos usados para la transferencia de datos durante la conmutación por recuperación.

1. En el registro, vaya a **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows Azure Backup\\Replication**.
	
	- Modifique el valor **UploadThreadsPerVM** (o cree la clave, si no existe) para controlar los subprocesos utilizados para la replicación de disco. 
	- Modifique el valor **DownloadThreadsPerVM** (o cree la clave, si no existe) para controlar los subprocesos utilizados para el tráfico de conmutación por recuperación desde Azure. 
2. El valor predeterminado es 4. En una red "sobreaprovisionada", se deben cambiar los valores predeterminados de estas claves de registro. El valor máximo es 32. Supervise el tráfico para optimizar el valor. 

## Paso 6: Habilitamiento de la replicación

Ahora habilite la replicación como sigue:

1. Haga clic en **Paso 2: Replicar la aplicación** > **Origen**. Después de habilitar la replicación por primera vez haga clic en **+Replicar** en el almacén para habilitar la replicación de máquinas adicionales.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/enable-replication1.png)

2. En la hoja **Origen** > seleccione el servidor VMM y la nube en la que se encuentran los hosts de Hyper-V. y, a continuación, haga clic en **Aceptar**.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/enable-replication-source.png)

3. En **Destino** seleccione la suscripción, el modelo de implementación posterior a la conmutación por error y la cuenta de almacenamiento que va a usar para los datos replicados.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/enable-replication-target.png)

4. Seleccione la cuenta de almacenamiento que desea usar. Si desea utilizar una cuenta de almacenamiento diferente de las que tiene, puede [crear una](#set-up-an-azure-storage-account). Para crear una cuenta de almacenamiento mediante el modelo de ARM, haga clic en **Crear nueva**. Si desea crear una cuenta de almacenamiento mediante el modelo clásico, lo hará [en el Portal de Azure](../storage/storage-create-storage-account-classic-portal.md). y, a continuación, haga clic en **Aceptar**.
5. Seleccione la red y la subred de Azure a la que se conectarán las máquinas virtuales de Azure cuando se pongan en marcha después de la conmutación por error. Seleccione la opción **Configurar ahora para las máquinas seleccionadas** para aplicar la configuración de red a todas las máquinas que seleccione para su protección. Seleccione **Configurar más tarde** para seleccionar la red de Azure por máquina. Si desea utilizar una red diferente de las que tiene, puede [crear una](#set-up-an-azure-network). Para crear una red mediante el modelo de ARM, haga clic en **Crear nueva**. Si desea crear una red mediante el modelo clásico, hágalo [en el Portal de Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Seleccione una subred si es posible. y, a continuación, haga clic en **Aceptar**.
6. En **Máquinas virtuales** > **Seleccionar máquinas virtuales** haga clic y seleccione cada equipo que desee replicar. Solo puede seleccionar aquellas máquinas en las que se pueda habilitar la replicación. y, a continuación, haga clic en **Aceptar**.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/enable-replication5.png)

5. En **Propiedades** > **Configurar propiedades**, seleccione el sistema operativo para las máquinas virtuales seleccionadas y el disco del sistema operativo. y, a continuación, haga clic en **Aceptar**. Puede establecer propiedades adicionales más adelante.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/enable-replication6.png)

	
12. En **Configuración de replicación** > **Establecer configuración de replicación**, seleccione la directiva de replicación que desea aplicar para las máquinas virtuales protegidas. y, a continuación, haga clic en **Aceptar**. Puede modificar la directiva de replicación en **Configuración** > **Directivas de replicación** > Nombre de directiva > **Editar configuración**. Los cambios que aplique se utilizarán tanto para las máquinas que ya se estén replicando como para otras nuevas.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/enable-replication7.png)

Puede hacer un seguimiento del progreso del trabajo de **Habilitar la protección** en **Configuración** > **Trabajos** > **Trabajos de Site Recovery**. La máquina estará preparada para la conmutación por error después de que finalice el trabajo de **Finalizar protección**.

### Visualización y administración de las propiedades de la máquina virtual

Es recomendable que compruebe las propiedades de la máquina de origen. Recuerde que el nombre de la máquina virtual debe cumplir los [Requisitos para las máquinas virtuales de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

1. Haga clic en **Configuración** > **Elementos protegidos** > **Elementos replicados** > y seleccione la máquina para ver sus detalles.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/vm-essentials.png)

2. En **Propiedades** puede ver la información de replicación y conmutación por error de la máquina virtual.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/test-failover2.png)

3. En **Proceso y red** > **Propiedades de proceso** puede especificar el nombre y el tamaño de destino de la máquina virtual de Azure. Modifique el nombre para que cumpla con los [requisitos de Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) si es necesario. También puede ver y modificar la información acerca de la red, la subred y la dirección IP de destino que se asignarán a la máquina virtual de Azure. Tenga en cuenta lo siguiente:

	- Puede establecer la dirección IP de destino. Si no proporciona una dirección, la máquina conmutada por error usará DHCP. Si establece una dirección que no está disponible en el momento de la conmutación por error, se producirá un error. Se puede utilizar la misma dirección IP de destino para la conmutación por error de prueba si la dirección está disponible en la red.
	- El número de adaptadores de red viene determinado por el tamaño que especifique para la máquina virtual de destino, de la siguiente manera:

		- Si el número de adaptadores de red en el equipo de origen es menor o igual al número de adaptadores permitido para el tamaño de la máquina de destino, el destino tendrá el mismo número de adaptadores que el origen.
		- Si el número de adaptadores para la máquina virtual de origen supera el número permitido para el tamaño de destino, entonces se utilizará el tamaño máximo de destino.
		- Por ejemplo, si una máquina de origen tiene dos adaptadores de red y el tamaño de la máquina de destino es compatible con cuatro, el equipo de destino tendrá dos adaptadores. Si el equipo de origen tiene dos adaptadores pero el tamaño de destino compatible solo admite uno, el equipo de destino tendrá solo un adaptador. 	
		- Si la máquina virtual tiene varios adaptadores de red, todos ellos se conectarán a la misma red.

	![Habilitar replicación](./media/site-recovery-vmm-to-azure/test-failover4.png)

5.	En **Discos** puede ver los discos de datos y del sistema operativo en la máquina virtual que se va a replicar.



## Paso 7: Prueba de la implementación

Para probar la implementación, puede ejecutar una conmutación por error de prueba para una sola máquina virtual o un plan de recuperación que contenga una o varias máquinas virtuales.


### Preparación para la conmutación por error

- Para ejecutar una conmutación por error de prueba, le recomendamos que cree una nueva red de Azure que esté aislada de la red de producción de Azure (este es el comportamiento predeterminado cuando se crea una nueva red de Azure). [Más información](site-recovery-failover.md#run-a-test-failover) sobre la ejecución de conmutaciones por error de prueba.
- Para obtener el mejor rendimiento cuando realice una conmutación por error en Azure, instale el agente de Azure en la máquina protegida. Hace más rápido el arranque y ayuda a solucionar problemas. Instale el agente de [Linux](https://github.com/Azure/WALinuxAgent) o de [Windows](http://go.microsoft.com/fwlink/?LinkID=394789). 
- Para probar completamente la implementación necesitará una infraestructura para que la máquina replicada funcione según lo esperado. Si desea probar Active Directory y DNS puede crear una máquina virtual como controlador de dominio con DNS y replicar esta en Azure con Azure Site Recovery. Más información en [consideraciones de la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- Si desea ejecutar una conmutación por error no planeada en lugar de una de prueba tenga en cuenta lo siguiente:

	- Si es posible, debe apagar las máquinas primarias antes de ejecutar una conmutación por error no planeada. Esto garantiza que la máquina de origen y de réplica no se ejecuten al mismo tiempo. 
	- Cuando ejecuta una conmutación por error no planeada, se detiene la replicación de datos desde las máquinas principales para que no se transfiera ningún diferencial de datos después de que comienza una conmutación por error no planeada. Además, si ejecuta una conmutación por error no planeada en un plan de recuperación, se ejecutará hasta que haya finalizado, incluso si se produce un error.

### Preparación para la conexión a las máquinas virtuales de Azure después de la conmutación por error

Si desea conectarse a máquinas virtuales de Azure mediante RDP después de la conmutación por error, asegúrese de que hace lo siguiente:

**En la máquina local antes de la conmutación por error**:

- Para tener acceso a través de Internet, habilite el protocolo RDP, asegúrese de que se agregan las reglas de TCP y UDP para el **Público**, y asegúrese de que el protocolo RDP está permitido en el **Firewall de Windows** -> **Aplicaciones y características permitidas** para todos los perfiles.
- Para el acceso a través de una conexión de sitio a sitio, habilite el protocolo RDP en la máquina y asegúrese de que este está permitido en el **Firewall de Windows** -> **Aplicaciones y características permitidas** para redes de **Dominio** y **Privadas**.
- Instale el [agente de máquina virtual de Azure](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) en la máquina local.
- Asegúrese de que la directiva SAN del sistema operativo está establecida en OnlineAll. [Más información](https://support.microsoft.com/kb/3031135)
- Desactive el servicio IPSec antes de ejecutar la conmutación por error.

**En la máquina virtual de Azure después de la conmutación por error**:

- Agregue un punto de conexión público para el protocolo RDP (puerto 3389) y especifique las credenciales de inicio de sesión.
- Asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.
- Intente conectarse. Si no se puede conectar, compruebe que se está ejecutando la máquina virtual. Para obtener más sugerencias sobre solución de problemas, consulte este [artículo](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Si desea acceder a una máquina virtual de Azure con Linux después de la conmutación por error mediante un cliente de Secure Shell (ssh), haga lo siguiente:

**En la máquina local antes de la conmutación por error**:

- Asegúrese de que el servicio de Secure Shell en la máquina virtual de Azure está configurado para iniciarse automáticamente en el arranque del sistema.
- Compruebe que las reglas de firewall permiten una conexión SSH.

**En la máquina virtual de Azure después de la conmutación por error**:

- Las reglas del grupo de seguridad de red de la máquina virtual conmutada por error y la subred de Azure a la que esta se conecta necesitan permitir las conexiones entrantes al puerto SSH.
- Debe crearse un punto de conexión público para permitir las conexiones entrantes en el puerto SSH (de forma predeterminada es el puerto TCP 22).
- Si se accede a la máquina virtual a través de una conexión VPN (Express Route o VPN de sitio a sitio), el cliente puede utilizarse para conectarse directamente a la máquina virtual a través de SSH.


### Ejecución de una conmutación por error de prueba

Para ejecutar una conmutación por error de prueba, realice lo siguiente:

1. Para conmutar por error una sola máquina virtual, en **Configuración** > **Elementos replicados**, haga clic en la máquina virtual > **+Conmutación por error**.
2. Para conmutar por error un plan de recuperación, en **Configuración** > **Planes de recuperación**, haga clic con el botón derecho en el plan > **Conmutación por error de prueba**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md)

3. En **Conmutación por error de prueba**, seleccione la red de Azure a la que se conectarán las máquinas virtuales después de la conmutación por error.
4. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede hacer un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo de **Conmutación por error de prueba** en **Configuración** > **Trabajos de Site Recovery**.
5. Cuando la conmutación por error alcance la fase **Completar pruebas**, haga lo siguiente:

	1. Vea la máquina virtual de réplica en el portal de Azure. Compruebe que la máquina virtual se inicia correctamente.
	2. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.
	3. Haga clic en **Completar la prueba** para terminarla.
	4. Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.
	5. Haga clic en **La conmutación por error de prueba se ha completado**. Limpie el entorno de prueba para apagar y eliminar automáticamente la máquina virtual de prueba.
	6. Se eliminarán todos los elementos o las máquinas virtuales que Site Recovery creó automáticamente durante la conmutación por error de prueba. No se eliminarán los elementos adicionales que haya creado para la conmutación por error de prueba.
	
	> [AZURE.NOTE] Si una conmutación por error de prueba continua durante más de dos semanas, se dará por terminada de manera forzada.

6. Cuando se complete la conmutación por error, debería ver la máquina de réplica de Azure en el Portal de Azure > **Máquinas virtuales**. Debe asegurarse de que la máquina virtual tiene el tamaño adecuado, que se ha conectado a la red correspondiente y que se está ejecutando.
7. Si se [preparó para las conexiones después de la conmutación por error](#prepare-to-connect-to-Azure-VMs-after-failover) debe ser capaz de conectarse a la máquina virtual de Azure.


## Supervisión de la implementación

Le mostramos cómo puede supervisar la configuración y el estado de la implementación de Site Recovery:

1. Haga clic en el nombre del almacén para acceder al panel de **Essentials**. En este panel puede ver los trabajos de Site Recovery, el estado de la replicación, los planes de recuperación, y el estado y los eventos del servidor. Puede personalizar Essentials para mostrar los iconos y los diseños que sean más útiles para usted, incluido el estado de otros almacenes de Copia de seguridad y Site Recovery.

	![Essentials](./media/site-recovery-vmm-to-azure/essentials.png)

2. En el icono de **Estado** puede supervisar los servidores del sitio (servidores VMM o de configuración) que están experimentando el problema y los eventos provocados por Site Recovery en las últimas 24 horas.
3. Puede administrar y supervisar la replicación en los iconos de **Elementos replicados**, **Planes de recuperación** y **Trabajos de Site Recovery**. Puede ver más detalles de los trabajos en **Configuración** -> **Trabajos** -> **Trabajos de Site Recovery**.


## Pasos siguientes

Después de que la implementación esté configurada y en ejecución, [obtenga más información](site-recovery-failover.md) acerca de los diferentes tipos de conmutación por error.

<!---HONumber=AcomDC_0511_2016-->