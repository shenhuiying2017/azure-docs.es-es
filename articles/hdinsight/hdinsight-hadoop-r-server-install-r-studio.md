<properties
	pageTitle="Instalación de RStudio con R Server en HDInsight (versión preliminar) | Microsoft Azure"
	description="Instalación de RStudio con R Server en HDInsight (versión preliminar)."
	services="hdinsight"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/22/2016"
   ms.author="jeffstok"/>


# Instalación de RStudio con R Server en HDInsight (versión preliminar)

Actualmente existen varios entornos de desarrollo integrado (IDE) disponibles para R, entre los que se incluyen el recién anunciado [R Tools para Visual Studio](https://www.visualstudio.com/es-ES/features/rtvs-vs.aspx) (RTVS) de Microsoft, una familia de herramientas de escritorio y servidor de [RStudio](https://www.rstudio.com/products/rstudio-server/), o el IDE [StatET](http://www.walware.de/goto/statet) basado en Eclipse de Walware. Entre los más usados en Linux se incluye [RStudio Server](https://www.rstudio.com/products/rstudio-server/), que ofrece un IDE basado en explorador que pueden usar los clientes remotos. Si RStudio Server se instala en el nodo perimetral de un clúster de HDInsight Premium, ofrece una experiencia de IDE completa para el desarrollo y la ejecución de scripts de R con el servidor de R en el clúster. Además, puede ser notablemente más productivo que el uso predeterminado de la consola de R.

En este artículo aprenderá a instalar la versión de la comunidad (gratuita) de RStudio Server en el nodo perimetral de un clúster mediante un script personalizado. Si prefiere la versión Pro con licencia comercial de RStudio Server, debe seguir las instrucciones de instalación de [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] Los pasos de este documento requieren R Server en clúster de HDInsight y no funcionarán correctamente si está usando un clúster de HDInsight donde R se instaló mediante la [acción de script de instalación de R](hdinsight-hadoop-r-scripts-linux.md).

## Requisitos previos

* Un clúster de HDInsight de Azure con servidor de R instalado. Para obtener instrucciones, consulte [Get started with R Server on HDInsight clusters](hdinsight-hadoop-r-server-get-started.md) (Introducción a R Server en clústeres de HDInsight).
* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el comando `ssh` se proporciona con el sistema operativo. Para Windows, se recomienda [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).


## Instalación de RStudio en el clúster mediante un script personalizado

1. Identifique el nodo perimetral del clúster. Para un clúster de HDInsight con servidor de R, la siguiente es la convención de nomenclatura para el nodo principal y el nodo perimetral.

	* Nodo principal: `CLUSTERNAME-ssh.azurehdinsight.net`
	* Nodo perimetral: `R-Server.CLUSTERNAME-ssh.azurehdinsight.net`

2. SSH en el nodo perimetral del clúster mediante el patrón de nomenclatura anterior.
 
	* Si se conecta desde un cliente de Linux, consulte [Conexión a un clúster de HDInsight basado en Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
	* Si se conecta desde un cliente de Windows, consulte [Conexión a un clúster de HDInsight basado en Linux mediante PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Una vez conectado, se convierte en un usuario raíz en el clúster. En la sesión SSH, use el comando siguiente.

		sudo su -

4. Descargue el script personalizado para instalar RStudio. Use el comando siguiente.

		wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Cambie los permisos en el archivo de script personalizado y ejecute el script. Use los comandos siguientes.

		chmod 755 InstallRStudio.sh
		./InstallRStudio.sh

6. Si utiliza una contraseña SSH al crear un clúster de HDInsight con el servidor de R, puede omitir este paso y continuar en el siguiente. Si en su lugar utiliza una clave SSH para crear el clúster, debe establecer una contraseña para el usuario SSH. Necesitará esta contraseña para conectarse a RStudio. Ejecute los comandos siguientes: Cuando se le pida la **contraseña Kerberos actual**, simplemente presione **INTRO**. Tenga en cuenta que debe reemplazar `USERNAME` por un usuario SSH de su clúster de HDInsight.

		passwd USERNAME
		Current Kerberos password:
		New password:
		Retype new password:
		Current Kerberos password:
		
	Si la contraseña se estableció correctamente, verá un mensaje similar al siguiente.

		passwd: password updated successfully


	Salga de la sesión SSH.

7. Cree un túnel SSH al clúster asignando `localhost:8787` en el clúster de HDInsight al equipo cliente. Debe crear un túnel SSH antes de abrir una nueva sesión del explorador.

	* En un cliente de Linux o de Windows (mediante [Cygwin](http://www.redhat.com/services/custom/cygwin/)), abra una sesión de terminal y use el comando siguiente.

			ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
			
		Reemplace **USERNAME** por un usuario SSH para su clúster de HDInsight y **CLUSTERNAME** por el nombre de su clúster de HDInsight. También puede utilizar una clave SSH en lugar de una contraseña agregando `-i id_rsa_key`.

	* En un cliente de Windows, cree un túnel SSH con PuTTY.

		1.  Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usarlo con HDInsight.
		2.  En la sección **Category** (Categoría) a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Tunnels** (Túneles).
		3.  Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puertos SSH):

			* **Source port**: el puerto en el cliente que desea desviar. Por ejemplo, **8787**.
			* **Destino**: el destino que debe asignarse al equipo cliente local. Por ejemplo, **localhost:8787**.

			![Creación de un túnel SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Creación de un túnel SSH")

		4. Haga clic en **Add** (Agregar) para agregar la configuración y, a continuación, en **Open** (Abrir) para abrir una conexión SSH.
		5. Cuando se le solicite, inicie sesión en el servidor. Esto establecerá una sesión SSH y habilitará el túnel.

8. Abra un explorador web y escriba la siguiente dirección URL basada en el puerto especificado para el túnel.

		http://localhost:8787/ 

9. Se le pedirá que escriba el nombre de usuario SSH y la contraseña para conectarse al clúster. Si usó una clave SSH al crear el clúster, debe escribir la contraseña que creó en el paso 5 anterior.

	![Conexión a R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Creación de un túnel SSH")

10. Para probar si la instalación de RStudio fue correcta, puede ejecutar un script de prueba que ejecute trabajos MapReduce y Spark basados en R en el clúster. Vuelva a la consola SSH y escriba los comandos siguientes para descargar el script de prueba para su ejecución en RStudio.

	* Si creó un clúster de Hadoop con R, use este comando.
		
			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

	* Si creó un clúster de Spark con R, use este comando.

			wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. En RStudio, verá el script de prueba que descargó. Haga doble clic en el archivo para abrirlo, seleccione el contenido del archivo y haga clic en **Ejecutar**. Debería ver el resultado en el panel **Consola**.
 
	![Prueba de la instalación](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Prueba de la instalación")

Otra opción sería escribir `source(testhdi.r)` o `source(testhdi_spark.r)` para ejecutar el script.

## Otras referencias

- [Opciones de contexto de proceso del servidor de R en HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure Storage options for R Server on HDInsight Premium (Opciones de almacenamiento de Azure del servidor de R en HDInsight Premium)](hdinsight-hadoop-r-server-storage.md)


 

<!---HONumber=AcomDC_0824_2016-->