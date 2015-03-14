<properties 
	pageTitle="utorial: Configuración de la protección entre un sitio de VMM local y Azure" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales de Hyper-V ubicadas en nubes VMM o en instalaciones locales a Azure." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>


# Introducción a la recuperación de sitios de Azure: Protección local de sitio de VMM a Azure con replicación de Hyper-V



<div class="dev-callout"> 

<p>Azure Site Recovery contribuye a su estrategia de continuidad de negocio y carga de trabajo mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación.<p>

<P>En este tutorial se describe cómo implementar Azure Site Recovery para organizar la protección entre un sitio de VMM local y Azure con la replicación de Hyper-V.  El tutorial usa la ruta de acceso de implementación más rápida y la configuración predeterminada cuando sea posible.</P>

<UL>
<LI>Si desea obtener más información para una implementación lea las guías de <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Planeación</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=402679">Implementación</a>.</LI>
<LI>Encontrará información acerca de escenarios de implementación de Azure Site Recovery en <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Introducción a Azure Site Recovery</a>.</LI>
<LI>Si experimenta problemas durante este tutorial, revise el artículo de wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: Escenarios de error comunes y soluciones</a>, o publique sus preguntas en el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Foro de servicios de recuperación de Azure</a>.</LI>
</UL>
</div>


<h2><a id="before"></a>Requisitos previos</h2> 
<div class="dev-callout"> 
<P>Asegúrese de que tiene todo colocado antes de comenzar el tutorial.</P>

<UL>
<LI><b>Cuenta de Azure</b>: necesitará una cuenta de Azure. Si no tiene una, consulte <a href="http://aka.ms/try-azure">Evaluación gratuita de Azure</a>. Obtenga información acerca de los precios de suscripción en <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalles de precios de Azure Site Recovery Manager</a>.</LI>

<LI><b>Cuenta de almacenamiento de Azure</b>: necesitará una cuenta de almacenamiento de Azure para almacenar los datos replicados en Azure. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción. Para obtener más información sobre la configuración del almacenamiento de Azure, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introducción al almacenamiento de Microsoft Azure</a>.</LI><LI><b>Servidor VMM</b>: un servidor VMM que se ejecute en System Center 2012 R2.</LI>
<LI><b>Nubes VMM</b>: una nube al menos en el servidor VMM que contenga:
	<UL>
	<LI>Uno o más grupos de hosts de VMM</LI>
	<LI>Uno o más servidores host de Hyper-V o clústeres en cada grupo de hosts</LI>
	<li>Una o varias máquinas virtuales ubicadas en el servidor de Hyper-V de origen en la nube. Las máquinas virtuales deben ser de la generación 1.</li>
		</UL></LI>	
<LI><b>Máquina virtual</b>: necesitará máquinas virtuales que cumplan con los requisitos de Azure. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Requisitos previos y compatibilidad</a> en la Guía de planeación.</LI>
<LI>Para obtener una lista completa de los requisitos de máquinas virtuales para conmutación por error a Azure, lea  </LI>
</UL>


<h2><a id="tutorial"></a>Pasos del tutorial</h2> 

Después de comprobar los requisitos previos, realice las siguientes tareas:
<UL>

<LI><a href="#vault">Paso 1: Crear una almacén</a>: Crear un almacén para Azure Site Recovery.</LI>
<LI><a href="#download">Paso 2: Instalar la aplicación de proveedor en el servidor VMM</a>: permite generar una clave de registro en el almacén y descargar el archivo de instalación del proveedor. Ejecute el programa de instalación en el servidor VMM para instalar el proveedor y registrar el servidor de VMM en el almacén.</LI>
<LI><a href="#storage">Paso 3: Agregar una cuenta de almacenamiento de Azure</a>: si no tiene una cuenta, cree una. </LI>
<LI><a href="#agent">Paso 4: Instalar la aplicación del agente</a>: instale el agente de servicios de recuperación de Microsoft Azure en cada host de Hyper-V ubicado en nubes de VMM que desea proteger.</LI>
<LI><a href="#clouds">Paso 5: Configurar la protección de la nube</a>: Configure los parámetros de protección para las nubes VMM.</LI>
<LI><a href="#NetworkMapping">Paso 6: Configurar la asignación de red</a>: opcionalmente puede configurar la asignación de red para asignar redes VM de origen a redes Azure de destino.</LI>
<LI><a href="#virtualmachines">Paso 7: Habilitar protección para las máquinas virtuales</a>: habilite la protección para las máquinas virtuales ubicadas en las nubes VMM.</LI>
<LI><a href="#test">Paso 8: Probar la implementación</a>: puede ejecutar una conmutación por error de prueba para una sola máquina virtual o puede crear un plan de recuperación y ejecutar una conmutación por error de prueba para el plan para asegurarse de que está funcionando.</LI>
</UL>



