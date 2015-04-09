<properties 
	pageTitle="Tutorial: Configuración de la protección entre un sitio de Hyper-V local y Azure" 
	description="Azure Site Recovery coordina la replicación, la conmutación por error y la recuperación de máquinas virtuales entre un sitio de Hyper-V local y Azure." 
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

# Tutorial: Configuración de la protección entre un sitio de Hyper-V local y Azure


<h2><a id="overview" name="overview" href="#overview"></a>Información general</h2>
<p>Azure Site Recovery contribuye a su estrategia de continuidad de negocio y carga de trabajo mediante la organización de la replicación, la conmutación por error y la recuperación de máquinas virtuales en varios escenarios de implementación.<p>

En este tutorial se describe cómo implementar Azure Site Recovery para proteger las cargas de trabajo que se ejecutan en un Hyper-V Server con Windows Server 2012 R2 en un sitio local. Las máquinas virtuales de Hyper-V Server se replican en Azure mediante la replicación de Hyper-V. Esta implementación es especialmente útil si tiene servidores Hyper-V en su oficina o sucursal pero VMM de System Center no está implementado.


<LI>En este tutorial se usa la ruta de acceso de implementación más rápida con la configuración mínima y predeterminada.
Puede leer las instrucciones de implementación completa en las Guías de <a href="http://go.microsoft.com/fwlink/?LinkId=522087">planeación</a> e <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">implementación</a>.</LI>
<LI>Encontrará información acerca de escenarios de implementación de Azure Site Recovery en <a href="http://go.microsoft.com/fwlink/?LinkId=518690">Introducción a Azure Site Recovery</a>.</LI>
<LI>Si experimenta problemas durante este tutorial, revise el artículo de wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Azure Site Recovery: escenarios de error comunes y soluciones</a>, o publique sus preguntas en el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</LI>
</UL>

</div>

<h2><a id="before" name="before" href="#before"></a>Requisitos previos</h2>
<div class="dev-callout"> 
<P>Asegúrese de que tiene todo colocado antes de comenzar.</P>

<UL>
<LI><b>Cuenta de Azure</b>: necesitará una cuenta de Azure. Si no dispone de una, consulte <a href="http://aka.ms/try-azure">Evaluación gratuita de Azure</a>. Puede obtener información sobre precios en la página <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalles de precios de Azure Site Recovery Manager</a>.</LI>
<LI><b>Hyper-V</b>: en el sitio local de origen necesitará al menos un servidor que ejecute Windows Server 2012 R2 con el rol de Hyper-V Hyper-V Server debe contener una o más máquinas virtuales. Los servidores Hyper-V deben estar conectados a Internet, directamente o a través de un servidor proxy.</LI>
<LI><b>Máquinas virtuales</b>: las máquinas virtuales que quiere proteger deben cumplir los requisitos previos de Azure para máquinas virtuales. Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=522287">Compatibilidad de máquina virtual</a>.</LI>

</UL>
<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Pasos del tutorial</h2>
Después de comprobar los requisitos previos, realice las siguientes tareas:
<UL>
<LI><a href="#vault">Paso 1: Creación de un almacén</a>: cree un almacén de Azure Site Recovery.</LI>
<LI><a href="#site">Paso 2: Crear un sitio de Hyper-V</a>: cree un sitio de Hyper-V como contenedor lógico para todos los servidores Hyper-V que contienen máquinas virtuales que quiere proteger.</LI>
<LI><a href="#download">Paso 3: Preparar servidores Hyper-V</a>: genere una clave de registro y descargue el archivo de instalación del proveedor. Ejecute el archivo en cada Hyper-V Server del sitio y seleccione la clave para registrar el servidor en el almacén.</LI>
<LI><a href="#resources">Paso 4: Preparar recursos</a>: cree una cuenta de almacenamiento de Azure para almacenar máquinas virtuales replicadas.</LI>
<LI><a href="#protectiongroup">Paso 5: Crear y configurar grupos de protección</a>: cree un grupo de protección y aplíquele la configuración de protección. La configuración de protección se aplicará a todas las máquinas virtuales que agregue al grupo.</LI>
<LI><a href="#enablevirtual">Paso 6: Habilitar protección para las máquinas virtuales</a>: habilite la protección para máquinas virtuales agregándolas a un grupo de protección.</LI>
<LI><a href="#recovery plans">Paso 7: Prueba de la implementación</a>: ejecute una conmutación por error de prueba para una máquina virtual.</LI>

</UL>




<a name="vault"></a> <h3>Paso 1: Creación de un almacén</h3>

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com).


2. Expanda <b>Servicios de datos</b>, luego <b>Servicios de recuperación</b> y haga clic en <b>Almacén de Site Recovery</b>.


3. Haga clic en <b>Crear nuevo</b> y luego en <b>Creación rápida</b>.
	
