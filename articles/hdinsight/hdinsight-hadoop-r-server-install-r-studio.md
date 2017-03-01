---
title: "Instalación de RStudio con R Server en HDInsight | Microsoft Docs"
description: "Cómo instalar RStudio con R Server en HDInsight."
services: hdinsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2df17cddf629cb72b7fa4d590dfaa69311c96aa4
ms.openlocfilehash: 8a73f04b0b2d6e308f7b2888ef14d48c50c13ce1
ms.lasthandoff: 01/10/2017


---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Instalación de RStudio con R Server en HDInsight
Actualmente existen varios entornos de desarrollo integrado (IDE) disponibles para R, entre los que se incluyen el recién anunciado [R Tools para Visual Studio (RTVS)](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) de Microsoft, una familia de herramientas de escritorio y servidor de [RStudio](https://www.rstudio.com/products/rstudio-server/), o el IDE [StatET](http://www.walware.de/goto/statet) basado en Eclipse de Walware. Uno de los más usados en Linux es [RStudio Server](https://www.rstudio.com/products/rstudio-server/), que ofrece un IDE basado en explorador que pueden usar los clientes remotos.  Si RStudio Server se instala en el nodo perimetral de un clúster de HDInsight, ofrece una experiencia de IDE completa para el desarrollo y la ejecución de scripts de R con R Server en el clúster. Además, puede ser notablemente más productivo en comparación con el uso predeterminado de la consola de R.

> [!NOTE]
> El procedimiento descrito en este artículo solo es pertinente si no seleccionó instalar la edición community de RStudio Server al aprovisionar el clúster.  Si la agregó durante el aprovisionamiento, puede acceder a ella haciendo clic en el icono **R Server Dashboards** (Paneles de R Server) en la entrada de Azure Portal para el clúster y, a continuación, en el icono **R Studio Server**. 

En este artículo aprenderá a instalar la versión de la comunidad (gratuita) de RStudio Server en el nodo perimetral de un clúster mediante un script personalizado. Si prefiere la versión Pro con licencia comercial de RStudio Server, debe seguir las instrucciones de instalación de [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [!NOTE]
> Los pasos de este documento requieren R Server en clúster de HDInsight y no funcionarán correctamente si está usando un clúster de HDInsight donde R se instaló mediante la [acción de script de instalación de R](hdinsight-hadoop-r-scripts-linux.md).
>
> 

## <a name="prerequisites"></a>Requisitos previos
* Un clúster de HDInsight de Azure con servidor de R instalado. Para obtener instrucciones, consulte [Get started with R Server on HDInsight clusters](hdinsight-hadoop-r-server-get-started.md)(Introducción a R Server en clústeres de HDInsight).
* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el comando `ssh` se proporciona con el sistema operativo. En Windows, se recomienda [Cygwin](http://www.redhat.com/services/custom/cygwin/) con la [opción OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU) o [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Instalación de RStudio en el clúster mediante un script personalizado
1. Identifique el nodo perimetral del clúster. Para un clúster de HDInsight con servidor de R, la siguiente es la convención de nomenclatura para el nodo principal y el nodo perimetral.

   * Nodo principal `CLUSTERNAME-ssh.azurehdinsight.net`
   * Nodo perimetral `CLUSTERNAME-ed-ssh.azurehdinsight.net` 
2. SSH en el nodo perimetral del clúster mediante el patrón de nomenclatura anterior. 

   * Si se conecta desde un cliente Linux, consulte [Conexión a un clúster de HDInsight basado en Linux](hdinsight-hadoop-linux-use-ssh-unix.md).
   * Si se conecta desde un cliente Windows, consulte [Conexión a un clúster de HDInsight basado en Linux mediante PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md).
3. Una vez conectado, se convierte en un usuario raíz en el clúster. En la sesión SSH, use el comando siguiente.

        sudo su -
4. Descargue el script personalizado para instalar RStudio. Use el comando siguiente.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh
5. Cambie los permisos en el archivo de script personalizado y ejecute el script. Use los comandos siguientes.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh
6. Si utiliza una contraseña SSH al crear un clúster de HDInsight con el servidor de R, puede omitir este paso y continuar en el siguiente. Si en su lugar utiliza una clave SSH para crear el clúster, debe establecer una contraseña para el usuario SSH. Necesitará esta contraseña para conectarse a RStudio. Ejecute los comandos siguientes: Cuando se le pida la **contraseña Kerberos actual**, basta con que presione **ENTRAR**.  Tenga en cuenta que debe reemplazar `USERNAME` por un usuario SSH de su clúster de HDInsight.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:

    Si la contraseña se estableció correctamente, verá un mensaje similar al siguiente.

        passwd: password updated successfully

    Salga de la sesión SSH.

7. Cree un túnel SSH al clúster asignando `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` en el clúster de HDInsight al equipo cliente. Debe crear un túnel SSH antes de abrir una nueva sesión del explorador.

   * En un cliente de Linux o de Windows con [Cygwin](http://www.redhat.com/services/custom/cygwin/) , abra una sesión de terminal y use el comando siguiente.

           ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Reemplace **USERNAME** por un usuario SSH para su clúster de HDInsight y **CLUSTERNAME** por el nombre de su clúster de HDInsight. También puede usar una clave SSH en lugar de una contraseña si agrega `-i id_rsa_key`.        
   * Si usa un cliente de Windows con PuTTY:

     1. Abra PuTTY y escriba la información de conexión. Si no está familiarizado con PuTTY, vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obtener información sobre cómo usarlo con HDInsight.
     2. En la sección **Category** (Categoría) a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Tunnels** (Túneles).
     3. Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puertos SSH):

        * **Source port** : el puerto en el cliente que desea desviar. Por ejemplo, **8787**.
        * **Destino** : el destino que debe asignarse al equipo cliente local. Por ejemplo, **localhost:8787**.

        ![Creación de un túnel de SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Creación de un túnel de SSH")
     4. Haga clic en **Add** (Agregar) para agregar la configuración y, a continuación, en **Open** (Abrir) para abrir una conexión SSH.
     5. Cuando se le solicite, inicie sesión en el servidor. Esto establecerá una sesión SSH y habilitará el túnel.
8. Abra un explorador web y escriba la siguiente dirección URL basada en el puerto especificado para el túnel.

        http://localhost:8787/ 
9. Se le pedirá que escriba el nombre de usuario SSH y la contraseña para conectarse al clúster. Si usó una clave SSH al crear el clúster, debe escribir la contraseña que creó en el paso 5 anterior.

    ![Conexión a R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Creación de un túnel de SSH")
10. Para probar si la instalación de RStudio fue correcta, puede ejecutar un script de prueba que ejecute trabajos MapReduce y Spark basados en R en el clúster. Vuelva a la consola SSH y escriba los comandos siguientes para descargar el script de prueba para su ejecución en RStudio.

*    Si creó un clúster de Hadoop con R, use este comando.

           wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
*    Si creó un clúster de Spark con R, use este comando.

                    wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r
11. En RStudio, verá el script de prueba que descargó. Haga doble clic en el archivo para abrirlo, seleccione el contenido del archivo y haga clic en **Ejecutar**. Debería ver el resultado en el panel **Consola** .

   ![Prueba de la instalación](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Prueba de la instalación")

Otra opción sería escribir `source(testhdi.r)` o `source(testhdi_spark.r)` para ejecutar el script.

## <a name="see-also"></a>Consulte también
* [Opciones de contexto de proceso del servidor de R en HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opciones de Azure Storage para R Server en HDInsight](hdinsight-hadoop-r-server-storage.md)


