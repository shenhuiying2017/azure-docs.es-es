<properties 
	pageTitle="Tutorial: Configuración de la protección entre sitios de VMM locales con SAN" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V entre sitios locales mediante la replicación de SAN." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Tutorial: Configuración de la protección entre sitios de VMM locales con SAN



<h2><a id="overview" name="overview" href="#overview"></a>Información general</h2>
<p>Azure Site Recovery contribuye a su estrategia de continuidad de negocio y carga de trabajo mediante la coordinación de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación.<p>

<P>Este tutorial describe cómo implementar Azure Site Recovery para coordinar y automatizar la protección de las cargas de trabajo que se ejecutan en un servidor de Hyper-V administrado mediante System Center VMM en un sitio local a otro sitio local de VMM mediante la replicación basada en la matriz de almacenamiento (SAN). El tutorial usa la ruta de acceso de implementación más rápida y la configuración predeterminada cuando sea posible. En este escenario:</P>

<ul>
	<li>Podrá aprovechar su infraestructura SAN existente para proteger aplicaciones críticas implementadas en clústeres de Hyper-V.</li>
	<li>La replicación de SAN permite el uso de clústeres de invitados y garantiza la coherencia de la replicación en distintos niveles de las aplicaciones gracias a la replicación sincrónica y asincrónica, dependiendo de las capacidades de la matriz de almacenamiento.</li>
	<li>Podrá configurar y habilitar la replicación en VMM y en el almacén de Azure Site Azure Site Recovery. También podrá descubrir y clasificar el almacenamiento SAN en VMM, así como aprovisionar LUN y asignar el almacenamiento a los clústeres de Hyper-V. Azure Site Recovery automatiza la replicación y coordina la conmutación por error. </li>
	</ul>

<UL>
<LI>Puede leer las instrucciones de implementación completa en las Guías de <a href="http://go.microsoft.com/fwlink/?LinkId=321294">planeación</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=519251">implementación</a>.</LI>
<LI>Encontrará información acerca de escenarios de implementación de Azure Site Recovery en <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Introducción a Azure Site Recovery</a>.</LI>
<LI>Si experimenta problemas durante este tutorial, revise el artículo de wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: escenarios de error comunes y soluciones</a>, o publique sus preguntas en el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</LI>
</UL>




<h2><a id="before" name="before" href="#before"></a>antes de</h2>
<div class="dev-callout"> 
<P>Asegúrese de que tiene todo preparado antes de comenzar el tutorial.</P>

<UL>
<LI><b>Cuenta de Azure</b>: necesitará una cuenta de Azure. Si no dispone de una, consulte <a href="http://aka.ms/try-azure">Evaluación gratuita de Azure</a>. Puede obtener información sobre precios en la página <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalles de precios de Azure Site Recovery Manager</a>.</LI>
<LI>Si desea obtener información acerca de la información que se recopila, procesa o transmite durante las operaciones de Azure Site Recovery, lea <a href="http://go.microsoft.com/fwlink/?LinkId=513016">requisitos de privacidad</a> en MSDN.</LI>
<LI><b>Servidor VMM</b>: necesitará un servidor de VMM en cada sitio local, implementado como servidor físico o virtual independiente o como clúster virtual, que se ejecute en System Center 2012 R2 con la <a href="http://go.microsoft.com/fwlink/?LinkId=517707">vista previa de VMM Update Tollup 5.0.</a>.</LI>
<LI>Necesitará un clúster de hosts de Hyper-V implementado en sitios principales y secundarios que ejecuten al menos Windows Server 2012 con las actualizaciones más recientes.</LI>
<LI><b>Nubes de VMM</b>: debe tener al menos una nube en el servidor VMM principal que desea proteger y otra en el servidor VMM secundario. La nube principal que desea proteger debe contener:<UL>
	<LI>Uno o más grupos de hosts de VMM</LI>
	<LI>Uno o más clústeres de Hyper-V en cada grupo de host.</LI>
	<li>Una o varias máquinas virtuales ubicadas en el servidor de Hyper-V de origen en la nube.</li>
		</UL>Para obtener más información acerca de la configuración de nubes de VMM, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=513015">Planeación de la infraestructura de VMM</a> en la Guía de planeación.</LI>
