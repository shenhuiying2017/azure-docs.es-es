<properties urlDisplayName="Administration" pageTitle="Administración de clústeres de Hadoop en HDInsight usando el portal de Azure | Azure" metaKeywords="" description="Vea cómo administrar el servicio HDInsight. Cree un clúster de HDInsight, abra la consola interactiva de JavaScript y la consola de comandos de Hadoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Manage Hadoop clusters in HDInsight using the Azure Management Portal" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="jgao" />



# Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure

Con la ayuda del Portal de administración de Azure, puede aprovisionar clústeres de Hadoop en HDInsight, cambiar la contraseña de usuario de Hadoop y habilitar RDP para que pueda tener acceso a la consola de comandos de Hadoop en el clúster. También hay otras herramientas disponibles para administrar HDInsight además del portal de administración. 

- Para obtener más información acerca de la administración de HDInsight con Azure PowerShell, consulte [Administración de HDInsight con PowerShell][hdinsight-admin-powershell].

- Para obtener más información acerca de la administración de HDInsight con las herramientas de línea de comandos entre plataformas, consulte [Administración de HDInsight mediante la interfaz de la línea de comandos entre plataformas][hdinsight-admin-cross-platform]. 

**Requisitos previos:

Antes de empezar este artículo, debe tener lo siguiente:

- **Suscripción de Azure**. Azure es una plataforma basada en suscripción. Para obtener información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Evaluación gratuita][azure-free-trial].


##En este artículo

