<properties linkid="manage-services-hdinsight-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using Hadoop in HDInsight, a big data solution. Learn how to provision clusters, run hive jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Introducción al uso de Hadoop 1.2 en HDInsight

<div class="dev-center-tutorial-selector sublanding">
<a href="../hdinsight-get-started" title="Introducci&oacute;n al uso de Hadoop 2.2 en HDInsight">Hadoop 2.2</a>
<a href="../hdinsight-get-started-31" title="Introducci&oacute;n al uso de Hadoop 2.4 en HDInsight">Hadoop 2.4</a>
<a href="../hdinsight-get-started-21" title="Introducci&oacute;n al uso de Hadoop 1.2 en HDInsight" class="current">Hadoop 1.2</a>
</div>

HDInsight pone [Apache Hadoop][Apache Hadoop] a disposición de los usuarios como servicio en la nube. De esta forma, el marco de software de MapReduce está disponible en un entorno de Azure más sencillo, escalable y rentable. HDInsight también ofrece una solución rentable para la administración y el almacenamiento de datos mediante el almacenamiento de blobs de Azure.

En este tutorial, aprovisionará un clúster de Hadoop en HDInsight mediante el uso del Portal de administración de Azure, enviará un trabajo de Hive para consultar en una tabla de Hive de ejemplo con el panel de clúster y luego importará los datos de salida del trabajo de Hive en Excel a fin de examinarlos.

> [WACOM.NOTE] En este tutorial se trata el uso de los clústeres de Hadoop 1,2 en HDInsight. Para otras versiones admitidas, haga clic en el selector en la parte superior de la página. Para obtener información de la versión, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight][Novedades en las versiones de clústeres proporcionadas por HDInsight]

La demostración en vivo de este artículo:

<center><a href="https://www.youtube.com/watch?v=Y4aNjnoeaHA&list=PLDrz-Fkcb9WWdY-Yp6D4fTC1ll_3lU-QS" target = "_blank">![HDI.getstarted.video][img-hdi-getstarted-video]</a></center>

Además de poner HDInsight de Azure a disposición de los usuarios, Microsoft también ha lanzado el emulador de HDInsight para Azure, anteriormente conocido como *Microsoft HDInsight Developer Preview*. Este producto está destinado a los desarrolladores y, como tal, solo admite implementaciones de un solo nodo. Para usar el emulador de HDInsight, consulte [Introducción al emulador de HDInsight][Introducción al emulador de HDInsight].

**Requisitos previos:**

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Una suscripción de Azure. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][Opciones de compra], [Ofertas para miembros][Ofertas para miembros] o [Evaluación gratuita][Evaluación gratuita].
-   Un equipo con Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

**Duración aproximada:** 30 minutos

## Apartados de este tutorial

-   [Aprovisionamiento de un clúster de HDInsight][Aprovisionamiento de un clúster de HDInsight]
-   [Ejecución de un trabajo de Hive][Ejecución de un trabajo de Hive]
-   [Conexión a las herramientas de inteligencia empresarial de Microsoft][Conexión a las herramientas de inteligencia empresarial de Microsoft]
-   [Pasos siguientes][Pasos siguientes]

## <a name="provision"></a>Aprovisionamiento de un clúster de HDInsight

HDInsight usa el almacenamiento de blobs de Azure para almacenar datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight].

Cuando se aprovisiona un clúster de HDInsight, se designan una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs específico de dicha cuenta como sistema de archivos predeterminado, de la misma forma que en HDFS. Dicha cuenta debe estar ubicada en el mismo centro de datos que los recursos de proceso de HDInsight. En la actualidad, solo pueden aprovisionarse clústeres de HDInsight en los siguientes centros de datos:

-   Sudeste asiático
-   Europa del Norte
-   Europa occidental
-   Este de EE. UU.
-   Oeste de EE. UU.

Además de esta cuenta de almacenamiento, puede agregar más cuentas de almacenamiento desde la misma suscripción de Azure o desde otras diferentes. Para obtener instrucciones acerca de cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight][Aprovisionamiento de clústeres de HDInsight].

