<properties
	pageTitle="Configuración de la protección entre un sitio de VMM local y Azure"
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V ubicadas en nubes VMM o en instalaciones locales a Azure."
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
	ms.date="05/07/2015"
	ms.author="raynew"/>

#  Configuración de la protección entre un sitio de VMM local y Azure

## Información general

Azure Site Recovery contribuye a su estrategia de continuidad de negocio y recuperación ante desastres (BCDR) mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación. Para obtener una lista completa de los escenarios de implementación, consulte [Información general sobre Azure Site Recovery](site-recovery-overview.md)

Esta guía de escenarios describe cómo implementar Site Recovery para orquestar y automatizar la protección de las cargas de trabajo que se ejecutan en máquinas virtuales en los servidores host de Hyper-V que se encuentran en las nubes privadas de VMM. En este escenario se replican las máquinas virtuales desde un sitio principal de VMM en Azure con la réplica de Hyper-V.

La guía incluye los requisitos previos para el escenario y muestra cómo configurar un almacén de Site Recovery, instalar Azure el proveedor de Azure Site Recovery en el servidor VMM de origen, registrar el servidor en el almacén, agregar una cuenta de almacenamiento de Azure, instalar al agente Servicios de recuperación de Azure en servidores host de Hyper-V, configurar protección de las nubes VMM que se aplicará a todas las máquinas virtuales protegidas y, a continuación, habilitar la protección de esas máquinas virtuales. Se termina comprobando la conmutación por error para asegurarse de que todo funciona según lo esperado.