<a name="vault"></a> <h2>Paso 1: Creación de un almacén</h2>

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).


2. Expanda <b>Servicios de datos</b>, luego <b>Servicios de recuperación</b> y haga clic en <b>Almacén de Site Recovery</b>.

3. Haga clic en <b>Crear nuevo</b> y luego en <b>Creación rápida</b>.
	

4. En <b>Name</b>, escriba un nombre descriptivo para identificar el almacén.

5. En <b>Región</b>, seleccione la región geográfica del almacén. Las regiones geográficas disponibles incluyen Asia Oriental, Europa Occidental, Oeste de los EE. UU., Este de EE. UU., Europa del Norte y Sudeste asiático.
6. Haga clic en <b>Crear almacén</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como <b>Activo</b> en la página principal de Servicios de recuperación.</P>





 <a name="download"></a> <h2>Paso 2: Generación de una clave de registro e instalación del proveedor de Azure Site Recovery</h2>
 

1. En la página <b>Servicios de recuperación</b>, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. En la lista desplegable seleccione **Entre un sitio Hyper-V local y Microsoft Azure**.
3. En **Preparar servidores VMM**, haga clic en el archivo **Generar clave de registro**. La clave será válida para 5 días a partir del momento en que se genera. Copie el archivo al servidor VMM. Lo necesitará al configurar el proveedor.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SR_E2ARegisterKey.png)

4. En la página de <b>Inicio rápido</b>, en **Preparar servidores VMM**, haga clic en <b>Descargar el proveedor de Microsoft Azure Site Recovery para la instalación en servidores VMM</b> a fin de obtener la versión más reciente del archivo de instalación del proveedor.

2. Ejecute este archivo en el servidor VMM de origen.


3. En la **Comprobación de requisitos previos** seleccione detener el servicio VMM para iniciar la configuración del proveedor. Este servicio se detendrá y se volverá a iniciar automáticamente cuando finalice la configuración.

	![Prerequisites](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderPrereq.png)

4. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta configuración habilitada, las actualizaciones del proveedor se instalarán según su directiva de Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderUpdate.png)

Una vez instalado el proveedor, siga con la instalación para registrar el servidor en el almacén.

5. En **Conexión a Internet**, especifique cómo se conecta a Internet el proveedor que se ejecuta en el servidor VMM. Seleccione <b>Utilizar la configuración proxy del sistema predeterminado</b> para usar la configuración predeterminada de conexión a Internet establecida en el servidor.

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png)

6. En **Clave de registro**, seleccione la que ha descargado de Azure Site Recovery y copiado en el servidor VMM.
7. En **Nombre del almacén**, compruebe el nombre del almacén en el que se registrará el servidor.
8. En **Nombre del servidor**, especifique un nombre descriptivo para identificar el servidor VMM en el almacén.

	
	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderRegKeyServerName.png)

8. En la sincronización de **Metadatos de la nube inicial** seleccione si desea sincronizar los metadatos de todas las nubes en el servidor de VMM con el almacén. Esta acción solo se debe ejecutar una vez en cada servidor. Si no desea sincronizar todas las nubes, puede dejar este parámetro sin marcar y sincronizar cada nube individualmente en las propiedades de la nube de la consola de VMM.


9. En **Cifrado de datos**, especifique una ubicación para guardar un certificado SSL que se genera automáticamente para el cifrado de datos. Este certificado se usa si habilita el cifrado de datos para una nube protegida por Azure en el portal de Azure Site Recovery. Mantenga el certificado en un lugar seguro. Cuando ejecute una conmutación por error en Azure, la seleccionará para descifrar los datos cifrados. 
Esta opción no es relevante si está replicando desde un sitio local a otro.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SR_ProviderSyncEncrypt.png)

8. Haga clic en <b>Register</b> para finalizar el proceso. Después del registro, la Recuperación del sitio de Azure recupera los metadatos del servidor VMM. El servidor se mostrará en el extremo de la pestaña <b>Recursos</b> en la página **Servidores** del almacén.

<h2><a id="storage"></a>Paso 3: Crear una cuenta de almacenamiento de Azure</h2>
Si no dispone de una cuenta de almacenamiento de Azure, haga clic en **Agregar una cuenta de almacenamiento de Azure**. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el servicio Azure Site Recovery y estar asociada a la misma suscripción.