* [Aprovisionamiento de clústeres de HDInsight](#create)
* [Personalización de clústeres de HDInsight](#customize)
* [Cambio de nombre de usuario y contraseña del clúster de HDInsight](#password)
* [Conexión a los clústeres de HDInsight con RDP](#rdp)
* [Creación de un certificado autofirmado](#cert)
* [Concesión/Revocación del acceso a los servicios de HTTP](#httpservice)
* [Apertura de la línea de comandos de Hadoop](#hadoopcmd)
* [Pasos siguientes](#nextsteps)

##<a id="create"></a> Aprovisionamiento de clústeres de HDInsight

Hay varios métodos para crear clústeres de HDInsight, este artículo solo cubre el uso de la opción Quick Create en el portal de administración de Azure. Para conocer otras opciones, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

Un clúster de HDInsight usa contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Para obtener más información acerca de cómo el almacenamiento de blobs de Azure ofrece una experiencia perfecta con los clústeres de HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

Se debe crear una cuenta de almacenamiento de Azure en el mismo centro de datos donde se debe aprovisionar el clúster de HDInsight. Actualmente los clústeres de HDInsight se pueden aprovisionar en cinco centros de datos: 

- Sudeste asiático 
- Europa del Norte
- Europa occidental 
- Este de EE. UU. 
- Oeste de EE. UU. 

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure, consulte [Creación de una cuenta de almacenamiento][azure-create-storageaccount].


**Para aprovisionar un clúster de HDInsight**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **NUEVO** en la parte inferior de la página, haga clic en **SERVICIOS DE DATOS**, haga clic **HDINSIGHT** y después haga clic en **CREACIÓN RÁPIDA**.

3. Proporcione los valores de **Nombre del clúster**, **Tamaño del clúster**, **Constraseña de administración del clúster** y una **cuenta de almacenamiento** de Azure y después haga clic en **Crear clúster de HDInsight**. Después de que el clúster se crea y está en funcionamiento, el estado muestra *Running*.

	![HDI.QuickCreate][image-cluster-quickcreate]

	Al utilizar la opción Creación rápida para crear un clúster, el nombre de usuario predeterminado para la cuenta de usuario de Hadoop es *admin*. Para darle a la cuenta un nombre de usuario diferente, puede usar la opción Custom Create en lugar de Quick Create. También puede cambiar el nombre de la cuenta después de que se haya aprovisionado.

	Al usar la opción Quick Create para crear un clúster, se crea automáticamente un nuevo contenedor con el nombre del clúster de HDInsight en la cuenta de almacenamiento especificada. Si desea personalizar el nombre del contenedor para que el clúster lo use de manera predeterminada, debe usar la opción de creación personalizada. 

	> [WACOM.NOTE] Una vez que se elige una cuenta de almacenamiento de Azure para su clúster de HDInsight, la única forma de cambiar la cuenta de almacenamiento es eliminar el clúster y crear un clúster nuevo con la cuenta de almacenamiento que desee.

4. Haga clic en el clúster recién creado.  Se muestra la página de aterrizaje:

	![HDI.ClusterLanding][image-cluster-landing]


##<a id="customize"></a> Personalización de clústeres de HDInsight

HDInsight trabaja con una amplia gama de componentes de Hadoop. Para ver la lista de los componentes que han sido comprobados y admitidos, consulte [¿Qué versión de Hadoop tiene HDInsight de Azure?][hdinsight-versions]. La personalización de HDInsight se puede hacer con una de las siguientes opciones:

- Use los parámetros de personalización de clústeres en SDK .NET de HDInsight o Azure PowerShell durante el aprovisionamiento de clústeres. Al hacerlo, estos cambios de configuración se conservan durante toda la vida del clúster y no se ven afectados por el restablecimiento de imágenes de nodos del clúster que la plataforma Azure realiza periódicamente para su mantenimiento. Para obtener más información acerca del uso de los parámetros de personalización del clúster, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].
- Algunos de los componentes nativos de Java, como Mahout, Cascading, se pueden ejecutar en el clúster como archivos JAR. Estos archivos JAR se pueden distribuir al almacenamiento de blobs de Azure (WASB) y someterse a los clústeres de HDInsight usando los mecanismos de envío de trabajo de Hadoop. Para obtener más información, consulte [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]. 


	>[WACOM.NOTE] Si tiene problemas con la implementación de los archivos jar en los clústeres de HDInsight o con la llamada de los archivos jar en los clústeres de HDInsight, póngase en contacto con [Soporte técnico de Microsoft][hdinsight-support].
	
	> La presentación en cascada no se admite en HDInsight, y no puede optar a recibir soporte técnico de Microsoft. Para ver las listas de los componentes compatibles, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][hdinsight-versions].


La instalación de software personalizado en el clúster mediante la conexión de escritorio remoto no es compatible. Debe evitar el almacenamiento de cualquier archivo en las unidades del nodo principal, debido a que se pierden si necesita volver a crear los clústeres. Se recomienda almacenar los archivos en el almacenamiento de blobs de Azure. El almacenamiento de blobs es persistente.

##<a id="password"></a> Cambio de nombre de usuario y contraseña del clúster de HDInsight
Un clúster de HDInsight puede tener dos cuentas de usuario.  La cuenta de usuario del clúster de HDInsight se crea durante el proceso de aprovisionamiento.  También puede crear una cuenta de usuario de RDP para tener acceso al clúster mediante RDP. Consulte [Habilitación de escritorio remoto](#enablerdp).

**Para cambiar el nombre de usuario y la contraseña del clúster de HDInsight**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight en el que desea restablecer el nombre de usuario y la contraseña.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Haga clic en **OFF** al lado de **SERVICIOS DE HADOOP**.
6. Haga clic en **GUARDAR** en la parte inferior de la página y espere a que se complete la deshabilitación.
7. Después de que el servicio se haya deshabilitado, haga clic en **ON** al lado de **SERVICIOS DE HADOOP**.
8. Escriba los valores de **NOMBRE DE USUARIO** y **NUEVA CONTRASEÑA**.  Estos serán el nuevo nombre de usuario y la contraseña del clúster.
8. Haga clic en **GUARDAR**.


##<a id="rdp"></a> Conexión a los clústeres de HDInsight con RDP

Las credenciales para el clúster que proporcionó en su creación dan acceso a los servicios en el clúster, pero no al clúster mismo a través del escritorio remoto. El acceso a Escritorio remoto está desactivado de manera predeterminada, por lo que el acceso directo al clúster que lo usa requiere una configuración adicional después de la creación.

**Para habilitar el escritorio remoto**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight al que desea conectarse.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Desde la parte inferior de la página, haga clic en **HABILITAR DE FORMA REMOTA**.
6. En el asistente **Configurar Escritorio remoto**, escriba un nombre de usuario y una contraseña para el escritorio remoto. Tenga en cuenta que el nombre de usuario debe ser diferente al usado para crear el clúster (*admin* de forma predeterminada con la opción Creación rápida). Escriba una fecha de caducidad en el cuadro **CADUCA EL**. Tenga en cuenta que la fecha de caducidad debe estar en el futuro y no después de una semana desde el presente. La hora del día de la caducidad se supone de manera predeterminada en la medianoche de la fecha especificada. A continuación, haga clic en el icono de comprobación.

	![HDI.CreateRDPUser][image-hdi-create-rpd-user]

	La fecha de caducidad debe ser en el futuro y como máximo siete días a partir ahora. El momento es la medianoche de la fecha seleccionada.

> [WACOM.NOTE] Una vez RDP está habilitado para un clúster, debe actualizar la página antes de poder conectarse al clúster.
 
**Para conectarse a un clúster con RDP**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight al que desea conectarse.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Haga clic en **CONECTAR** y siga las instrucciones.

##<a id="cert"></a>Creación de un certificado autofirmado

Si desea realizar operaciones en el clúster usando el SDK de .NET, debe crear un certificado autofirmado en la estación de trabajo y cargarlo en la suscripción de Azure. Esta tarea solo se realiza una vez. Puede instalar el mismo certificado en otras máquinas, siempre y cuando el certificado sea válido.

**Para crear un certificado autofirmado**

1. Cree el certificado autofirmado que se usa para autentica las solicitudes. Puede usar IIS o [makecert][makecert-info] para crear el certificado.
 
2. Busque la ubicación del certificado, haga clic con el botón secundario en él, después haga clic en **Instalar certificado** y, por último, instale el certificado en la tienda personal del equipo. Edite las propiedades del certificado para asignarle un nombre descriptivo.

3. Importe el certificado en el Portal de administración de Azure. Desde el portal, haga clic en **Configuración** en la parte inferior izquierda de la página y después haga clic en **Certificados de administración**. En la parte inferior de la página, haga clic en **Cargar** y siga las instrucciones para cargar el archivo .cer que creó en el paso anterior.

	![HDI.ClusterCreate.UploadCert][image-hdiclustercreate-uploadcert]


##<a id="httpservice"></a> Concesión/Revocación del acceso a los servicios de HTTP

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso desde el portal de la administración. 

>[WACOM.NOTE] Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del usuario del clúster.

**Para conceder/revocar el acceso a los servicios web de HTTP**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de HDInsight implementados.
3. Haga clic en el clúster de HDInsight que desea configurar.
4. Desde la parte superior de la página, haga clic en **CONFIGURACIÓN**.
5. Haga clic en **ON** o en **OFF** junto a **SERVICIOS DE HADOOP**.  
6. Escriba los valores de **NOMBRE DE USUARIO** y **NUEVA CONTRASEÑA**.  Estos serán el nuevo nombre de usuario y la contraseña del clúster.
7. Haga clic en **GUARDAR**.

Esto también se puede hacer con los cmdlets de Azure PowerShell:

- Grant-AzureHDInsightHttpServicesAccess
- Revoke-AzureHDInsightHttpServicesAccess

Consulte [Administración de HDInsight con PowerShell][hdinsight-admin-powershell].

##<a id="hadoopcmd"></a> Apertura de la línea de comandos de Hadoop

Para conectarse al clúster mediante escritorio remoto y utilizar la línea de comandos de Hadoop, primero debe tener habilitado el acceso de escritorio remoto al clúster como se describe en la sección anterior. 

**Para abrir la línea de comandos de Hadoop**

1. Inicie sesión en el [Portal de administración de Azure][azure-management-portal].
2. Haga clic en **HDINSIGHT** en el panel izquierdo. Aparecerá una lista de los clústeres de Hadoop implementados.
3. Haga clic en el clúster de HDInsight al que desea conectarse.
3. Haga clic en **CONFIGURACIÓN** en la parte superior de la página.
4. Haga clic en **Conectar** en la parte inferior de la página.
5. Haga clic en **Abrir**.
6. Escriba sus credenciales y, a continuación, haga clic en **Aceptar**.  Utilice el nombre de usuario y la contraseña que configuró al crear el clúster.
7. Haga clic en **Sí**.
8. En el escritorio, haga doble clic en **Línea de comandos de Hadoop**.
		
	![HDI.HadoopCommandLine][image-hadoopcommandline]


	Para obtener más información acerca de los comandos de Hadoop, consulte [Referencia de comandos de Hadoop][hadoop-command-reference].

En la captura de pantalla anterior, el nombre de la carpeta tiene el número de versión de Hadoop incrustado. El número de versión se puede cambiar según la versión de los componentes de Hadoop instalados en el clúster. Puede usar las variables de entorno de Hadoop para referirse a esas carpetas.  Por ejemplo:

	cd %hadoop_home%
	cd %hive_home%
	cd %pig_home%
	cd %sqoop_home%   
	cd %hcatalog_home%

##<a id="nextsteps"></a> Pasos siguientes
En este artículo, ha aprendido a crear un clúster de HDInsight mediante el Portal de administración de Azure y cómo abrir la herramienta de línea de comandos de Hadoop. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight con PowerShell][hdinsight-admin-powershell]
* [Administración de HDInsight mediante la interfaz de la línea de comandos entre plataformas][hdinsight-admin-cross-platform]
* [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [¿Qué versión de Hadoop tiene HDInsight de Azure?][hdinsight-versions]

[hdinsight-admin-cross-platform]: ../hdinsight-administer-use-command-line/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-support]: http://azure.microsoft.com/en-us/support/options/
[makecert-info]: http://msdn.microsoft.com/en-us/library/bfsktky3(v=vs.110).aspx

[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-management-portal]: https://manage.windowsazure.com/

[azure-purchase-options]: http://azure.microsoft.com/en-us/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/en-us/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/en-us/pricing/free-trial/


[hadoop-command-reference]: http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html

[image-cluster-quickcreate]: ./media/hdinsight-administer-use-management-portal/HDI.QuickCreateCluster.png
[image-cluster-landing]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterLanding.PNG "Cluster landing page"
[image-hdi-create-rpd-user]: ./media/hdinsight-administer-use-management-portal/HDI.CreateRDPUser.png
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/HDI.HadoopCommandLine.PNG "Hadoop command line"
[image-hdiclustercreate-uploadcert]: ./media/hdinsight-administer-use-management-portal/HDI.ClusterCreate.UploadCert.png

<!--HONumber=35.1-->