<LI><b>Almacenamiento SAN</b>: la replicación de SAN permite replicar máquinas virtuales agrupadas en clústeres de invitado con almacenamiento de canal de fibra o iSCSI, o con discos duros virtuales compartidos (vhdx). Los requisitos previos de SAN son los siguientes:</LI>
	<UL>
	<LI>Deberá configurar dos matrices de SAN, una en el sitio principal y otra en el secundario.</LI>
	<LI>La infraestructura de red debe configurarse entre las matrices. Es necesario configurar la replicación y el emparejamiento. Licencias de replicación deben configurarse de acuerdo con los requisitos de la matriz de almacenamiento.</LI>
	<LI>Las funciones de red deben configurarse entre los servidores de host de Hyper-V y la matriz de almacenamiento para que los hosts puedan comunicarse con los LUN de almacenamiento mediante ISCSI o el canal de fibra.</LI>
	<LI>Vea una lista de matrices de almacenamiento compatibles en <a href="http://go.microsoft.com/fwlink/?LinkId=518669">Implementación de Azure Site Recovery con VMM y SAN: matrices de almacenamiento compatibles</a>.</LI>
	
	<LI>Es necesario instalar los proveedores de SMI-S proporcionados por EMC y NetApp. Asimismo, el proveedor deberá administrar las matrices de SAN. Configure el proveedor de acuerdo con su documentación.</LI>
	<LI>Asegúrese de que el proveedor de SMI-S para la matriz se encuentre en un servidor al que pueda tener acceso el servidor VMM a través de la red por dirección IP o FQDN.</LI>
	<LI>Cada matriz de SAN debe tener uno o más grupos de almacenamiento disponibles para el uso en esta implementación.</LI>
	<LI>El servidor VMM del sitio principal necesitará administrar la matriz principal, mientras que el servidor VMM secundario administrará la matriz secundaria.</LI>
	</UL>
<LI><b>Redes</b>Es posible configurar de manera opcional la asignación de red para asegurarse de que las máquinas virtuales de réplica se colocan de manera óptima en los servidores host de Hyper-V tras la conmutación por error y que se pueden conectar a las redes de VM adecuadas. Cuando se habilite la asignación de red, se conectará una máquina virtual de la ubicación principal a una red y su réplica de la ubicación de destino se conectará a la red asignada. Si no configura la asignación de red, las máquinas virtuales no se conectarán a las redes de VM después de la conmutación por error. En este tutorial se describe la configuración más sencilla del tutorial y no incluye la asignación de red, pero puede leer más información en:</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Asignación de red</a> en la Guía de planeación.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522290">Habilitación de asignación de red</a> en la Guía de implementación de SAN.</LI>
	</UL>
</UL>
</div>

<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Pasos del tutorial</h2>
Después de comprobar los requisitos previos, realice las siguientes tareas:

1. <a href="#VMM">Paso 1: Preparar la infraestructura de VMM</a>
	- Integrar y clasificar el almacenamiento SAN en VMM
	- Aprovisione las unidades lógicas (LUN) junto con los grupos de almacenamiento y asigne el almacenamiento a los clústeres de Hyper-V.
	- Cree grupos de replicación para las LUN que se deben replicar entre sí. 
2. <a href="#vault">Paso 2: Creación de un almacén</a>
3. <a href="#download">Paso 3: Instalar el proveedor en los servidores VMM</a>: permite generar una clave de registro en el almacén y descargar el archivo de instalación del proveedor. Ejecute el programa de instalación en los servidores VMM para instalar el proveedor y registrar los servidores en el almacén.
4. <a href="#storage">Paso 4: Asignar los grupos y las matrices de almacenamiento</a>: asigne las matrices para especificar qué grupo de almacenamiento secundario recibe datos de replicación desde el grupo principal. Asigne el almacenamiento antes de configurar la protección de la nube, ya que la información de asignación se utiliza cuando se habilita la protección de los grupos de replicación.
5. <a href="#clouds">Paso 5: Configurar la protección de la nube</a>: defina la configuración de la protección para las nubes de VMM.
7. <a href="#replication">Paso 6: Habilitar los grupos de replicación</a>: para poder habilitar la protección de máquinas virtuales deberá habilitar la replicación para grupos de almacenamiento de replicación.
8. <a href="#enablevirtual">Paso 7: Habilitar protección para las máquinas virtuales</a>: tras la replicación de los grupos de replicación, habilite la protección para máquinas virtuales.
9. <a href="#recovery plans">Paso 8: Prueba de la implementación</a>: ejecute una conmutación por error de prueba para comprobar que las máquinas virtuales y los datos realizan la conmutación por error de la forma esperada. 


