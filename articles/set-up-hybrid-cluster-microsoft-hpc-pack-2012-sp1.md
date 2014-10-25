<properties linkid="itpro-windows-howto-howto-setup-hybrid-cluster-hpcpack" urlDisplayName="" pageTitle="Set up a Hybrid Compute Cluster in Azure with Microsoft HPC Pack" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Compute Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="cloud-services" ms.workload="big-compute" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/16/2014" ms.author="danlep"></tags>

# Configurar un clúster de proceso híbrido con Microsoft HPC Pack

Este tutorial le indica cómo usar Microsoft HPC Pack 2012 R2 y Azure para configurar un pequeño clúster híbrido de informática de alto rendimiento (HPC). El clúster constará de un nodo principal local (un equipo con el sistema operativo Windows Server y HPC Pack) y otros nodos de ejecución que implemente localmente como instancias de rol de trabajo en un servicio en la nube de Azure. A continuación, podrá ejecutar trabajos informáticos en el clúster híbrido.

![Clúster híbrido de HPC][]

Este tutorial muestra un enfoque, en ocasiones denominado clúster "ráfaga en la nube", para usar recursos informáticos escalables y bajo demanda en Azure a fin de ejecutar aplicaciones informáticas que consumen numerosos recursos.

En este tutorial se supone que no cuenta con experiencia previa con los clústeres informáticos o con HPC Pack. Únicamente está destinado a ayudarle a implementar un clúster de proceso híbrido rápidamente con fines de demostración. Para obtener más información, así como pasos para implementar un clúster híbrido HPC Pack a mayor escala en un entorno de producción, consulte la [información detallada][].

> [WACOM.NOTE] Azure ofrece una [serie de tamaños][] para los recursos informáticos, adecuados para las diferentes cargas de trabajo. Por ejemplo, las instancias A8 y A9 combinan alto rendimiento y acceso a baja latencia, aunque se necesita una red de aplicaciones de alto rendimiento para determinadas aplicaciones de HPC. Para obtener más información, consulte [Sobre las instancias informáticas intensivas A8 y A9][].

Este tutorial le guiará a través de estos pasos básicos:

-   [Requisitos previos][]
-   [Instalación de HPC Pack en el nodo principal][]
-   [Preparación de la suscripción de Azure][]
-   [Configuración del nodo principal][]
-   [Incorporación de nodos de Azure al clúster][]
-   [Inicio de los nodos de Azure][]
-   [Ejecución de un comando en el clúster][]
-   [Ejecución de un trabajo de prueba][]
-   [Detención de los nodos de Azure][]

## Requisitos previos

> [WACOM.NOTE] Necesita una cuenta de Azure para completar este tutorial. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Creación de una cuenta de Azure][].

Además, necesitará satisfacer los siguientes requisitos previos para este tutorial:

-   Un equipo local que ejecute una edición de Windows Server 2012 R2 o Windows Server 2012. Este equipo será el nodo principal del clúster de HPC. Si todavía no tiene Windows Server, puede descargar e instalar una [versión de evaluación][].

    -   El equipo debe estar unido a un dominio de Active Directory.

    -   Asegúrese de que no haya otros roles de servidor o servicios de rol adicionales instalados.

    -   Para la compatibilidad con HPC Pack, el sistema operativo debe estar instalado en uno de estos idiomas: inglés, japonés o chino (simplificado).

    -   Compruebe que estén instaladas las actualizaciones importantes y esenciales.

-   Archivos de instalación para HPC Pack 2012 R2, disponibles de forma gratuita. [Descargue][] el paquete de instalación completo y copie los archivos en el equipo del nodo principal o en una ubicación de red. Elija los archivos de instalación que tengan el mismo idioma que su instalación de Windows Server.

-   Una cuenta de dominio con permisos de administrador local en el nodo principal.

-   Conectividad TCP en el puerto 443 desde el nodo principal a Azure.

## Instalación de HPC Pack en el nodo principal

En primer lugar, debe instalar Microsoft HPC Pack en un equipo local con un Windows Server que será el nodo principal del clúster.

1.  Inicie sesión en el nodo principal usando una cuenta de dominio que tenga permisos de administrador local.

2.  Inicie el asistente de instalación de HPC Pack ejecutando el archivo Setup.exe desde los archivos de instalación de HPC Pack.

