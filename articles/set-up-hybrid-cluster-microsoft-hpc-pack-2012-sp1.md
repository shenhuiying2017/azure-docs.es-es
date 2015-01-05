<properties pageTitle="Configurar un clúster de proceso híbrido con Microsoft HPC Pack" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep" />


#Configurar un clúster de proceso híbrido con Microsoft HPC Pack
Este tutorial le indica cómo usar Microsoft HPC Pack 2012 R2 y Azure para configurar un pequeño clúster híbrido de informática de alto rendimiento (HPC). El clúster constará de un nodo principal local (un equipo con el sistema operativo Windows Server y HPC Pack) y otros nodos de ejecución que implemente localmente como instancias de rol de trabajo en un servicio en la nube de Azure. A continuación, podrá ejecutar trabajos informáticos en el clúster híbrido.
 
![Hybrid HPC cluster][Overview] 

Este tutorial muestra un enfoque, en ocasiones denominado clúster "ráfaga en la nube", para usar recursos informáticos escalables y bajo demanda en Azure a fin de ejecutar aplicaciones informáticas que consumen numerosos recursos.

En este tutorial se supone que no cuenta con experiencia previa con los clústeres informáticos o con HPC Pack. Únicamente está destinado a ayudarle a implementar un clúster de proceso híbrido rápidamente con fines de demostración. Para obtener más información, así como pasos para implementar un clúster híbrido HPC Pack a mayor escala en un entorno de producción, consulte la [información detallada](http://go.microsoft.com/fwlink/p/?LinkID=200493). Si desea configurar un clúster de HPC Pack completo en Azure, consulte [Microsoft HPC Pack en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?linkid=330375). 

>[WACOM.NOTE] Azure ofrece una [serie de tamaños](http://go.microsoft.com/fwlink/p/?LinkId=389844) para los recursos informáticos, adecuados para las diferentes cargas de trabajo. Por ejemplo, las instancias A8 y A9 combinan alto rendimiento y acceso a baja latencia, aunque se necesita una red de aplicaciones de alto rendimiento para determinadas aplicaciones de HPC. Para obtener más información, consulte [Acerca de las instancias informáticas intensivas A8 y A9](http://go.microsoft.com/fwlink/p/?Linkid=328042). 

Este tutorial le guiará a través de estos pasos básicos:

* [Requisitos previos](#BKMK_Prereq)
* [Instalación de HPC Pack en el nodo principal](#BKMK_DeployHN)
* [Preparación de la suscripción de Azure](#BKMK_Prpare)
* [Configuración del nodo principal](#BKMK_ConfigHN)
* [Incorporación de nodos de Azure al clúster](#BKMK_worker)
* [Inicio de los nodos de Azure](#BKMK_start)
* [Ejecución de un comando en el clúster](#BKMK_RunCommand)
* [Ejecución de un trabajo de prueba](#BKMK_RunJob)
* [Detención de los nodos de Azure](#BKMK_stop)

<h2 id="BKMK_Prereq">Requisitos previos</h2>

>[WACOM.NOTE]para completar este tutorial, deberá tener una cuenta de Azure. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure](http://www.windowsazure.com/en-us/develop/php/tutorials/create-a-windows-azure-account/).

Además, necesitará satisfacer los siguientes requisitos previos para este tutorial:

* Un equipo local que ejecute una edición de Windows Server 2012 R2 o Windows Server 2012. Este equipo será el nodo principal del clúster de HPC. Si todavía no tiene Windows Server, puede descargar e instalar una [versión de evaluación](http://technet.microsoft.com/evalcenter/dn205286.aspx).

	* El equipo debe estar unido a un dominio de Active Directory.

	* Asegúrese de que no haya otros roles de servidor o servicios de rol adicionales instalados.

	* Para la compatibilidad con HPC Pack, el sistema operativo debe estar instalado en uno de estos idiomas: inglés, japonés o chino (simplificado).

	* Compruebe que estén instaladas las actualizaciones importantes y esenciales.
	
* Archivos de instalación para HPC Pack 2012 R2, disponibles de forma gratuita. La versión más reciente es HPC Pack 2012 R2 actualización 1. [Descargue](http://go.microsoft.com/fwlink/p/?linkid=328024) el paquete de instalación completo y copie los archivos en el equipo del nodo principal o en una ubicación de red. Elija los archivos de instalación que tengan el mismo idioma que su instalación de Windows Server.

* Una cuenta de dominio con permisos de administrador local en el nodo principal.

* Conectividad TCP en el puerto 443 desde el nodo principal a Azure.

<h2 id="BKMK_DeployHN">Instalación de HPC Pack en el nodo principal</h2>

En primer lugar, debe instalar Microsoft HPC Pack en un equipo local con un Windows Server que será el nodo principal del clúster.

1. Inicie sesión en el nodo principal usando una cuenta de dominio que tenga permisos de administrador local.

2. Inicie el asistente de instalación de HPC Pack ejecutando el archivo Setup.exe desde los archivos de instalación de HPC Pack.

3. En la pantalla de **instalación de HPC Pack 2012 R12**, haga clic en **Nueva instalación o agregar características a una instalación existente**.

	![HPC Pack 2012 Setup][install_hpc1]

4. En la página **Contrato de usuario de software de Microsoft**, haga clic en **Siguiente**.

5. En la página **Seleccionar tipo de instalación**, haga clic en **Crear un clúster de HPC creando un nodo principal** y, a continuación, haga clic en **Siguiente**.

	![Select Installation Type][install_hpc2]

6. El asistente ejecuta varias pruebas de preinstalación. Haga clic en **Siguiente** en la página **Reglas de instalación** si se pasan todas las pruebas. Si no, revise la información proporcionada y realice los cambios necesarios en su entorno. A continuación, ejecute las pruebas de nuevo o, si fuera necesario, vuelva a iniciar el asistente de instalación. 

	![Installation Rules][install_hpc3]

7. En la página **Configuración de la base de datos de HPC**, asegúrese de haber seleccionado **Nodo principal** en todas las bases de datos de HPC y, a continuación, haga clic en **Siguiente**.

	![DB Configuration][install_hpc4]

8. Acepte las opciones seleccionadas de forma predeterminada en las páginas restantes del asistente. En la página **Instalar componentes requeridos**, haga clic en **Instalar**.

	![Install][install_hpc6]

9. Cuando finalice la instalación, desactive **Iniciar Administrador de clústeres de HPC** and y, a continuación, haga clic en **Finalizar**. (Más adelante iniciará el administrador de clústeres de HPC para completar la configuración del nodo principal).

	![Finish][install_hpc7]

<h2 id="BKMK_Prepare">Preparación de la suscripción de Azure</h2>
Use el [Portal de administración](https://manage.windowsazure.com) para realizar los siguientes pasos con su suscripción de Azure. Estos pasos son necesarios para poder implementar más adelante los nodos de Azure desde el nodo principal local: 

- Cargue un certificado de administración (necesario para las conexiones seguras entre el nodo principal y los servicios de Azure).
 
- Cree un servicio en la nube de Azure en el que se ejecutarán los nodos de Azure (instancias de rol de trabajo).

- Creación de una cuenta de almacenamiento de Azure
	
	>[WACOM.NOTE]Anote también el Id. de la suscripción de Azure, ya que lo necesitará más adelante. Podrá encontrarlo en la información de su <a href="[https://account.windowsazure.com/Subscriptions">cuenta de Azure</a>.

<h3>Cargar el certificado de administración predeterminado</h3>
HPC Pack instala un certificado autofirmado en el nodo principal, llamado Default Microsoft HPC Azure Management, que podrá cargar como certificado de administración de Azure. Este certificado se proporciona con fines de prueba e implementaciones de prueba de concepto.

1. En el equipo del nodo principal, inicie sesión en el [Portal de administración](https://manage.windowsazure.com).

2. Haga clic en **Configuración** y, a continuación, en **Certificados de administración**.

3. En la barra de comandos, haga clic en **Cargar**.

	![Certificate Settings][upload_cert1]

4. Busque el archivo C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer en el nodo principal. A continuación, haga clic en el botón **Check**.

	![Upload Certificate][install_hpc10]

Aparecerá **Default HPC Azure Management** en la lista de certificados de administración.

<h3>Crear un servicio en la nube de Azure</h3> 

>[WACOM.NOTE]Para conseguir un mejor rendimiento, cree el servicio en la nube y la cuenta de almacenamiento en la misma región geográfica.

1. En la barra de comandos del Portal de administración, haga clic en **Nuevo**.

2. Haga clic en **Proceso**, en **Servicio en la nube** y, a continuación, en **Creación rápida**.

3. Escriba una dirección URL para el servicio en la nube y, a continuación, haga clic en **Creación de servicio en la nube**.

	![Create Service][createservice1]

<h3>Creación de una cuenta de almacenamiento de Azure</h3>

1. En la barra de comandos del Portal de administración, haga clic en **Nuevo**.

2. Haga clic en **Servicios de datos**, en **Almacenamiento** y, a continuación, en **Creación rápida**.

3. Escriba una dirección URL para la cuenta y, a continuación, haga clic en **Crear cuenta de almacenamiento**.

	![Create Storage][createstorage1]

<h2 id="BKMK_ConfigHN">Configuración del nodo principal</h2>

Para usar el administrador de clústeres de HPC para implementar nodos de Azure y enviar trabajos, primero debe seguir los pasos necesarios para configurar el clúster. 

1. En el nodo principal, inicie el administrador de clústeres de HPC. Si aparece el cuadro de diálogo **Seleccionar nodo principal**, haga clic en **Equipo local**. Aparecerá la **lista To-do de implementación**.

2. En **Tareas de implementación necesarias**, haga clic en **Configurar la red**.

	![Configure Network][config_hpc2]

3. En el Asistente para configuración de red, seleccione **Todos los nodos solo en una red empresarial** (topología 5).

	![Topology 5][config_hpc3] 

	>[WACOM.NOTE]Esta es la configuración más sencilla para fines de demostración, ya que el nodo principal solo necesita un solo adaptador de red para conectarse a Active Directory e Internet. Este tutorial no trata las situaciones de clúster que requieren redes adicionales. 
	 
4. Haga clic en **Siguiente** para aceptar los valores predeterminados de las páginas restantes del asistente. A continuación, en la pestaña **Revisar**, haga clic en **Configurar** para completar la configuración de red.

5. En la **lista To-Do de implementación**, haga clic en **Proporcionar credenciales de instalación**. 

6. En el cuadro de diálogo **Credenciales de instalación**, escriba las credenciales de la cuenta de dominio que ha usado para instalar HPC Pack. A continuación, haga clic en **Aceptar**.

	![Installation Credentials][config_hpc6]

	>[WACOM.NOTE]Los servicios de HPC Pack solo usan credenciales de instalación para implementar nodos de ejecución locales.
	
7. En la **lista To-Do de implementación**, haga clic en **Configurar la nomenclatura de nodos nuevos**. 

8. En el cuadro de diálogo **Especificar serie de nomenclatura de nodos**, acepte la serie de nomenclatura predeterminada y haga clic en **Aceptar**.

	![Node Naming][config_hpc8]

	>[WACOM.NOTE]La serie de nomenclatura solo genera nombres para nodos de ejecución unidos a un dominio. Los nodos de Azure reciben un nombre de forma automática.
	  
9. En la **lista To-Do de implementación**, haga clic en **Crear una plantilla de nodo**. Usará una plantilla de nodo para agregar nodos de Azure al clúster.

10. En el asistente de creación de plantillas de nodo, siga estos pasos:

	a. En la página **Elegir tipo de plantilla de nodo**, haga clic en **Plantilla de nodo de Azure** y, a continuación, haga clic en **Siguiente**.

	![Node Template][config_hpc10]

	b. Haga clic en **Siguiente** para aceptar el nombre de plantilla predeterminado.

	c. En la página **Proporcionar información de suscripción**, escriba su Id. de suscripción de Azure (disponible en la información de <a href="[https://account.windowsazure.com/Subscriptions">cuenta de Azure</a>). A continuación, en **Certificado de administración**, haga clic en **Examinar** y seleccione **Administración de Azure de HPC predeterminada.** A continuación, haga clic en **Cliente**.

	![Node Template][config_hpc12]

	d. En la página **Proporcionar información del servicio**, seleccione el servicio en la nube y la cuenta de almacenamiento que ha creado en un paso anterior. A continuación, haga clic en **Cliente**.

	![Node Template][config_hpc13]

	e. Haga clic en **Siguiente** para aceptar los valores predeterminados en las páginas restantes del asistente. A continuación, en la pestaña **Revisar**, haga clic en **Crear** para crear la plantilla de nodo.

	>[WACOM.NOTE]De forma predeterminada, la plantilla de nodo de Azure incluye la configuración para que pueda iniciar (aprovisionar) y detener los nodos manualmente. También puede configurar una programación para iniciar y detener los nodos de Azure automáticamente. 
	
<h2 id="#BKMK_worker">Incorporación de nodos de Azure al clúster</h2>

Ahora usará la plantilla de nodo para agregar nodos de Azure al clúster. Al agregar los nodos al clúster, su información de configuración se almacena para que pueda iniciarlos (aprovisionarlos) en cualquier momento como instancias de rol en el servicio en la nube. Su suscripción solo se carga para los nodos de Azure después de que las instancias de rol se ejecuten en el servicio en la nube.

En este tutorial, agregará dos nodos pequeños.

1. En el administrador de clústeres de HPC, en **Administrador de nodos**, en el panel **Acciones**, haga clic en **Agregar nodo**. 

	![Add Node][add_node1]

2. En el asistente para agregar nodos, en la página **Seleccionar método de implementación**, haga clic en **Agregar nodos de Azure** y, a continuación, en **Siguiente**.

	![Add Azure Node][add_node1_1]

3. En la página **Especificar nodos nuevos**, seleccione la plantilla de nodo de Azure que creó anteriormente (llamada de manera predeterminada **Default AzureNode Template**). A continuación, especifique **dos** nodos de tamaño **pequeño** y, a continuación, haga clic en **Siguiente**.

	![Specify Nodes][add_node2]

	Para obtener más información acerca de los tamaños disponibles de las máquinas virtuales, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx).

4. En la página **Finalización del asistente para agregar nodos**, haga clic en **Finalizar**. 

	 Ahora aparecerán dos nodos llamados **AzureCN-0001** y **AzureCN-0002** en el administrador de clústeres de HPC. Ambos tienen el estado **No implementado**.

	![Added Nodes][add_node3]

<h2 id="BKMK_Start">Inicio de los nodos de Azure</h2>
Cuando quiera usar los recursos del clúster de Azure, use el administrador de clústeres de HPC para iniciar (aprovisionar) los nodos de Azure y conectarlos a la red.

1.	En el administrador de clústeres de HPC, en **Administrador de nodos**, haga clic en uno o en ambos nodos y, a continuación, en el panel **Acciones**, haga clic en **Inicio**. 

	![Start Nodes][add_node4]

2. En el cuadro de diálogo **Iniciar nodos de Azure**, haga clic en **Iniciar**.

	![Start Nodes][add_node5]
 
	Los nodos pasan al estado **Aprovisionamiento**. Puede ver el registro de aprovisionamiento para realizar el seguimiento del progreso de aprovisionamiento.

	![Provision Nodes][add_node6]

3. Después de unos minutos, los nodos de Azure terminan de aprovisionarse y pasan al estado **Sin conexión**. Las instancias de rol se ejecutan en este estado, pero no aceptan los trabajos de clúster.

4. Para confirmar que las instancias de rol estén en ejecución, en el [Portal de administración](https://manage.windowsazure.com), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio en la nube y, a continuación, haga clic en **Instancias**. 

	![Running Instances][view_instances1]

	Verá que hay dos instancias de rol de trabajo ejecutándose en el servicio. HPC Pack también implementa automáticamente dos instancias **HpcProxy** (tamaño medio) para controlar la comunicación entre el nodo principal y Azure.

5. Para conectar los nodos de Azure y que ejecuten los trabajos de clúster, seleccione los nodos, haga clic con el botón secundario y, a continuación, haga clic en **Poner en línea**.

	![Offline Nodes][add_node7]
 
	El administrador de clústeres de HPC indica que los nodos están en estado **En línea**.

<h2 id="BKMK_RunCommand">Ejecución de un comando en el clúster</h2>	
Puede usar el comando **clusrun** de HPC Pack para ejecutar un comando o una aplicación en uno o varios nodos del clúster. Por ejemplo, use **clusrun** para obtener la configuración IP de los nodos de Azure.

1. En el nodo principal, abra un símbolo del sistema.

2. Escriba el siguiente comando:

	`clusrun /nodes:azurecn* ipconfig`

3. Verá un resultado similar al siguiente.

	![Clusrun][clusrun1]

<h2 id="BKMK_RunJob">Ejecución de un trabajo de prueba</h2>

Puede enviar un trabajo de prueba que se ejecute en el clúster híbrido. Este ejemplo es un sencillo trabajo de "barrido paramétrico" (un tipo de computación intrínsecamente paralelo) que ejecuta subtareas que se agregan un entero a sí mismas usando el comando **set /a**. Todos los nodos del clúster contribuyen a finalizar las subtareas para enteros del 1 al 100. 

1. En el administrador de clústeres de HPC, en **Administración de trabajos**, en el panel **Acciones**, haga clic en **Nuevo trabajo de barrido paramétrico**.

	![New Job][test_job1]

2. En el cuadro de diálogo **Nuevo trabajo de barrido paramétrico**, en **Línea de comandos**, escriba `set /a *+*` (sobrescribiendo la línea de comandos predeterminada que aparezca). Deje los valores predeterminados para el resto de la configuración y, a continuación, haga clic en **Enviar** para enviar el trabajo.

	![Parametric Sweep][param_sweep1]

3. Cuando termine el trabajo, haga doble clic en el trabajo **Mi tarea de barrido**.

4. Haga clic en **Ver tareas** y, a continuación, haga clic en una subtarea para ver la salida calculada de dicha subtarea.

	![Task Results][view_job361]

5. Para ver qué nodo ha realizado el cálculo en esa subtarea, haga clic en **Nodos asignados**. (Su clúster podría mostrar un nombre de nodo diferente).

	![Task Results][view_job362]

<h2 id="BKMK_stop">Detención de los nodos de Azure</h2>

Después de probar el clúster, puede usar el administrador de clústeres de HPC para detener los nodos de Azure e impedir que se produzcan cargas innecesarias en la cuenta. De este modo se detiene el servicio en la nube y se eliminan las instancias de rol de Azure. 

1. En el administrador de clústeres de HPC, en **Administración de nodos,** seleccione ambos nodos de Azure. A continuación, en el panel **Acciones**, haga clic en **Detener**. 

	![Stop Nodes][stop_node1]

2. En el cuadro de diálogo **Detener nodos de Azure**, haga clic en **Detener**.

	![Stop Nodes][stop_node2]

3. Los nodos pasan al estado **En detención**. Después de unos minutos, el administrador de clústeres de HPC indica que los nodos tienen el estado **No implementado**.

	![Not Deployed Nodes][stop_node4]

4. Para confirmar que las instancias de rol estén en ejecución, en el [Portal de administración](https://manage.windowsazure.com), haga clic en **Servicios en la nube**, haga clic en el nombre del servicio en la nube y, a continuación, haga clic en **Instancias**. No se implementarán instancias en el entorno de producción.

	![No Instances][view_instances2]

	Con esto finaliza el tutorial.

<h2 id="">Recursos relacionados</h2>

* [HPC Pack 2012 R2 y HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697)
* [Ráfaga en Azure con Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Microsoft HPC Pack en máquinas virtuales de Azure](http://go.microsoft.com/fwlink/p/?linkid=330375)
* [Azure Big Compute: HPC y lote](http://azure.microsoft.com/en-us/solutions/big-compute/)


[Overview]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
[install_hpc1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
[install_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
[install_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
[install_hpc4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
[install_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
[install_hpc7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
[install_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
[upload_cert1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
[createstorage1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
[createservice1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
[config_hpc2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
[config_hpc3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
[config_hpc6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
[config_hpc8]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
[config_hpc10]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
[config_hpc12]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
[config_hpc13]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
[add_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
[add_node1_1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
[add_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
[add_node3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
[add_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
[add_node5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
[add_node6]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
[add_node7]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
[view_instances1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
[clusrun1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
[test_job1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
[param_sweep1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
[view_job361]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
[view_job362]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
[stop_node1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
[stop_node2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
[stop_node4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
[view_instances2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png

<!--HONumber=35.1-->