<a name="VMM"></a> <h3>Paso 1: Preparar la infraestructura de VMM</h3>

<a name="SAN"></a> <h4>Integrar y clasificar el almacenamiento SAN en VMM</h4>


1. En el área de trabajo **Tejido**, haga clic en **Almacenamiento**. Haga clic en **Inicio** > **Agregar recursos** > **Dispositivos de almacenamiento** para iniciar el Asistente para agregar dispositivos de almacenamiento.
2. En **Seleccionar un tipo de proveedor de almacenamiento**, seleccione **Dispositivos NAS y SAN detectados y administrados con un proveedor de SMI-S**.

	![Provider type](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Providertype.png)

3. En la página **Especificar el protocolo y la dirección del proveedor de SMI-S de almacenamiento**, seleccione **SMI-S CIMXMLS** y especifique la configuración para conectar con el proveedor.
4. En **Dirección IP o FQDN del proveedor** y **Puerto TCP/IP**, especifique la configuración para conectar con el proveedor. Solo puede utilizar una conexión SSL para SMI-S CIMXML.

	![Provider connect](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ConnectSettings.png)

5. En **Cuenta de ejecución**, especifique una cuenta de ejecución de VMM que pueda tener acceso al proveedor, o bien cree una cuenta nueva.
6. En la página **Recopilar información**, VMM intenta detectar e importar la información del dispositivo de almacenamiento de forma automática. Para volver a intentar la detección, haga clic en **Proveedor de examen**. Si el proceso de detección se realiza correctamente, se mostrarán en la página las matrices de almacenamiento detectadas, los grupos de almacenamiento, el fabricante, el modelo y la capacidad.

	![Discover storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Discover.png)

7. En **Seleccionar grupos de almacenamiento para administrar y asignar una clasificación**, seleccione los grupos de almacenamiento que VMM va a administrar y asigne una clasificación. La información de LUN se importará desde los grupos de almacenamiento. Cree LUN en función de las aplicaciones que necesita proteger, de sus requisitos de capacidad y de las necesidades de replicación.

	![Classify storage](./media/hyper-v-recovery-manager-configure-vault/SRSAN_Classify.png)

<a name="LUNs"></a> <h4>Crear LUN y asignar almacenamiento</h4>


1. Una vez integrado el almacenamiento SAN en el VMM, deberá crear (aprovisionar) las LUN. Para obtener información, vea:
	
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518490">Selección de un método para crear unidades lógicas en VMM</a>
	- <a href="http://go.microsoft.com/fwlink/?LinkId=518491">Aprovisionamiento de unidades lógicas de almacenamiento en VMM</a>
2. A continuación, asigne la capacidad de almacenamiento al clúster del host de Hyper-V para que VMM pueda implementar datos de las máquinas virtuales en el almacenamiento aprovisionado: 
	- Antes de asignar el almacenamiento al clúster, deberá asignar el almacenamiento al grupo de host de VMM en el que reside el clúster. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=518493">Cómo asignar unidades lógicas de almacenamiento a un grupo host</a> y <a href="http://go.microsoft.com/fwlink/?LinkId=518492">Cómo asignar los grupos de almacenamiento a un grupo host</a>.
	- Asigne capacidad de almacenamiento al clúster tal como se describe en <a href="http://go.microsoft.com/fwlink/?LinkId=513017">Cómo configurar el almacenamiento en un clúster de hosts de Hyper-V en VMM</a>.
	

<a name="RGs"></a> <h4>Crear grupos de replicación</h4>

1. Cree un grupo de replicación que incluya todas las LUN necesarias para la replicación conjunta.
2. En la consola VMM, abra la pestaña **Grupos de replicación** de las propiedades de la matriz de almacenamiento y haga clic en **Nuevo**.
	![SAN replication group](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RepGroup.png)



