---
title: "Conexión de Excel a Hadoop con el controlador ODBC de Microsoft Hive : Azure HDInsight | Microsoft Docs"
description: "Aprenda a configurar y usar el controlador ODBC de Microsoft Hive para que Excel consulte datos en un clúster de HDInsight desde Microsoft Excel"
keywords: Excel en Hadoop, Excel en Hive, ODBC de Hive
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
tags: azure-portal
editor: cgronlun
ms.assetid: a7665a14-0211-4521-b3e7-3b26e8029cc0
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/12/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 62a5b47d3b3fe452bfdff3005192e5066bb7c7da
ms.contentlocale: es-es
ms.lasthandoff: 05/12/2017


---
# <a name="connect-excel-to-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Conexión de Excel a Hadoop en Azure HDInsight con el controlador ODBC de Microsoft Hive

[!INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

La solución de datos de gran tamaño de Microsoft integra componentes de inteligencia empresarial (BI) de Microsoft con clústeres Apache Hadoop implementados por HDInsight de Azure. Un ejemplo de esta integración es la capacidad de conectar Excel al almacenamiento de datos de Hive de un clúster Hadoop en HDInsight usando Microsoft Hive Open Database Connectivity (ODBC) Driver.

También es posible conectar desde Excel los datos asociados con un clúster de HDInsight y otros orígenes de datos, incluidos otros clústeres Hadoop (que no sean de HDInsight), con la utilización del complemento Microsoft Power Query para Excel. Para obtener información acerca de la instalación y el uso de Power Query, consulte [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query].

> [!NOTE]
> Aunque los pasos descritos en este artículo se pueden usar con un clúster de HDInsight basado en Linux o Windows, Windows es necesario para la estación de trabajo del cliente.
> 
> 

**Requisitos previos**:

Antes de empezar este artículo, debe tener los siguientes elementos:

* **Un clúster de HDInsight**. Para crear uno, consulte [Introducción a HDInsight de Azure][hdinsight-get-started].
* **Un equipo** con Office Professional Plus 2013, Office 365 Pro Plus, Excel 2013 Standalone u Office Professional Plus 2010.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalación de Microsoft Hive ODBC Driver
Descargue e instale Microsoft Hive ODBC Driver desde el [Centro de descarga][hive-odbc-driver-download].

Este controlador se puede instalar en las versiones de 32 bits o 64 bits de Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 y Windows Server 2012. Además, puede conectarse con Azure HDInsight (versión 1.6 y posterior) y el emulador de Azure HDInsight (versión 1.0.0.0 y posterior). Debe instalar la versión que coincida con la de la aplicación en la que va a usar el controlador ODBC. Para este tutorial, el controlador se usará desde Office Excel.

## <a name="create-hive-odbc-data-source"></a>Creación de un origen de datos de Hive ODBC
En los siguientes pasos se explica cómo crear un origen de datos de Hive ODBC.

1. En Windows 8 o Windows 10, presione la tecla Windows para abrir la pantalla Inicio y luego escriba **orígenes de datos**.
2. Haga clic en **Configurar orígenes de datos ODBC (32 bits)** o **Configurar orígenes de datos ODBC (64 bits)**, en función de la versión de Office de que se trate. Si usa Windows 7, elija **Orígenes de datos ODBC (32 bits)** u **Orígenes de datos ODBC (64 bits)** en **Herramientas administrativas**. Después, se abrirá el cuadro de diálogo **Administrador de orígenes de datos ODBC**.
   
    ![Administrador de orígenes de datos ODBC](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "Configuración de un DSN mediante el Administrador de orígenes de datos ODBC")

3. En la pestaña DNS de usuario, haga clic en **Agregar** para abrir el asistente **Crear nuevo origen de datos**.
4. Elija **Microsoft Hive ODBC Driver** y, después, haga clic en **Finalizar**. Se abrirá el cuadro de diálogo **Microsoft Hive ODBC Driver DNS Setup** (Configuración de DNS del controlador ODBC de Microsoft Hive).
5. Escriba o seleccione los valores siguientes:
   
   | Propiedad | Description |
   | --- | --- |
   |  Data Source Name |Asigne un nombre al origen de datos |
   |  Host |Escriba &lt;NombreClústerHDInsight>.azurehdinsight.net. Por ejemplo, myHDICluster.azurehdinsight.net |
   |  Port |Use <strong>443</strong>. (Este puerto se ha cambiado de 563 a 443). |
   |  Base de datos |Use el <strong>valor predeterminado</strong> |
   |  Hive Server Type |Seleccione <strong>Hive Server 2</strong> |
   |  Mechanism |Seleccione <strong>Azure HDInsight Service</strong> |
   |  HTTP Path |Deje este parámetro en blanco. |
   |  User Name |Escriba el nombre de usuario HTTP del clúster de HDInsight. El nombre de usuario predeterminado es <strong>admin</strong>. |
   |  Password |Escriba la contraseña del usuario del clúster de HDInsight. |
   
    </table>
   
    Hay algunos parámetros importantes que se deben tener en cuenta al hacer clic en **Opciones avanzadas**:
   
   | Parámetro | Description |
   | --- | --- |
   |  Use Native Query |Cuando esta opción está seleccionada, el controlador ODBC NO tratará de convertir TSQL en HiveQL. Solo debe usarla si está totalmente seguro de que va a enviar instrucciones de HiveQL puras. Al conectarse a SQL Server o a la Base de datos SQL de Azure, debe dejar esta opción desactivada. |
   |  Rows fetched per block |Al capturar un gran volumen de registros, es posible que sea necesario ajustar este parámetro para garantizar un rendimiento óptimo. |
   |  Default string column length, Binary column length, Decimal column scale |La longitud y precisión del tipo de datos pueden afectar a la forma en que se devuelven los datos. Pueden dar lugar a que se devuelva información incorrecta debido a la pérdida de precisión o al truncamiento. |

    ![Opciones avanzadas](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "Opciones de configuración avanzada de DSN")

1. Haga clic en **Probar** para probar el origen de datos. Cuando el origen de datos esté configurado correctamente, aparecerá el mensaje *Pruebas completadas correctamente*.
2. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba. Ahora el nuevo origen de datos debería aparecer en el **Administrador de orígenes de datos ODBC**.
3. Haga clic en **Aceptar** para salir del asistente.

## <a name="import-data-into-excel-from-hdinsight"></a>Importación de datos en Excel desde HDInsight
En los pasos siguientes se describe cómo importar datos desde una tabla de Hive a un libro de Excel mediante el origen de datos ODBC creado en los pasos anteriores.

1. Abra un libro de Excel nuevo o existente.
2. En la pestaña **Datos**, haga clic en **From Other Data Sources** (Desde otros orígenes de datos) y en **Desde el Asistente para la conexión de datos** para iniciar el **Asistente para la conexión de datos**.
   
    ![Apertura del Asistente para la conexión de datos](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "Apertura del Asistente para la conexión de datos")
3. Seleccione **DSN ODBC** como origen de datos y haga clic en **Siguiente**.
4. En los orígenes de datos ODBC, seleccione el nombre del origen de datos creado en el paso anterior y haga clic en **Siguiente**.
5. Vuelva a escribir la contraseña para el clúster en el asistente y, luego, haga clic en **Probar** para comprobar la configuración.
6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de prueba.
7. Haga clic en **Aceptar**. Espere a que se abra el cuadro de diálogo **Seleccionar base de datos y tabla** . Este paso puede tardar unos segundos.
8. Seleccione la tabla que desea importar y, a continuación, haga clic en **Siguiente**. *hivesampletable* es una tabla de Hive de muestra integrada en los clústeres de HDInsight.  Puede seleccionarla si no ha creado ninguna. Para más información acerca de cómo ejecutar consultas de Hive y crear tablas de Hive, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].
9. Haga clic en **Finalizar**
10. En el cuadro de diálogo **Importar datos** , puede cambiar o especificar la consulta. Para ello, haga clic en **Propiedades**. Este paso puede tardar unos segundos.
11. Haga clic en la pestaña **Definición** y, luego, anexe **LIMIT 200** a la instrucción select de Hive del cuadro de texto **Texto de comando**. La modificación limitará el conjunto de registros devueltos a 200.
    
    ![Propiedades de conexión](./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png "Configuración de las propiedades de conexión DSN")
12. Haga clic en **Aceptar** para cerrar el cuadro de diálogo Propiedades de conexión.
13. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Importar datos**.  
14. Vuelva a escribir la contraseña y, a continuación, haga clic en **Aceptar**. La importación de los datos a Excel tarda algunos segundos.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se proporciona información sobre cómo usar el controlador ODBC de Microsoft Hive para recuperar datos del servicio HDInsight en Excel. De manera similar, puede recuperar datos del servicio HDInsight en la Base de datos SQL. Es posible cargar datos en un servicio HDInsight. Para obtener más información, consulte:

* [Análisis de la información de retraso de vuelos con HDInsight][hdinsight-analyze-flight-data]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Uso de Sqoop con HDInsight][hdinsight-use-sqoop]

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698



