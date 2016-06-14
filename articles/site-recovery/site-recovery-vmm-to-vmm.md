<properties
	pageTitle="Replicación de máquinas virtuales de Hyper-V (en nubes de VMM) en un sitio de VMM secundario con el Portal de Azure | Microsoft Azure"
	description="Se describe cómo implementar Azure Site Recovery para organizar la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V de nubes de VMM en un sitio de VMM secundario mediante el Portal de Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/26/2016"
	ms.author="raynew"/>

# Replicación de máquinas virtuales de Hyper-V (en nubes VMM) en un sitio de VMM secundario mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Portal de Azure](site-recovery-vmm-to-vmm.md)
- [Portal clásico](site-recovery-vmm-to-vmm-classic.md)

Bienvenido a Azure Site Recovery. Utilice este artículo si quiere replicar máquinas virtuales locales de Hyper-V administradas en nubes de System Center Virtual Machine Manager (VMM) en un sitio secundario. En este artículo se describe cómo configurar la replicación mediante Azure Site Recovery en el Portal de Azure.

> [AZURE.NOTE] Azure tiene dos [modelos de implementación](../resource-manager-deployment-model.md) diferentes para crear recursos y trabajar con ellos: Azure Resource Manager (ARM) y el clásico. Azure también tiene dos portales: el Portal de Azure clásico que admite el modelo de implementación clásico y el Portal de Azure que es compatible con ambos modelos de implementación.


Azure Site Recovery en el Portal de Azure proporciona una serie de nuevas características:

- En el Portal de Azure los servicios de Copia de seguridad de Azure y Azure Site Recovery se combinan en un único almacén de Servicios de recuperación para que pueda configurar y administrar la continuidad empresarial y la recuperación ante desastres (BCDR) desde una sola ubicación. Un panel unificado permite supervisar y administrar operaciones en los sitios locales y en la nube pública de Azure.
- Los usuarios con suscripciones de Azure aprovisionadas con el programa Proveedor de soluciones en la nube (CSP) pueden administrar ya las operaciones de Site Recovery en el Portal de Azure.


