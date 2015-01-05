<properties urlDisplayName="HDInsight and Excel" pageTitle="Conexión de Excel a Hadoop con Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Learn how to take advantage of business intelligence components and use Excel to access data stored in Azure HDInsight using Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#Conexión de Excel a Hadoop con Power Query

Una de las características clave de la solución para Big Data de Microsoft es la integración de los componentes de Microsoft Business Intelligence (BI) con los clústeres de Hadoop en HDInsight. Un ejemplo importante de esta integración es la capacidad de conectar Excel a la cuenta de almacenamiento de Azure que contiene los datos asociados a su clúster de Hadoop usando Microsoft Power Query para Excel. En este artículo se ofrecen las pautas para configurar y usar Power Query desde Excel para consultar los datos asociados con un clúster de Hadoop administrado con HDInsight. 

**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- Un clúster de HDInsight. Para configurarlo, consulte [Introducción a HDInsight de Azure][hdinsight-get-started].
- Un equipo que ejecute Windows 8, Windows 7, Windows Server 2012 o Windows Server 2008 R2.
- Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

## En este artículo

- [Instalación de Microsoft Power Query para Excel](#InstallPowerQuery)
- [Importación de datos a Excel](#ImportData)
- [Pasos siguientes](#NextSteps)


## <a id="InstallPowerQuery"></a>Instalación de Microsoft Power Query para Excel

Se puede usar Power Query para importar datos desde diferentes orígenes en Microsoft Excel, donde se pueden habilitar herramientas de inteligencia empresarial (BI) como PowerPivot y Power View. En concreto, Power Query puede importar datos ofrecidos o generados por un trabajo de Hadoop ejecutado en un clúster de HDInsight. 

Descargue Microsoft Power Query para Excel en el [Centro de descarga de Microsoft][powerquery-download] e instálelo.

## <a id="ImportData"></a>Importación de datos de HDInsight a Excel

El complemento de Power Query para Excel facilita la importación de datos desde su clúster de HDInsight a Excel, donde se pueden usar herramientas de inteligencia empresarial como PowerPivot y Power Map para la inspección, el análisis y la presentación de los datos.

**Importación de datos desde un clúster de HDInsight**

1. Abra Excel.

2. Cree un libro vacío.

3. Haga clic en el menú **Power Query**, en **Desde otros orígenes** y, a continuación, en **De HDInsight de Azure**. 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Nota: Si no ve el menú **Power Query**, vaya a **Archivo** > **Opciones** > **Complementos** y seleccione **Complementos COM** en el cuadro desplegable **Manager** situado al final de la página. Seleccione el botón **Go...** y compruebe que la casilla del complemento de Microsoft Office Power Query para Excel esté activada.

3. En **Nombre de cuenta**, escriba el nombre de la cuenta de almacenamiento de blobs de Azure asociada con su clúster y, a continuación, haga clic en **Aceptar**. 

4. En **Account Key**, escriba la clave de cuenta para la cuenta de almacenamiento de blobs y, a continuación, haga clic en **Save**. (Solo tendrá que hacer esto la primera vez que obtenga acceso a este almacén).	

5. En el panel **Navigator** situado a la izquierda de **Query Editor**, haga doble clic en el nombre del contenedor de almacenamiento de blobs. De forma predeterminada, el nombre del contenedor es el mismo que el del clúster. 

6. Busque **HiveSampleData.txt** en la columna **Name** (la ruta de la carpeta es **../hive/warehouse/hivesampletable/**) y, a continuación, haga clic en **Binary** a la izquierda de HiveSampleData.txt.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Si quiere, puede cambiar el nombre de las columnas. Cuando haya terminado, haga clic en **Aplicar y cerrar**.	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Pasos siguientes

En este artículo ha aprendido a usar Power Query para recuperar datos de HDInsight en Excel. Del mismo modo puede recuperar datos de HDInsight en SQL Azure. También se pueden cargar los datos en HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][hdinsight-ODBC]
* [Carga de datos en HDInsight][hdinsight-upload-data].

[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 

<!--HONumber=35.1-->