<a name="vault"></a> <h3>Paso 2: Creación de un almacén</h3>
Tras validar los requisitos previos de implementación, cree un almacén de Azure Site Recovery. También puede utilizar un almacén existente y configurar la replicación de SAN.

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).


2. Expanda <b>Servicios de datos</b>, luego <b>Servicios de recuperación</b> y haga clic en <b>Almacén de Site Recovery</b>.


3. Haga clic en <b>Crear nuevo</b> y luego en <b>Creación rápida</b>.
	
4. En <b>Name</b>, escriba un nombre descriptivo para identificar el almacén.

5. En <b>Región</b>, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalles de precios de Azure Site Recovery</a>

6. Haga clic en <b>Crear almacén</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como <b>Activo</b> en la página principal de Servicios de recuperación.</P>

<a name="upload"></a> <h2>Paso 3: Configuración del almacén</h3>


1. En la página <b>Servicios de recuperación</b>, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. En la lista desplegable, seleccione **Entre sitios locales de Hyper-V mediante la replicación de la matriz**.


<a name="download"></a> <h3>Paso 3: Instalación del proveedor de Azure Site Recovery</h3>
Una vez creado el almacén, cree un archivo de registro que incluya una clave de registro. Seleccione este archivo cuando instale el proveedor de Azure Site Recovery. 

1. En la página <b>Inicio rápido</b>, en **Preparar servidores VMM**, haga clic en **Generar archivo de clave de registro**. La clave será válida para 5 días a partir del momento en que se genera. Debe descargar el archivo en una ubicación segura accesible para los servidores VMM. Por ejemplo, un recurso compartido. Deberá seleccionar este archivo cuando configure el proveedor.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartRegKey.png)


2. En la página de <b>Inicio rápido</b>, en **Preparar servidores VMM**, haga clic en <b>Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM</b> a fin de obtener la versión más reciente del archivo de instalación del proveedor.
3. Ejecute el archivo en servidores VMM de origen y de destino.
4. En **Comprobación de requisitos previos** seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderPrereq.png)

5. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.

6. En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione <b>Utilizar la configuración proxy del sistema predeterminado</b> para utilizar los parámetros de conexión a Internet predeterminados configurados en el servidor.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderProxy.png)

7. En **Clave de registro**, seleccione la que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderRegKeyServerName.png)

9. En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor de VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube 
10. 
11. individualmente en las propiedades de la nube de la consola de VMM.

10. La opción **Cifrado de datos** no es relevante en este escenario. 

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_ProviderSyncEncrypt.png)

11. Haga clic en <b>Register</b> para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se muestra en la pestaña <b>Recursos</b> de la página **Servidores** del almacén. Una vez realizada la instalación del proveedor, modifique la configuración del proveedor en la consola VMM.

 
<h3><a id="storage"></a>Paso 4: Asignar los grupos y las matrices de almacenamiento</h3>

Necesitará crear la asignación entre las matrices de almacenamiento y los grupos en los sitios principales y secundarios.

Antes de iniciar el proceso, asegúrese de que las nubes aparecen en el almacén. Las nubes se detectan mediante la selección de la sincronización de todas las nubes durante la instalación del proveedor, o mediante la sincronización de una nube específica en la pestaña **General** de las propiedades de la nube en la consola VMM. A continuación, asigne las matrices de almacenamiento de la forma siguiente:

1. Haga clic en **Recursos** > **Almacenamiento del servidor** > **Asignar matrices de origen y destino**.
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMap.png)
2. Seleccione las matrices de almacenamiento en el sitio principal y asígnelas a las matrices de almacenamiento en el sitio secundario.
3.  Asigne los grupos de almacenamiento de origen y de destino en las matrices. Para ello, en **Grupos de almacenamiento**, seleccione un grupo de almacenamiento de origen y de destino para asignar.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRSAN_StorageMapPool.png)




<h3><a id="clouds"></a>Paso 5: Configurar la protección de la nube</h3>

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña <b>Elementos protegidos</b> del almacén.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SRSAN_CloudsList.png)