3.  En la pantalla de **instalación de HPC Pack 2012 R2**, haga clic en **New installation or add new features to an existing installation**.

    ![Instalación de HPC Pack 2012][]

4.  En la página **Microsoft Software User Agreement**, haga clic en **Next**.

5.  En la página **Select Installation Type**, haga clic en **Create a new HPC cluster by creating a head node** y, a continuación, en **Next**.

    ![Selección del tipo de instalación][]

6.  El asistente ejecuta varias pruebas de preinstalación. Haga clic en **Next** en la página **Installation Rules** si se pasan todas las pruebas. Si no, revise la información proporcionada y realice los cambios necesarios en su entorno. A continuación, ejecute las pruebas de nuevo o, si fuera necesario, vuelva a iniciar el asistente de instalación.

    ![Reglas de instalación][]

7.  En la página **HPC DB Configuration**, asegúrese de haber seleccionado **Head Node** en todas las bases de datos de HPC y, a continuación, haga clic en **Next**.

    ![Configuración de la base de datos][]

8.  Acepte las opciones seleccionadas de forma predeterminada en las páginas restantes del asistente. En la página **Install Required Components**, haga clic en **Install**.

    ![Instalación][]

9.  Cuando finalice la instalación, desactive **Start HPC Cluster Manager** y, a continuación, haga clic en **Finish**. (Más adelante iniciará el administrador de clústeres de HPC para completar la configuración del nodo principal).

    ![Finalización][]

## Preparación de la suscripción de Azure

Utilice el [Portal de administración][] para realizar los siguientes pasos con su suscripción de Azure. Estos pasos son necesarios para poder implementar más adelante los nodos de Azure desde el nodo principal local:

-   Cargue un certificado de administración (necesario para las conexiones seguras entre el nodo principal y los servicios de Azure).

-   Cree un servicio en la nube de Azure en el que se ejecutarán los nodos de Azure (instancias de rol de trabajo).

-   Crear una cuenta de almacenamiento de Azure

    > [WACOM.NOTE]Anote también el Id. de la suscripción de Azure, ya que lo necesitará más adelante. Podrá encontrarlo en la [información de la cuenta][] de Azure.

### Cargar el certificado de administración predeterminado

HPC Pack instala un certificado autofirmado en el nodo principal, llamado Default Microsoft HPC Azure Management, que podrá cargar como certificado de administración de Azure. Este certificado se proporciona con fines de prueba e implementaciones de prueba de concepto.

1.  En el quipo del nodo principal, inicie sesión en el [Portal de administración][].

2.  Haga clic en **Settings** y, a continuación, en **Management Certificates**.

3.  En la barra de comandos, haga clic en **Upload**.

    ![Configuración del certificado][]

4.  Busque el archivo C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer en el nodo principal. A continuación, haga clic en el botón **Check**.

    ![Carga del certificado][]

Aparecerá **Default HPC Azure Management** en la lista de certificados de administración.

### Crear un servicio en la nube de Azure

> [WACOM.NOTE]Para conseguir un mejor rendimiento, cree el servicio en la nube y la cuenta de almacenamiento en la misma región geográfica.

1.  En la barra de comandos del Portal de administración, haga clic en **New**.

2.  Haga clic en **Proceso**, en **Servicio en la nube** y, a continuación, en **Quick Create**.

3.  Escriba una URL para el servicio en la nube y, a continuación, haga clic en **Create Cloud Service**.

    ![Creación de un servicio][]

### Crear una cuenta de almacenamiento de Azure

1.  En la barra de comandos del Portal de administración, haga clic en **New**.

2.  Haga clic en **Servicios de datos**, en **Almacenamiento** y, a continuación, en **Quick Create**.

3.  Escriba una URL para la cuenta y, a continuación, haga clic en **Create Storage Account**.

    ![Creación de almacenamiento][]

## Configuración del nodo principal

Para usar el administrador de clústeres de HPC para implementar nodos de Azure y enviar trabajos, primero debe seguir los pasos necesarios para configurar el clúster.

1.  En el nodo principal, inicie el administrador de clústeres de HPC. Si aparece el cuadro de diálogo **Select Head Node**, haga clic en **Local Computer**. Aparecerá **la lista To-do de implementación**.

