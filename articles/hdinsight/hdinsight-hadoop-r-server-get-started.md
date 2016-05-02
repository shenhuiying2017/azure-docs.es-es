<properties
   pageTitle="Introducción a R Server en HDInsight (versión preliminar)| Azure"
   description="Obtenga información sobre cómo crear un Apache Spark en un clúster de HDInsight (versión preliminar) que incluya R Server y después enviar un script de R al clúster."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/25/2016"
   ms.author="jeffstok"/>

#Introducción al uso de R Server en HDInsight (versión preliminar)

La oferta de nivel Premium para HDInsight incluye R Server en HDInsight (versión preliminar). Esto permite que los scripts de R usen MapReduce y Spark para ejecutar cálculos distribuidos. En este documento, obtendrá información sobre cómo crear un nuevo servidor de R en HDInsight y después ejecutar un script de R que demuestre el uso de Spark para cálculos de R distribuidos.

![Diagrama del flujo de trabajo de este documento](./media/hdinsight-getting-started-with-r/rgettingstarted.png)

## Requisitos previos

* __Una suscripción a Azure__: antes de comenzar este tutorial, debe tener una suscripción a Azure. Consulte [Obtener una evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) para obtener más información.

* __Un cliente de Secure Shell (SSH)__: el cliente de SSH se usa para conectarse al clúster de HDInsight de forma remota y ejecutar comandos directamente desde el clúster. Los sistemas Linux, Unix y OS X proporcionan un cliente de SSH a través del comando `ssh`. Para sistemas Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    * __Claves de SSH (opcional)__: puede proteger la cuenta de SSH usada para conectarse al clúster mediante una contraseña o una clave pública. Usar una contraseña es más sencillo y le permite empezar sin tener que crear un par de clave pública o privada. Sin embargo, el uso de una clave es más seguro.
    
        Los pasos de este documento asumen que está usando una contraseña. Para obtener información sobre cómo crear y usar claves de SSH con HDInsight, consulte los siguientes documentos:
        
        * [Use SSH with HDInsight from Linux, Unix, or OS X clients (Uso de SSH con HDInsight desde clientes de Linux, Unix u OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        * [Use SSH with HDInsight from Windows clients (Uso de SSH con HDInsight desde clientes de Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)

## Creación de clústeres

> [AZURE.NOTE] Los pasos descritos en este documento crean un servidor de R en HDInsight con la información de configuración básica. Para obtener otras opciones de configuración del clúster (como agregar cuentas de almacenamiento adicional, usar una red virtual de Azure o crear una tienda de metadatos para Hive), consulte [Creación de clústeres de HDInsight basados en Linux](hdinsight-hadoop-provision-linux-clusters.md).

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Seleccione __NUEVO__, __Datos + Análisis__ y después __HDInsight__.

    ![Imagen de la creación de un nuevo clúster](./media/hdinsight-getting-started-with-r/newcluster.png)

3. Escriba un nombre para el clúster en el campo __Nombre del clúster__. Si tiene varias suscripciones a Azure, use la entrada __Suscripción__ para seleccionar la que quiere usar.

    ![Selecciones del nombre y la suscripción del clúster](./media/hdinsight-getting-started-with-r/clustername.png)

4. Seleccione __Seleccionar tipo de clúster__. En la hoja __Tipo de clúster__, seleccione las opciones siguientes:

    * __Tipo de clúster__: servidor de R en Spark
    
    * __Nivel de clúster__: Premium

    Deje las demás opciones en los valores predeterminados y después use el botón __Seleccionar__ para guardar el tipo de clúster.
    
    ![Captura de pantalla de la hoja del tipo de clúster](./media/hdinsight-getting-started-with-r/clustertypeconfig.png)
    
    > [AZURE.NOTE] También puede agregar un servidor de R a otros tipos de clúster de HDInsight (por ejemplo, Hadoop o HBase), seleccionando el tipo de clúster y después seleccionando __Premium__.

5. Seleccione **Grupo de recursos** para ver una lista de los grupos de recursos existentes y seleccione el grupo en el que quiere crear el clúster. O también puede seleccionar **Crear nuevo** y después escribir el nombre del nuevo grupo de recursos. Aparecerá una marca de comprobación verde para indicar que el nuevo nombre de grupo está disponible.

    > [AZURE.NOTE] Esta entrada se establecerá de manera predeterminada en uno de sus grupos de recursos existentes, si hay alguno disponible.
    
    Use el botón __Seleccionar__ para guardar el grupo de recursos.

6. Seleccione **Credenciales** y, a continuación, especifique un **Nombre de usuario de inicio de sesión de clúster** y una **Contraseña de inicio de sesión de clúster**.

    Escriba un __Nombre de usuario de SSH__ y seleccione __Contraseña__, después escriba la __Contraseña de SSH__ para configurar la cuenta de SSH. SSH se usa para conectarse de forma remota al clúster con un cliente de Secure Shell (SSH).
    
    Use el botón __Seleccionar__ para guardar las credenciales.
    
    ![Hoja Credenciales](./media/hdinsight-getting-started-with-r/clustercredentials.png)

7. Seleccione **Origen de datos** para seleccionar un origen de datos del clúster. Seleccione una cuenta de almacenamiento existente mediante la selección de __Seleccionar cuenta de almacenamiento__ y después seleccione la cuenta o cree una nueva cuenta con el vínculo __Nuevo__ de la sección __Seleccionar cuenta de almacenamiento__.

    Si selecciona __Nuevo__, debe escribir un nombre para la nueva cuenta de almacenamiento. Si el nombre está aceptado, aparecerá una marca de comprobación verde.

    El __Contenedor predeterminado__ será de forma predeterminada el nombre del clúster. Déjelo como valor.
    
    Seleccione __Ubicación__ para seleccionar la región en la que crear la cuenta de almacenamiento.
    
    > [AZURE.IMPORTANT] Seleccionar la ubicación del origen de datos predeterminado también establecerá la ubicación del clúster de HDInsight. El origen de datos predeterminado y el clúster deben encontrarse en la misma región.

    Use el botón **Seleccionar** para guardar la configuración del origen de datos.
    
    ![Hoja Origen de datos](./media/hdinsight-getting-started-with-r/datastore.png)

8. Seleccione **Planes de tarifa de nodo** para mostrar información sobre los nodos que se crearán para este clúster. A menos que sepa que necesitará un clúster de mayor tamaño, deje el número de nodos de trabajo en el valor predeterminado de `4`. El costo estimado del clúster se mostrará en la hoja.

    ![Hoja Niveles de precios de nodo](./media/hdinsight-getting-started-with-r/pricingtier.png)

    Use el botón **Seleccionar** para guardar la configuración de precios del nodo.
    
9. En la hoja **Nuevo clúster de HDInsight**, asegúrese de que la opción **Anclar a Panel de inicio** está seleccionada y después seleccione **Crear**. Esto creará el clúster y agregará un icono para él en el panel de inicio de su Portal de Azure. El icono indicará que el clúster se está creando y, cuando se haya completado el proceso, cambiará para mostrar el icono de HDInsight.

    | Al crear | Creación completa |
    | ------------------ | --------------------- |
    | ![Indicador de creación en el Panel de inicio](./media/hdinsight-getting-started-with-r/provisioning.png) | ![Icono de clúster creado](./media/hdinsight-getting-started-with-r/provisioned.png) |

    > [AZURE.NOTE] El clúster tardará algo de tiempo en crearse, normalmente unos 15 minutos. Use el icono del Panel de inicio o la entrada **Notificaciones** de la izquierda de la página para comprobar el proceso de creación.

## Conectarse al nodo perimetral del servidor de R

Conectarse al nodo perimetral del servidor de R del clúster de HDInsight con SSH:

    ssh USERNAME@rserver.CLUSTERNAME.ssh.azurehdinsight.net
    
> [AZURE.NOTE] También puede encontrar la dirección de `RServer.CLUSTERNAME.ssh.azurehdinsight.net` en el Portal de Azure al seleccionar su clúster y después __Todas las configuraciones__, __Aplicaciones__ y __RServer__. Se mostrará la información del punto de conexión de SSH para el nodo perimetral.
>
> ![Imagen del punto de conexión SSH del nodo perimetral](./media/hdinsight-getting-started-with-r/sshendpoint.png)
    
Si usó una contraseña para proteger la cuenta de usuario SSH, se le pedirá la contraseña. Si usa una clave pública, tal vez tenga que usar el parámetro `-i` para especificar la ruta de acceso a la correspondiente clave privada. Por ejemplo: `ssh -i ~/.ssh/id_rsa USERNAME@RServer.CLUSTERNAME.ssh.azurehdinsight.net`.
    
Para obtener más información sobre el uso de SSH con HDInsight basado en Linux, vea los siguientes artículos:

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Una vez conectado, llegará a un símbolo de sistema similar al siguiente.

    username@ed00-myrser:~$

## Usar la consola de R

1. En la sesión SSH, use el siguiente comando para iniciar la consola de R.

        R
    
    Verá un resultado similar al siguiente.
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.

        >

2. Desde el símbolo de sistema de `>`, puede escribir el código de R. El servidor de R incluye paquetes que le permiten interactuar con Hadoop y ejecutar cálculos distribuidos fácilmente. Por ejemplo, use el siguiente comando para ver la raíz del sistema de archivos predeterminado del clúster de HDInsight.

        rxHadoopListFiles("/")
    
    También puede usar el direccionamiento de estilo WASB.
    
        rxHadoopListFiles("wasb:///")

##Usar un contexto de proceso

Un contexto de proceso le permite controlar si un cálculo se realizará de forma local en el nodo perimetral, o si se distribuirá en los nodos del clúster de HDInsight.
        
1. Desde la consola de R, use las opciones siguientes para cargar datos de ejemplo en el almacenamiento predeterminado de HDInsight.

        # Set the NameNode and port for the cluster
        myNameNode <- "default"
        myPort <- 0
        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"
        # Source for the data to load
        source <- system.file("SampleData/AirlineDemoSmall.csv", package="RevoScaleR")
        # Directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirlineDemoSmall") 
        # Make the directory
        rxHadoopMakeDir(inputDir)
        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, inputDir)

2. A continuación, vamos a crear algunos factores y definir un origen de datos de modo que podamos trabajar con los datos.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, 
                                   port=myPort)
        # Create Factors for the days of the week
        colInfo <- list(DayOfWeek = list(type = "factor",
             levels = c("Monday", 
                        "Tuesday", 
                        "Wednesday", 
                        "Thursday", 
                        "Friday", 
                        "Saturday", 
                        "Sunday")))
        # Define the data source
        airDS <- RxTextData(file = inputDir, 
                            missingValueString = "M",
                            colInfo  = colInfo, 
                            fileSystem = hdfsFS)

3. Vamos a ejecutar una regresión lineal a través de los datos con el contexto de proceso local.

        # Set a local compute context
        rxSetComputeContext("local")
        # Run a linear regression
        system.time(
            modelLocal <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek,
                                   data = airDS)
        )
        # Display a summary 
        summary(modelLocal) 

    Debería ver una salida que finalice con líneas similares a las siguientes.
    
        Residual standard error: 40.39 on 582620 degrees of freedom
        Multiple R-squared: 0.01465
        Adjusted R-squared: 0.01464
        F-statistic:  1238 on 7 and 582620 DF,  p-value: < 2.2e-16
        Condition number: 10.6542

