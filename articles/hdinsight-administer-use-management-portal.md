<properties linkid="manage-services-hdinsight-howto-administer-hdinsight" urlDisplayName="Administration" pageTitle="Administer HDInsight clusters with Management Portal | Azure" metaKeywords="" description="Learn how to administer HDInsight Service. Create an HDInsight cluster, open the interactive JavaScript console, and open the Hadoop command console." metaCanonical="" services="hdinsight" documentationCenter="" title="Administer HDInsight clusters using Management Portal" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Administración de clústeres de HDInsight con el portal de administración
========================================================================

Con la ayuda del portal de administración de Azure, puede aprovisionar clústeres de HDInsight, cambiar la contraseña de usuario de Hadoop y habilitar RDP para que pueda tener acceso a la consola de comandos de Hadoop en el clúster. También hay otras herramientas disponibles para administrar HDInsight además del portal de administración.

-   Para obtener más información acerca de la administración de HDInsight con Azure PowerShell, consulte [Administración de HDInsight con PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/).

-   Para obtener más información acerca de la administración de HDInsight con las herramientas de línea de comandos entre plataformas, consulte [Administración de HDInsight mediante la interfaz de la línea de comandos entre plataformas](/en-us/manage/services/hdinsight/administer-hdinsight-using-command-line-interface/).

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

