<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in on-premises VMM clouds to another on-premises site." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to On-Premises Protection" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew" />

# Introducción a Azure Site Recovery: Protección de instalaciones locales a instalaciones locales

<div class="dev-callout">

<p>Utilice Azure Site Recovery para organizar la protección de máquinas virtuales en servidores host de Hyper-V locales ubicados en nubes VMM. Puede configurar:</p>

<ul>
<li><b>Protección de instalaciones locales a instalaciones locales</b>: Replicar máquinas virtuales locales a otras instalaciones locales. Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Los datos de máquinas virtuales se replican de un servidor Hyper-V local a otro. Más información acerca de esta situación en <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introducción a Azure Site Recovery: Protección de instalaciones locales a instalaciones locales</a>.</li>
<li><b>Protección de instalaciones locales a instalaciones de Azure</b>: Replicar máquinas virtuales locales a máquinas de Azure. Configure y habilite la configuración de protección en los almacenes de Azure Site Recovery. Los datos de máquinas virtuales se replican de un servidor Hyper-V local al almacenamiento de Azure. Más información acerca de esta situación en <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Introducción a Azure Site Recovery: Protección de instalaciones locales a Azure</a></li>

</ul>




<h2><a id="about"></a>Acerca de este tutorial</h2>

<P>Utilice este tutorial para configurar una prueba de concepto rápida para Azure Site Recovery en una implementación de local a Azure. Utiliza la ruta más rápida y la configuración predeterminada siempre que sea posible. Creará un almacén de Azure Site Recovery, instalará el proveedor de Azure Site Recovery en el servidor VMM, configurará los parámetros de protección de la nube, habilitará la protección de máquinas virtuales y comprobará su implementación.</P>

<P>Si desea obtener más información acerca de la implementación completa vea:</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Plan de implementación de Azure Site Recovery</a>: Describe los pasos del plan que debe realizar antes de comenzar una implementación completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321295">Implementar Azure Site Recovery: Protección de instalaciones locales a instalaciones locales</a>: Proporciona instrucciones paso a paso para realizar una implementación completa.</LI>

</UL>
<P>Si durante el transcurso de este tutorial se encuentra con problemas, revise el artículo de wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Common Error Scenarios and Resolutions</a> o publique sus preguntas en el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</P>

</div>


<h2><a id="before"></a>Antes de empezar</h2> 
<div class="dev-callout"> 
<P>Antes de empezar con este tutorial, compruebe los requisitos previos.</P>

<h3><a id="HVRMPrereq"></a>Requisitos previos</h3>

<UL>
<LI><b>Cuenta de Azure</b>: Necesitará una cuenta de Azure. Si no tiene una, vea <a href="http://aka.ms/try-azure">Evaluación gratuita de Azure</a>. Consiga información acerca de los precios en <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalles de precios del administrador de Azure Site Recovery</a>.</LI>
<LI><b>Servidor VMM</b>: Necesitará al menos un servidor VMM que se ejecute en System Center 2012 SP1 o en System Center 2012 R2.</LI>
<LI><b>Nubes VMM</b>: necesitará al menos una nube en el servidor VMM de origen que desea proteger y otra en el servidor VMM de destino. Si utiliza un servidor VMM, necesitará dos nubes. La nube principal que desea proteger debe contener:<UL>
	<LI>Uno o más grupos de hosts de VMM</LI>
	<LI>Uno o más servidores host de Hyper-V o clústers en cada grupo de hosts.</LI>
	<li>Una o más máquinas virtuales ubicadas en el servidor Hyper-V de origen en la nube.</li>
		</UL></LI>
</UL>
</div>



<h2><a id="tutorial"></a>Pasos del tutorial</h2>

