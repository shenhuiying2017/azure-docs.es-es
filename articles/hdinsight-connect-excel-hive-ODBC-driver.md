<properties linkid="manage-services-hdinsight-connect-excel-with-hive-ODBC" urlDisplayName="Connect Excel to HDInsight" pageTitle="Connect Excel to Hadoop with the Hive ODBC Driver | Azure" metaKeywords="" description="Learn how to set up and use the Microsoft Hive ODBC driver for Excel to query data in an HDInsight cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with the Microsoft Hive ODBC Driver" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Conexión de Excel a Hadoop con Microsoft Hive ODBC Driver

Una característica clave de la solución de datos de gran tamaño de Microsoft es la integración de componentes de inteligencia empresarial de Microsoft con clústeres Apache Hadoop implementados por HDInsight de Azure. Un ejemplo de esta integración es la capacidad de conectar Excel al almacén de datos de Hive de un clúster Hadoop en HDInsight usando Microsoft Hive Open Database Connectivity (ODBC) Driver.

También es posible conectar desde Excel los datos asociados con un clúster de HDInsight y otros orígenes de datos, incluidos otros clústeres Hadoop (que no sean de HDInsight), con la utilización del complemento Microsoft Power Query para Excel. Para obtener información acerca de la instalación y el uso de Power Query, consulte [Conexión de Excel a HDInsight con Power Query][Conexión de Excel a HDInsight con Power Query].

**Requisitos previos**:

Antes de empezar este artículo, debe tener lo siguiente:

-   Un clúster de HDInsight. Para configurarlo, consulte [Introducción a HDInsight de Azure][Introducción a HDInsight de Azure].
-   Un equipo que ejecute Windows 8, Windows 7, Windows Server 2012 o Windows Server 2008 R2.
-   Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

## En este artículo

1.  [Instalación de Microsoft Hive ODBC Driver][Instalación de Microsoft Hive ODBC Driver]
2.  [Creación de un origen de datos de Hive ODBC][Creación de un origen de datos de Hive ODBC]
3.  [Importación de datos a Excel desde un clúster de HDInsight][Importación de datos a Excel desde un clúster de HDInsight]
4.  [Pasos siguientes][Pasos siguientes]

## <span id="InstallHiveODBCDriver"></span></a>Instalación de Microsoft Hive ODBC Driver

Descargue e instale Microsoft Hive ODBC Driver desde el [Centro de descarga][Centro de descarga].

Este controlador puede instalarse en versiones de 32 o 64 bits de Windows 7, Windows 8, Windows Server 2008 R2 y Windows Server 2012 y permitirá la conexión con HDInsight de Azure (versión 1.6 y posterior) y emulador de HDInsight de Azure (v.1.0.0.0 y posterior). Debe instalar la versión que coincida con la versión de la aplicación en que va a usar el controlador ODBC. Para este tutorial, el controlador se usará desde Office Excel.

## <span id="CreateHiveODBCDataSource"></span></a>Creación de un origen de datos de Hive ODBC

En los siguientes pasos se explica cómo crear un origen de datos de Hive ODBC.

1.  En Windows 8, presione la tecla Windows para abrir la pantalla Inicio y, a continuación, escriba **orígenes de datos**.
2.  Haga clic en **Configurar orígenes de datos ODBC (32 bits)** o **Configurar orígenes de datos ODBC (64 bits)**, en función de la versión de Office de que se trate. Si usa Windows 7, seleccione **Orígenes de datos ODBC (32 bits)** u **Orígenes de datos ODBC (64 bits)** en **Herramientas administrativas**. A continuación, se abrirá el cuadro de diálogo **Administrador de orígenes de datos ODBC**.

    ![Administrador de orígenes de datos OBDC][Administrador de orígenes de datos OBDC]

3.  En la pestaña DNS de usuario, haga clic en **Agregar** para abrir el asistente **Crear nuevo origen de datos**.
4.  Seleccione **Microsoft Hive ODBC Driver** y, a continuación, haga clic en **Finalizar**. Se abrirá el cuadro de diálogo **Microsoft Hive ODBC Driver DNS Setup**.

5.  Escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><td><strong>Propiedad</strong></td><td><strong>Descripción</strong></td></tr>
	<tr><td>Data Source Name</td><td>Asigne un nombre al origen de datos</td></tr>
	<tr><td>Host</td><td>Escriba <hdinsightclustername>.azurehdinsight.net. Por ejemplo, myHDICluster.azurehdinsight.net</td></tr>
	<tr><td>Port</td><td>Use <strong>443</strong>. (Este puerto se ha cambiado de 563 a 443).</td></tr>
	<tr><td>Database</td><td>Use <strong>Default</strong>.</td></tr>
	<tr><td>Hive Server Type</td><td>Seleccione <strong>Hive Server 2</strong></td></tr>
	<tr><td>Mechanism</td><td>Seleccione <strong>Azure HDInsight Service</strong></td></tr>
	<tr><td>HTTP Path</td><td>Deje este parámetro en blanco.</td></tr>
	<tr><td>User Name</td><td>Escriba el nombre del usuario del clúster de HDInsight. Se trata del nombre de usuario creado durante el proceso de aprovisionamiento del clúster. Si ha usado una opción de creación rápida, el nombre de usuario predeterminado es <strong>admin</strong>.</td></tr>
	<tr><td>Password</td><td>Escriba la contraseña del usuario del clúster de HDInsight.</td></tr>
	</table>

    Hay algunos parámetros importantes que se deben tener en cuenta al hacer clic en **Opciones avanzadas**:

	<table border="1">
	<tr><td>Use Native Query</td><td>Cuando esta opción está seleccionada, el controlador ODBC NO tratará de convertir TSQL en HiveQL. Solo debe usarla si está totalmente seguro de que va a enviar instrucciones de HiveQL puras. Al conectarse a SQL Server o a la Base de datos SQL de Azure, debe dejar esta opción desactivada.</td></tr>
	<tr><td>Rows fetched per block</td><td>Al capturar un gran volumen de registros, es posible que sea necesario ajustar este parámetro para garantizar un rendimiento óptimo.</td></tr>
	<tr><td>Default string column length, <br/>
			Binary column length,  <br/>
			Decimal column scale</td><td>La longitud y precisión del tipo de datos pueden afectar a la forma en que se devuelven los datos. Pueden dar lugar a que se devuelva información incorrecta debido a la pérdida de precisión o al truncamiento.</td></tr>
	</table>

    ![Opciones avanzadas][Opciones avanzadas]

