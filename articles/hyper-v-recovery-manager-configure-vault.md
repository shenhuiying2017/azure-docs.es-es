<properties 
	pageTitle="Tutorial: Configuración de la protección entre sitios de VMM locales" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V entre sitios de VMM locales." 
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


# Introducción a la recuperación de sitios de Azure:  Protección de sitio de VMM de local a local con replicación de Hyper-V


<div class="dev-callout"> 

<p>Azure Site Recovery contribuye a su estrategia de continuidad de negocio y carga de trabajo mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación.<p>

<P>En este tutorial se describe cómo implementar Azure Site Recovery para organizar y automatizar la protección de las cargas de trabajo que se ejecutan en un sitio VMM local a otro sitio VMM local, mediante la replicación de Hyper-V.  El tutorial usa la ruta de acceso de implementación más rápida y la configuración predeterminada cuando sea posible.</P>

<UL>
<LI>Encontrará instrucciones detalladas de implementación en las guías de <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planeación</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Implementación</a>.</LI>
<LI>Encontrará información acerca de escenarios de implementación de Azure Site Recovery en <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Introducción a Azure Site Recovery</a>.</LI>
<LI>Si experimenta problemas durante este tutorial, revise el artículo de wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Escenarios de error comunes y soluciones</a>, o publique sus preguntas en el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Foro de servicios de recuperación de Azure</a>.</LI>
</UL>

</div>


<h2><a id="before"></a>Requisitos previos</h2> 
<div class="dev-callout"> 
<P>Asegúrese de que tiene todo colocado antes de comenzar el tutorial.</P>

<UL>
<LI><b>Cuenta de Azure</b>: necesitará una cuenta de Azure. Si no tiene una, consulte <a href="http://aka.ms/try-azure">Evaluación gratuita de Azure</a>. Puede obtener información sobre precios en la página de <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalles de precios de Azure Site Recovery Manager</a>.</LI>
<LI><b>Servidor VMM</b>: necesitará al menos un servidor VMM que se ejecute en System Center 2012 SP1 o en System Center 2012 R2.</LI>
<LI><b>Nubes VMM</b>: necesitará al menos una nube en el servidor VMM de origen que desea proteger y otra en el servidor VMM de destino. Si utiliza un servidor VMM, necesitará dos nubes. La nube principal que desea proteger debe contener:<UL>
	<LI>Uno o más grupos de hosts de VMM</LI>
	<LI>Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts</LI>
	<li>Una o varias máquinas virtuales ubicadas en el servidor de Hyper-V de origen en la nube.</li>
		</UL></LI>
<LI>**Redes**: puede configurar de manera óptima la asignación de red para asegurarse de que las máquinas virtuales de réplica se colocan de manera óptima en los servidores host de Hyper-V tras la conmutación por error y que se pueden conectar a las redes de VM adecuadas. Cuando se habilite la asignación de red, se conectará una máquina virtual de la ubicación principal a una red y su réplica de la ubicación de destino se conectará a la red asignada. Si no configura la asignación de red, las máquinas virtuales no se conectarán a las redes de VM después de la conmutación por error. En este tutorial se describe la configuración más sencilla del tutorial y no incluye la asignación de red, pero puede leer más información en:</LI>
	<UL>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522289">Asignación de red</a> en la Guía de planeación.</LI>
	<LI><a href="http://go.microsoft.com/fwlink/?LinkId=522293">Habilite la asignación de red</a> en la guía de implementación.</LI>
	</UL></LI>

</UL>



<h2><a id="tutorial"></a>Pasos del tutorial</h2> 