Después de comprobar los requisitos previos, realice las siguientes tareas:
<UL>
<LI><a href="#vault">Paso 1: Crear una almacén</a>: Crear un almacén para Azure Site Recovery.</LI>
<LI><a href="#download">Paso 2: Instalar la aplicación del proveedor</a>: Genere una clave de registro y ejecute la aplicación del proveedor de Microsoft Azure Site Recovery en el servidor VMM. Esto instala el proveedor y registra el servidor VMM en el almacén.</LI>
<LI><a href="#clouds">Paso 3: Configurar la protección de la nube</a>: Configure los parámetros de protección para las nubes VMM.</LI>
<LI><a href="#networkmapping">Paso 5: Configurar la asignación de red</a>: Opcionalmente, puede configurar la asignación de red para signar redes VM de origen a redes VM de destino.</LI>
<LI><a href="#storagemapping">Paso 6: Configurar la asignación de almacenamiento</a>: Opcionalmente, puede configurar la asignación de almacenamiento para asignar clasificaciones de almacenamiento en el servidor VM de origen a clasificaciones de almacenamiento en el servidor VM de destino.</LI>
<LI><a href="#enablevirtual">Paso 7: Habilitar protección para las máquinas virtuales</a>: Habilite la protección para las máquinas virtuales ubicadas en las nubes VMM.</LI>
<LI><a href="#recovery plans">Paso 8: Configurar y ejecutar planes de recuperación</a>: Cree un plan de recuperación y ejecute una conmutación por error de prueba para el plan.</LI>

</UL>




<a name="vault"></a> <h2>Paso 1: Crear un almacén</h2>


1.  Inicie sesión en el [Portal de administración][Portal de administración].<br>
<br>
2.  Expanda <b>Servicios de datos</b>, expanda <b>Servicios de recuperación</b> y haga clic en <b>Almacén de recuperación del sitio</b>.<br><br>

3.  Haga clic en <b>Create New</b> y a continuación en <b>Quick Create</b>.<br>
<br>
4.  En <b>Name</b>, escriba un nombre descriptivo para identificar el almacén.<br>
<br>
5.  En <b>Región</b>, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas vea Disponibilidad geográfico en <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalles de precios de Azure Site Recovery</a>
<br><br>
6.  Haga clic en <b>Crear almacén</b>.

    ![Almacén nuevo][Almacén nuevo]

<p>Revise la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá en una lista como <b>Activo</b> en la página principal de Servicios de recuperación.</p>

<a name="upload"></a> <h2>Paso 2: Configurar el almacén</h2>

1.  En la página <b>Servicios de recuperación</b>, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento con el icono.
<br><br>
    ![Icono de inicio rápido][Icono de inicio rápido]
<br><br>
2.  En la lista desplegable seleccione <b>Entre dos sitio Hyper-V locales</b>.<br><br>
3.  En <b>Preparar servidores VMM</b>, haga clic en el archivo <b>Generar clave de registro</b>. La clave será válida para 5 días a partir del momento en que se genera. Copie el archivo al servidor VMM. Lo necesitará al configurar el proveedor.
<br><br>
    ![Clave de registro][Clave de registro]
	<br><br>


<a name="download"></a> <h2>Paso 3: Instalar el proveedor de Azure Site Recovery</h2>

1.  En la página de  <b>Inicio rápido </b>, en <b>Preparar servidores VMM</b>, haga clic en  <b>Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM </b> a fin de obtener la versión más reciente del archivo de instalación del proveedor.
<br><br>
2.  Ejecute el archivo en servidores VMM de origen y de destino.
<br><br>
3.  En la <b>Comprobación de requisitos previos</b> seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración.
<br><br>
    ![Requisitos previos][Requisitos previos]
<br><br>
4.  En <b>Microsoft Update</b> puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.
<br><br>
	![Microsoft Updates][Microsoft Updates]
<br><br>
Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.
<br><br>
5.  En la página de conexión a Internet, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione  <b>Utilizar la configuración proxy del sistema predeterminado </b> para utilizar los parámetros de conexión a Internet predeterminados configurados en el servidor.
<br><br>
    ![Configuración de Internet][Configuración de Internet]