2.  Debajo de **Required deployment tasks**, haga clic en **Configure your network**.

    ![Configuración de la red][]

3.  En el Asistente para configuración de red, seleccione **All nodes only on an enterprise network** (Topología 5).

    ![Topología 5][]

    > [WACOM.NOTE]Esta es la configuración más sencilla para fines de demostración, ya que el nodo principal solo necesita un solo adaptador de red para conectarse a Active Directory e Internet. Este tutorial no trata las situaciones de clúster que requieren redes adicionales.

4.  Haga clic en **Next** para aceptar los valores predeterminados de las páginas restantes del asistente. A continuación, en la pestaña **Review**, haga clic en **Configure** para completar la configuración de red.

5.  En la **lista To-do de implementación**, haga clic en **Provide installation credentials**.

6.  En el cuadro de diálogo **Installation Credentials**, escriba las credenciales de la cuenta de dominio que ha usado para instalar HPC Pack. A continuación, haga clic en **Aceptar**.

    ![Credenciales de instalación][]

    > [WACOM.NOTE]Los servicios de HPC Pack solo usan credenciales de instalación para implementar nodos de ejecución locales.

7.  En la **lista To-do de implementación**, haga clic en **Configure the naming of new nodes**.

8.  En el cuadro de diálogo **Specify Node Naming Series**, acepte la serie de nomenclatura predeterminada y haga clic en **OK**.

    ![Nomenclatura de nodos][]

    > [WACOM.NOTE]La serie de nomenclatura solo genera nombres para nodos de ejecución unidos a un dominio. Los nodos de Azure reciben un nombre de forma automática.

9.  En la **lista To-do de implementación**, haga clic en **Create a node template**. Usará una plantilla de nodo para agregar nodos de Azure al clúster.

10. En el asistente de creación de plantillas de nodo, siga estos pasos:

    a. En la página **Choose Node Template Type**, haga clic en **Azure node template** y, a continuación, en **Next**.

    ![Plantilla de nodo][]

    b. Haga clic en **Next** para aceptar el nombre de plantilla predeterminado.

    c. En la página **Provide Subscription Information**, introduzca su Id. de suscripción de Azure (disponible en la [información de cuenta][información de la cuenta] de Azure). A continuación, en **Management certificate**, haga clic en **Browse** y seleccione **Default HPC Azure Management**. A continuación, haga clic en **Siguiente**.

    ![Plantilla de nodo][1]

    d. En la página **Provide Service Information**, seleccione el servicio en la nube y la cuenta de almacenamiento que ha creado en un paso anterior. A continuación, haga clic en **Siguiente**.

    ![Plantilla de nodo][2]

    e. Haga clic en **Next** para aceptar los valores predeterminados en las páginas restantes del asistente. A continuación, en la pestaña **Review**, haga clic en **Create** para crear la plantilla de nodo.

    > [WACOM.NOTE]De forma predeterminada, la plantilla de nodo de Azure incluye la configuración para que pueda iniciar (aprovisionar) y detener los nodos manualmente. También puede configurar una programación para iniciar y detener los nodos de Azure automáticamente.

## Incorporación de nodos de Azure al clúster

Ahora usará la plantilla de nodo para agregar nodos de Azure al clúster. Al agregar los nodos al clúster, su información de configuración se almacena para que pueda iniciarlos (aprovisionarlos) en cualquier momento como instancias de rol en el servicio en la nube. Su suscripción solo se carga para los nodos de Azure después de que las instancias de rol se ejecuten en el servicio en la nube.

En este tutorial, agregará dos nodos pequeños.

1.  En el administrador de clústeres de HPC, en **Node Management**, en el panel **Actions**, haga clic en **Add Node**.

    ![Agregar un nodo][]

2.  En el asistente para agregar nodos, en la página **Select Deployment Method**, haga clic en **Add Azure nodes** y, a continuación, en **Next**.

    ![Agregar un nodo de Azure][]

3.  En la página **Specify New Nodes**, seleccione la plantilla de nodo de Azure que ha creado anteriormente (cuyo nombre predeterminado es **Default AzureNode Template**). A continuación, especifique **2** nodos de tamaño **pequeño** y, a continuación, haga clic en **Next**.

    ![Especificar los nodos][]

    Para obtener más información acerca de los tamaños disponibles de las máquinas virtuales, consulte [Tamaños de máquinas virtuales y servicios en la nube de Azure][].