6.  Haga clic en **Probar** para probar el origen de datos. Cuando el origen de datos esté configurado correctamente, aparecerá el mensaje *PRUEBAS COMPLETADAS CORRECTAMENTE*.
7.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba. Ahora el nuevo origen de datos debería aparecer en el **Administrador de orígenes de datos ODBC**.
8.  Haga clic en **Aceptar** para salir del asistente.

## <span id="ImportData"></span></a>Importación de datos a Excel desde un clúster de HDInsight

En los pasos siguientes se describe cómo importar datos desde una tabla de Hive a un libro de Excel mediante el origen de datos ODBC creado en los pasos anteriores.

1.  Abra un libro de Excel nuevo o existente.
2.  En la pestaña **Datos**, haga clic en **Obtener datos externos**, haga clic en **From Other Data Sources** y, a continuación, haga clic en **Desde el Asistente para la conexión de datos** para abrir el **Asistente para la conexión de datos**.

    ![Abrir el Asistente para la conexión de datos][Abrir el Asistente para la conexión de datos]

3.  Seleccione **DSN ODBC** como el origen de datos y, a continuación, haga clic en **Siguiente**.
4.  En los orígenes de datos ODBC, seleccione el nombre del origen de datos creado en el paso anterior y, a continuación, haga clic en **Siguiente**.
5.  Vuelva a escribir la contraseña para el clúster en el asistente y, a continuación, haga clic en **Probar** para comprobar la configuración.
6.  Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba.
7.  Haga clic en **OK**. Espere a que se abra el cuadro de diálogo **Seleccionar base de datos y tabla**. Esta operación puede tardar unos segundos.
8.  Seleccione la tabla que desea importar y, a continuación, haga clic en **Siguiente**. *hivesampletable* es una tabla de Hive de muestra integrada en los clústeres de HDInsight. Puede seleccionarla si no ha creado ninguna. Para obtener más información acerca de cómo ejecutar consultas de Hive y crear tablas de Hive, consulte [Uso de Hive con HDInsight][Uso de Hive con HDInsight].
9.  Haga clic en **Finish**.
10. En el cuadro de diálogo **Importar datos**, puede cambiar o especificar la consulta. Para ello, haga clic en **Propiedades**. Esta operación puede tardar unos segundos.
11. Haga clic en la pestaña **Definición** y, a continuación, anexe **LIMIT 200** a la instrucción select de Hive en el cuadro de texto **Texto de comando**. La modificación limitará el conjunto de registros devueltos a 200.

    ![Propiedades de conexión][Propiedades de conexión]

12. Haga clic en **Aceptar** para cerrar el cuadro de diálogo Propiedades de conexión.
13. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Importar datos**.
14. Vuelva a escribir la contraseña y, a continuación, haga clic en **Aceptar**. La importación de los datos a Excel tarda algunos segundos.

## <span id="nextsteps"></span></a>Pasos siguientes

En este artículo se proporciona información acerca de cómo usar Microsoft Hive ODBC Driver para recuperar datos del servicio HDInsight en Excel. De manera similar, puede recuperar datos del servicio HDInsight en la Base de datos SQL. Es posible cargar datos en un servicio HDInsight. Para obtener más información, consulte:

-   [Análisis de la información de retraso de vuelos usando HDInsight][Análisis de la información de retraso de vuelos usando HDInsight]
-   [Carga de datos en HDInsight][Carga de datos en HDInsight]
-   [Uso de Sqoop con HDInsight][Uso de Sqoop con HDInsight]

  [Conexión de Excel a HDInsight con Power Query]: ../hdinsight-connect-excel-power-query/
  [Introducción a HDInsight de Azure]: ../hdinsight-get-started/
  [Instalación de Microsoft Hive ODBC Driver]: #InstallHiveODBCDriver
  [Creación de un origen de datos de Hive ODBC]: #CreateHiveODBCDataSource
  [Importación de datos a Excel desde un clúster de HDInsight]: #ImportData
  [Pasos siguientes]: #nextsteps
  [Centro de descarga]: http://go.microsoft.com/fwlink/?LinkID=286698
  [Administrador de orígenes de datos OBDC]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
  [Opciones avanzadas]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
  [Abrir el Asistente para la conexión de datos]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
  [Propiedades de conexión]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
  [Análisis de la información de retraso de vuelos usando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Carga de datos en HDInsight]: ../hdinsight-upload-data/
  [Uso de Sqoop con HDInsight]: ../hdinsight-use-sqoop/