4. En <b>Name</b>, escriba un nombre descriptivo para identificar el almacén.

5. En <b>Región</b>, seleccione la región geográfica del almacén. Para comprobar las regiones admitidas, consulte Disponibilidad geográfica en <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Detalles de precios de Azure Site Recovery</a>.

6. Haga clic en <b>Crear almacén</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png)

<P>Compruebe la barra de estado para confirmar que el almacén se ha creado correctamente. El almacén aparecerá como <b>Activo</b> en la página principal de Servicios de recuperación.</P>

<a name="site"></a> <h3>Paso 2: Crear un sitio de Hyper-V</h3>

1. En la página Servicios de recuperación, haga clic en el almacén para abrir la página de inicio rápido. El inicio rápido también se puede abrir en cualquier momento mediante el icono.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png)

2. En la lista desplegable, seleccione **Entre un sitio de Hyper-V local y Azure**.

	![Hyper-V site scenario](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectScenario.png)

3. En **Crear un sitio de Hyper-V** haga clic en **Crear sitio de Hyper-V**. Especifique un nombre de sitio y guarde.

	![Hyper-V site](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateSite2.png)

<a name="download"></a> <h3>Paso 3: Preparar servidores Hyper-V</h3>
	

1. En **Preparar servidores Hyper-V**, haga clic en **Descargar una clave de registro**.
2. En la página **Descargar clave de registro**, haga clic en **Descargar** junto al sitio. Descargue la clave en una ubicación segura a la que Hyper-V pueda acceder con facilidad. La clave será válida para 5 días a partir del momento en que se genera.

	![Registration key](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_DownloadKey2.png)

4. Haga clic en <b>Descargar proveedor</b> para obtener la versión más reciente.
5. Ejecute el archivo en cada servidor de Hyper-V que desee registrar en el almacén. El archivo instala dos componentes:
	- **Proveedor de Azure Site Recovery**: controla la comunicación y la coordinación entre Hyper-V Server y el portal de Azure Site Recovery. 
	- **Agente de los Servicios de recuperación de Azure**: controla el transporte de datos entre las máquinas virtuales que se ejecutan en el servidor Hyper-V de origen y el almacenamiento de Azure. 
6. En **Microsoft Update** puede optar por recibir actualizaciones. Con esta opción habilitada, las actualizaciones del proveedor y del agente se instalarán según la directiva de Microsoft Update.

	![Microsoft Updates](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider1.png)

7. En **Instalación** especifique dónde desea instalar el proveedor y el agente en el servidor de Hyper-V.

	![Install location](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider2.png)

8. Una vez se complete la instalación, continúe con la instalación para registrar el servidor en el almacén.

	![Installation complete](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider3.png)


9. En la página **Conexión a Internet** especifique cómo se conecta el proveedor a Azure Site Recovery. Seleccione <b>Utilizar la configuración proxy del sistema predeterminado</b> para usar la configuración predeterminada de conexión a Internet establecida en el servidor. 

	![Internet Settings](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider4.png)

9. En la página **Configuración de almacén**, haga clic en **Examinar** para seleccionar el archivo de clave. Especifique la suscripción de Azure Site Recovery, el nombre del almacén y el sitio de Hyper-V al que pertenece el servidor Hyper-V.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_SelectKey.png)


11. El registro empieza a registrar el servidor en el almacén.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider6.png)

11. Después de que finalice el registro, los metadatos de Hyper-V Server se recuperan mediante Azure Site Recovery y el servidor se muestra en la pestaña **Sitios de Hyper-V** de la página **Servidores** del almacén.

	![Server registration](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_Provider7.png)


<h3><a id="resources"></a>Paso 4: Preparar recursos</h3>


1. En la página de inicio rápido, en **Preparar recursos** seleccione **Crear cuenta de almacenamiento** para crear una cuenta de almacenamiento de Azure si no tiene una. La cuenta debe tener habilitada la replicación geográfica. Además, debe estar en la misma región que el almacén de Azure Site Recovery y estar asociada a la misma suscripción.

	![Create storage account](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_CreateResources1.png)



<h3><a id="protectiongroup"></a>Paso 5: Crear y configurar grupos de protección</h3>

<p>Los grupos de protección agrupan máquinas virtuales que tienen la misma configuración de protección. Aplique la configuración de protección a un grupo de protección y esa configuración se aplicará a todas las máquinas virtuales que agregue al grupo.</p>
1. En **Crear y configurar grupos de protección** haga clic en **Crear un grupo de protección**. Si no están establecidos los requisitos previos, se emitirá un mensaje y podrá hacer clic en **Ver detalles** para obtener más información.

2. En la pestaña **Grupos de protección**, agregue un grupo de protección. Especifique un nombre, el sitio de Hyper-V de origen, el **Azure** de destino, el nombre de la suscripción de Azure Site Recovery y la cuenta de almacenamiento de Azure.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroupCreate.png)