<P>Utilice este tutorial para configurar una prueba de concepto rápida para Azure Site Recovery en una implementación de local a Azure. Se utiliza la ruta más rápida y la configuración predeterminada siempre que es posible. Creará un almacén de Azure Site Recovery, instalará el proveedor de Azure Site Recovery en el servidor VMM, instalará el agente de los Servicios de recuperación de Azure en servidores host de Hyper-V situados en nubes VMM, configurará los parámetros de protección de la nube, habilitará la protección de máquinas virtuales y comprobará su implementación.</P>

![Storage account](./media/hyper-v-recovery-manager-configure-vault/SR_E2AStorageAgent.png)

<h2><a id="agent"></a>Paso 4: Instalación del agente de los Servicios de recuperación de Azure en servidores host de Hyper-V</h2>

Instale el agente de los Servicios de recuperación de Azure en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.

1. En la página de inicio rápido, haga clic en <b>Descargar el agente de los servicios de Azure Site Recovery e instalar en los hosts</b> para obtener la última versión del archivo de instalación del agente.

	![Install Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_E2AInstallHyperVAgent.png)

2. Ejecute el archivo de instalación en cada servidor host de Hyper-V situado en las nubes VMM que desea proteger.
3. En la página **Comprobación de requisitos previos**, haga clic en <b>Siguiente</b>. Todos los requisitos previos que falten se instalarán automáticamente.

	![Prerequisites Recovery Services Agent](./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png)

4. En la página **Configuración de la instalación**, especifique dónde desea instalar el agente y seleccione la ubicación de la caché en la que se instalarán los metadatos de copia de seguridad. Luego haga clic en <b>Instalar</b>.


<h2><a id="clouds"></a>Paso 5: Configurar la protección de la nube</h2>

Una vez que los servidores VMM están registrados, puede configurar la protección de la nube. La opción **Sincronizar datos de nube con el almacén** se habilita cuando se instala el proveedor, de modo que todas las nubes del servidor VMM aparecerán en la pestaña <b>Elementos protegidos</b> del almacén.

![Published Cloud](./media/hyper-v-recovery-manager-configure-vault/SR_CloudsList.png)


1. En la página de inicio rápido, haga clic en **Configurar protección para nubes VMM**.
2. En la pestaña **Elementos protegidos**, haga clic en la nube que desea configurar y vaya a la pestaña **Configuración**.
3. En <b>Destino</b>, seleccione <b>Microsoft Azure</b>.
4. En <b>Cuenta de almacenamiento</b>, seleccione la cuenta de Azure que desea usar para almacenar las máquinas virtuales de Azure.
5. Establezca <b>Cifrar datos almacenados</b> en <b>Desactivado</b>. Este valor especifica que los datos de deben cifrar replicados entre el sitio local y Azure.
6. En <b>Copiar frecuencia</b>, deje la configuración predeterminada. Este valor especifica la frecuencia con que se deben sincronizar los datos entre las ubicaciones de origen y de destino.
7. En <b>Retener puntos de recuperación para</b>, deje la configuración predeterminada. Con un valor predeterminado de cero, el punto de recuperación más reciente para una máquina virtual es el único que se almacena en un servidor host de réplica.
8. En <b>Frecuencia de las instantáneas coherentes con la aplicación</b>, deje la configuración predeterminada. Este valor especifica la frecuencia de creación de instantáneas. Las instantáneas utilizan el Servicio de instantáneas de volumen (VSS) para asegurarse de que las aplicaciones se encuentren en un estado coherente cuando se captura la instantánea.  Si establece un valor, asegúrese de que sea inferior al número de puntos de recuperación adicionales que configure.
9. En <b>Hora de inicio de la replicación</b>, especifique cuándo debe comenzar la replicación de los datos en Azure. Se usará la zona horaria del servidor host de Hyper-V. Se recomienda que programe la replicación inicial durante las horas de menos actividad. 

	![Cloud replication settings](./media/hyper-v-recovery-manager-configure-vault/SR_CloudSettingsE2A.png)

Tras guardar la configuración, se creará un trabajo que se podrá supervisar en la pestaña <b>Trabajos</b>. Todos los servidores host de Hyper-V de la nube de origen VMM se configurarán para la replicación.

Tras guardar, puede modificar la configuración de la nube en la pestaña <b>Configurar</b>. Para modificar la ubicación de destino o el almacenamiento de destino, deberá eliminar la configuración de la nube y luego volver a configurar la nube. Tenga en cuenta que si cambia la cuenta de almacenamiento, el cambio solo se aplicará a las máquinas virtuales que tengan la protección habilitada después de que la cuenta de almacenamiento se ha modificado. Las máquinas virtuales existentes no se migrarán a la nueva cuenta de almacenamiento.</p>

<h2><a id="networkmapping"></a>Paso 6: Configurar asignación de red</h2>