Si tiene problemas al configurar este escenario, publique sus preguntas en el [Foro de servicios de recuperación de Azure](http://go.microsoft.com/fwlink/?LinkId=313628).

## Antes de comenzar

Asegúrese de que tiene preparados estos requisitos previos:
### Requisitos previos de Azure

- Necesitará una cuenta de [Microsoft Azure](http://azure.microsoft.com/). Si no tiene una, comience con una [evaluación gratuita](http://aka.ms/try-azure). También puede leer sobre los precios del [Administrador de Azure Site Recovery](http://go.microsoft.com/fwlink/?LinkId=378268).
- Necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados en Azure. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción. Para obtener más información sobre la configuración del almacenamiento de Azure, consulte [Introducción al almacenamiento de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=398704).
- Deberá asegurarse de que las máquinas virtuales que desea proteger cumplen los requisitos de Azure. Consulte [Compatibilidad de máquinas virtuales](https://msdn.microsoft.com/library/azure/dn469078.aspx#BKMK_E2A) para obtener más información.

### Requisitos previos de VMM
- Necesitará un servidor VMM que se ejecute en System Center 2012 R2.
- Cualquier servidor VMM que contiene máquinas virtuales que desea proteger debe estar ejecutando el proveedor de Azure Site Recovery, que se instala durante la implementación de Azure Site Recovery.
- Necesitará al menos una nube en el servidor VMM que desea proteger. La nube debe contener:
	- Uno o más grupos de hosts de VMM
	- Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts.
	- Una o más máquinas virtuales en el servidor de Hyper-V de origen. Las máquinas virtuales deben ser de la generación 1.
- Más información acerca de cómo configurar las nubes de VMM:
	- Obtenga más información acerca de las nubes privadas de VMM en [Novedades de la nube privada con System Center 2012 R2 VMM](http://go.microsoft.com/fwlink/?LinkId=324952) y en [VMM 2012 y las nubes](http://go.microsoft.com/fwlink/?LinkId=324956).
	- Obtenga información acerca de cómo [configurar el tejido de la nube de VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric).
	- Cuando los elementos del tejido de nube están configurados, obtenga información acerca de cómo crear nubes privadas en [Creación de una nube privada en VMM](http://go.microsoft.com/fwlink/?LinkId=324953) y [Tutorial: Creación de nubes privadas con System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/?LinkId=324954).

### Requisitos previos de Hyper-V

- Los servidores host de Hyper-V deben estar ejecutando al menos Windows Server 2012 R2 con el rol Hyper-V y tener instaladas las actualizaciones más recientes.
- Si está ejecutando Hyper-V en un clúster, tenga en cuenta que ese agente de clúster no se crea automáticamente si tiene un clúster basado en una dirección IP estática. Tendrá que configurar manualmente el agente de clúster. Para obtener instrucciones, consulte [Configuración del agente de réplicas de Hyper-V](http://go.microsoft.com/fwlink/?LinkId=403937).
- Cualquier servidor o clúster del host de Hyper-V para el que desee administrar la protección debe incluirse en una nube de VMM.

La siguiente imagen muestra los distintos canales y puertos de comunicación utilizados por Azure Site Recovery para la orquestación y la replicación

![Topología E2A](./media/site-recovery-vmm-to-azure/E2ATopology.png)

### Requisitos previos de asignación de redes
Al proteger las máquinas virtuales en los mapas de asignación de redes de Azure entre redes de VM en el servidor de VMM de origen y las redes de Azure de destino para habilitar lo siguiente:

- Todas las máquinas con conmutación por error en la misma red pueden conectarse entre sí, independientemente del plan de recuperación en el que se encuentran.
- Si se configura una puerta de enlace de red en la red Azure de destino, las máquinas virtuales se pueden conectar a otras máquinas virtuales locales.
- Si no configura la asignación de redes, solo las máquinas virtuales con conmutación por error en el mismo plan de recuperación podrán conectarse entre sí después de la conmutación por error en Azure.

Si desea implementar la asignación de redes, necesitará lo siguiente:

- Las máquinas virtuales que desea proteger en el servidor VMM de origen deben estar conectadas a una red de máquina virtual. Esa red debe estar vinculada a una red lógica asociada con la nube.
- Una red de Azure a la que pueden conectarse máquinas virtuales replicadas después de la conmutación por error. Seleccionará esta red en el momento de la conmutación por error. La red debe estar en la misma región que su suscripción de Azure Site Recovery.
- Obtenga más información acerca de la asignación de redes:
	- [Configuración de redes lógicas en VMM](http://go.microsoft.com/fwlink/?LinkId=386307)
	- [Configuración de redes de máquina virtual y puertas de enlace en VMM](http://go.microsoft.com/fwlink/?LinkId=386308)
	- [Configuración y supervisión de redes virtuales en Azure](http://go.microsoft.com/fwlink/?LinkId=402555)


## Paso 1: Creación de un almacén de recuperación del sitio

1. Inicie sesión en el [Portal de administración](https://portal.azure.com) desde el servidor VMM que desee registrar.


2. Expanda
3. *Servicios de datos*, luego *Servicios de recuperación* y haga clic en *Almacén de Site Recovery*.*
3. Haga clic en *Crear nuevo* y, a continuación, en *Creación rápida*.


4. En *Nombre*, escriba un nombre descriptivo para identificar el almacén.

5. En *Región*, seleccione la región geográfica del almacén. Las regiones geográficas disponibles incluyen Asia Oriental, Europa Occidental, Oeste de los EE. UU., Este de EE. UU., Europa del Norte y Sudeste asiático.
6. Haga clic en *Crear almacén*.

	![Almacén nuevo](./media/site-recovery-vmm-to-azure/ASRE2AVMM_HvVault.png)

<P>Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como *Activo* en la página principal de Servicios de recuperación.</P>


## Paso 2: Generación de una clave de registro de almacén

Generación de una clave de registro en el almacén. Después de descargar el proveedor de Azure Site Recovery y de instalarlo en el servidor VMM, usará esta clave para registrar el servidor VMM en el almacén.

1. En la página *Servicios de recuperación*, haga clic en el almacén para abrir la página Inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Icono de inicio rápido](./media/site-recovery-vmm-to-azure/ASRE2AVMM_QuickStartIcon.png)

2. En la lista desplegable, seleccione **Entre un sitio Hyper-V local y Microsoft Azure**.
3. En **Preparar servidores VMM**, haga clic en **Generar archivo de clave de registro**. El archivo de clave se genera automáticamente y es válido durante 5 días después de su generación. Si no tiene acceso al Portal de Azure desde el servidor VMM, tendrá que copiar este archivo en el servidor.

	![Clave de registro](./media/site-recovery-vmm-to-azure/ASRE2AVMM_RegisterKey.png)

## Paso 3: Instalación del proveedor de Azure Site Recovery

4. En la página de *Inicio rápido*, en **Preparar servidores VMM**, haga clic en *Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM* a fin de obtener la versión más reciente del archivo de instalación del proveedor.

2. Ejecute este archivo en el servidor VMM de origen. Si VMM está implementado en un clúster y va a instalar el proveedor por primera vez, instálelo en un nodo activo y finalice la instalación para registrar el servidor VMM en el almacén. A continuación, instale el proveedor en los demás nodos. Tenga en cuenta que si está actualizando el proveedor, tendrá que actualizarlo en todos los nodos, ya que deben ejecutar la misma versión del proveedor.


3. En **Comprobación de requisitos previos**, seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración. Si va a instalarlo en un clúster VMM, se le pedirá que detenga el rol de clúster.

	![Requisitos previos](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderPrereq.png)

4. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

	![Microsoft Updates](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderUpdate.png)

Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.

5. En **Conexión a Internet**, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione *Utilizar la configuración proxy del sistema predeterminado* para usar la configuración predeterminada de conexión a Internet establecida en el servidor.

	![Configuración de Internet](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderProxy.png): si desea utilizar un proxy personalizado, debe configurarlo antes de instalar el proveedor. Al configurar las opciones del proxy personalizado, se ejecuta una prueba para comprobar la conexión del proxy. - Si utiliza a un proxy personalizado o el proxy predeterminado requiere autenticación, tendrá que especificar los detalles del proxy, incluida la dirección y el puerto del proxy. - Debe poder obtener acceso a las siguientes direcciones URL desde los hosts de Hyper-v y del servidor VMM - *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net - Permita las direcciones IP descritas en [Intervalos de direcciones IP del centro de datos de Azure](http://go.microsoft.com/fwlink/?LinkId=511094) y el protocolo HTTPS (443). Tendrá que incluir en una lista blanca los intervalos de direcciones IP de la región de Azure que va a usar y los del Oeste de EE. UU.

	- Si utiliza un proxy personalizado, se creará una cuenta de ejecución de VMM (DRAProxyAccount) mediante el uso automático de las credenciales de proxy especificadas. Configure el servidor proxy para que esta cuenta pueda autenticarse correctamente. La configuración de la cuenta de ejecución de VMM puede modificarse en la consola VMM. Para ello, abra el área de trabajo Configuración, expanda Seguridad, haga clic en Cuentas de ejecución y, a continuación, modifique la contraseña de DRAProxyAccount. Deberá reiniciar el servicio VMM para que esta configuración surta efecto.

6. En **Clave de registro**, seleccione lo que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
7. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor.
8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén. En una configuración de clúster, especifique el nombre del rol de clúster VMM.


	![Registro de servidor](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderRegKeyServerName.png)

8. En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.


9. En **Cifrado de datos**, especifique una ubicación para guardar un certificado SSL que se genera automáticamente para el cifrado de datos. Este certificado se usa si habilita el cifrado de datos para una nube protegida por Azure en el portal de Azure Site Recovery. Mantenga el certificado en un lugar seguro. Cuando ejecute una conmutación por error en Azure, la seleccionará para descifrar los datos cifrados.

	![Registro de servidor](./media/site-recovery-vmm-to-azure/ASRE2AVMM_ProviderSyncEncrypt.png)

8. Haga clic en *Registrar* para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se mostrará en el extremo de la pestaña *Recursos* en la página **Servidores** del almacén.

## Paso 4: Creación de una cuenta de almacenamiento de Azure

Si no dispone de una cuenta de almacenamiento de Azure, haga clic en **Agregar una cuenta de almacenamiento de Azure**. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.


![Cuenta de almacenamiento](./media/site-recovery-vmm-to-azure/ASRE2AVMM_StorageAgent.png)

## Paso 5: Instalación del agente de los Servicios de recuperación de Azure

Instale el agente de los Servicios de recuperación de Azure en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.

1. En la página de inicio rápido, haga clic en <b>Descargar el agente de los servicios de Azure Site Recovery e instalar en los hosts</b> para obtener la última versión del archivo de instalación del agente.

	![Install Recovery Services Agent](./media/site-recovery-vmm-to-azure/ASRE2AVMM_InstallHyperVAgent.png)

2. Ejecute el archivo de instalación en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.
3. En la página **Comprobación de requisitos previos**, haga clic en <b>Siguiente</b>. Todos los requisitos previos que falten se instalarán automáticamente.

	![Prerequisites Recovery Services Agent](./media/site-recovery-vmm-to-azure/ASRE2AVMM_AgentPrereqs.png)

4. En la página **Configuración de la instalación**, especifique dónde desea instalar el agente y seleccione la ubicación de la caché en la que se instalarán los metadatos de copia de seguridad. A continuación, haga clic en <b>Instalar</b>.

## Paso 6: Configuración de la protección de la nube

Una vez registrado el servidor de VMM, puede configurar los valores de protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña <b>Elementos protegidos</b> del almacén.

![Nube publicada](./media/site-recovery-vmm-to-azure/ASRE2AVMM_CloudsList.png)


1. En la página Inicio rápido, haga clic en **Configurar protección para nubes de VMM**.
2. En la pestaña **Elementos protegidos**, haga clic en la nube que desea configurar y vaya a la pestaña **Configuración**.
3. En <b>Destino</b>, seleccione <b>Microsoft Azure</b>.
4. En <b>Cuenta de almacenamiento</b>, seleccione la cuenta de Azure que desea usar para replicar las máquinas virtuales.
5. Establezca <b>Cifrar datos almacenados</b> en <b>Desactivado</b>. Este valor especifica que los datos de deben cifrar replicados entre el sitio local y Azure.
6. En <b>Copiar frecuencia</b>, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino.
7. En <b>Retener puntos de recuperación para</b>, deje la configuración predeterminada. Con un valor predeterminado de cero, el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
8. En <b>Frecuencia de las instantáneas coherentes con la aplicación</b>, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Si establece un valor, asegúrese de que sea inferior al número de puntos de recuperación adicionales que configure.
9. En <b>Hora de inicio de la replicación</b>, especifique cuándo debe comenzar la replicación de los datos en Azure. Se usará la zona horaria del servidor host de Hyper-V. Se recomienda que programe la replicación inicial durante las horas de menos actividad.

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/ASRE2AVMM_CloudSettings.png)

Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña <b>Trabajos</b>. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación.

Tras guardar, puede modificar la configuración de la nube en la pestaña <b>Configurar</b>. Para modificar la ubicación de destino o la cuenta de almacenamiento de destino, deberá eliminar la configuración de la nube y luego volver a configurar la nube. Tenga en cuenta que si cambia la cuenta de almacenamiento, el cambio solo se aplicará a las máquinas virtuales que tengan la protección habilitada después de que la cuenta de almacenamiento se ha modificado. Las máquinas virtuales existentes no se migrarán a la nueva cuenta de almacenamiento.</p>

## Paso 7: Configuración de la asignación de red
Antes de comenzar la asignación de red, compruebe que las máquinas virtuales en el servidor de VMM de origen están conectadas a una red de VM. Además, cree una o varias redes virtuales de Azure. Tenga en cuenta que pueden asignarse varias redes de VM a una sola red de Azure.

1. En la página de inicio rápido, haga clic en **Asignar redes**.
2. En la pestaña **Redes**, en **Ubicación de origen**, seleccione el servidor de VMM de origen. En **Ubicación de destino**, seleccione Azure.
3. En **Red de origen** se muestra una lista de las redes de VMM asociadas al servidor de VMM. En **Red de destino** se muestran las redes de Azure asociadas a la suscripción.
4. Seleccione la red de VM de origen y haga clic en **Asignar**.
5. En la página **Seleccione una red de destino**, seleccione la red de Azure de destino que desea utilizar.
6. Haga clic en la marca de verificación para completar el proceso de asignación.

	![Cloud replication settings](./media/site-recovery-vmm-to-azure/ASRE2AVMM_MapNetworks.png)

Después de guardar la configuración, se inicia un trabajo para realizar un seguimiento del progreso de la asignación, que puede supervisarse en la pestaña Trabajos. Todas las máquinas virtuales de réplica existentes que correspondan a la red de VM de origen se conectarán a las redes de Azure de destino. Las nuevas máquinas virtuales que se conecten a la red de VM de origen se conectarán a la red de Azure asignada después de la replicación. Si modifica una asignación existente a una nueva red, las máquinas virtuales de réplica se conectarán con la nueva configuración.

Tenga en cuenta que si la red de destino tiene varias subredes y una de estas subredes tiene el mismo nombre que la subred en la que se encuentra la máquina virtual de origen, la máquina virtual de réplica se conectará a esa subred de destino después de la conmutación por error. Si no hay ninguna subred de destino con un nombre coincidente, la máquina virtual se conectará a la primera subred de la red.

## Paso 8: Habilitación de la protección para las máquinas virtuales

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube. Tenga en cuenta lo siguiente:

- Las máquinas virtuales de deben cumplir los requisitos de Azure. Consúltelos en <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Requisitos previos y compatibilidad</a> en la Guía de planeación.
- Para habilitar la protección del sistema operativo y el disco del sistema operativo, deben establecerse las propiedades de la máquina virtual. Al crear una máquina virtual en VMM con una plantilla de máquina virtual puede establecer la propiedad. También puede establecer estas propiedades para máquinas virtuales existentes en las pestañas **General** y **Configuración del hardware** de las propiedades de la máquina virtual. Si no ve estas propiedades en VMM, podrá configurarlas en el portal de Azure Site Recovery.

![Create virtual machine](./media/site-recovery-vmm-to-azure/ASRE2AVMM_EnableNew.png)

![Modify virtual machine properties](./media/site-recovery-vmm-to-azure/ASRE2AVMM_EnableExisting.png)


1. Para habilitar la protección, en la pestaña <b>Máquinas virtuales</b> de la nube en la que se encuentra la máquina virtual, haga clic en <b>Habilitar protección</b> y seleccione <b>Agregar máquinas virtuales</b>.
2. En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.

	![Enable virtual machine protection](./media/site-recovery-vmm-to-azure/ASRE2AVMM_SelectVM.png)

	Siga el progreso de la acción de habilitación de la protección en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.


	![Virtual machine protection job](./media/site-recovery-vmm-to-azure/ASRE2AVMM_VMJobs.png)

3. Compruebe las propiedades de la máquina virtual y modifíquelas según sea necesario.

	![Verify virtual machines](./media/site-recovery-vmm-to-azure/VMProperties.png)

4. En la pestaña de configuración de las propiedades de la máquina virtual, se pueden modificar las siguientes propiedades de red.

    1. El número de adaptadores de red de la máquina virtual de destino: el número de adaptadores de red en la máquina virtual de destino depende del tamaño de la máquina virtual elegida. El número de adaptadores de red de máquinas virtuales de destino es el mínimo número de adaptadores de red en la máquina virtual de origen y el número máximo de adaptadores de red compatible con el tamaño de la máquina virtual elegida.  

	1. Red de la máquina virtual de destino: la red a la que se conecta la máquina virtual está determinada por la asignación de red de la red de la máquina virtual de origen. En el caso de que la máquina virtual de origen tenga más de un adaptador de red y las redes de origen se asignen a distintas redes en el destino, el usuario tendría que elegir entre una de las redes de destino.

	1. Subred de cada adaptador de red: para cada adaptador de red el usuario puede elegir la subred a la que se conectará la máquina virtual con la conmutación por error.

	1. IP de destino: si el adaptador de red de la máquina virtual de origen se configura para usar la dirección IP estática, el usuario puede proporcionar la dirección IP para la máquina virtual de destino. El usuario puede usar esta capacidad para mantener la dirección IP de la máquina virtual de origen después de una conmutación por error. Si no se proporciona la dirección IP, no se ofrecerá ninguna dirección IP disponible al adaptador de red en el momento de la conmutación por error. En el caso de que la dirección IP de destino proporcionada por el usuario ya esté en uso por parte de otra máquina virtual que ya se esté ejecutando en Azure, es posible que se produzca un error en la conmutación por error.

		![Modificación de las propiedades de red](./media/site-recovery-vmm-to-azure/MultiNic.png)

## Prueba de la implementación
Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada. Observe lo siguiente:

- Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba.
- Después de la conmutación por error, usará una dirección IP pública para conectarse a la máquina virtual de Azure mediante Escritorio remoto. Si desea realizar esto, asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.

### Creación de un plan de recuperación

1. En la pestaña **Planes de recuperación**, agregue un plan nuevo. Especifique un nombre, **VMM** en **Tipo de origen** y el servidor VMM de origen en **Origen**. El destino será Azure.

	![Creación de un plan de recuperación](./media/site-recovery-vmm-to-azure/ASRE2AVMM_RP1.png)

2. En la página **Seleccionar máquinas virtuales**, seleccione las máquinas virtuales que se agregarán al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado del plan de recuperación: grupo 1. Se ha probado un máximo de 100 máquinas virtuales en un solo plan de recuperación.

	- Si desea comprobar las propiedades de la máquina virtual antes de agregarlas al plan, haga clic en la máquina virtual en la página de propiedades de la nube en la que se encuentra. También puede configurar las propiedades de la máquina virtual en la consola VMM.
	- Todas las máquinas virtuales que se muestran se han habilitado para la protección. La lista incluye las máquinas virtuales que se han habilitado para protección y cuya replicación inicial se ha completado, así como las que están habilitadas para protección y cuya replicación inicial está pendiente. Solo las máquinas virtuales con la replicación inicial completada pueden conmutar por error como parte de un plan de recuperación. Por tanto, compruebe el estado de la replicación inicial de las máquinas virtuales en el plan antes de iniciar la conmutación por error del plan de recuperación.


	![Creación de un plan de recuperación](./media/site-recovery-vmm-to-azure/ASRE2AVMM_SelectVMRP.png)

Una vez creado un plan de recuperación, aparecerá en la pestaña **Planes de recuperación**. También puede agregar [Runbooks de Automatización de Azure](site-recovery-runbook-automation.md) al plan de recuperación para automatizar acciones en tiempo de conmutación por error.

### Ejecución de una conmutación por error de prueba

Hay dos maneras de ejecutar una prueba de conmutación por error en Azure.

- Probar la conmutación por error sin una red de Azure: este tipo de conmutación por error de prueba comprueba que la máquina virtual incluye correctamente en Azure. La máquina virtual no estará conectada a ninguna red de Azure después de la conmutación por error.
- Probar la conmutación por error con una red de Azure: este tipo de conmutación por error comprueba que todo el entorno de replicación se incluye como se esperaba y que conmutan las máquinas virtuales se conectarán al red de Azure de destino especificada. En el caso del control de subredes, para probar la conmutación por error de la subred se averiguará la máquina virtual de prueba de acuerdo con la subred de la máquina virtual de réplica. Esto es diferente a la replicación normal cuando la subred de una máquina virtual de réplica se basa en la subred de la máquina virtual de origen.

Si desea ejecutar una conmutación por error de prueba para una máquina virtual habilitada para protección en Azure sin especificar una red de Azure de destino, no es necesario preparar nada. Para ejecutar una prueba de conmutación por error con una red de Azure de destino, es necesario crear una nueva red de Azure que esté aislada de su red de Azure de producción (el comportamiento predeterminado cuando se crea una nueva red de Azure) y configurar la infraestructura de la máquina virtual replicada para que funcione como se espera. Por ejemplo, una máquina virtual con controlador de dominio y DNS se pueden replicar en Azure con Azure Site Recovery y se puede crear en la red de prueba mediante pruebas de conmutación por error. Para ejecutar una conmutación por error de prueba siga estos pasos:

1. Realice una conmutación por error de prueba de la máquina virtual con controlador de dominio y DNS en la misma red que se usará para la conmutación por error de prueba real de la máquina virtual local.
2. Anote las direcciones IP que se asignaron a los errores en la máquina virtual de DNS.
3. En la red virtual de Azure que se utilizará para la conmutación por error, agregue la dirección IP como dirección del servidor DNS.
4. Ejecute la conmutación por error de prueba de las máquinas virtuales locales de origen, especificando la red de prueba de Azure.
5. Después de comprobar que el error de prueba funciona según lo esperado, marque la conmutación por error de prueba como completada para el plan de recuperación y, a continuación, marque la conmutación por error de prueba como completada para las máquinas virtuales de controlador de dominio y DNS.

Para ejecutar un conmutación por error de prueba, realice lo siguiente:

1. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
1. En la página **Confirmar conmutación por error de prueba**, seleccione **Ninguna** o una red de Azure concreta. Tenga en cuenta que si selecciona Ninguna, la conmutación por error de prueba comprueba que la máquina virtual se ha replicado correctamente en Azure, pero no comprueba la configuración de red de replicación.

	![Sin red](./media/site-recovery-vmm-to-azure/ASRE2AVMM_TestFailoverNoNetwork.png)

1. Si el cifrado de datos para la nube está habilitado, en **Clave de cifrado**, seleccione el certificado que se emitió durante la instalación del proveedor en el servidor de VMM, cuando activó la opción para habilitar el cifrado de datos para una nube.
1. En la pestaña **Trabajos** puede seguir el progreso de la conmutación por error. También debe poder ver la réplica de prueba de la máquina virtual en el Portal de Azure. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.
1. Cuando la conmutación por error alcance la fase **Completar pruebas**, haga clic en **Completar prueba** para terminar la conmutación por error de prueba. Puede profundizar hasta la pestaña **Trabajo** para realizar un seguimiento de la conmutación por error del progreso y el estado, y llevar a cabo las acciones necesarias.
1. Después de la conmutación por error, podrá ver la réplica de prueba de la máquina virtual en el Portal de Azure. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual. Observe lo siguiente:

    1. Compruebe que las máquinas virtuales se inician correctamente.
    1. Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba. También necesitará agregar un extremo RDP a la máquina virtual. Puede aprovechar un [Runbooks de automatización de Azure](site-recovery-runbook-automation.md) para hacerlo.
    1. Después de conmutación por error, si usa una dirección IP pública para conectarse a la máquina virtual en Azure mediante Escritorio remoto, asegúrese de no tener directivas de dominio que le impidan conectarse a una máquina virtual con una dirección pública.

1.  Cuando se complete la prueba, haga lo siguiente:
	- Haga clic en **La conmutación por error de prueba se ha completado**. Limpie el entorno de prueba para apagar y eliminar automáticamente las máquinas virtuales de prueba.
	- Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.

## <a id="runtest" name="runtest" href="#runtest"></a>Supervisión de la actividad
<p>Puede usar la pestaña *Trabajos* y el *Panel* para ver y supervisar los principales trabajos que se realizan en el almacén de Azure Site Recovery, como la configuración de la protección para una nube, la habilitación y deshabilitación de la protección para una máquina virtual, la ejecución de una conmutación por error (planeada, no planeada o de prueba) y la realización de una conmutación por error no planeada.</p>

<p>En la pestaña *Trabajos*, puede ver los trabajos, profundizar en los detalles y los errores del trabajo, ejecutar consultas del trabajos para recuperar aquellos que coincidan con criterios específicos, exportar trabajos a Excel y reiniciar los trabajos con errores.</p>

<p>En el *Panel*, puede descargar las versiones más recientes de los archivos de instalación del proveedor y el agente, obtener información de configuración del almacén, ver el número de máquinas virtuales cuya protección se gestiona en el almacén, ver los trabajos recientes, administrar el certificado del almacén y volver a sincronizar las máquinas virtuales.</p>

<p>Para obtener más información sobre la interacción con los trabajos y el panel, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guía de operaciones y supervisión</a>.</p>

##<a id="next" name="next" href="#next"></a>Pasos siguientes
<UL>
<LI>Para planificar e implementar Azure Site Recovery en un entorno completo de producción, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guía de planeación de Azure Site Recovery</a> y la <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guía de implementación de Azure Site Recovery</a>.</LI>


<LI>Si tiene preguntas, visite el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</LI> </UL>
 

<!---HONumber=July15_HO2-->