Después de comprobar los requisitos previos, realice las siguientes tareas:
<UL>
<LI><a href="#vault">Paso 1: Crear una almacén</a>: Crear un almacén para Azure Site Recovery.</LI>
<LI><a href="#download">Paso 2: Instalar la aplicación de proveedor en cada servidor VMM</a>: permite generar una clave de registro en el almacén y descargar el archivo de instalación del proveedor. Ejecute el programa de instalación en cada servidor VMM para instalar el proveedor y registrar el servidor de VMM en el almacén.</LI>
<LI><a href="#clouds">Paso 3: Configurar la protección de la nube</a>: Configure los parámetros de protección para las nubes VMM. Esta configuración de protección se aplica a todas las máquinas virtuales de la nube que habilita para la protección de Azure Site Recovery.</LI>
<LI><a href="#storagemapping">Paso 4: Configurar la asignación de almacenamiento</a>: si desea especificar dónde se almacenan los datos de replicación puede configurar la asignación de almacenamiento. Esto asigna las clasificaciones de almacenamiento en el servidores VMM de origen a las clasificaciones de almacenamiento en el servidor de destino.</LI>
<LI><a href="#enablevirtual">Paso 5: Habilitar protección para las máquinas virtuales</a>: Habilite la protección para las máquinas virtuales ubicadas en las nubes VMM.</LI>
<LI><a href="#recovery plans">Paso 6: Configurar y ejecutar planes de recuperación</a>: cree un plan de recuperación y ejecute una conmutación por error de prueba para el plan para asegurarse de que está funcionando.</LI>

</UL>




<a name="vault"></a> <h2>Paso 1: Creación de un almacén</h2>

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).


2. Expanda <b>Servicios de datos</b>, luego <b>Servicios de recuperación</b> y haga clic en <b>Almacén de Site Recovery</b>.


3. Haga clic en <b>Crear nuevo</b> y luego en <b>Creación rápida</b>.
	
4. En <b>Name</b>, escriba un nombre descriptivo para identificar el almacén.

5. En <b>Región</b>, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas vea Disponibilidad geográfica en <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalles de precios de Azure Site Recovery</a>

6. Haga clic en <b>Crear almacén</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como <b>Activo</b> en la página principal de Servicios de recuperación.</P>

<a name="upload"></a> <h2>Paso 2: Configuración del almacén</h2>


1. En la página <b>Servicios de recuperación</b>, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. En la lista desplegable seleccione **Entre dos sitio Hyper-V locales**.
3. En **Preparar servidores VMM**, haga clic en el archivo **Generar clave de registro**. La clave será válida para 5 días a partir del momento en que se genera. Copie el archivo al servidor VMM. Lo necesitará al configurar el proveedor.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png)
	



<a name="download"></a> <h2>Paso 3: Instalación del proveedor de Azure Site Recovery</h2>
	

1. En la página de <b>Inicio rápido</b>, en **Preparar servidores VMM**, haga clic en <b>Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM</b> a fin de obtener la versión más reciente del archivo de instalación del proveedor.

2. Ejecute el archivo en servidores VMM de origen y de destino.

3. En la **Comprobación de requisitos previos** seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración. 

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.

5. En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione <b>Utilizar la configuración proxy del sistema predeterminado</b> para utilizar los parámetros de conexión a Internet predeterminados configurados en el servidor.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. En **Clave de registro**, seleccione la que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
7. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor.
8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)


9. En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor de VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.


7. En **Cifrado de datos** genera un certificado que se usa para cifrar los datos protegidos en Azure. 
Esta opción no es relevante para el escenario descrito en este tutorial.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Haga clic en <b>Register</b> para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se muestra en la pestaña <b>Recursos</b> de la página **Servidores** del almacén.




<h2><a id="clouds"></a>Paso 4: Configurar la protección de la nube</h2>

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña <b>Elementos protegidos</b> del almacén.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)

1. En la página de inicio rápido, haga clic en **Configurar protección para nubes VMM**.
2. En la pestaña **Protected Items**, seleccione la nube que desea configurar y vaya a la pestaña **Configuration**. Observe lo siguiente:
3. En <b>Destino</b>, seleccione <b>VMM</b>.
4. En <b>Ubicación de destino</b>, seleccione el servidor VMM que administra la nube que desea utilizar para recuperación.
4. En <b>Nube de destino</b>, seleccione la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen. Observe lo siguiente:
	- Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
	- Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.