Para simplificar este tutorial, solo se van a usar el contenedor de blobs predeterminado y la cuenta de almacenamiento predeterminada, y todos los archivos se almacenarán en el contenedor del sistema de archivos predeterminado ubicado en */tutorials/getstarted/*. En la práctica, los archivos de datos normalmente se almacenan en una cuenta de almacenamiento designada.

**Para crear una cuenta de Almacenamiento de Azure**

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en **NEW** en la esquina inferior izquierda, seleccione **SERVICIOS DE DATOS**, **ALMACENAMIENTO** y, a continuación, haga clic en **CREACIÓN RÁPIDA**.

    ![HDI.StorageAccount.QuickCreate][HDI.StorageAccount.QuickCreate]

3.  Escriba los detalles de **URL**, **UBICACIÓN** y **REPLICACIÓN** y, a continuación, haga clic en **CREAR CUENTA DE ALMACENAMIENTO**. No se admiten grupos de afinidad. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.
4.  Espere hasta que la característica **STATUS** de la nueva cuenta de almacenamiento cambie a **Online**.
5.  Haga clic en la nueva cuenta de almacenamiento en la lista para seleccionarla.
6.  Haga clic en **MANAGE ACCESS KEYS** en la parte inferior de la página.
7.  Tome nota de los valores de los campos **NOMBRE DE CUENTA DE ALMACENAMIENTO** y **CLAVE DE ACCESO PRIMARIA** (o **CLAVE DE ACCESO SECUNDARIA**. Cualquiera de las claves funciona). Los necesitará más adelante en el tutorial.

Para obtener más información, consulte
[Creación de una cuenta de almacenamiento][Creación de una cuenta de almacenamiento] y [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight].

**Para aprovisionar un clúster de HDInsight**

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].

2.  Haga clic en **HDINSIGHT** en la izquierda para ver los clústeres de HDInsight en la cuenta. En la captura de pantalla siguiente no hay ningún clúster de HDInsight.

    ![HDI.ClusterStatus][HDI.ClusterStatus]

3.  Haga clic en **New** en la esquina inferior izquierda y, después, en **Servicios de datos**, **HDInsight** y en **Creación personalizada**.

    ![ClústerCreaciónPersonalizada.HDI][ClústerCreaciónPersonalizada.HDI]

4.  En la pestaña de detalles del clúster, escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><th>Nombre</th><th>Valor</th></tr>
	<tr><td><strong>Cluster Name</strong></td><td>Nombre del cl&uacute;ster.</td></tr>
	<tr><td><strong>Nodos de datos</strong></td><td>N&uacute;mero de nodos de datos que desea implementar. Para prop&oacute;sitos de prueba, cree un cl&uacute;ster de un solo nodo. <br />El l&iacute;mite del tama&ntilde;o del cl&uacute;ster var&iacute;a seg&uacute;n las suscripciones a Azure. P&oacute;ngase en contacto con el servicio de soporte relacionado con la facturaci&oacute;n de Azure para aumentar el l&iacute;mite.</td></tr>
	<tr><td><strong>Versi&oacute;n de HDInsight</strong></td><td>Seleccione <strong>2,1</strong> para crear un cl&uacute;ster de Hadoop 1,2 en HDInsight.</td></tr>
	<tr><td><strong>Region</strong></td><td>Seleccione la misma regi&oacute;n que la cuenta de almacenamiento que cre&oacute; en el &uacute;ltimo procedimiento. HDInsight requiere que la cuenta de almacenamiento se encuentre en la misma regi&oacute;n. Posteriormente, en la configuraci&oacute;n, puede seleccionar solo una cuenta de almacenamiento que se encuentre en la misma regi&oacute;n que especific&oacute; aqu&iacute;.
	</td></tr>
	</table>

5.  Haga clic en la flecha derecha en la esquina inferior derecha para configurar el usuario del clúster.
6.  En la pestaña para configurar usuario de clúster, especifique el **nombre de usuario** y la **contraseña** para la cuenta del usuario del clúster de HDInsight. Además de esta cuenta, puede crear una cuenta de usuario de RDP después de que se haya realizado el aprovisionamiento del clúster, por lo que puede disponer de un escritorio remoto en el clúster. Para obtener instrucciones, consulte [Administer HDInsight using Management portal][hdinsight-admin-portal]
7.  Haga clic en la flecha derecha en la esquina inferior derecha para configurar la cuenta de almacenamiento.
8.  En la etiqueta de cuenta de almacenamiento, escriba o seleccione los valores siguientes:

    | Nombre                     | Valor                                                                                                                                                                                                      |
    |----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | STORAGE ACCOUNT            | Seleccione **Usar almacenamiento existente**. También tiene la opción de usar el portal de administración para crear una nueva cuenta de almacenamiento si no ha creado una.                               |
    | ACCOUNT NAME               | Especifique la cuenta de almacenamiento que creó en el último procedimiento de este tutorial. Tenga en cuenta que solo se muestran las cuentas de almacenamiento de la misma región en el cuadro de lista. |
    | DEFAULT CONTAINER          | Seleccione **Crear contenedor predeterminado**. Cuando se seleccione esta opción, el nombre del contenedor predeterminado tendrá el mismo nombre que el clúster.                                           |
    | ADDITIONAL STORAGE ACCOUNT | Seleccione **0**. Tiene la opción de conectar el clúster a hasta 7 cuentas de almacenamiento adicionales.                                                                                                  |

9.  Haga clic en el icono de verificación de la esquina inferior derecha para crear el clúster. Una vez completado el proceso de aprovisionamiento, la columna de estado mostrará **Running**.

## <a name="sample"></a>Ejecución de un trabajo de Hive

Ahora ya se ha aprovisionado un clúster de HDInsight. El siguiente paso es ejecutar un trabajo de Hive para consultar una tabla de Hive de ejemplo que viene con clústeres de HDInsight. El nombre de la tabla es *hivesampletable*.

**Para abrir el panel de clúster, siga estos pasos:**

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en **HDINSIGHT** en el panel izquierdo. Verá una lista de los clústeres creados, junto con el que acaba de crear en la última sección.
3.  Haga clic en el nombre del clúster donde desea ejecutar el trabajo de Hive.
4.  Haga clic en **ADMINISTRAR CLÚSTER** en la parte inferior de la página para abrir el panel de clúster. Se abre una página web en una pestaña de explorador diferente.
5.  Escriba el nombre de usuario y la contraseña de la cuenta de usuario de Hadoop. El nombre de usuario predeterminado es **admin** y la contraseña es la que ha escrito durante el proceso de aprovisionamiento. El panel tiene este aspecto:

    ![hdi.dashboard][hdi.dashboard]

    Hay varias pestañas en la parte superior. La pestaña predeterminada es *Editor de Hive*, otras pestañas incluyen Trabajos y Archivos. Mediante el panel, puede enviar consultas de Hive, comprobar registros de trabajo de Hive y examinar archivos WASB.

> [wacom.note] Observe que la URL es *\<ClusterName\>.azurehdinsight.net*. En lugar de abrir el panel desde el Portal de administración, también puede abrirlo desde el explorador web mediante la dirección URL.

**Para ejecutar una consulta de Hive, siga estos pasos:**

1.  En el panel de clúster, haga clic en **Editor de Hive** en la parte superior.
2.  En **Nombre de la consulta**, escriba **HTC20**. El nombre de la consulta es el título del trabajo.
3.  En el panel de consultas, escriba la siguiente consulta:

        SELECT * FROM hivesampletable
            WHERE devicemake LIKE "HTC%"
            LIMIT 20;

    ![hdi.dashboard.query.select][hdi.dashboard.query.select]

4.  Haga clic en **Enviar**. Los resultados tardan unos segundos en aparecer. La pantalla se actualiza cada 30 segundos. También puede hacer clic en **Actualizar** para renovar la pantalla.

    Una vez completado el proceso, la pantalla tendrá el siguiente aspecto:

    ![hdi.dashboard.query.select.result][hdi.dashboard.query.select.result]

    Anote el valor de **Hora de inicio del trabajo (UTC)**. Lo necesitará más adelante.

    Desplácese un poco más abajo y verá **Registro de trabajo**. La salida del trabajo es stdout y el registro del trabajo es stderr.

5.  Si desea volver a abrir el archivo de registro en el futuro, puede hacer clic en **Trabajos** en la parte superior de la pantalla y luego hacer clic en el título del trabajo (nombre de la consulta). Por ejemplo **HTC20** en este caso.

**Para examinar el archivo de salida, siga estos pasos:**

1.  En el panel del clúster, haga clic en **Archivos** en la parte superior.
2.  Haga clic en **Templeton-Job-Status**.
3.  Haga clic en el número de GUID que tiene la hora de última modificación un poco después que la hora de inicio del trabajo que anotara anteriormente. Tome nota de este GUID. Lo necesitará en la próxima sesión.
4.  El archivo **stdout** tiene los datos que necesita en la próxima sección. Puede hacer clic en **stdout** para descargar una copia del archivo de datos que desea.

## <a name="powerquery"></a>Conexión a las herramientas de inteligencia empresarial de Microsoft

El complemento Power Query de Excel se puede usar para exportar los resultados de HDInsight a Excel y aplicarles las herramientas de Microsoft Business Intelligence (BI) para seguir procesándolos o mostrar los resultados.

Para completar esta parte del tutorial, debe tener instalado Excel 2010 o 2013.

**Para descargar Microsoft Power Query para Excel**

-   Descargue Microsoft Power Query para Excel en el [Centro de descarga de Microsoft][Centro de descarga de Microsoft] e instálelo.

**Para importar datos de HDInsight**

1.  Abra Excel y cree un libro en blanco.
2.  Haga clic en el menú **Power Query**, en **Desde otros orígenes** y, a continuación, en **De HDInsight de Azure**.

    ![HDI.GettingStarted.PowerQuery.ImportData][HDI.GettingStarted.PowerQuery.ImportData]

3.  En **Nombre de cuenta**, escriba el nombre de la cuenta de almacenamiento de blobs de Azure asociada con su clúster y, a continuación, haga clic en **Aceptar**. Esta es la cuenta de almacenamiento creada previamente en el tutorial.
4.  En **Clave de cuenta**, escriba la clave de la cuenta de almacenamiento de blobs de Azure y, a continuación, haga clic en **Guardar**.
5.  En el panel de navegación de la derecha, haga doble clic en el nombre del contenedor de almacenamiento de blobs. De forma predeterminada, el nombre del contenedor es el mismo que el del clúster.

6.  Busque **stdout** en la columna **Name** (la ruta de la carpeta es *.../Templeton-Job-Status/<guid>*) y, a continuación, haga clic en **Binary** a la izquierda de **stdout**. El <guid> debe coincidir con el que escribiera en la última sección.

    ![HDI.GettingStarted.PowerQuery.ImportData2][HDI.GettingStarted.PowerQuery.ImportData2]

7.  Haga clic en **Apply & Close** en la esquina superior izquierda. La consulta importa luego la salida del trabajo de Hive en Excel.

## <a name="nextsteps"></a>Pasos siguientes

En este tutorial ha aprendido cómo aprovisionar un clúster con HDInsight, cómo ejecutar un trabajo de MapReduce en este y cómo importar los resultados en Excel para procesarlos mejor y representarlos gráficamente mediante las herramientas de BI. Para obtener más información, consulte los artículos siguientes:

-   [Introducción al emulador de HDInsight][Introducción al emulador de HDInsight]
-   [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight]
-   [Administración de HDInsight con PowerShell][Administración de HDInsight con PowerShell]
-   [Carga de datos en HDInsight][Carga de datos en HDInsight]
-   [Uso de MapReduce con HDInsight][Uso de MapReduce con HDInsight]
-   [Uso de Hive con HDInsight][Uso de Hive con HDInsight]
-   [Uso de Pig con HDInsight][Uso de Pig con HDInsight]
-   [Uso de Oozie con HDInsight][Uso de Oozie con HDInsight]
-   [Desarrollo de programas de streaming de Hadoop C# para HDInsight][Desarrollo de programas de streaming de Hadoop C# para HDInsight]
-   [Desarrollo de programas MapReduce de Java para HDInsight][Desarrollo de programas MapReduce de Java para HDInsight]

  [Hadoop 2.2]: ../hdinsight-get-started "Introducción al uso de Hadoop 2.2 en HDInsight"
  [Hadoop 2.4]: ../hdinsight-get-started-31 "Introducción al uso de Hadoop 2.4 en HDInsight"
  [Hadoop 1.2]: ../hdinsight-get-started-21 "Introducción al uso de Hadoop 1.2 en HDInsight"
  [Apache Hadoop]: http://hadoop.apache.org/
  [Novedades en las versiones de clústeres proporcionadas por HDInsight]: ../hdinsight-component-versioning/
  
  [Introducción al emulador de HDInsight]: ../hdinsight-get-started-emulator/
  [Opciones de compra]: http://azure.microsoft.com/es-es/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/es-es/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/es-es/pricing/free-trial/
  [Aprovisionamiento de un clúster de HDInsight]: #provision
  [Ejecución de un trabajo de Hive]: #sample
  [Conexión a las herramientas de inteligencia empresarial de Microsoft]: #powerquery
  [Pasos siguientes]: #nextsteps
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [HDI.StorageAccount.QuickCreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
  [Creación de una cuenta de almacenamiento]: ../storage-create-storage-account/
  [HDI.ClusterStatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
  [ClústerCreaciónPersonalizada.HDI]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [hdi.dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
  [hdi.dashboard.query.select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
  [hdi.dashboard.query.select.result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
  [Centro de descarga de Microsoft]: http://www.microsoft.com/es-es/download/details.aspx?id=39379
  [HDI.GettingStarted.PowerQuery.ImportData]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
  [HDI.GettingStarted.PowerQuery.ImportData2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Carga de datos en HDInsight]: ../hdinsight-upload-data/
  [Uso de MapReduce con HDInsight]: ../hdinsight-use-mapreduce
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
  [Uso de Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Desarrollo de programas de streaming de Hadoop C# para HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Desarrollo de programas MapReduce de Java para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