-   **Suscripción de Azure**. Azure es una plataforma basada en suscripción. Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Ofertas para miembros](https://www.windowsazure.com/en-us/pricing/member-offers/) o [Evaluación gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

En este artículo
----------------

-   [Aprovisionamiento de clústeres de HDInsight](#create)
-   [Personalización de clústeres de HDInsight](#customize)
-   [Cambio de nombre de usuario y contraseña del clúster de HDInsight](#password)
-   [Conexión a los clústeres de HDInsight con RDP](#rdp)
-   [Concesión/Revocación del acceso a los servicios de HTTP](#httpservice)
-   [Apertura de la línea de comandos de Hadoop](#hadoopcmd)
-   [Pasos siguientes](#nextsteps)

Aprovisionamiento de clústeres de HDInsight
-------------------------------------------

Hay varios métodos para crear clústeres de HDInsight, este artículo solo cubre el uso de la opción Quick Create en el portal de administración de Azure. Para conocer otras opciones, consulte [Aprovisionamiento de clústeres de HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).

Un clúster de HDInsight usa contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Para obtener más información acerca de cómo el almacenamiento de blobs de Azure ofrece una experiencia perfecta con los clústeres de HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/).

Se debe crear una cuenta de almacenamiento de Azure en el mismo centro de datos donde se debe aprovisionar el clúster de HDInsight. Actualmente los clústeres de HDInsight se pueden aprovisionar en cinco centros de datos:

-   Sudeste asiático
-   Europa del Norte
-   Europa occidental
-   Este de EE. UU.
-   Oeste de EE. UU.

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento](/en-us/manage/services/storage/how-to-create-a-storage-account/).

**Para aprovisionar un clúster de HDInsight**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **NEW** en la parte inferior de la página, después en **DATA SERVICES**, a continuación en **HDINSIGHT** y, finalmente, en **QUICK CREATE**.

3.  Proporcione los valores de **Cluster Name**, **Cluster Size**, **Cluster Admin Password** y una **Cuenta de almacenamiento** de Azure y, a continuación, haga clic en **Crear clúster de HDInsight**. Después de que el clúster se crea y está en funcionamiento, el estado muestra *Running*.

    ![HDI.QuickCreate](./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png)

    Al utilizar la opción Quick Create para crear un clúster, el nombre de usuario predeterminado para la cuenta de administrador es *admin*. Para darle a la cuenta un nombre de usuario diferente, puede usar la opción Custom Create en lugar de Quick Create. También puede cambiar el nombre de la cuenta después de que se haya aprovisionado.

    Al usar la opción Quick Create para crear un clúster, se crea automáticamente un nuevo contenedor con el nombre del clúster de HDInsight en la cuenta de almacenamiento especificada. Si desea personalizar el nombre del contenedor para que el clúster lo use de manera predeterminada, debe usar la opción de creación personalizada.

    > [WACOM.NOTE] Una vez que se elige una cuenta de almacenamiento de Azure para su clúster de HDInsight, la única forma de cambiar la cuenta de almacenamiento es eliminar el clúster y crear un clúster nuevo con la cuenta de almacenamiento que desee.

4.  Haga clic en el clúster recién creado. Se muestra la página de aterrizaje:

    ![HDI.AterrizajeClúster](./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Página de aterrizaje del clúster")

Personalización de clústeres de HDInsight
-----------------------------------------

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?](/en-us/manage/services/hdinsight/versioning-in-hdinsight/) La personalización de HDInsight se puede hacer con una de las siguientes opciones:

-   Use los parámetros de personalización de clústeres en SDK .NET de HDInsight o Azure PowerShell durante el aprovisionamiento de clústeres. Al hacerlo, estos cambios de configuración se conservan durante toda la vida del clúster y no se ven afectados por el restablecimiento de imágenes de nodos del clúster que la plataforma Azure realiza periódicamente para su mantenimiento. Para obtener más información acerca del uso de los parámetros de personalización del clúster, consulte [Aprovisionamiento de clústeres de HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).
-   Algunos de los componentes nativos de Java, como Mahout, Cascading, se pueden ejecutar en el clúster como archivos JAR. Estos archivos JAR se pueden distribuir al almacenamiento de blobs de Azure (WASB) y someterse a los clústeres de HDInsight usando los mecanismos de envío de trabajo de Hadoop. Para obtener más información, consulte [Envío de trabajos de Hadoop mediante programación](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/).

    > [WACOM.NOTE] Si tiene problemas con la implementación de los archivos jar en los clústeres de HDInsight o con la llamada de los archivos jar en los clústeres de HDInsight, póngase en contacto con [Soporte técnico de Microsoft](http://www.windowsazure.com/en-us/support/options/).

    > Tanto Mahout como Cascading no son compatibles con HDInsight y no son elegibles para el Soporte técnico de Microsoft. Para ver las listas de los componentes compatibles, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight](/en-us/manage/services/hdinsight/versioning-in-hdinsight/).

La instalación de software personalizado en el clúster mediante la conexión de escritorio remoto no es compatible. Debe evitar el almacenamiento de cualquier archivo en las unidades del nodo principal, debido a que se pierden si necesita volver a crear los clústeres. Se recomienda almacenar los archivos en el almacenamiento de blobs de Azure. El almacenamiento de blobs es persistente.

Cambio de nombre de usuario y contraseña del clúster de HDInsight
-----------------------------------------------------------------

Un clúster de HDInsight puede tener dos cuentas de usuario. La cuenta de usuario del clúster de HDInsight se crea durante el proceso de aprovisionamiento. También puede crear una cuenta de usuario de RDP para tener acceso al clúster mediante RDP. Consulte [Habilitación de escritorio remoto](#enablerdp).

**Para cambiar el nombre de usuario y la contraseña del clúster de HDInsight**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3.  Haga clic en el clúster de HDInsight en el que desea restablecer el nombre de usuario y la contraseña.
4.  Desde la parte superior de la página, haga clic en **CONFIGURATION**.
5.  Haga clic en **OFF** al lado de **SERVICIOS DE HADOOP**.
6.  Haga clic en **SAVE** en la parte inferior de la página y espere a que se complete la deshabilitación.
7.  Después de que el servicio se haya deshabilitado, haga clic en **ON** al lado de **SERVICIOS DE HADOOP**.
8.  Escriba los valores de **USER NAME** y **NEW PASSWORD**. Estos serán el nuevo nombre de usuario y la contraseña del clúster.
9.  Haga clic en **SAVE**.

Conexión a los clústeres de HDInsight con RDP
---------------------------------------------

Las credenciales para el clúster que proporcionó en su creación dan acceso a los servicios en el clúster, pero no al clúster mismo a través del escritorio remoto. El acceso a Escritorio remoto está desactivado de manera predeterminada, por lo que el acceso directo al clúster que lo usa requiere una configuración adicional después de la creación.

**Para habilitar el escritorio remoto**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3.  Haga clic en el clúster de HDInsight al que desea conectarse.
4.  Desde la parte superior de la página, haga clic en **CONFIGURATION**.
5.  Desde la parte inferior de la página, haga clic **ENABLE REMOTE**.
6.  En el asistente **Configure Remote Desktop**, escriba un nombre de usuario y una contraseña para el escritorio remoto. Tenga en cuenta que el nombre de usuario debe ser diferente al usado para crear el clúster (*admin* de forma predeterminada con la opción Quick Create). Escriba una fecha de caducidad en el cuadro **EXPIRES ON**. Tenga en cuenta que la fecha de caducidad debe estar en el futuro y no después de una semana desde el presente. La hora del día de la caducidad se supone de manera predeterminada en la medianoche de la fecha especificada. A continuación, haga clic en el icono de comprobación.

    ![HDI.UsuarioCrearRDP](./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png)

    La fecha de caducidad debe ser en el futuro y como máximo siete días a partir ahora. El momento es la medianoche de la fecha seleccionada.

> [WACOM.NOTE] Una vez RDP está habilitado para un clúster, debe actualizar la página antes de poder conectarse al clúster.

**Para conectarse a un clúster con RDP**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3.  Haga clic en el clúster de HDInsight al que desea conectarse.
4.  Desde la parte superior de la página, haga clic en **CONFIGURATION**.
5.  Haga clic en **CONNECT** y siga las instrucciones.

Concesión/Revocación del acceso a los servicios de HTTP
-------------------------------------------------------

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso desde el portal de la administración.

> [WACOM.NOTE] Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del usuario del clúster.

**Para conceder/revocar el acceso a los servicios web de HTTP**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3.  Haga clic en el clúster de HDInsight que desea configurar.
4.  Desde la parte superior de la página, haga clic en **CONFIGURATION**.
5.  Haga clic en **ON** u **OFF** al lado de **SERVICIOS DE HADOOP**.
6.  Escriba los valores de **USER NAME** y **NEW PASSWORD**. Estos serán el nuevo nombre de usuario y la contraseña del clúster.
7.  Haga clic en **SAVE**.

Esto también se puede hacer con los cmdlets de Azure PowerShell:

-   Grant-AzureHDInsightHttpServicesAccess
-   Revoke-AzureHDInsightHttpServicesAccess

Consulte [Administración de HDInsight con PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/).

Apertura de la línea de comandos de Hadoop
------------------------------------------

Para conectarse al clúster mediante escritorio remoto y utilizar la línea de comandos de Hadoop, primero debe tener habilitado el acceso de escritorio remoto al clúster como se describe en la sección anterior.

**Para abrir la línea de comandos de Hadoop**

1.  Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com/).
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de Hadoop implementados.
3.  Haga clic en el clúster de HDInsight al que desea conectarse.
4.  Haga clic en **CONFIGURATION** en la parte superior de la página.
5.  Haga clic en **Connect** en la parte inferior de la página.
6.  Haga clic en **Open**.
7.  Escriba sus credenciales y, a continuación, haga clic en **OK**. Utilice el nombre de usuario y la contraseña que configuró al crear el clúster.
8.  Haga clic en **Yes**.
9.  En el escritorio, haga doble clic en **Hadoop Command Line**.

    ![HDI.LíneaComandosHadoop](./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Línea de comandos de Hadoop")

    Para obtener más información acerca de los comandos de Hadoop, consulte [Hadoop commands reference](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html).

En la captura de pantalla anterior, el nombre de la carpeta tiene el número de versión de Hadoop incrustado. El número de versión se puede cambiar según la versión de los componentes de Hadoop instalados en el clúster. Puede usar las variables de entorno de Hadoop para referirse a esas carpetas. Por ejemplo:

    cd %hadoop_home%
    cd %hive_home%
    cd %pig_home%
    cd %sqoop_home%   
    cd %hcatalog_home%

Pasos siguientes
----------------

En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal de administración de Azure y cómo abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

-   [Administración de HDInsight con PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Administración de HDInsight mediante la interfaz de la línea de comandos entre plataformas](/en-us/manage/services/hdinsight/administer-hdinsight-using-command-line-interface/)
-   [Aprovisionamiento de clústeres de HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/)
-   [Envío de trabajos de Hadoop mediante programación](/en-us/manage/services/hdinsight/submit-hadoop-jobs-programmatically/)
-   [Introducción a HDInsight de Azure](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [¿Qué versión de Hadoop tiene HDInsight de Azure?](/en-us/manage/services/hdinsight/versioning-in-hdinsight/)

