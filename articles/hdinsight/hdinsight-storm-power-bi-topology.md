<properties
 pageTitle="Uso de Apache Storm con Power BI | Microsoft Azure"
 description="Creación de un informe de Power BI usando datos de una topología de C# que se ejecuta en un clúster de Apache Storm en HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="05/27/2016"
 ms.author="larryfr"/>

# Uso de Power BI para visualizar datos de una topología de Apache Storm

Power BI permite mostrar visualmente los datos como informes. Mediante las plantillas de Visual Studio para Storm en HDInsight, se pueden utilizar fácilmente datos almacenados de una topología que se ejecutan en un clúster de HDInsight de Apache Storm en SQL Azure y luego visualizarlos con Power BI.

En este documento, aprenderá a usar Power BI para crear un informe a partir de los datos generados por una topología de Apache Storm y almacenados en Base de datos de SQL de Azure.

## Requisitos previos

- Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un usuario de Azure Active Directory con acceso a [Power BI](https://powerbi.com)

* Visual Studio (una de las siguientes versiones)

    * Visual Studio 2012 con [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 con [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) o [Visual Studio Community 2013](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* Herramientas de HDInsight para Visual Studio: vea [Introducción al uso de Herramientas de HDInsight para Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) para obtener información acerca de la instalación.

## Cómo funciona

Este ejemplo contiene una topología de Storm en C# que genera de forma aleatoria los datos de registro de Internet Information Services (IIS). Estos datos se escriben en una Base de datos SQL y desde allí se utilizan para generar informes en Power BI.

La siguiente es una lista de los archivos que implementan la funcionalidad principal de este ejemplo.

* **SqlAzureBolt.cs**: escribe la información generada en la topología de Storm para Base de datos SQL.

* **IISLogsTable.sql**: las instrucciones Transact-SQL usadas para generar la base de datos en la que se almacenan los datos.

> [AZURE.WARNING] Tiene que crear la tabla en Base de datos SQL antes de iniciar la topología en el clúster de HDInsight.

## Descarga del ejemplo

Descargue el [ejemplo de Power BI de Storm en C# de HDInsight ](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Para descargarlo, bifúrquelo o clónelo mediante [git](http://git-scm.com/), o use el vínculo **Descargar** para descargar un archivo .zip del archivo.

## Creación de una base de datos

1. Siga los pasos del [Tutorial de Base de datos SQL](../sql-database/sql-database-get-started.md) para crear una nueva Base de datos SQL.

2. Conecte a la base de datos siguiendo los pasos del documento [Conexión a una Base de datos de SQL con Visual Studio](../sql-database/sql-database-connect-query.md) para conectarse a la base de datos.

4. Haga clic con el botón derecho en el Explorador de objetos de base de datos y cree una __Nueva consulta__. Pegue el contenido del archivo __IISLogsTable.sql__ incluido en el proyecto descargado en la ventana de consulta, y use Ctrl + Mayús + E para ejecutar la consulta. Debería recibir un mensaje de que los comandos se han completado correctamente.

    Una vez que se haya completado este proceso, habrá una nueva tabla llamada __IISLOGS__ en la base de datos.

## Configuración del ejemplo

1. En el [Portal de Azure](https://portal.azure.com), seleccione la Base de datos SQL. Desde la sección __Información esencial__ de la hoja de Base de datos SQL, seleccione __Mostrar las cadenas de conexión de la base de datos__. En la lista que aparece, copie la información __ADO.NET (autenticación de SQL)__.

1. Abra el ejemplo en Visual Studio. Desde el **Explorador de soluciones**, abra el archivo **App.config** y luego busque el elemento:

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    Reemplace el valor __TOBEFILLED ##__ con la cadena de conexión de base de datos copiada en el paso anterior. Reemplace __{your\_username}__ y __{your\_password}__ con el nombre de usuario y la contraseña para la base de datos.

2. Guarde y cierre los archivos.

## Implementación del ejemplo

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto **StormToSQL** y seleccione **Submit to Storm on HDInsight** (Enviar a Storm en HDInsight). Seleccione el clúster de HDInsight desde el cuadro desplegable **Clúster de Storm**.

    > [AZURE.NOTE] El cuadro desplegable **Storm clúster** puede tardar unos segundos en rellenarse con los nombres de servidor.
    >
    > Si se le solicita, escriba las credenciales de inicio de sesión de su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contenga el clúster de Storm en HDInsight.

2. Cuando la topología se envíe correctamente, debe aparecer topologías de Storm para el clúster. Seleccione la entrada SqlAzureWriterTopology en la lista para consultar la información de la topología en ejecución.

    ![Las topologías, con la topología seleccionada](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    Puede utilizar esta vista para ver información de la topología o hacer doble clic en las entradas (como SqlAzureBolt) para ver información específica de un componente de la topología.

3. Después de que la topología se haya ejecutado durante unos minutos, vuelva a la ventana de consulta SQL que usó para crear la base de datos. Sustituya las instrucciones existentes por las siguientes:

        select * from iislogs;
    
    Use Ctrl + Mayús + E para ejecutar la consulta, debería recibir resultados similares a los siguientes:
    
        1	2016-05-27 17:57:14.797	255.255.255.255	/bar	GET	200
        2	2016-05-27 17:57:14.843	127.0.0.1	/spam/eggs	POST	500
        3	2016-05-27 17:57:14.850	123.123.123.123	/eggs	DELETE	200
        4	2016-05-27 17:57:14.853	127.0.0.1	/foo	POST	404
        5	2016-05-27 17:57:14.853	10.9.8.7	/bar	GET	200
        6	2016-05-27 17:57:14.857	192.168.1.1	/spam	DELETE	200

    Se trata de datos que se ha escrito a partir de la topología de Storm.

## Creación de un informe

1. Conectarse al [conector de Base de datos SQL de Azure](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) para Power BI.

2. Dentro de __Bases de datos__, seleccione __Obtener__.

3. Seleccione __Base de datos SQL de Azure__ y __Conectar__.

4. Escriba la información para conectarse a la Base de datos SQL de Azure. Puede encontrar esto acudiendo al [Portal de Azure](https://portal.azure.com) y seleccionando la Base de datos SQL.

    > [AZURE.NOTE] También puede establecer el intervalo de actualización y los filtros personalizados con __Habilitar opciones avanzadas__ en el cuadro de diálogo Conectar.

5. Una vez que haya conectado, verá un nuevo conjunto de datos con el mismo nombre que la base de datos a la que está conectado. Seleccione el conjunto de datos para comenzar a diseñar un informe.

3. Desde __Campos__, expanda la entrada __IISLOGS__. Seleccione la casilla de __URISTEM__. Esto creará un nuevo informe que enumera los recursos URI (/ foo/bar, etc.) registrados en la base de datos.

    ![Creación de un informe](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. A continuación, arrastre __METHOD__ al informe. El informe se actualizará para mostrar los recursos y el método HTTP correspondiente que se utiliza para la solicitud HTTP.

    ![incorporación de los datos de método](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. En la columna __Visualizaciones__, seleccione el icono __Campos__ icono y luego seleccione la flecha hacia abajo junto a __METHOD__ en la sección __Valores__. En la lista que aparece, seleccione __Recuento__. Esto cambiará el informe para mostrar un recuento de las veces que se ha accedido a un identificador URI específico.

    ![Cambio a un recuento de métodos](./media/hdinsight-storm-power-bi-topology/count.png)

6. A continuación, seleccione __Gráfico de columnas apiladas__ para cambiar la forma en la que se muestra la información.

    ![Cambio a un gráfico apilado](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. Una vez que tenga el informe en la forma que desea, utilice la entrada __Guardar__ en el menú para escribir un nombre y guardar el informe.

## Detención de la topología

La topología continuará ejecutándose hasta que la detenga o elimine el clúster de Storm en HDInsight. Realice los pasos siguientes para detener la topología.

1. En Visual Studio, vuelva al visor de topologías y seleccione la topología.

2. Seleccione el botón **Cerrar** para detener la topología.

    ![Botón Cerrar botón en el resumen de la topología](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Eliminación del clúster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Pasos siguientes

En este documento ha aprendido a enviar datos de una topología de Storm a Base de datos SQL, y a visualizar los datos después usando Power BI. Para obtener información sobre cómo trabajar con otras tecnologías de Azure usando Storm en HDInsight, consulte el siguiente artículo:

* [Topologías de ejemplo para Storm en HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0601_2016-->