6. En <b>Copiar frecuencia</b>, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino. Solo es pertinente cuando el host de Hyper-V ejecuta Windows Server 2012 R2. En el caso de otros servidores, se utiliza una configuración predeterminada de cinco minutos.
7. En <b>Puntos de recuperación adicionales</b>, deje la configuración predeterminada. Este valor especifica si desea crear puntos de recuperación adicionales. El valor predeterminado de cero especifica que el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
8. En <b>Frecuencia de las instantáneas coherentes con la aplicación</b>, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea.  Si de todos modos desea definir este valor para el tutorial, asegúrese de definirlo en un número menor que la cantidad de puntos de recuperación adicionales que configure.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png)
9. En <b>Transferencia de datos comprimida</b>, especifique si se deben comprimir los datos replicados que se transfieren. 
10. En <b>Authentication</b>, especifique cómo se autentica el tráfico entre el servidor host de Hyper-V principal y el servidor host de Hyper-V de recuperación. Para este tutorial, seleccione HTTPS, a no ser que tenga configurado un entorno Kerberos activo. Azure Site Recovery configurará automáticamente los certificados para la autenticación de HTTPS. No se requiere configuración manual. Observe que esta configuración solo es pertinente para servidores host de Hyper-V que se ejecutan en Windows Server 2012 R2.
11. En <b>Port</b>, deje la configuración predeterminada. Este valor define el número de puerto en el que los equipos host de Hyper-V de origen y de destino escuchan el tráfico de replicación.
12. En <b>Método de replicación</b>, especifique cómo se manejará la replicación inicial de los datos desde la ubicación de origen a la ubicación de destino, antes de que comience la replicación normal. 
	- <b>Por la red</b>: copiar los datos sobre la red puede ser un proceso lento y que consume muchos recursos. Le recomendamos utilizar esta opción si la nube contiene máquinas virtuales con discos duros virtuales relativamente pequeños y si el servidor VMM principal está conectado al servidor VMM secundario a través de una conexión con un amplio ancho de banda. Puede especificar que la copia se inicie de inmediato, o bien, seleccionar una hora. Si utiliza la replicación de red, le recomendamos programarla evitando las horas de mayor consumo.
	- <b>Sin conexión</b>: este método especifica que la replicación inicial se realizará usando medios externos. Esta opción resulta útil si desea evitar la degradación del rendimiento de la red o en el caso de ubicaciones geográficamente remotas. Para utilizar este método, especifique la ubicación de exportación en la nube de origen y la ubicación de importación en la nube de destino. Cuando habilita la protección de una máquina virtual, el disco duro virtual se copia en la ubicación de exportación especificada. Usted lo envía al sitio de destino y lo copia en la ubicación de importación. El sistema copia la información importada en las máquinas virtuales de réplica. Si desea obtener una lista completa de los requisitos previos para la replicación sin conexión, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Paso 3: Configurar la protección para nubes VMM</a> en la Guía de implementación.
13. Seleccione **Eliminar máquina virtual de réplica** para especificar la máquina virtual de réplica que se debe eliminar si deja de proteger la máquina virtual al seleccionar la opción **Eliminar la protección para la máquina virtual** de la pestaña de máquinas virtuales de las propiedades de la nube. Con esta configuración deshabilitada, cuando deshabilite la protección, la máquina virtual se elimina de Azure Site Recovery, la configuración de Site Recovery para la máquina virtual se elimina de la consola VMM y la réplica se elimina.
	![Configure protection settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png)

<p>Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña <b>Trabajos</b>. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación. Puede modificar la configuración de la nube en la pestaña <b>Configure</b>. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.</p>

<h2><a id="storagemapping"></a>Paso 5: Configurar la asignación de almacenamiento</h2>

<p>En este tutorial se describe la ruta más sencilla para implementar Azure Site Recovery en un entorno de prueba. Para configurar la asignación de almacenamiento como parte de este tutorial, lea <a href="http://go.microsoft.com/fwlink/?LinkId=402535">Configurar la asignación de almacenamiento</a> en la guía de implementación.</p>


<h2><a id="enablevirtual"></a>Paso 6: Enable virtual machine protection</h2>
<p>Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube.</p>
<OL>
<li>En la pestaña <b>Máquinas virtuales</b> en la nube en la que está situada la máquina virtual, haga clic en <b>Habilitar protección</b> y después seleccione <b>Agregar máquinas virtuales</b>. </li>
<li>En la lista de máquinas virtuales de la nube, seleccione la que desea proteger.</li> 
</OL>

![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png)


<P>Siga el progreso de la acción de habilitación de la protección en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.</p>