<br><br>
6.  En <b>Clave de registro</b>, seleccione la que ha descargado de Azure Site Recovery y copiado en el servidor VMM.<br><br>
7.  En <b>Nombre del almacén</b>, compruebe el nombre del almacén en el que se registrará el servidor.<br><br>
8.  En <b>Nombre del servidor</b>, especifique un nombre descriptivo para identificar el servidor VMM en el almacén.<br><br>

    ![Registro de servidor][Registro de servidor]
<br><br>
9.  En la sincronización de <b>Metadatos de la nube inicial</b> seleccione si desea sincronizar los metadatos de todas las nubes en el servidor de VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.<br><br>

7.  En <b>Cifrado de datos</b> puede generar un certificado que se utiliza para cifrar datos protegidos en Azure.
    Esta opción no es relevante si está replicando desde un sitio local a otro.

    ![Registro de servidor][1]
<br><br>
8.  Haga clic en  <b>Register </b> para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se mostrará en el extremo de la pestaña  <b>Recursos </b> en la página <b>Servidores</b> del almacén.
<br><br>


<h2><a id="clouds"></a>Paso 4: Configurar la protección de la nube</h2>

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. Habilitó la opción <b>Synchronize cloud data with the vault</b> cuando instaló el proveedor de manera que todas las nubes del servidor VMM aparecerán en la pestaña <b>Protected Items</b> del almacén.

![Nube publicada][Nube publicada]
<br><br>
1.  En la página Inicio rápido, haga clic en <b>Set up protection for VMM clouds</b>.<br><br>
2.  En la pestaña <b>Protected Items</b>, seleccione la nube que desea configurar y vaya a la pestaña <b>Configuration</b>. Observe lo siguiente:<br><br>
3.  En <b>Target</b>, seleccione <b>VMM</b>.<br><br>
4.  En <b>Target location</b>, seleccione el servidor VMM que administra la nube que desea utilizar para recuperación.<br><br>
5.  En <b>Target cloud</b>, seleccione la nube de destino que desea utilizar para la conmutación por error de máquinas virtuales en la nube de origen. Observe lo siguiente:
<br><br>
    -   Recomendamos seleccionar una nube de destino que cumpla con los requisitos de recuperación para las máquinas virtuales que protegerá.
    -   Una nube solo puede pertenecer a un único par de nubes, ya sea como nube principal o de destino.
<br><br>
6.  En <b>Copy Frequency</b>, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino. Solo es pertinente cuando el host de Hyper-V ejecuta Windows Server 2012 R2. En el caso de otros servidores, se utiliza una configuración predeterminada de cinco minutos.<br><br>
7.  En <b>Additional recovery points</b>, deje la configuración predeterminada. Este valor especifica si desea crear puntos de recuperación adicionales. El valor predeterminado de cero especifica que el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.<br><br>
8.  En <b>Frequency of application-consistent snapshots</b>, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea. Si de todos modos desea definir este valor para el tutorial, asegúrese de definirlo en un número menor que la cantidad de puntos de recuperación adicionales que configure.<br><br>
    ![Configure protection settings][Configure protection settings]<br><br>
9.  En <b>Data transfer compressed</b>, especifique si se deben comprimir los datos replicados que se transfieren.<br><br>
10. En <b>Authentication</b>, especifique cómo se autentica el tráfico entre el servidor host de Hyper-V principal y el servidor host de Hyper-V de recuperación. Para este tutorial, seleccione HTTPS, a no ser que tenga configurado un entorno Kerberos activo. Azure Site Recovery configurará automáticamente los certificados para la autenticación de HTTPS. No se requiere configuración manual. Observe que esta configuración solo es pertinente para servidores host de Hyper-V que se ejecutan en Windows Server 2012 R2.<br><br>
11. En <b>Port</b>, deje la configuración predeterminada. Este valor define el número de puerto en el que los equipos host de Hyper-V de origen y de destino escuchan el tráfico de replicación.<br><br>
12. En <b>Replication method</b>, especifique cómo se manejará la replicación inicial de los datos desde la ubicación de origen a la ubicación de destino, antes de que comience la replicación normal.
<br><br>
    -   <b>Over the network</b>: Copiar los datos sobre la red puede ser un proceso lento y que consume muchos recursos. Le recomendamos utilizar esta opción si la nube contiene máquinas virtuales con discos duros virtuales relativamente pequeños y si el servidor VMM principal está conectado al servidor VMM secundario a través de una conexión con un amplio ancho de banda. Puede especificar que la copia se inicie de inmediato, o bien, seleccionar una hora. Si utiliza la replicación de red, le recomendamos programarla evitando las horas de mayor consumo.<br><br>
    -   <b>Offline</b>: este método especifica que la replicación inicial se realizará usando medios externos. Esta opción resulta útil si desea evitar la degradación del rendimiento de la red o en el caso de ubicaciones geográficamente remotas. Para utilizar este método, especifique la ubicación de exportación en la nube de origen y la ubicación de importación en la nube de destino. Cuando habilita la protección de una máquina virtual, el disco duro virtual se copia en la ubicación de exportación especificada. Usted lo envía al sitio de destino y lo copia en la ubicación de importación. El sistema copia la información importada en las máquinas virtuales de réplica. Si desea obtener una lista completa de los requisitos previos para la replicación sin conexión, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Paso 3: Configurar la protección para nubes VMM</a> en la Guía de implementación.