<p>En este tutorial se describe la ruta más sencilla para implementar Azure Site Recovery en un entorno de prueba. Para configurar la asignación de red como parte de este tutorial, lea <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Preparar la asignación de red</a> en la Guía de planeación. Para configurar la asignación siga los pasos hasta <a href="http://go.microsoft.com/fwlink/?LinkId=402533">Configurar la asignación de red</a> en la guía de implementación.</p>




<h2><a id="virtualmachines"></a>Paso 7: Habilitar protección para las máquinas virtuales</h2>

Una vez que los servidores, las nubes y las redes se configuran correctamente, puede habilitar la protección para las máquinas virtuales en la nube. Tenga en cuenta lo siguiente:

- Las máquinas virtuales de deben cumplir los requisitos de Azure. Consúltelos en <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Requisitos previos y compatibilidad</a> en la Guía de planeación.
- Para habilitar la protección del sistema operativo y el disco del sistema operativo, deben establecerse las propiedades de la máquina virtual. Al crear una máquina virtual en VMM con una plantilla de máquina virtual puede establecer la propiedad. También puede establecer estas propiedades para máquinas virtuales existentes en las pestañas**General** y **Configuración del hardware** de las propiedades de la máquina virtual. Si no ve estas propiedades en VMM, podrá configurarlas en el portal de Azure Site Recovery.

![Create virtual machine](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ANew.png)

![Modify virtual machine properties](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2AExisting.png)


1. Para habilitar la protección, en la pestaña <b>Máquinas virtuales</b> de la nube en la que se encuentra la máquina virtual, haga clic en <b>Habilitar protección</b> y luego seleccione <b>Agregar máquinas virtuales</b>
2. En la lista de máquinas virtuales de la nube, seleccione la que desea proteger. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVMME2ASelectVM.png)

3. Compruebe las propiedades de la máquina virtual y modifíquelas según sea necesario.

	![Verify virtual machines](./media/hyper-v-recovery-manager-configure-vault/SR_EnableVME2AProps.png)

Siga el progreso de la acción de habilitación de la protección en la pestaña **Trabajos**, incluida la replicación inicial. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Después de habilitar la protección y replicar las máquinas virtuales, podrá verlas en Azure.


![Virtual machine protection job](./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png)


<h2><a id="test"></a>Paso 8: Prueba de la implementación</h2>
Para probar la implementación puede realizar una prueba de conmutación por error para una máquina virtual individual o crear un plan de recuperación que incluya numerosas máquinas virtuales y realizar una conmutación por error de prueba para el plan.  La conmutación por error de prueba simula su mecanismo de conmutación por error y recuperación en una red aislada. Tenga en cuenta lo siguiente:
<UL>
<li>Si después de la conmutación por error desea conectarse a la máquina virtual de Azure mediante Escritorio remoto, habilite Conexión a Escritorio remoto en la máquina virtual antes de ejecutar la prueba.</li>
<li>Después de la conmutación por error, usará una dirección IP pública para conectarse a la máquina virtual de Azure mediante Escritorio remoto. Si desea realizar esto, asegúrese de no tener ninguna directiva de dominio que impida que se conecte a una máquina virtual mediante una dirección pública.</li>
</UL>

1. En la pestaña **Planes de recuperación**, agregue un plan nuevo. Especifique un nombre, **VMM** en **Tipo de origen** y el servidor VMM de origen en **Origen**. El destino será Azure.

	![Create recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRAzure_RP1.png)

2. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. Tenga en cuenta que una conmutación por error de prueba con esta configuración comprobará que la máquina virtual se replicó correctamente en Azure, pero no comprobará la configuración de red de replicación. Si desea ejecutar la prueba con una red de Azure especificada, vea href="http://go.microsoft.com/fwlink/?LinkId=522292">Probar una instalación local en Azure</a>.

	![No network](./media/hyper-v-recovery-manager-configure-vault/SRAzure_TestFailoverNoNetwork.png)


3. Cuando la conmutación por error alcance la fase **Completar pruebas**, haga clic en **Completar prueba** para terminar la conmutación por error de prueba. Puede profundizar hasta la pestaña **Trabajo** para realizar un seguimiento de la conmutación por error del progreso y el estado, y llevar a cabo las acciones necesarias.
4. Cuando se complete la conmutación por error, haga lo siguiente:
	- Compruebe que las máquinas virtuales se inician correctamente.
	- Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.
	- Haga clic en **La conmutación por error de prueba se ha completado**. Limpie el entorno de prueba para apagar automáticamente la máquina virtual de prueba y eliminar la red de Azure de prueba.
5. Después de la conmutación por error podrá ver la réplica de prueba de la máquina virtual en el portal de Azure. Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.


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