![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="recoveryplans"></a>Paso 7: Prueba de la implementación</h2>

Pruebe la implementación para asegurarse de que la conmutación por error de las máquinas virtuales y los datos se realiza de la manera esperada. Para ello, creará un plan de recuperación seleccionando los grupos de replicación. A continuación, ejecute una conmutación por error de prueba en el plan.

1. En la pestaña **Planes de recuperación**, haga clic en **Crear plan de recuperación**.
2. Especifique un nombre para el plan de recuperación, y los servidores VMM de origen y destino. El servidor de origen debe tener máquinas virtuales habilitadas para conmutación por error y recuperación. Seleccione **Hyper-V** para ver solo las nubes que están configuradas para la replicación de Hyper-V.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP1.png)

3. En **Seleccionar máquina virtual**, seleccione grupos de replicación. Se seleccionarán todas las máquinas virtuales asociadas al grupo de replicación y se agregarán al plan de recuperación. Estas máquinas virtuales se agregan al grupo predeterminado del plan de recuperación: grupo 1. Puede agregar más grupos si es necesario. Tenga en cuenta que tras la replicación las máquinas virtuales se iniciarán según el orden de los grupos del plan de recuperación.

	![Add virtual machines](./media/hyper-v-recovery-manager-configure-vault/SRE2E_RP2.png)	

4. Cuando se haya creado un plan de recuperación, aparecerá en la lista de la pestaña **Planes de recuperación**. 
5. En la pestaña **Planes de recuperación**, seleccione el plan y haga clic en **Probar conmutación por error**.
6. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. Tenga en cuenta que con esta opción habilitada las máquinas virtuales de réplica de conmutación por error no se conectarán a ninguna red. Esto probará que la máquina virtual realiza un conmutación por error de la manera esperada pero no prueba su entorno de red de replicación. Si desea ejecutar una conmutación por error de prueba más completa, vea <a href="http://go.microsoft.com/fwlink/?LinkId=522291">Probar una implementación local en MSDN</a>.

	![Select test network](./media/hyper-v-recovery-manager-configure-vault/SRSAN_TestFailover1.png)


7. La máquina virtual de prueba se creará en el mismo host en el que existe la máquina virtual de réplica. No se agregará a la nube en la que se encuentra la máquina virtual de réplica.
8. Después de la replicación, la máquina virtual de réplica tendrá una dirección IP que no es la misma que la dirección IP de la máquina virtual principal. Si está emitiendo direcciones desde DHCP, DNS se actualizará de manera automática. Si no utiliza DHCP y desea asegurarse de que las direcciones son las mismas, deberá ejecutar un par de scripts.
9. Ejecute este script de ejemplo para recuperar la dirección IP.
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


<h3><a id="runtest"></a>Supervisión de la actividad</h3>
<p>Puede usar la pestaña <b>Trabajos</b> y el <b>Panel</b> para ver y supervisar los principales trabajos que se realizan en el almacén de Azure Site Recovery, como la configuración de la protección para una nube, la habilitación y deshabilitación de la protección para una máquina virtual, la ejecución de una conmutación por error (planificada, no planificada o de prueba) y la realización de una conmutación por error no planificada.</p>

<p>En la pestaña <b>Trabajos</b>, puede ver los trabajos, profundizar en los detalles y los errores del trabajo, ejecutar consultas del trabajos para recuperar aquellos que coincidan con criterios específicos, exportar trabajos a Excel y reiniciar los trabajos con errores.</p>

<p>En el <b>Panel</b>, puede descargar las versiones más recientes de los archivos de instalación del proveedor y el agente, obtener información de configuración del almacén, ver el número de máquinas virtuales cuya protección se gestiona en el almacén, ver los trabajos recientes, administrar el certificado del almacén y volver a sincronizar las máquinas virtuales.</p>

<p>Para obtener más información sobre la interacción con los trabajos y el panel, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">guía de operaciones y supervisión</a>.</p>
	
<h2><a id="next"></a>Pasos siguientes</h2>
<UL>
<LI>Para planificar e implementar Azure Site Recovery en un entorno completo de producción, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guía de planificación de Azure Site Recovery</a> y <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guía de implementación de Azure Site Recovery</a>.</LI>
<LI>Si tiene preguntas, visite el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Foro de servicios de recuperación de Azure</a>.</LI> 
</UL>

<!--HONumber=35.2-->

<!--HONumber=46--> 

<!--HONumber=46--> 