<br><br>
13. Seleccione <b>Delete Replica Virtual Machine</b> para especificar la máquina virtual de réplica que se debe eliminar si deja de proteger la máquina virtual al seleccionar la opción <b>Delete protection for the virtual machine</b> en la pestaña de máquinas virtuales de las propiedades de la nube. Con esta configuración deshabilitada, cuando deshabilite la protección, la máquina virtual se elimina de Azure Site Recovery, la configuración de Site Recovery para la máquina virtual se elimina de la consola VMM y la réplica se elimina.<br><br>
    ![Configure protection settings][2]

<p>Después de guardar la configuración, se creará un trabajo, que puede supervisar en la pestaña <b>Jobs</b>. Todos los servidores host Hyper-V en la nube VMM de origen se configurarán para la replicación. Puede modificar la configuración de la nube en la pestaña <b>Configure</b>. Si desea modificar la ubicación de destino o la nube de destino, deberá eliminar la configuración de la nube y después volver a configurarla.</p>


<h2><a id="networkmapping"></a>Paso 5: Configurar asignación de red</h2>

<p>En este tutorial se describe la ruta más sencilla para implementar Azure Site Recovery en un entorno de prueba. Para configurar la asignación de red como parte de este tutorial, lea <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Preparar la asignación de red</a> en la Guía de planeación. Para configurar la asignación siga los pasos hasta <a href="http://go.microsoft.com/fwlink/?LinkId=402534">Configurar la asignación de red</a> en la guía de implementación.</p>

<h2><a id="storagemapping"></a>Paso 6: Configurar la asignación de almacenamiento</h2>


En este tutorial se describe la ruta más sencilla para implementar Azure Site Recovery en un entorno de prueba. Para configurar la asignación de almacenamiento como parte de este tutorial, lea <a href="http://go.microsoft.com/fwlink/?LinkId=402535">Configurar la asignación de almacenamiento</a> en la guía de implementación.

<h2><a id="enablevirtual"></a>Paso 7: Habilite la protección de la máquina virtual</h2>

<p>Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube.</p>
<OL>
<li>En la pestaña <b>Virtual Machines</b> en la nube en la que está situada la máquina virtual, haga clic en <b>Enable protection</b> y después seleccione <b>Add virtual machines</b>.</li>
<li>En la lista de máquinas virtuales en la nube, elija la que desee proteger.</li> 
</OL>

![Habilite la protección de la máquina virtual][Habilite la protección de la máquina virtual]

<p>Siga el progreso de la acción de habilitación de la protección en la pestaña <b>Jobs</b>, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.</p>

![Trabajo de protección de máquina virtual][Trabajo de protección de máquina virtual]

<h2><a id="recoveryplans"></a>Paso 8: Prueba de la implementación</h2>

Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan. La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada.