4.  En la página **Completing the Add Node Wizard**, haga clic en **Finish**.

    Ahora aparecerán dos nodos llamados **AzureCN-0001** y **AzureCN-0002** en el administrador de clústeres de HPC. Ambos tienen el estado **Not-Deployed**.

    ![Nodos agregados][]

## Inicio de los nodos de Azure

Cuando quiera usar los recursos del clúster de Azure, use el administrador de clústeres de HPC para iniciar (aprovisionar) los nodos de Azure y conectarlos a la red.

1.  En el administrador de clústeres de HPC, en **Node Management**, en el panel **Actions**, haga clic en **Start**.

    ![Iniciar los nodos][]

2.  En el cuadro de diálogo **Start Azure Nodes**, haga clic en **Start**.

    ![Iniciar los nodos][3]

    Los nodos pasan al estado **Provisioning**. Puede ver el registro de aprovisionamiento para realizar el seguimiento del progreso de aprovisionamiento.

    ![Aprovisionar nodos][]

3.  Después de unos minutos, los nodos de Azure terminan de aprovisionarse y pasan al estado **Offline**. Las instancias de rol se ejecutan en este estado, pero no aceptan los trabajos de clúster.

4.  Para confirmar que las instancias de rol se estén ejecutando, en el [Portal de administración][], haga clic en **Servicios en la nube**, después en el nombre de su servicio en la nube y, a continuación, en **Instances**.

    ![Instancias en ejecución][]

    Verá que hay dos instancias de rol de trabajo ejecutándose en el servicio. HPC Pack también implementa automáticamente dos instancias **HpcProxy** (tamaño medio) para controlar la comunicación entre el nodo principal y Azure.

5.  Para conectar los nodos de Azure y que ejecuten los trabajos de clúster, seleccione los nodos, haga clic con el botón secundario y, a continuación, haga clic en **Bring Online**.

    ![Nodos desconectados][]

    El administrador de clústeres de HPC indica que los nodos están en estado **Online**.

## Ejecución de un comando en el clúster

Puede usar el comando **clusrun** de HPC Pack para ejecutar un comando o una aplicación en uno o varios nodos del clúster. Por ejemplo, use **clusrun** para obtener la configuración IP de los nodos de Azure.

1.  En el nodo principal, abra un símbolo del sistema.

2.  Escriba el siguiente comando:

    `clusrun /nodes:azurecn* ipconfig`

3.  Verá un resultado similar al siguiente.

    ![Clusrun][]

## Ejecución de un trabajo de prueba

Puede enviar un trabajo de prueba que se ejecute en el clúster híbrido. Este ejemplo es un sencillo trabajo de "barrido paramétrico" (un tipo de computación intrínsecamente paralelo) que ejecuta subtareas que se agregan un entero a sí mismas usando el comando **set /a**. Todos los nodos del clúster contribuyen a finalizar las subtareas para enteros del 1 al 100.

1.  En el administrador de clústeres de HPC, en **Job Management**, en el panel **Actions**, haga clic en **New Parametric Sweep Job**.

    ![Nuevo trabajo][]

2.  En el cuadro de diálogo **New Parametric Sweep Job**, en la **línea de comandos**, escriba `set /a *+*` (sobrescribiendo la línea de comandos predeterminada que aparezca). Deje los valores predeterminados para el resto de la configuración y, a continuación, haga clic en **Submit** para enviar el trabajo.

    ![Barrido paramétrico][]

3.  Cuando termine el trabajo, haga doble clic en el trabajo **My Sweep Task**.

4.  Haga clic en **View Tasks** y, a continuación, haga clic en una subtarea para ver la salida calculada de dicha subtarea.

    ![Resultados de la tarea][]

5.  Para ver qué nodo ha realizado el cálculo en esa subtarea, haga clic en **Allocated Nodes**. (Su clúster podría mostrar un nombre de nodo diferente).

    ![Resultados de la tarea][4]

## Detención de los nodos de Azure

Después de probar el clúster, puede usar el administrador de clústeres de HPC para detener los nodos de Azure e impedir que se produzcan cargas innecesarias en la cuenta. De este modo se detiene el servicio en la nube y se eliminan las instancias de rol de Azure.