4. A continuación, vamos a ejecutar la misma regresión lineal usando el contexto de Spark. El contexto de Spark distribuirá el procesamiento en todos los nodos de trabajo del clúster de HDInsight.

        # Define the Spark compute context 
        mySparkCluster <- RxSpark(consoleOutput=TRUE) 
        # Set the compute context 
        rxSetComputeContext(mySparkCluster) 
        # Run a linear regression 
        system.time(  
            modelSpark <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS) 
        )
        # Display a summary
        summary(modelSpark)

    El resultado del procesamiento de Spark se escribe en la consola, dado que hemos establecido `consoleOutput=TRUE`.
    
    > [AZURE.NOTE] También puede usar MapReduce para distribuir el cálculo en los nodos del clúster. Para obtener más información sobre el contexto de proceso, consulte [Compute context options for R Server on HDInsight premium (Opciones de contexto de proceso del servidor de R en HDInsight Premium)](hdinsight-hadoop-r-server-compute-contexts.md).

##Distribuir el código de R a varios nodos

Con el servidor de R puede seleccionar fácilmente el código de R existente y ejecutarlo en varios nodos del clúster mediante `rxExec`. Esto es útil cuando se realiza un barrido de parámetros o simulaciones. Lo que se muestra a continuación es un ejemplo de cómo usar `rxExec`.

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )
    