2. En **Configuración de replicación**, deje la predeterminada.

	![Protection group](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_ProtectionGroup2.png)


<h3><a id="enablevirtual"></a>Paso 6: Enable virtual machine protection</h3>
<p>Habilite la protección para máquinas virtuales agregándolas a un grupo de protección.</p>

1. En la pestaña <b>Máquinas</b> del grupo de protección, haga clic en <b>Agregar máquinas virtuales a grupos de protección para habilitar protección</b>.
2. En la página **Habilitar protección de máquina virtual** seleccione las máquinas virtuales que desea proteger. 

	![Enable virtual machine protection](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_AddVM1.png)

Comienzan los trabajos de Habilitar protección. Puede realizar un seguimiento del progreso en la pestaña **Trabajos**. La máquina virtual estará preparada para la conmutación por error después de que finalice el trabajo de Finalizar protección. Cuando se habilite la protección y se complete la replicación inicial, podrá ver máquinas virtuales en Azure.

En Azure Site Recovery puede ver máquinas virtuales protegidas en **Elementos protegidos** > **Grupos de protección** >  *protectiongroup_name* > **Máquinas virtuales**. 


<h3><a id="recoveryplans"></a>Paso 7: Prueba de la implementación</h3>

Pruebe su implementación para simular su conmutación por error y mecanismo de recuperación en una red aislada sin afectar a su entorno de producción. Para ello ejecutará una conmutación por error de prueba para una máquina virtual protegida.

1. **Elementos protegidos** > **Grupos de protección** >  *protectiongroup_name* > **Máquinas virtuales** seleccione la máquina virtual que desee conmutar por error y haga clic en **Probar conmutación por error**.
2. En la página **Confirmar conmutación por error de prueba** seleccione **Ninguno**. 

	![Recovery plan](./media/hyper-v-recovery-manager-configure-vault/SRHVSite_TestFailoverNoNetwork.png)

3. Puede realizar el seguimiento del estado y del progreso de conmutación por error en la pestaña **Trabajo**.
4. Cuando la conmutación por error alcance la fase **Completar pruebas**, complete la comprobación de la siguiente manera:
	- Después de la conmutación por error vea la máquina virtual de réplica en el portal de Azure. Compruebe que la máquina virtual se inicia correctamente.
	- Si está configurando para acceder a máquinas virtuales desde la red local puede iniciar una conexión de Escritorio remoto a la máquina virtual.
	- Haga clic en **Completar la prueba** para terminarla.
	- Haga clic en **Notas** para registrar y guardar las observaciones asociadas a la conmutación por error de prueba.
	- Haga clic en **La conmutación por error de prueba se ha completado**. Limpie el entorno de prueba para apagar y eliminar automáticamente la máquina virtual de prueba.

<h2><a id="runtest" name="runtest" href="#runtest"></a>Supervisión de la actividad</h2>
<p>Puede usar la pestaña <b>Trabajos</b> y el <b>Panel</b> para ver y supervisar los principales trabajos que se realizan en el almacén de Azure Site Recovery, como la configuración de la protección para una nube, la habilitación y deshabilitación de la protección para una máquina virtual, la ejecución de una conmutación por error (planificada, no planificada o de prueba) y la realización de una conmutación por error no planificada.</p>

<p>En la pestaña <b>Trabajos</b>, puede ver los trabajos, profundizar en los detalles y los errores del trabajo, ejecutar consultas del trabajos para recuperar aquellos que coincidan con criterios específicos, exportar trabajos a Excel y reiniciar los trabajos con errores.</p>

<p>En el <b>Panel</b>, puede descargar las versiones más recientes de los archivos de instalación del proveedor y el agente, obtener información de configuración del almacén, ver el número de máquinas virtuales cuya protección se gestiona en el almacén, ver los trabajos recientes, administrar el certificado del almacén y volver a sincronizar las máquinas virtuales.</p>

<p>Para obtener más información sobre la interacción con los trabajos y el panel, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=398534">Guía de operaciones y supervisión</a>.</p>

<h2><a id="next" name="next" href="#next"></a>Pasos siguientes</h2>
<UL>
<LI>Para planear e implementar Azure Site Recovery en un entorno completo de producción, consulte la <a href="http://go.microsoft.com/fwlink/?LinkId=522087">Guía de planeación de Azure Site Recovery</a> y la <a href=" http://go.microsoft.com/fwlink/?LinkId=522088">Guía de implementación de Azure Site Recovery</a>.</LI>
<LI>Si tiene preguntas, visite el <a href="http://go.microsoft.com/fwlink/?LinkId=313628">foro de Servicios de recuperación de Azure</a>.</LI> 
</UL>

<!--HONumber=49-->