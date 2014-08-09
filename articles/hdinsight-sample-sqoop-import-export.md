<properties linkid="manage-services-hdinsight-sample-sqoop-import-export" urlDisplayName="HDInsight Samples" pageTitle="Sqoop Import-Export sample | Windows Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to run Sqoop import and export on HDInsight." umbracoNaviHide="0" disqusComments="1" writer="bradsev" editor="cgronlun" manager="paulettm" title="Sqoop Import-Export sample" />

Muestra de importación y exportación en Sqoop
=============================================

En este tema de ejemplo se describe cómo utilizar Apache Sqoop para importar datos desde una base de datos SQL en Azure a un clúster de HDFS de Hadoop.

A pesar de que Hadoop es una opción natural para procesar datos no estructurados y datos semiestructurados, como registros y archivos, es posible que también sea necesario procesar datos estructurados almacenados en bases de datos relacionales.

Sqoop es una herramienta diseñada para transferir datos entre clústeres de Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como SQL, MySQL u Oracle en el sistema de archivos distribuidos Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS. En este tutorial, utilizará una base de datos SQL como base de datos relacional.

Sqoop es un producto de software de código abierto de Cloudera, Inc. El desarrollo de software para Sqoop se cambió en 2011 desde gitHub al sitio [Apache Sqoop](http://sqoop.apache.org/).

En HDInsight de Azure, Sqoop se implementa desde el shell de comandos de Hadoop en el nodo principal del clúster de HDFS. Puede usar la característica de Escritorio remoto disponible en Hadoop en el portal de Azure para tener acceso al nodo principal del clúster para esta implementación.

**Aprenderá a:**

-   Usar Azure PowerShell para ejecutar un programa de MapReduce en HDInsight de Azure que analiza los datos contenidos en un archivo.

**Requisitos previos**: 

tiene una cuenta de Azure y ha habilitado el servicio HDInsight para la suscripción. Ha instalado Azure PowerShell y las herramientas de PowerShell para HDInsight de Azure; además, las ha configurado para usarlas con su cuenta. Si desea ver instrucciones sobre cómo hacerlo, consulte [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/)

También necesitará la dirección IP externa para la ubicación actual en el momento de configurar el firewall en la Base de datos SQL. Para obtenerla, vaya al sitio [WhatIsMyIP](http://www.whatismyip.com/) y anótela. Más adelante en el procedimiento, también necesitará la dirección IP externa para el encabezado del clúster de Hadoop. Puede obtener esta dirección IP de la misma manera.

**Esquema** 

En este tema se muestra cómo ejecutar la muestra, se presenta el código Java para el programa de MapReduce, se resume lo que ha aprendido y se describen algunos pasos siguientes. Tiene las siguientes secciones.

1.  [Configuración de una base de datos SQL](#set-up-sql)
2.  [Uso de Sqoop desde HDInsight para importar datos a un clúster](#java-code)
3.  [Resumen](#summary)
4.  [Pasos siguientes](#next-steps)

Configuración de una base de datos SQL
--------------------------------------

Por determinar: capturas de pantalla para esta sección; este todavía es el enfoque RDP.

**Para crear una base de datos SQL**

1.  Inicie sesión en el Portal de administración de Azure.
2.  Haga clic en NEW en la esquina inferior izquierda, en SERVICIOS DE DATOS y en BASE DE DATOS SQL. Inicie sesión en la cuenta de Azure. Para crear un servidor de base de datos, haga clic en el icono de **base de datos** que se encuentra en la esquina inferior izquierda de la página.

3.  En la página **Getting Started**, haga clic en la opción **Crear un nuevo servidor de base de datos SQL**.

4.  Seleccione el tipo de suscripción (como **Pago por uso**) asociado con la cuenta en la ventana **Crear servidor** y pulse **Next**.

5.  Seleccione la **región** adecuada en la ventana **Crear servidor** y haga clic en **Next**.

6.  Especifique el inicio de sesión y la contraseña del inicio de sesión principal a nivel de servidor del servidor de Base de datos SQL y, a continuación, haga clic en **Next**.

7.  Haga clic en **Agregar** para especificar una regla de firewall que permita que la ubicación actual tenga acceso a la Base de datos SQL para cargar la base de datos AdventureWorks. El firewall otorga acceso según la dirección IP de origen de cada solicitud. Utilice la dirección IP que encontró con los pasos preliminares de configuración de este tutorial para conocer los valores que se deben agregar. Especifique un nombre de regla, tal como se muestra aquí, pero recuerde usar la dirección IP y no la que se utiliza en la ilustración. (También debe agregar la dirección IP externa del nodo principal en el clúster de Hadoop. Si ya sabe cuál es, agréguelo ahora). A continuación, haga clic en el botón **Finish**.

**Para importar AdventureWorks2012**

1.  Descargue la base de datos de AdventureWorks2012 en la máquina local desde el vínculo Recommended Downloads del sitio AdventureWorks for SQL Database.

2.  Descomprima el archivo, abra un símbolo del sistema de administrador y navegue al directorio de AdventureWorks dentro de la carpeta AdventureWorks2012ForSQLAzure.

3.  Escriba lo siguiente para ejecutar CreateAdventureWorksForSQLAzure.cmd:

  CreateAdventureWorksForSQLAzure.cmd servername username password

  Por ejemplo, si el servidor de Base de datos SQL asignado se llama b1gl33p, el nombre de usuario del administrador es "Fred" y la contraseña es "Secret", debe escribir lo siguiente:

  CreateAdventureWorksForSQLAzure.cmd b1gl33p.database.windows.net Fred@b1gl33p Secret

  Con este script se crea la base de datos, se instala el esquema y la base de datos se completa con datos de ejemplo.

4.  Vuelva a la página del portal **AzurePlatform**, haga clic en la suscripción que aparece a la izquierda (**Pago por uso** en el ejemplo que aparece más abajo) y seleccione la base de datos (que aquí se llama wq6xlbyoq0). AventureWorks2012 debe aparecer en la columna **Database Name**. Selecciónelo y haga clic en el icono **Manage** en la parte superior de la página.

5.  Escriba las credenciales de la Base de datos SQL cuando se le solicite y haga clic en **Log on**.

6.  Esta acción abre la interfaz web para la base de datos AdventureWorks en Base de datos SQL. Haga clic en el icono **New Query** que se encuentra en la parte superior para abrir el editor de consulta.

7.  Como Sqoop actualmente agrega corchetes al nombre de la tabla, necesitamos agregar un sinónimo para permitir una nomenclatura de dos partes para las tablas de SQL Server. Para hacerlo, ejecute la siguiente consulta:

  `CREATE SYNONYM [Sales.SalesOrderDetail] FOR Sales.SalesOrderDetail`

8.  Ejecute la siguiente consulta y revise su resultado.

  `select top 200 * from [Sales.SalesOrderDetail] `

Uso de Sqoop desde HDInsight para importar datos a un clúster
-------------------------------------------------------------

1.  En la página de la cuenta, desplácese hacia abajo hasta el icono Open Ports en la sección Your cluster y haga clic en el icono para abrir el puerto del servidor ODBC en el nodo principal del clúster.

2.  Vuelva a la página de la cuenta, desplácese hasta la sección Your cluster y haga clic en el icono de **Escritorio remoto** esta vez para abrir el nodo principal en el clúster. Por determinar: actualización para utilizar con PS.

3.  Seleccione **Open** cuando se le solicite para abrir el archivo .rdp.

4.  Seleccione Connect en la ventana Remote Desktop Connection.

5.  Escriba las credenciales para el clúster de Hadoop (no su Hadoop en la cuenta de Azure) en la ventana **Windows Security** y seleccione **Aceptar**.

6.  Abra Internet Explorer y vaya al sitio WhatIsMyIP para obtener la dirección IP externa del nodo principal del clúster. Vuelva a la página de administración de Base de datos SQL y agregue una regla de firewall que permita al clúster de Hadoop tener acceso a la Base de datos SQL. El firewall otorga acceso según la dirección IP de origen de cada solicitud.

7.  Haga doble clic en el icono de shell de comandos de Hadoop que aparece en la esquina superior izquierda del escritorio para abrirlo. Navegue al directorio "c:\\Apps\\dist\\sqoop\\bin" y ejecute el siguiente comando:

    `sqoop import --connect "jdbc:sqlserver://[serverName].database.windows.net;username=[userName]@[serverName];password=[password];database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

    De ese modo, por ejemplo, para los siguientes valores:
 * nombre de servidor: wq6xlbyoq0
 * nombre de usuario: HadoopOnAzureSqoopAdmin
 * contraseña: Pa$$w0rd

    El comando de sqoop es:

    `sqoop import --connect "jdbc:sqlserver://wq6xlbyoq0.database.windows.net;username=HadoopOnAzureSqoopAdmin@wq6xlbyoq0;password=Pa$$w0rd;;database=AdventureWorks2012" --table Sales.SalesOrderDetail --target-dir /data/lineitemData -m 1`

8.  Vuelva a la página **Accounts** para Hadoop en el portal de Azure y esta vez abra la Consola interactiva. Ejecute el comando \#lsr desde la consola JavaScript para mostrar los archivos y directorios que se encuentran en el clúster de HDFS. Por determinar: Actualización para la disponibilidad general.

9.  Ejecute el comando \#tail para ver los resultados seleccionados desde el archivo part-m-0000. `tail /user/RAdmin/data/SalesOrderDetail/part-m-00000`

Resumen
-------

En este tutorial, vio cómo transferir datos entre un clúster de Hadoop administrado por HDInsight de Azure y una base de datos relacional con Apache Sqoop.

Pasos siguientes
----------------

Si desea consultar tutoriales que ejecutan otras muestras y proporcionan instrucciones sobre el uso de Pig, Hive y los trabajos de MapReduce en HDInsight de Azure con Azure PowerShell, consulte los siguientes temas:

-   [Muestra: Estimador de Pi](/es-es/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/)
-   [Muestra: WordCount](/es-es/manage/services/hdinsight/howto-run-samples/sample-wordcount/)
-   [Muestra: C\# Steaming](/es-es/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/)
-   [Muestra: GraySort de 10 GB](/es-es/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/)
-   [Tutorial: Uso de Pig](/es-es/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Tutorial: Uso de Hive](/es-es/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Tutorial: Uso de MapReduce](/es-es/manage/services/hdinsight/using-mapreduce-with-hdinsight/)
-   [Documentación sobre el SDK de HDInsight de Azure](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx)