Si todavía usa el contexto de Spark o MapReduce, esto devolverá el valor nodename para los nodos de trabajo en los que se ejecuta el código (`Sys.info()["nodename"]`). Por ejemplo, en un clúster de cuatro nodos, puede recibir un resultado similar al siguiente.

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"

##Instalar paquetes de R

Si quiere instalar los paquetes de R adicionales en el nodo perimetral, puede usar `install.packages()` directamente desde la consola de R cuando se conecta al nodo perimetral a través de SSH. Sin embargo, si necesita instalar paquetes de R en los nodos de trabajo del clúster, debe usar Acción de script.

Acciones de script son scripts de Bash que se usan para realizar cambios en la configuración del clúster de HDInsight o para instalar software adicional. En este caso, para instalar paquetes de R adicionales. Para instalar paquetes adicionales con una Acción de script, use los pasos siguientes.

> [AZURE.IMPORTANT] Usar Acciones de script para instalar paquetes de R adicionales solo se permite una vez creado el clúster. No debe usarse durante la creación del clúster, ya que el script cuenta con que el servidor de R esté completamente configurado e instalado.

1. En el [Portal de Azure](https://portal.azure.com), seleccione su servidor de R en el clúster de HDInsight.

2. En la hoja del clúster, seleccione __Todas las configuraciones__ y luego __Acciones de script__. Desde la hoja __Acciones de script__, seleccione __Enviar nuevo__ para enviar una nueva Acción de script.

    ![Imagen de la hoja Acciones de script](./media/hdinsight-getting-started-with-r/newscriptaction.png)

3. Desde la hoja __Enviar acción de script__, proporcione la siguiente información.

    * __Nombre__: un nombre descriptivo que se use para identificar este script
    * __Identificador URI del script de Bash__: http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh
    * __Principal__: debe estar __desactivado__
    * __Trabajo__: debe estar __activado__
    * __Zookeeper__: debe estar __desactivado__
    * __Parámetros__: paquetes de R que se deben instalar. Por ejemplo, `bitops stringr arules`
    * __Conservar este script...__: debe estar __activado__
    
    > [AZURE.IMPORTANT] Si los paquetes de R que instale necesitan que se agreguen bibliotecas del sistema, entonces debe descargar el script base usado aquí y agregar los pasos para instalar las bibliotecas del sistema. A continuación, debe cargar el script modificado a un contenedor de blobs público en el almacenamiento de Azure y usar el script modificado para instalar los paquetes.
    >
    >Para obtener más información sobre el desarrollo de Acciones de Script, consulte [Desarrollo de la acción de script](hdinsight-hadoop-script-actions-linux.md).
    
    ![Agregar una Acción de script](./media/hdinsight-getting-started-with-r/scriptaction.png)

4. Seleccione __Crear__ para ejecutar el script. Una vez que finalice el script, los paquetes de R estarán disponibles en todos los nodos de trabajo.
    
## Pasos siguientes

Ahora que sabe cómo crear un nuevo clúster de HDInsight que incluya un servidor de R y los aspectos básicos del uso de la consola de R desde una sesión de SSH, use las siguientes opciones para descubrir otras formas de trabajar con el servidor de R en HDInsight.

- [Add RStudio Server to HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md) (Agregar un servidor de RStudio a HDInsight Premium)

- [Compute context options for R Server on HDInsight premium](hdinsight-hadoop-r-server-compute-contexts.md) (Opciones de contexto de proceso del servidor de R en HDInsight Premium)

- [Azure Storage options for R Server on HDInsight Premium](hdinsight-hadoop-r-server-storage.md) (Opciones de almacenamiento de Azure del servidor de R en HDInsight Premium)

### Plantillas de Azure Resource Manager

Si le interesa automatizar la creación del servidor de R en HDInsight con plantillas de Azure Resource Manager, consulte las siguientes plantillas de ejemplo.

* [Crear un servidor de R en un clúster de HDInsight con una clave pública de SSH](http://go.microsoft.com/fwlink/p/?LinkID=780809)
* [Crear un servidor de R en un clúster de HDInsight con una contraseña de SSH](http://go.microsoft.com/fwlink/p/?LinkID=780810)

Ambas plantillas crean un nuevo clúster de HDInsight y una cuenta de almacenamiento asociada, y se pueden usar desde la CLI de Azure, Azure PowerShell o el Portal de Azure.

Para obtener información general acerca del uso de las plantillas de ARM, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

<!---HONumber=AcomDC_0420_2016-->