1.  En el administrador de clústeres de HPC, en **Node Management**, seleccione ambos nodos de Azure. A continuación, en el panel **Actions**, haga clic en **Stop**.

    ![Detener los nodos][]

2.  En el cuadro de diálogo **Stop Azure Nodes**, haga clic en **Stop**.

    ![Detener los nodos][5]

3.  Los nodos pasan al estado **Stopping**. Después de unos minutos, el administrador de clústeres de HPC indica que los nodos tienen el estado **Not-Deployed**.

    ![Nodos no implementados][]

4.  Para confirmar que las instancias han dejado de ejecutarse en Azure, en el [Portal de administración][], haga clic en **Servicios en la nube**, después en el nombre de su servicio en la nube y, a continuación, en **Instances**. No se implementarán instancias en el entorno de producción.

    ![Sin instancias][]

    Con esto finaliza el tutorial.

## Recursos relacionados

-   [HPC Pack 2012 R2 and HPC Pack 2012][]
-   [Burst to Azure with Microsoft HPC Pack][información detallada]
-   [Deploying Applications to Azure Nodes][]
-   [Microsoft HPC Pack en máquinas virtuales de Azure][]

  [Clúster híbrido de HPC]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png
  [información detallada]: http://go.microsoft.com/fwlink/p/?LinkID=200493
  [serie de tamaños]: http://go.microsoft.com/fwlink/p/?LinkId=389844
  [Sobre las instancias informáticas intensivas A8 y A9]: http://go.microsoft.com/fwlink/p/?Linkid=328042
  [Requisitos previos]: #BKMK_Prereq
  [Instalación de HPC Pack en el nodo principal]: #BKMK_DeployHN
  [Preparación de la suscripción de Azure]: #BKMK_Prpare
  [Configuración del nodo principal]: #BKMK_ConfigHN
  [Incorporación de nodos de Azure al clúster]: #BKMK_worker
  [Inicio de los nodos de Azure]: #BKMK_start
  [Ejecución de un comando en el clúster]: #BKMK_RunCommand
  [Ejecución de un trabajo de prueba]: #BKMK_RunJob
  [Detención de los nodos de Azure]: #BKMK_stop
  [Creación de una cuenta de Azure]: http://www.windowsazure.com/es-es/develop/php/tutorials/create-a-windows-azure-account/
  [versión de evaluación]: http://technet.microsoft.com/evalcenter/dn205286.aspx
  [Descargue]: http://go.microsoft.com/fwlink/p/?linkid=389557
  [Instalación de HPC Pack 2012]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png
  [Selección del tipo de instalación]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png
  [Reglas de instalación]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png
  [Configuración de la base de datos]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png
  [Instalación]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png
  [Finalización]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png
  [Portal de administración]: https://manage.windowsazure.com
  [información de la cuenta]: [https://account.windowsazure.com/Subscriptions
  [Configuración del certificado]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png
  [Carga del certificado]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png
  [Creación de un servicio]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png
  [Creación de almacenamiento]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png
  [Configuración de la red]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png
  [Topología 5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png
  [Credenciales de instalación]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png
  [Nomenclatura de nodos]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png
  [Plantilla de nodo]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png
  [1]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png
  [2]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png
  [Agregar un nodo]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png
  [Agregar un nodo de Azure]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png
  [Especificar los nodos]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png
  [Tamaños de máquinas virtuales y servicios en la nube de Azure]: http://msdn.microsoft.com/library/windowsazure/dn197896.aspx
  [Nodos agregados]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png
  [Iniciar los nodos]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png
  [3]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png
  [Aprovisionar nodos]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png
  [Instancias en ejecución]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png
  [Nodos desconectados]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png
  [Clusrun]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png
  [Nuevo trabajo]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png
  [Barrido paramétrico]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png
  [Resultados de la tarea]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png
  [4]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png
  [Detener los nodos]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png
  [5]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png
  [Nodos no implementados]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png
  [Sin instancias]: ./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png
  [HPC Pack 2012 R2 and HPC Pack 2012]: http://go.microsoft.com/fwlink/p/?LinkID=263697
  [Deploying Applications to Azure Nodes]: http://go.microsoft.com/fwlink/p/?LinkId=325317
  [Microsoft HPC Pack en máquinas virtuales de Azure]: http://go.microsoft.com/fwlink/p/?linkid=330375