1. En la página de inicio rápido, haga clic en **Configurar protección para nubes VMM**.
2. En la pestaña **Protected Items**, seleccione la nube que desea configurar y vaya a la pestaña **Configuration**. Observe lo siguiente:
3. En <b>Destino</b>, seleccione <b>VMM</b>.
4. En <b>Ubicación de destino</b>, seleccione el servidor VMM que administra la nube que desea utilizar para recuperación.
5. En <b>Nube de destino</b>, seleccione la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen. Observe lo siguiente:
	- Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
	- Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.
6. Azure Site Recovery comprueba que las nubes tienen acceso al almacenamiento compatible con la replicación de SAN y que las matrices de almacenamiento están emparejadas. Se mostrarán las parejas de matrices participantes.
7. Si la comprobación se realiza correctamente, en **Tipo de replicación**, seleccione **SAN**.

<p>Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña <b>Trabajos</b>. Puede modificar la configuración de la nube en la pestaña <b>Configure</b>. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.</p>


<h3><a id="replication"></a>Paso 6: Habilitar la replicación de los grupos de replicación</h3>

: para poder habilitar la protección de máquinas virtuales deberá habilitar la replicación para grupos de almacenamiento de replicación. 

1. En el portal de Azure Site Recovery, en la página de propiedades de la nube principal, abra la pestaña **Máquinas virtuales**. Haga clic en **Agregar grupo de replicación**.
2. Seleccione uno o más grupos de replicación de VMM asociados con la nube, compruebe las matrices de origen y de destino y especifique la frecuencia de replicación.

<P>Una vez completada esta operación, Azure Site Recovery junto con VMM y los proveedores de SMI-S aprovisionarán las LUN de almacenamiento del sitio de destino y se habilitará la replicación de almacenamiento. Si el grupo de replicación ya está replicado, Azure Site Recovery reutilizará la relación de replicación existente y actualizará la información de Azure Site Recovery.</P>

<h2><a id="enablevirtual"></a>Paso 7: Enable virtual machine protection</h2>
<p>Cuando la replicación del grupo de almacenamiento esté en curso, habilite la protección para máquinas virtuales en la consola VMM mediante cualquiera de los métodos siguientes:</p>



- **Nueva máquina virtual**: en la consola VMM, cuando se crea una nueva máquina virtual, se habilita la protección de Azure Site Recovery y se asocia la máquina virtual al grupo de replicación.
Con esta opción, VMM usa la ubicación inteligente para colocar de forma óptima el almacenamiento de la máquina virtual en las LUN del grupo de replicación. Azure Site Recovery organiza la creación de una máquina virtual de instantáneas en el sitio secundario y asigna capacidad para que las máquinas virtuales de réplica se puedan iniciar tras la conmutación por error.
- **Máquina virtual existente**: en caso de que la máquina virtual ya esté implementada en VMM, puede habilitar la protección de Azure Site Recovery y realizar una migración del almacenamiento a un grupo de replicación. Tras finalizar este proceso, VMM y Azure Site Recovery detectarán la nueva máquina virtual y comenzarán a administrarla en Azure Site Recovery para ofrecer protección. En el sitio secundario se creará una máquina virtual de instantáneas y se asignará capacidad suficiente para que se pueda iniciar la máquina virtual de réplica tras la conmutación por error.


	![Enable protection](./media/hyper-v-recovery-manager-configure-vault/SRSAN_EnableProtection.png)
	

<P>Tras habilitar las máquinas virtuales para su protección, estas aparecerán en la consola de Azure Site Recovery. Puede ver las propiedades de la máquina virtual, realizar un seguimiento de estado y conmutar por error los grupos de replicación que contienen varias máquinas virtuales. Tenga en cuenta que en la replicación de SAN, todas las máquinas virtuales asociadas a un grupo de replicación deberán realizar la conmutación por error de manera conjunta. Esto es porque la conmutación por error se realiza en la capa de almacenamiento en primer lugar. Es importante agrupar correctamente los grupos de replicación y colocar solo las máquinas virtuales asociadas juntas.</P>

Siga el progreso de la acción de habilitación de la protección en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. 
	![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SRSAN_JobPropertiesTab.png)

<h3><a id="recoveryplans"></a>Paso 8: Prueba de la implementación</h3>