<UL>
<li>Para obtener instrucciones acerca de la creación de un plan de recuperación vea <a href="http://go.microsoft.com/fwlink/?LinkId=511492">Crear y personalizar los planes de recuperación De local a Azure</a>.</li>
<li>Para obtener instrucciones acerca de la realización de conmutaciones por error de prueba vea <a href="http://go.microsoft.com/fwlink/?LinkId=511493">Prueba de una implementación de instalaciones locales a instalaciones locales</a>.</li>
</UL>


<h3><a id="runtest"></a>Supervisar la actividad</h3>
<p>Puede utilizar las pestañas <b>Jobs</b> y <b>Dashboard</b> para ver y supervisar las tareas principales que ha realizado el almacén de Azure Site Recovery, incluida la configuración de la protección de una nube, la habilitación y deshabilitación de la protección de una máquina virtual, la ejecución de una conmutación por error (planeada, no planeada o de prueba) y la confirmación de una conmutación por error no planificada.</p>

<p>En la pestaña <b>Jobs</b> puede ver tareas, profundizar en los detalles y errores de las mismas, ejecutar consultas de las tareas para recuperar aquellas que cumplan ciertos criterios, exportar tareas a Excel y reiniciar tareas que no se habían completado.</p>

<p>En <b>Dashboard</b> puede descargar las versiones más recientes de los archivos de instalación del proveedor y del agente, conseguir información de configuración para el almacén, ver el número de máquinas virtuales que tienen protección administrada por el almacén, ver tareas recientes, administrar el certificado del almacén y volver a sincronizar máquinas virtuales.</p>

<p>Para obtener más información acerca de la interacción con trabajos y el panel, vea la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guía de funcionamiento y supervisión</a>.</p>

<h2><a id="next"></a>Pasos siguientes</h2>
<UL>
<LI>Para planear e implementar Azure Site Recovery en un entorno de producción completo, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Plan de implementación de Azure Site Recovery</a> y la <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Implementar Azure Site Recovery: Protección de instalaciones locales a instalaciones locales</a>.</LI>
<LI>Si tiene preguntas, visite el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</LI> 
</UL>



  [Introducción a Azure Site Recovery: Protección de instalaciones locales a instalaciones locales]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Introducción a Azure Site Recovery: Protección de instalaciones locales a Azure]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Plan de implementación de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Implementar Azure Site Recovery: Protección de instalaciones locales a instalaciones locales]: http://go.microsoft.com/fwlink/?LinkId=321295
  [Azure Site Recovery: Common Error Scenarios and Resolutions]: http://go.microsoft.com/fwlink/?LinkId=389879
  [foro de Servicios de recuperación de Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Evaluación gratuita de Azure]: http://aka.ms/try-azure
  [Detalles de precios del administrador de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Portal de administración]: https://manage.windowsazure.com
  [Detalles de precios de Azure Site Recovery]: http://go.microsoft.com/fwlink/?LinkId=389880
  [Almacén nuevo]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Icono de inicio rápido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Clave de registro]: ./media/hyper-v-recovery-manager-configure-vault/SR_E2ERegisterKey.png
  [Requisitos previos]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png
  [Microsoft Updates]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png
  [Configuración de Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registro de servidor]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png
  [Nube publicada]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png
  [Configure protection settings]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2E.png
  [Paso 3: Configurar la protección para nubes VMM]: http://go.microsoft.com/fwlink/?LinkId=323469
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2ERep.png
  [Preparar la asignación de red]: http://go.microsoft.com/fwlink/?LinkId=324817
  [Configurar la asignación de red]: http://go.microsoft.com/fwlink/?LinkId=402534
  [Configurar la asignación de almacenamiento]: http://go.microsoft.com/fwlink/?LinkId=402535
  [Habilite la protección de la máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Trabajo de protección de máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Crear y personalizar los planes de recuperación De local a Azure]: http://go.microsoft.com/fwlink/?LinkId=511492
  [Prueba de una implementación de instalaciones locales a instalaciones locales]: http://go.microsoft.com/fwlink/?LinkId=511493
  [Guía de funcionamiento y supervisión]: http://go.microsoft.com/fwlink/?LinkId=398534