Después de leer este artículo, publique los comentarios en la parte inferior de los comentarios de Disqus. Formule cualquier pregunta técnica en el [foro de Servicios de recuperación de Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Información general

Las organizaciones necesitan una estrategia de recuperación ante desastres y continuidad empresarial (BCDR) que determine cómo seguirán en funcionamiento y disponibles las aplicaciones, las cargas de trabajo y los datos durante los tiempos de inactividad planeados y no planeados, y cómo recuperar las condiciones de funcionamiento normales lo antes posible. Su estrategia de BCDR se centra en soluciones que mantengan los datos empresariales seguros y recuperables, y garanticen que las cargas de trabajo estarán disponibles continuamente en caso de desastre.

Site Recovery es un servicio de Azure que contribuye a su estrategia de BCDR mediante la coordinación de la replicación de servidores físicos locales y máquinas virtuales en la nube (Azure) o en un centro de datos secundario. Cuando se producen interrupciones en la ubicación principal, se realiza la conmutación por error a la ubicación secundaria para mantener disponibles las aplicaciones y cargas de trabajo. La conmutación por recuperación a la ubicación principal se produce cuando vuelve a su funcionamiento normal. Más información en [¿Qué es Site Recovery?](site-recovery-overview.md)

En este artículo se proporciona toda la información que necesita para replicar máquinas virtuales de Hyper-V locales de nubes de VMM en un sitio de VMM secundario. Se incluye información general sobre la arquitectura, información de planeamiento y pasos de implementación para la configuración de servidores locales, la configuración de la replicación y el planeamiento de la capacidad. Después de configurar la infraestructura, puede habilitar la replicación en los equipos que desea proteger y comprobar que funciona la conmutación por error.

## Ventajas empresariales

- Site Recovery ofrece protección para cargas de trabajo y aplicaciones empresariales que se ejecutan en máquinas virtuales de Hyper-V mediante su replicación en un servidor de Hyper-V secundario.
- El portal de Servicios de recuperación proporciona una ubicación única para configurar, administrar y supervisar la replicación, la conmutación por error y la recuperación.
- Se pueden ejecutar fácilmente conmutaciones por error de la infraestructura local principal al sitio secundario y conmutaciones por recuperación del sitio secundario al principal.
- Puede configurar planes de recuperación con varios equipos de modo que las cargas de trabajo de la aplicación en capas experimenten la conmutación por error al mismo tiempo. 

## Arquitectura del escenario

Estos son los componentes del escenario:

- **Sitio principal**: en el sitio principal, hay uno o varios servidores host de Hyper-V que ejecutan las máquinas virtuales de origen que quiere replicar. Estos servidores host principales están situados en una nube privada de VMM.
- **Sitio secundario**: en el sitio secundario, hay uno o varios servidores host de Hyper-V que ejecutan máquinas virtuales de destino en las que replicará las máquinas virtuales principales. Estos servidores host están situados en una nube privada de VMM. La nube puede estar en el servidor principal (si solo tiene un único servidor VMM) o en un servidor de VMM secundario.
- **Proveedor**: durante la implementación de Site Recovery se instala el proveedor de Azure Site Recovery en los servidores VMM y esos servidores se registran en un almacén de Servicios de recuperación. El proveedor que se ejecuta en el servidor VMM se comunica con Site Recovery a través de HTTPS 443 para coordinar la replicación. La replicación de datos se produce entre los servidores host de Hyper-V principal y secundario. Los datos replicados permanecen dentro de los sitios y las redes locales y no se envían a Azure. Más información sobre la [privacidad](#privacy-information-for-site-recovery).

![Topología E2E](./media/site-recovery-vmm-to-vmm/architecture.png)

### Información general sobre la privacidad de los datos

En esta tabla se resume cómo se almacenan los datos en este escenario:
****
Acción | **Detalles** | **Datos recopilados** | **Uso** | **Obligatorio** 
--- | --- | --- | --- | ---
**Registro** | Registra un servidor VMM en un almacén de Servicios de recuperación. Si más tarde desea anular el registro de un servidor, puede hacerlo eliminando la información de este en el Portal de Azure. | Después de que se registra un servidor VMM, Site Recovery recopila, procesa y transfiere metadatos sobre el servidor VMM y los nombres de las nubes VMM detectadas. | Los datos se utilizan para identificar el servidor VMM adecuado y comunicarse con él, y para configurar las nubes VMM correctas. | Esta característica es necesaria. Si no desea enviar esta información a Site Recovery, no utilice dicho servicio. 
**Habilitar replicación** | El proveedor de Azure Site Recovery se instala en el servidor VMM y es el conducto para la comunicación con el servicio Site Recovery. El proveedor es una biblioteca de vínculos dinámicos (DLL) que se hospeda en el proceso VMM. Después de instalar el proveedor, se habilita la característica "Datacenter Recovery" en la consola de administrador de VMM. Se puede habilitar esta característica en las máquinas virtuales nuevas y existentes para estar protegidas. | Con esta propiedad establecida, el proveedor envía el nombre y el identificador de la máquina virtual a Site Recovery. La replicación se habilita mediante la réplica de Hyper-V de Windows Server 2012 o Windows Server 2012 R2. Los datos de la máquina virtual se replican de un host de Hyper-V a otro (normalmente se encuentran en un centro de datos de "recuperación" diferente). | Site Recovery usa los metadatos para rellenar la información de la máquina virtual en el Portal de Azure. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información, no habilite la protección de Site Recovery para máquinas virtuales. Tenga en cuenta que todos los datos que envía el proveedor a Site Recovery es través de HTTPS.
**Plan de recuperación** | Los planes de recuperación le ayudan a crear un plan de orquestación para el centro de datos de recuperación. Puede definir el orden en el que se deben iniciar las máquinas virtuales, o un grupo de ellas, en el sitio de recuperación. También puede especificar los scripts automatizados que se pueden ejecutar, o cualquier acción manual que se debe emprender, en el momento de la recuperación para cada máquina virtual. Normalmente se activa la conmutación por error en el nivel del plan de recuperación para la recuperación coordinada. | Site Recovery recopila, procesa y transmite los metadatos para el plan de recuperación, incluidos los metadatos de la máquina virtual y los metadatos de todos los scripts de automatización y las notas de la acción manual. | Los metadatos se utilizan para crear el plan de recuperación en el Portal de Azure. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información a Site Recovery, no cree planes de recuperación.
**Asignación de red** | Permite asignar la información de red del centro de datos principal al centro de datos de recuperación. Cuando se recuperan máquinas virtuales en el sitio de recuperación, la asignación de red ayuda a establecer la conectividad de red. | Site Recovery recopila, procesa y transmite los metadatos de las redes lógicas de cada sitio (principal y centro de datos). | Los metadatos se usan para rellenar la configuración de red de modo que pueda asignar la información de red. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información a Site Recovery, no utilice la asignación de red.
**Conmutación por error (planeada o no planeada y prueba)** | Se produce un error en la conmutación por error en las máquinas virtuales entre un centro de datos administrado por VMM y otro. La acción de conmutación por error se desencadena manualmente en el Portal de Azure. | Site Recovery notifica al proveedor en el servidor VMM el evento de conmutación por error y este ejecuta una acción de conmutación por error en el host de Hyper-V mediante interfaces de VMM. La conmutación por error real de una máquina virtual se realiza de un host de Hyper-V a otro y se administra mediante la réplica de Hyper-V de Windows Server 2012 o Windows Server 2012 R2. Después de finalizar la conmutación por error, el proveedor en el servidor VMM del centro de datos de recuperación notifica a Site Recovery que la operación se ha realizado correctamente. | Site Recovery usa la información enviada para rellenar el estado de la información de acción de conmutación por error en el Portal de Azure. | Esta característica es una parte esencial del servicio y no se puede desactivar. Si no desea enviar esta información a Site Recovery, no utilice la conmutación por error.


## Requisitos previos de Azure

Esto es lo que necesita en Azure para implementar este escenario:

**Requisitos previos** | **Detalles** 
--- | ---
**Las tablas de Azure**| Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Puede comenzar con una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/). [Más información](https://azure.microsoft.com/pricing/details/site-recovery/) sobre los precios de Site Recovery. 


## Requisitos previos locales

Esto es lo que necesita tener en los sitios locales principal y secundario para implementar este escenario:

**Requisitos previos** | **Detalles** 
--- | ---
**VMM** | Se recomienda que implemente un servidor VMM en el sitio principal y un servidor VMM en el sitio secundario.<br/><br/> También puede [replicar entre nubes en un único servidor VMM](site-recovery-single-vmm.md). Para ello, necesitará al menos dos nubes configuradas en el servidor VMM.<br/><br/> Los servidores VMM deben estar ejecutando al menos System Center 2012 SP1 con las actualizaciones más recientes.<br/><br/> Cada servidor VMM debe tener una o más nubes configuradas y todas las nubes deben tener definido el perfil de capacidad de Hyper-V. <br/><br/>Las nubes deben contener uno o más grupos hosts de VMM.<br/><br/>Aprenda a configurar nubes VMM en [Preparación de la implementación de Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) y [Walkthrough: Creating private clouds with System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) (Tutorial: Creación de nubes privadas con System Center 2012 SP1 VMM).<br/><br/> Los servidores VMM necesitan acceso a Internet. 
**Hyper-V** | Los servidores de Hyper-V deben estar ejecutando al menos Windows Server 2012 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.<br/><br/> Un servidor de Hyper-V debe contener una o más máquinas virtuales.<br/><br/> Los servidores host de Hyper-V deben estar ubicados en grupos host en las nubes VMM principal y secundaria.<br/><br/> Si va a ejecutar Hyper-V en un clúster en Windows Server 2012 R2, debe instalar la [actualización 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Si va a ejecutar Hyper-V en un clúster de Windows Server 2012, tenga en cuenta que el agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. [Más información](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).
**Proveedor** | Durante la implementación de Site Recovery, se instala el proveedor de Azure Site Recovery en los servidores VMM. El proveedor se comunica con Site Recovery mediante HTTPS 443 para organizar la replicación. La replicación de datos se produce entre los servidores Hyper-V principal y secundario mediante la conexión LAN o VPN.<br/><br/> El proveedor que se ejecuta en el servidor VMM necesita acceso a estas direcciones URL: *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net.<br/><br/> Además, es necesario permitir la comunicación del firewall entre los servidores VMM y los [intervalos IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) y permitir el protocolo HTTPS (433).

## Preparación de la implementación

Para preparar la implementación deberá:

1. [Preparar el servidor VMM](#prepare-the-vmm-server) para la implementación de Site Recovery. 
2. [Preparar la asignación de red](#prepare-for-network-mapping). Configure las redes para poder configurar la asignación de red.

### Prepare el servidor VMM

Asegúrese de que el servidor VMM cumple los [requisitos previos](#on-premises-prerequisites) y que puede tener acceso a las direcciones URL enumeradas.


### Preparar la asignación de red

La asignación de red se realiza entre las redes de VM de VMM en los servidores VMM principal y secundario con el fin de:

- Colocar óptimamente las máquinas virtuales de réplica en los hosts de Hyper-V secundarios tras la conmutación por error.
- Conectar las máquinas virtuales de réplica a las redes VM adecuadas.
- Si no configura la asignación de red, las máquinas virtuales de réplica no se conectarán a ninguna red tras la conmutación por error.
- Si quiere configurar la asignación de red durante la implementación de Site Recovery, esto es lo que debe hacer:

	- Asegúrese de que las máquinas virtuales del servidor host de Hyper-V de origen estén conectadas a una red de máquinas virtuales de VMM. Esa red debe estar vinculada a una red lógica asociada con la nube.
	- Compruebe que la nube secundaria que se va a utilizar para la recuperación tenga configurada una red de VM correspondiente. Dicha red de máquina virtual debe estar vinculada a una red lógica asociada con la nube secundaria.

- [Más información](site-recovery-network-mapping.md) sobre cómo funciona la asignación de red.

## Preparación para la implementación con un único servidor VMM

Si solo tiene un servidor VMM, puede replicar las máquinas virtuales de hosts de Hyper-V situadas en la nube VMM en [Azure](site-recovery-vmm-to-azure.md) o en una nube VMM secundaria. Se recomienda la primera opción porque la replicación entre nubes no funciona muy bien, pero si necesita hacerlo estos son los pasos que debe seguir:

1. **Configure VMM en una máquina virtual de Hyper-V**. Para ello, se recomienda colocar la instancia de SQL Server que usa VMM en la misma máquina virtual. Esto permite ahorrar tiempo, ya que solo se tiene que crear una máquina virtual. Si quiere usar una instancia remota de SQL Server y se produce una interrupción, necesitará recuperar esa instancia para poder recuperar VMM.
2. **Asegúrese de que el servidor VMM tenga al menos dos nubes configuradas**. Una nube contendrá las máquinas virtuales que desee replicar y la otra nube actuará como la ubicación secundaria. La nube que contenga las máquinas virtuales que quiere proteger debe cumplir una serie de [requisitos previos](#on-premises-prerequisites).
3. Configure Site Recovery tal como se describe en este artículo. Cree y registre el servidor VMM en el almacén, configure una directiva de replicación y habilite la replicación. Debe especificar que la replicación inicial tenga lugar a través de la red.
4. Al configurar la asignación de red, asignará la red de VM para la nube principal a la red de VM para la nube secundaria.
5. En la consola de administrador de Hyper-V, habilite la réplica de Hyper-V en el host de Hyper-V que contiene la máquina virtual de VMM y habilite la replicación en la máquina virtual. Asegúrese de no agregar la máquina virtual VMM a las nubes que están protegidas por Site Recovery para garantizar que la configuración de la réplica de Hyper-V no la invalide Site Recovery.
6. Si crea planes de recuperación para la conmutación por error, usará el mismo servidor VMM de origen y de destino.
7. Podrá conmutar por error y realizar la recuperación de la manera siguiente:

	- Realice manualmente la conmutación por error de la VM de VMM en el sitio secundario utilizando el Administrador de Hyper-V con una conmutación por error planeada.
	- Conmute por error las máquinas virtuales.
	- Cuando la VM de VMM se haya recuperado, inicie sesión en el Portal de Azure -> Almacén de Servicios de recuperación y ejecute una conmutación por error no planeada de las máquinas virtuales desde el sitio secundario hasta el sitio principal.
	- Una vez completada la conmutación por error no planeada, se podrá acceder a todos los recursos de nuevo desde el sitio primario.


### Creación de un almacén de Servicios de recuperación

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo** > **Administración** > **Servicios de recuperación**. También puede hacer clic en **Examinar** > **Almacenes de Servicios de recuperación** > **Agregar**.

	![Almacén nuevo](./media/site-recovery-vmm-to-vmm/new-vault3.png)

3. En **Nombre**, especifique un nombre descriptivo para identificar el almacén. Si tiene más de una suscripción, seleccione una de ellas.
4. [Cree un grupo de recursos nuevo](../resource-group-portal.md#create-resource-group) o seleccione uno existente, y especifique una región de Azure. Las máquinas se replicarán en esta región. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en [Detalles de precios de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
4. Si quiere acceder rápidamente al almacén desde el panel, haga clic en **Anclar al panel** > **Crear almacén**.

	![Almacén nuevo](./media/site-recovery-vmm-to-vmm/new-vault-settings.png)

El nuevo almacén aparecerá en **Panel** > **Todos los recursos** y en la hoja principal de **Almacenes de Servicios de recuperación**.




## Introducción

Site Recovery proporciona una experiencia introductoria que le ayuda a realizar la implementación lo más rápido posible. Esta introducción comprueba los requisitos previos y le guía a través de los pasos de implementación de Site Recovery en el orden correcto.

En esta introducción puede seleccionar el tipo de máquinas que desea replicar y la ubicación donde desea replicarlas. Configurará servidores locales, creará directivas de replicación y realizará el planeamiento de la capacidad. Una vez que haya configurado la infraestructura, puede habilitar la replicación para máquinas virtuales. Puede ejecutar conmutaciones por error para máquinas específicas o crear planes de recuperación para conmutar por error varias máquinas.

Comience la experiencia de introducción decidiendo cómo desea implementar Site Recovery. El flujo descrito en esta introducción cambia ligeramente en función de los requisitos de replicación.

## Paso 1: Selección de los objetivos de protección

Seleccione aquello que desea replicar y la ubicación en donde se va a realizar la replicación.

1. En la hoja **Almacenes de Servicios de recuperación**, seleccione el almacén y haga clic en **Configuración**.
2. En **Configuración** > **Introducción**, haga clic en **Site Recovery** > **Step 1: Prepare Infrastructure** (Paso 1: Preparación de la infraestructura) > **Protection goal** (Objetivo de protección).
3. En **Protection goal** (Objetivo de protección), seleccione **To recovery site** (En el sitio de recuperación) y seleccione **Yes, with Hyper-V** (Sí, con Hyper-V). 
4. Seleccione **Sí** para indicar que usa VMM para administrar los hosts de Hyper-V y seleccione **Sí** si tiene un servidor VMM secundario. Si va a implementar la replicación entre nubes en un único servidor VMM, haga clic en **No**. y, a continuación, haga clic en **Aceptar**. 

	![Elegir objetivos](./media/site-recovery-vmm-to-vmm/choose-goals.png)


## Paso 2: Configuración del entorno de origen

Instale el proveedor de Azure Site Recovery en los servidores VMM y registre los servidores en el almacén.

1. Haga clic en **Step 2: Prepare Infrastructure** (Paso 2: Preparación de la infraestructura) > **Origen**. 

	![Configurar origen](./media/site-recovery-vmm-to-vmm/goals-source.png)

2. En **Prepare source** (Preparar origen) haga clic en **+ VMM** para agregar un servidor VMM.

	![Configurar origen](./media/site-recovery-vmm-to-vmm/set-source1.png)

2. En la hoja **Agregar servidor** compruebe que aparece el **servidor de System Center VMM** en **Tipo de servidor** y que este cumple con los [requisitos previos y los requisitos de direcciones URL](#on-premises-prerequisites).
4. Descargue el archivo de instalación del proveedor de Azure Site Recovery.
5. Descargue la clave de registro. Se le solicitará cuando ejecute el programa de instalación. La clave será válida durante 5 días a partir del momento en que se genera. 

	![Configurar origen](./media/site-recovery-vmm-to-vmm/set-source3.png)

6. Instale el proveedor de Azure Site Recovery en el servidor VMM.

> [AZURE.NOTE] No es necesario instalar explícitamente nada en los servidores host de Hyper-V.


### Instalación del proveedor de Azure Site Recovery

1. Ejecute el archivo de instalación del proveedor en cada servidor VMM. Si VMM está implementado en un clúster y va a instalar el proveedor por primera vez, instálelo en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. A continuación, instale el proveedor en los demás nodos. Los nodos del clúster deben ejecutar la misma versión del proveedor.
2. El programa de instalación ejecuta algunas comprobaciones de requisitos previos y solicita permiso para detener el servicio VMM. El servicio VMM se reiniciará automáticamente cuando finalice la instalación. Si va a instalarlo en un clúster VMM, se le pedirá que detenga el rol de clúster.

2.  En **Microsoft Update**, puede optar por recibir actualizaciones para que las actualizaciones del proveedor se realicen según la directiva de Microsoft Update.
3. En **Instalación**, acepte o modifique la ubicación predeterminada de instalación del proveedor y haga clic en **Instalar**.

	![Ubicación de instalación](./media/site-recovery-vmm-to-vmm/provider-location.png)

3. Cuando finalice la instalación, haga clic en **Registrar** para registrar el servidor en el almacén.

	![Ubicación de instalación](./media/site-recovery-vmm-to-vmm/provider-register.png)

4. En **Configuración de proxy**, especifique cómo se conectará el proveedor que se ejecuta en el servidor VMM a Site Recovery a través de Internet.

	- Si quiere que el proveedor se conecte directamente, seleccione **Connect directly without a proxy** (Conectarse directamente sin un proxy).
	- - Si quiere conectarse con el proxy configurado actualmente en el servidor, seleccione **Connect with existing proxy settings** (Conectarse con la configuración de proxy existente).
	- Si el proxy existente requiere autenticación, o quiere utilizar un proxy personalizado para la conexión del proveedor, seleccione **Connect with custom proxy settings** (Conectarse con una configuración de proxy personalizada).
	- Si utiliza un proxy personalizado, deberá especificar la dirección, el puerto y las credenciales.
	- Si utiliza un servidor proxy, se deberían haber permitido ya las direcciones URL descritas en los [requisitos previos](#provider-and-agent-prerequisites).
	- Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. En **Configuración**, expanda **Seguridad** > **Cuentas de ejecución** y, luego, modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.

	![Internet](./media/site-recovery-vmm-to-vmm/provider3.png)

4. En **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave descargado. Especifique la suscripción de Azure y el nombre del almacén.

	![Registro de servidor](./media/site-recovery-vmm-to-vmm/provider-key2.png)

5. En **Registro** > **Ubicación para guardar el certificado**, haga clic en **Siguiente**. Un certificado para el cifrado no es pertinente en este escenario. Solo se utiliza cuando se realiza la replicación en el Almacenamiento de Azure.

8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.
9. Habilite la **sincronización de metadatos de la nube** si desea sincronizar los metadatos de todas las nubes en el servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM. Haga clic en **Siguiente** para finalizar el proceso.

	![Registro de servidor](./media/site-recovery-vmm-to-vmm/provider-sync.png)

10. Con ello, se iniciará el registro. Cuando finaliza el registro, el servidor VMM aparece en la hoja **Configuración** > **Servidores** del almacén.
11. Después de que el servidor esté disponible en la consola de Site Recovery, en **Origen** > **Prepare source** (Preparar origen), seleccione el servidor VMM y seleccione la nube en la que se encuentra el host de Hyper-V. y, a continuación, haga clic en **Aceptar**.

#### Instalación de la línea de comandos

El proveedor de Azure Site Recovery puede instalarse desde la línea de comandos. Este método se puede usar para instalar el proveedor en un Server Core para Windows Server 2012 R2.

1. Descargue el archivo de instalación del proveedor y la clave de registro en una carpeta. Por ejemplo, C:\\ASR.
2. Detenga el servicio System Center Virtual Machine Manager.
3. Desde un símbolo del sistema con privilegios elevados, ejecute estos comandos para extraer el instalador del proveedor.

    	C:\Windows\System32> CD C:\ASR
    	C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Ejecute este comando para instalar el proveedor:

    	C:\ASR> setupdr.exe /i

5. Ejecute estos comandos para registrar el servidor en el almacén:

    	CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
    	C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Los parámetros son los siguientes:

 - **/Credentials**: parámetro obligatorio que especifica la ubicación donde se encuentra el archivo de clave de registro.  
 - **/FriendlyName**: parámetro obligatorio para el nombre del servidor host Hyper-V que aparece en el portal de Azure Site Recovery.
 - **/EncryptionEnabled** : parámetro opcional que se utiliza solo cuando se replica de VMM a Azure.
 - **/proxyAddress**: parámetro opcional que especifica la dirección del servidor proxy.
 - **/proxyport**: parámetro opcional que especifica el puerto del servidor proxy.
 - **/proxyUsername**: parámetro opcional que especifica el nombre de usuario de proxy (si el proxy requiere autenticación).
 - **/proxyPassword**: parámetro opcional que especifica la contraseña para autenticarse con el servidor proxy (si el proxy requiere autenticación).  

## Paso 3: Configuración del entorno de destino

Seleccione el servidor VMM y la nube de destino.

1. Haga clic en **Prepare infrastructure** (Preparar infraestructura) > **Destino** y seleccione el servidor VMM de destino que quiere usar. 
2.	Se mostrarán las nubes en el servidor que están sincronizadas con Site Recovery. Seleccione la nube de destino.

	![Destino](./media/site-recovery-vmm-to-vmm/target-vmm.png)

## Paso 4: Configuración de las opciones de replicación

1. Para crear una nueva directiva de replicación, haga clic en **Prepare infrastructure** (Preparar infraestructura) > **Configuración de la replicación** > **+Create and associate** (+Crear y asociar).

	![Red](./media/site-recovery-vmm-to-vmm/gs-replication.png)

2. En **Create and associate policy** (Crear y asociar directiva), especifique un nombre de directiva. El tipo de origen y destino debe ser **Hyper-V**.
3. En **Versión del host de Hyper-V**, seleccione qué sistema operativo se ejecuta en el host.

	> [AZURE.NOTE] La nube VMM puede contener hosts de Hyper-V que ejecutan diferentes versiones (admitidas) de Windows Server, pero se aplica una directiva de replicación a los hosts que ejecutan el mismo sistema operativo. Si tiene hosts que ejecutan más de una versión de sistema operativo, cree directivas de replicación independientes.

4. En **Authentication type** (Tipo de autenticación) y **Authentication port** (Puerto de autenticación), especifique cómo se autentica el tráfico entre los servidores host de Hyper-V principal y secundario. Seleccione **Certificado** a menos que tenga un entorno Kerberos activo. Azure Site Recovery configurará automáticamente los certificados para la autenticación de HTTPS. No es necesario hacer nada manualmente. De forma predeterminada, se abrirán los puertos 8083 y 8084 (para los certificados) en el Firewall de Windows en los servidores hosts de Hyper-V. Si selecciona **Kerberos**, se usará un vale de Kerberos para la autenticación mutua de los servidores host. Observe que esta configuración solo es pertinente para servidores host de Hyper-V que se ejecutan en Windows Server 2012 R2.
3. En **Frecuencia de copia**, especifique la frecuencia con la que desea replicar diferencias de datos después de la replicación inicial (cada 30 segundos, 5 o 15 minutos).
4. En **Retención de punto de recuperación**, especifique, en horas, el tiempo que estará disponible el período de retención para cada punto de recuperación. Los equipos protegidos se pueden recuperar en cualquier punto dentro de un período.
6. En **Frecuencia de instantánea coherente con la aplicación**, especifique la frecuencia (entre 1 y 12 horas) con la que se crearán los puntos de recuperación que contengan las instantáneas coherentes con la aplicación. Hyper-V usa dos tipos de instantáneas, una instantánea estándar que proporciona una instantánea incremental de toda la máquina virtual y una instantánea coherente con la aplicación que toma una instantánea en un momento concreto de los datos de la aplicación dentro de la máquina virtual. Las instantáneas coherentes con la aplicación utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Tenga en cuenta que si habilita las instantáneas coherentes con la aplicación, se verá afectado el rendimiento de aplicaciones que se ejecutan en las máquinas virtuales de origen. Asegúrese de que el valor establecido es menor que el número de puntos de recuperación adicionales configurados.
7. En **Compresión de transferencia de datos**, especifique si se deben comprimir los datos replicados que se transfieren.
8. Seleccione **Eliminar máquina virtual de réplica** para especificar que la máquina virtual de réplica debe eliminarse si se deshabilita la protección para la VM de origen. Si habilita esta configuración, cuando deshabilita la protección para la máquina virtual de origen, dicha máquina virtual se quita de la consola de Site Recovery, la configuración de Site Recovery de VMM se quita de la consola VMM y la réplica se elimina.
3. En **Método de replicación inicial**, si va a replicar a través de la red, especifique si iniciará la replicación inicial o la programará. Para ahorrar ancho de banda de red, puede que quiera programarla fuera de las horas punta. y, a continuación, haga clic en **Aceptar**.

	![Directiva de replicación](./media/site-recovery-vmm-to-vmm/gs-replication2.png)

6. Cuando se crea una nueva directiva se asocia automáticamente con la nube de VMM. En **Directiva de replicación**, haga clic en **Aceptar**. Puede asociar nubes VMM adicionales (y las máquinas virtuales que contienen) a esta directiva de replicación en **Configuración** > **Replicación** > nombre de directiva > **Associate VMM Cloud** (Asociar nube VMM).

	![Directiva de replicación](./media/site-recovery-vmm-to-vmm/policy-associate.png)

### Preparación para la replicación inicial sin conexión

Puede realizar la replicación sin conexión para la copia de datos inicial. Para preparar esta operación, haga lo siguiente:

- En el servidor de origen, especifique una ubicación de ruta de acceso desde la que se llevará a cabo la exportación de datos. Asigne Control total a NTFS y Compartir permisos con el servicio VMM en la ruta de acceso de exportación. En el servidor de destino, especifique una ubicación de ruta de acceso desde la cual tendrá lugar la importación de datos. Asigne los mismos permisos en esta ruta de acceso de importación.
- Si se comparte la ruta de acceso de importación o exportación, asigne la pertenencia de grupo de administrador, usuario avanzado, operador de impresión o de operador de servidor a la cuenta de servicio de VMM en el equipo remoto en el que se encuentra el recurso compartido.
- Si utiliza cualquier cuenta de ejecución para agregar hosts, en las rutas de acceso de importación y exportación, asigne permisos de lectura y escritura a las cuentas de ejecución de VMM.
- Los recursos compartidos de importación y exportación no deben ubicarse en ningún equipo utilizado como servidor host de Hyper-V, porque la configuración de bucle invertido no es compatible con Hyper-V.
- En Active Directory en cada servidor host de Hyper-V que contiene máquinas virtuales que desea proteger, habilite y configure la delegación restringida para confiar en los equipos remotos en los que se ubican las rutas de acceso de importación y exportación, de la forma siguiente:
	1. En el controlador de dominio, abra **Usuarios y equipos de Active Directory**.
	2. En el árbol de consola, haga clic en **Nombre de dominio** > **Equipos**.
	3. Haga clic con el botón derecho en el nombre del servidor host de Hyper-V > **Propiedades**.
	4. En la pestaña **Delegación**, haga clic en **Confiar en este equipo para la delegación solo a los servicios especificados**.
	5. Haga clic en **Usar cualquier protocolo de autenticación**.
	6. Haga clic en **Agregar** > **Usuarios y equipos**.
	7. Escriba el nombre del equipo que hospeda la ruta de acceso de exportación > **Aceptar**. En la lista de servicios disponibles, mantenga presionada la tecla CTRL y haga clic en **cifs** > **Aceptar**. Repita el proceso para el nombre del equipo que hospeda la ruta de acceso de importación. Repita según sea necesario para servidores host de Hyper-V adicionales.


### Configurar asignación de red

Configure la asignación de red entre las redes de origen y destino.

- [Lea](#prepare-for-network-mapping) una rápida introducción de cómo funciona la asignación de red. Además, siga [leyendo](site-recovery-network-mapping.md) para obtener una explicación más detallada.
- Compruebe que las máquinas virtuales de los servidores VMM están conectadas a una red de máquina virtual.

Configure la asignación como sigue:

1. En **Configuración** > **Infraestructura de Site Recovery** > **Asignación de red** > **Asignaciones de red**, haga clic en **+Asignación de red**.

	![Asignación de red](./media/site-recovery-vmm-to-azure/network-mapping1.png)

2. En la pestaña **Agregar asignación de red**, seleccione los servidores VMM de origen y destino. Se recuperan las redes de VM asociadas a los servidores VMM.
3. En **Red de origen**, seleccione la red que quiere utilizar de la lista de redes de VM asociadas con el servidor VMM principal. 
6. En **Red de destino**, seleccione la red que quiere usar en el servidor VMM secundario. y, a continuación, haga clic en **Aceptar**.

	![Asignación de red](./media/site-recovery-vmm-to-vmm/network-mapping2.png)

Esto es lo que sucede cuando comienza la asignación de red:

- Todas las máquinas virtuales de réplica existentes que correspondan a la red de VM de origen se conectarán a la red de la VM de destino.
- Las nuevas máquinas virtuales que se conecten a la red de VM de origen se conectarán a la red asignada de destino después de la replicación.
- Si modifica una asignación existente a una nueva red, las máquinas virtuales de réplica se conectarán con la nueva configuración.
- Si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

## Paso 5: Pleaneamiento de capacidad

Ahora que tiene la infraestructura básica configurada, puede planear la capacidad y averiguar si necesitará recursos adicionales.

Site Recovery proporciona una herramienta de planeamiento de capacidad basada en Excel para ayudarle a asignar los recursos adecuados para el entorno de origen, los componentes de recuperación del sitio, las redes y el almacenamiento. Puede ejecutar la herramienta de planeación en modo rápido para obtener resultados basados en un promedio de máquinas virtuales, discos y almacenamiento o en el modo detallado en el que podrá especificar las cifras en el nivel de carga de trabajo. Antes de empezar necesitará:

- Recopilar información sobre su entorno de replicación, incluidas las máquinas virtuales, discos por máquina virtual y almacenamiento por disco.
- Calcule la tasa de cambio (renovación) diaria que tendrá para las diferencias de datos replicadas. Para ayudarle a ello, puede usar la [herramienta de planeamiento de capacidad para las réplicas de Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057).

1.	Haga clic en **Descargar** para descargar la herramienta y luego ejecútela. [Lea el artículo](site-recovery-capacity-planner.md) que acompaña a la herramienta.
2.	Cuando haya terminado, seleccione **Yes** (Sí) en **Have you run the Capacity Planner**? (¿Ha ejecutado la herramienta Capacity Planner?).

	![Planificación de capacidad](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

### Control del ancho de banda

Después de haber recopilado la información de replicación diferencial en tiempo real mediante la réplica de Hyper-V de Capacity Planner, la herramienta de planeamiento de la capacidad basada en Excel le ayuda a calcular el ancho de banda que necesita para la replicación (inicial y diferencial). Para controlar la cantidad de ancho de banda utilizada para la replicación, puede configurar la directiva de NetQos mediante la directiva de grupo o Windows PowerShell. Hay varias maneras de hacer esto:

**PowerShell** | **Detalles**
--- | ---
**New-NetQosPolicy "QoS a subred de destino"** | Limita el tráfico entre un host de Hyper-V que ejecuta Windows Server 2012 R2 y una subred secundaria. Utilice esta directiva si las subredes principal y secundaria son diferentes.
**New-NetQosPolicy "QoS a puerto de destino"** | Limita el tráfico entre un host de Hyper-V que ejecuta Windows Server 2012 R2 y un puerto de destino.
**New-NetQosPolicy "Limitación de tráfico desde VMM"** | Limita el tráfico de vmms.exe. Esto limita el tráfico de Hyper-V y el tráfico de migración en vivo. Puede hacer coincidir los protocolos y puertos IP para refinar la operación.

Puede usar la configuración de peso de ancho de banda o limitar el tráfico de bits por cada subred secundaria. Si utiliza un clúster, deberá hacer esto en todos sus nodos. Para obtener más información, consulte:


- Blog de Thomas Maurer sobre la [limitación del tráfico de réplica de Hyper-V](http://www.thomasmaurer.ch/2013/12/throttling-hyper-v-replica-traffic/)
- Información adicional sobre el cmdlet [New-NetQosPolicy](https://technet.microsoft.com/library/hh967468.aspx).


## Paso 6: Habilitamiento de la replicación 

Ahora habilite la replicación como sigue:

1. Haga clic en **Step 2: Replicate application** (Paso 2: Replicación de la aplicación) > **Origen**. Después de habilitar la replicación por primera vez, haga clic en **+Replicar** en el almacén para habilitar la replicación de máquinas adicionales.

	![Habilitar replicación](./media/site-recovery-vmm-to-vmm/enable-replication1.png)

2. En la hoja **Origen** > seleccione el servidor VMM y la nube donde se encuentran los hosts de Hyper-V que quiere replicar. y, a continuación, haga clic en **Aceptar**.

	![Habilitar replicación](./media/site-recovery-vmm-to-vmm/enable-replication2.png)

3. En la hoja **Destino**, compruebe el servidor VMM y la nube secundarios.
4. En **Máquinas virtuales**, seleccione las máquinas virtuales que quiere proteger de la lista. 

	![Enable virtual machine protection](./media/site-recovery-vmm-to-vmm/enable-replication5.png)

Puede hacer un seguimiento del progreso de la acción **Habilitar protección** en Configuración > **Trabajos** > **Site Recovery jobs** (Trabajos de Site Recovery). La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de **Finalizar protección**.


>[AZURE.NOTE] También puede habilitar la protección de las máquinas virtuales en la consola VMM. Haga clic en **Habilitar protección** en la barra de herramientas de las propiedades de la máquina virtual > pestaña **Azure Site Recovery**.

Después de habilitar la replicación, puede ver las propiedades de la máquina virtual en **Configuración** > **Elementos replicados** > nombre de máquina virtual. En el panel de **información básica**, puede ver información sobre la directiva de replicación de la máquina virtual y su estado. Haga clic en **Propiedades** para más información.

### Incorporación de máquinas virtuales existentes

Si tiene máquinas virtuales en VMM que se replican mediante Réplica de Hyper-V, puede incorporarlas a la protección de Azure Site Recovery de la forma siguiente:

1. Asegúrese de que el servidor de Hyper-V que hospeda la máquina virtual existente se encuentra en la nube principal y que el servidor de Hyper-V que hospeda la máquina virtual de réplica se encuentra en la nube secundaria.
2. Asegúrese de que hay una directiva de replicación configurada para la nube VMM principal.
2. Habilite la replicación para la máquina virtual principal. Azure Site Recovery y VMM garantizan que se detecta el mismo host de réplica y la misma máquina virtual, y Azure Site Recovery reutilizará y restablecerá la replicación mediante los valores configurados.


## Paso 7: Prueba de la implementación

Para probar la implementación, puede ejecutar una conmutación por error de prueba para una sola máquina virtual o un plan de recuperación que contenga una o varias máquinas virtuales.

### Preparación para la conmutación por error

- Para probar completamente la implementación necesitará una infraestructura para que la máquina replicada funcione según lo esperado. Si desea probar Active Directory y DNS puede crear una máquina virtual como controlador de dominio con DNS y replicar esta en Azure con Azure Site Recovery. Más información en las [consideraciones de la conmutación por error de prueba para Active Directory](site-recovery-active-directory.md#considerations-for-test-failover).
- En las instrucciones de este artículo se describe cómo ejecutar una conmutación por error de prueba sin ninguna red. Esta opción comprueba que la máquina virtual conmuta por error, pero no prueba la configuración de red de la máquina virtual. [Más información](site-recovery-failover.md#run-a-test-failover) sobre otras opciones.
- Si desea ejecutar una conmutación por error no planeada en lugar de una de prueba tenga en cuenta lo siguiente:

	- Si es posible, debe apagar las máquinas primarias antes de ejecutar una conmutación por error no planeada. Esto garantiza que la máquina de origen y de réplica no se ejecuten al mismo tiempo. 
	- Cuando ejecuta una conmutación por error no planeada, se detiene la replicación de datos desde las máquinas principales para que no se transfiera ningún diferencial de datos después de que comienza una conmutación por error no planeada. Además, si ejecuta una conmutación por error no planeada en un plan de recuperación, se ejecutará hasta que haya finalizado, incluso si se produce un error.




### Ejecución de una conmutación por error de prueba

1. Para conmutar por error una sola máquina virtual, en **Configuración** > **Elementos replicados**, haga clic en la máquina virtual > **+Conmutación por error de prueba**.

	![Conmutación por error de prueba](./media/site-recovery-vmm-to-vmm/test-failover.png)

2. Para conmutar por error un plan de recuperación, en **Configuración** > **Planes de recuperación**, haga clic con el botón derecho en el plan > **Conmutación por error de prueba**. Para crear un plan de recuperación, [siga estas instrucciones](site-recovery-create-recovery-plans.md).
2. En **Conmutación por error de prueba**, seleccione **Ninguna**. Con esta opción se prueba que la máquina virtual conmuta por error como se esperaba, pero la máquina virtual replicada no estará conectada a ninguna red.

	![Conmutación por error de prueba](./media/site-recovery-vmm-to-vmm/test-failover1.png)

3. Haga clic en **Aceptar** para iniciar la conmutación por error. Puede hacer un seguimiento del progreso haciendo clic en la máquina virtual para abrir sus propiedades o en el trabajo de **Conmutación por error de prueba** en **Configuración** > **Trabajos** > **Site Recovery jobs** (Trabajos de Site Recovery).
4. Cuando el trabajo de conmutación por error alcance la fase **Completar prueba**, haga lo siguiente:

	-  Vea la máquina virtual de réplica en la nube VMM secundaria.
	-  Haga clic en **Completar la prueba** para finalizar la conmutación por error de prueba.
	-  Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.

5. La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. Se ha agregado a la misma nube en la que se encuentra la máquina virtual de réplica.
6. Después de comprobar que las máquinas virtuales se inician correctamente, haga clic en **Conmutación por error de prueba**. En esta fase se eliminarán todos los elementos que Site Recovery creó automáticamente durante la conmutación por error de prueba.  

	> [AZURE.NOTE] Si una conmutación por error de prueba continua durante más de dos semanas, se dará por terminada de manera forzada.



### Actualización de DNS con la dirección IP de la máquina virtual de réplica

Después de la conmutación por error, puede que la máquina virtual de réplica no tenga la misma dirección IP que la máquina virtual principal.

- Las máquinas virtuales actualizarán el servidor DNS que están utilizando después de iniciarse.
- También puede actualizar manualmente DNS de la manera siguiente:

#### Recupere la dirección IP.

Ejecute este script de ejemplo para recuperar la dirección IP.

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

#### Actualización de DNS

Ejecute este script de ejemplo para actualizar DNS, especificando la dirección IP que ha recuperado con el script de ejemplo anterior.

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord

## Pasos siguientes

Después de que la implementación esté configurada y en ejecución, [aprenda más](site-recovery-failover.md) sobre los diferentes tipos de conmutación por error.

<!---HONumber=AcomDC_0601_2016-->