Pruebe la implementación para asegurarse de que la conmutación por error de las máquinas virtuales y los datos se realiza de la manera esperada. Para ello, creará un plan de recuperación seleccionando los grupos de replicación. A continuación, ejecute una conmutación por error de prueba en el plan.

1. En la pestaña **Planes de recuperación**, haga clic en **Crear plan de recuperación**.
2. Especifique un nombre para el plan de recuperación, y los servidores VMM de origen y destino. El servidor de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación. Seleccione **SAN** para ver solo las nubes configuradas para la replicación de SAN.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlan.png)

4. En **Seleccionar máquina virtual**, seleccione grupos de replicación. Se seleccionarán todas las máquinas virtuales asociadas al grupo de replicación y se agregarán al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado del plan de recuperación: grupo 1. Puede agregar más grupos si es necesario. Tenga en cuenta que tras la replicación las máquinas virtuales se iniciarán según el orden de los grupos del plan de recuperación.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRSAN_RPlanVM.png)	
5. Cuando se haya creado un plan de recuperación, aparecerá en la lista de la pestaña **Planes de recuperación**. 
6. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Conmutación por error de prueba**.
7. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. Tenga en cuenta que con esta opción habilitada las máquinas virtuales de réplica de conmutación por error no se conectarán a ninguna red. Esto probará que la máquina virtual realiza un conmutación por error de la manera esperada pero no prueba su entorno de red de replicación. Si desea ejecutar una conmutación por error de prueba más amplia, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Prueba de una implementación local en MSDN</a>.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


8. La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.
9. Después de la replicación, la máquina virtual de réplica tendrá una dirección IP que no es la misma que la dirección IP de la máquina virtual principal. Si está emitiendo direcciones de DHCP, a continuación, se actualizará automáticamente. Si no utiliza DHCP y desea asegurarse de que las direcciones son las mismas, deberá ejecutar un par de scripts.
10. Ejecute este script de ejemplo para recuperar la dirección IP.
    **$vm = Get-SCVirtualMachine -Name <VM_NAME>
	$na = $vm[0].VirtualNetworkAdapters>
	$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
	$ip.address**  
11. Ejecute este script de ejemplo para actualizar DNS, especificando la dirección IP que ha recuperado con el script de ejemplo anterior.

	**[string]$Zone,
	[string]$name,
	[string]$IP
	)
	$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
	$newrecord = $record.clone()
	$newrecord.RecordData[0].IPv4Address  =  $IP
	Set-DnsServerResourceRecord -zonename com -OldInputObject $record -NewInputObject $Newrecord**

<h2><a id="runtest" name="runtest" href="#runtest"></a>Supervisión de la actividad</h2>
<p>Puede usar la pestaña <b>Trabajos</b> y el <b>Panel</b> para ver y supervisar los principales trabajos que se realizan en el almacén de Azure Site Recovery, como la configuración de la protección para una nube, la habilitación y deshabilitación de la protección para una máquina virtual, la ejecución de una conmutación por error (planificada, no planificada o de prueba) y la realización de una conmutación por error no planificada.</p>

<p>En la pestaña <b>Trabajos</b>, puede ver los trabajos, profundizar en los detalles y los errores del trabajo, ejecutar consultas del trabajos para recuperar aquellos que coincidan con criterios específicos, exportar trabajos a Excel y reiniciar los trabajos con errores.</p>

<p>En el <b>Panel</b>, puede descargar las versiones más recientes de los archivos de instalación del proveedor y el agente, obtener información de configuración del almacén, ver el número de máquinas virtuales cuya protección se gestiona en el almacén, ver los trabajos recientes, administrar el certificado del almacén y volver a sincronizar las máquinas virtuales.</p>

<p>Para obtener más información sobre la interacción con los trabajos y el panel, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guía de operaciones y supervisión</a>.</p>
	
<h2><a id="next" name="next" href="#next"></a>Pasos siguientes</h2>
<UL>
<LI>Para planificar e implementar Azure Site Recovery en un entorno completo de producción, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guía de planeación de Azure Site Recovery</a> y la <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guía de implementación de Azure Site Recovery</a>.</LI>
<LI>Si tiene preguntas, visite el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</LI> 
</UL>

<!--HONumber=49-->