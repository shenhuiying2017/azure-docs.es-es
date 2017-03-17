---
title: Uso de Apache Storm con Power BI | Microsoft Docs
description: "Creación de un informe de Power BI usando datos de una topología de C# que se ejecuta en un clúster de Apache Storm en HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 36fe3b9c-5232-4464-8d75-95403b6da7a1
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 75ab31176abaeed2865a77689a5733666f95a253
ms.lasthandoff: 03/07/2017


---
# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>Uso de Power BI para visualizar datos de una topología de Apache Storm

Power BI permite mostrar visualmente los datos como informes. Este documento proporciona un ejemplo de cómo usar Apache Storm en HDInsight para generar datos para Power BI.

> [!NOTE]
> Aunque los pasos descritos en este documento se basan en un entorno de desarrollo de Windows con Visual Studio, el proyecto compilado se puede enviar a un clúster de HDInsight basado en Windows o Linux. Solo los clústeres basados en Linux creados con posterioridad al 28/10/2016 admiten topologías SCP.NET.
> 
> Para usar una topología de C# con un clúster basado en Linux, actualice el paquete de NuGet Microsoft.SCP.Net.SDK usado en el proyecto a la versión 0.10.0.6 o superior. La versión del paquete también debe coincidir con la versión principal de Storm instalada en HDInsight. Por ejemplo, en las versiones de HDInsight 3.3 y 3.4 se usa Storm 0.10.x, mientras que en HDInsight 3.5 se usa Storm 1.0.x.
> 
> Las topologías de C# en clústeres basados en Linux deben usar .NET 4.5, y emplear Mono para ejecutarse en el clúster de HDInsight. La mayoría funcionará. No obstante, compruebe el documento de [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/) para ver las posibles incompatibilidades.
> 
> Para obtener una versión de Java de este proyecto que funciona con HDInsight basado en Windows o en Linux, vea [Procesamiento de eventos desde Azure Event Hubs con Storm en HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="prerequisites"></a>Requisitos previos

* Un usuario de Azure Active Directory con acceso a [Power BI](https://powerbi.com).
* Un clúster de HDInsight. Para más información, vea [Introducción a los ejemplos de Storm Starter para análisis de macrodatos en HDInsight basado en Linux](hdinsight-apache-storm-tutorial-get-started-linux.md).

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Visual Studio (una de las siguientes versiones)
  
  * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio Community 2013](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
  * Visual Studio 2017 (cualquier edición)

* Herramientas de HDInsight para Visual Studio: vea [Introducción al uso de Herramientas de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para obtener información acerca de la instalación.

## <a name="how-it-works"></a>Cómo funciona

Este ejemplo contiene una topología de Storm en C# que genera de forma aleatoria los datos de registro de Internet Information Services (IIS). Estos datos se escriben en una Base de datos SQL y desde allí se utilizan para generar informes en Power BI.

Los siguientes archivos implementan la funcionalidad principal de este ejemplo:

* **SqlAzureBolt.cs**: escribe la información generada en la topología de Storm para Base de datos SQL.
* **IISLogsTable.sql**: las instrucciones Transact-SQL usadas para generar la base de datos en la que se almacenan los datos.

> [!WARNING]
> Cree la tabla en SQL Database antes de iniciar la topología en el clúster de HDInsight.

## <a name="download-the-example"></a>Descarga del ejemplo

Descargue el [ejemplo de Power BI de Storm en C# de HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Para descargarlo, bifúrquelo o clónelo mediante [git](http://git-scm.com/), o use el vínculo **Descargar** para descargar un archivo .zip del archivo.

## <a name="create-a-database"></a>Creación de una base de datos

1. Para crear una base de datos, siga los pasos del documento [Tutorial de SQL Database](../sql-database/sql-database-get-started.md).

2. Para conectarse a la base de datos, siga los pasos del documento [Conexión a una base de datos SQL con Visual Studio](../sql-database/sql-database-connect-query.md).

3. En el Explorador de objetos, haga clic con el botón derecho en la base de datos y seleccione **Nueva consulta**. Pegue el contenido del archivo **IISLogsTable.sql** incluido en el proyecto descargado en la ventana de consulta, y use Ctrl + Mayús + E para ejecutar la consulta. Debería recibir un mensaje informándole de que los comandos se completaron correctamente.

## <a name="configure-the-sample"></a>Configuración del ejemplo

1. En el [Portal de Azure](https://portal.azure.com), seleccione la Base de datos SQL. Desde la sección **Aspectos básicos** de la hoja Base de datos SQL, seleccione **Mostrar las cadenas de conexión de la base de datos**. En la lista que aparece, copie la información **ADO.NET (autenticación de SQL)** .

2. Abra el ejemplo en Visual Studio. Desde el **Explorador de soluciones**, abra el archivo **App.config** y luego busque la siguiente entrada:
   
        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
   
    Reemplace el valor **TOBEFILLED ##** con la cadena de conexión de base de datos copiada en el paso anterior. Reemplace **{your\_username}** y **{your\_password}** por el nombre de usuario y la contraseña de la base de datos.

3. Guarde y cierre los archivos.

## <a name="deploy-the-sample"></a>Implementación del ejemplo

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **StormToSQL** y seleccione **Submit to Storm on HDInsight** (Enviar a Storm en HDInsight). Seleccione el clúster de HDInsight desde el cuadro desplegable **Clúster de Storm** .
   
   > [!NOTE]
   > El cuadro desplegable **Storm clúster** puede tardar unos segundos en rellenarse con los nombres de servidor.
   > 
   > Si se le solicita, introduzca las credenciales de inicio de sesión de su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contenga el clúster de Storm en HDInsight.

2. Cuando se haya enviado la topología, aparecerá el __Visor de topología__. Para ver esta topología, seleccione la entrada SqlAzureWriterTopology en la lista.
   
    ![Las topologías, con la topología seleccionada](./media/hdinsight-storm-power-bi-topology/topologyview.png)
   
    Puede usar esta vista para ver información de la topología o hacer doble clic en una entrada (como SqlAzureBolt) para ver información específica de un componente de la topología.

3. Después de que la topología se haya ejecutado durante unos minutos, vuelva a la ventana de consulta SQL que usó para crear la base de datos. Sustituya las instrucciones existentes por la consulta siguiente:
   
        select * from iislogs;
   
    Use Ctrl + Mayús + E para ejecutar la consulta, debería recibir resultados similares a los siguientes:
   
        1    2016-05-27 17:57:14.797    255.255.255.255    /bar    GET    200
        2    2016-05-27 17:57:14.843    127.0.0.1    /spam/eggs    POST    500
        3    2016-05-27 17:57:14.850    123.123.123.123    /eggs    DELETE    200
        4    2016-05-27 17:57:14.853    127.0.0.1    /foo    POST    404
        5    2016-05-27 17:57:14.853    10.9.8.7    /bar    GET    200
        6    2016-05-27 17:57:14.857    192.168.1.1    /spam    DELETE    200
   
    Estos datos se escribieron a partir de la topología de Storm.

## <a name="create-a-report"></a>Creación de un informe

1. Conectarse al [conector de Base de datos SQL de Azure](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) para Power BI.

2. Dentro de **Bases de datos**, seleccione **Obtener**.

3. Seleccione **Azure SQL Database** y luego **Conectar**.

4. Escriba la información para conectarse a la Base de datos SQL de Azure. Puede encontrar esta información si visita [Azure Portal](https://portal.azure.com) y selecciona la base de datos SQL.
   
   > [!NOTE]
   > También puede establecer el intervalo de actualización y los filtros personalizados con **Habilitar opciones avanzadas** en el cuadro de diálogo Conectar.
 
5. Una vez que haya conectado, verá un nuevo conjunto de datos con el mismo nombre que la base de datos a la que está conectado. Seleccione el conjunto de datos para comenzar a diseñar un informe.

6. En **Campos**, expanda la entrada **IISLOGS**. Active la casilla de **URISTEM**. Esto crea un informe que enumera los recursos (URI) visitados (/foo, /bar, etc.) registrados en la base de datos.
   
    ![Creación de un informe](./media/hdinsight-storm-power-bi-topology/createreport.png)

7. A continuación, arrastre **METHOD** al informe. El informe se actualizará para mostrar los recursos y el método HTTP correspondiente que se utiliza para la solicitud HTTP.
   
    ![incorporación de los datos de método](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

8. En la columna **Visualizaciones**, seleccione el icono **Campos** y luego seleccione la flecha hacia abajo junto a **METHOD** en la sección **Valores**. En la lista que aparece, seleccione **Recuento**. Esto cambia el informe para mostrar un recuento de las veces que se obtuvo acceso a un URI específico.
   
    ![Cambio a un recuento de métodos](./media/hdinsight-storm-power-bi-topology/count.png)

9. A continuación, seleccione **Gráfico de columnas apiladas** para cambiar la forma en la que se muestra la información.
   
    ![Cambio a un gráfico apilado](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

10. Para guardar el informe, haga clic en **Guardar** y escriba un nombre para el informe.

## <a name="stop-the-topology"></a>Detención de la topología

La topología continúa ejecutándose hasta que la detenga o elimine el clúster de Storm en HDInsight. Realice los pasos siguientes para detener la topología.

1. En Visual Studio, vuelva al visor de topologías y seleccione la topología.

2. Seleccione el botón **Cerrar** para detener la topología.
   
    ![Botón Cerrar en el resumen de la topología](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Pasos siguientes

En este documento ha aprendido a enviar datos de una topología de Storm a Base de datos SQL, y a visualizar los datos después usando Power BI. Para obtener información sobre cómo trabajar con otras tecnologías de Azure usando Storm en HDInsight, consulte el siguiente artículo:

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)


