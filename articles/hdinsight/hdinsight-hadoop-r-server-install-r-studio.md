---
title: "Instalación de RStudio con R Server en HDInsight: Azure | Microsoft Docs"
description: "Cómo instalar RStudio con R Server en HDInsight."
services: hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 918abb0d-8248-4bc5-98dc-089c0e007d49
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 416420d855505508735ebd8526e93efdb230ad53
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="installing-rstudio-with-r-server-on-hdinsight"></a>Instalación de RStudio con R Server en HDInsight

En este artículo se describe cómo instalar la versión de la comunidad (gratuita) de [RStudio Server](https://www.rstudio.com/products/rstudio-server/) en el nodo perimetral de un clúster mediante un script personalizado. RStudio Server proporciona un IDE basado en explorador que pueden usar los clientes remotos y se utiliza ampliamente en Linux. Hoy en día, hay varios entornos de desarrollo integrados (IDE) disponibles para R, incluidos los siguientes:

- [Herramientas de R para Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTVS) de Microsoft 
- [RStudio Server](https://www.rstudio.com/products/rstudio-server/) 
- [StatET](http://www.walware.de/goto/statet) basado en Eclipse de Walware.

La ventaja de instalar RStudio Server en el nodo perimetral de un clúster de HDInsight es que ofrece una experiencia de IDE completa para desarrollar y ejecutar scripts de R con R Server en el clúster. Esta configuración puede ser mucho más productiva que el uso predeterminado de la consola de R.

> [!NOTE]
> El procedimiento descrito en este artículo solo es pertinente si no seleccionó instalar la edición community de RStudio Server al aprovisionar el clúster. Si la agregó durante el aprovisionamiento, puede acceder a ella haciendo clic en el icono **Paneles de R Server** en la entrada de Azure Portal para el clúster y luego en el icono **R Studio Server**. 

Si prefiere utilizar la versión Pro con licencia comercial de RStudio Server, debe seguir las instrucciones de instalación de [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/).

> [!NOTE]
> Si está usando un clúster de HDInsight para el que R se instaló mediante la [acción de script de instalación de R](hdinsight-hadoop-r-scripts-linux.md), los pasos de este documento no funcionarán correctamente, ya que requieren R Server en clúster de HDInsight.
>
> 

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight de Azure con servidor de R instalado. Para obtener instrucciones, consulte [Get started with R Server on HDInsight clusters](hdinsight-hadoop-r-server-get-started.md)(Introducción a R Server en clústeres de HDInsight).
* Un cliente SSH. Para las distribuciones de Linux y Unix o Macintosh OS X, el comando `ssh` se proporciona con el sistema operativo. En Windows, se recomienda [Cygwin](http://www.redhat.com/services/custom/cygwin/) con la [opción OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU) o [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  

## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Instalación de RStudio en el clúster mediante un script personalizado

Estos son los pasos que se deben seguir:

1. Identifique el nodo perimetral del clúster. Para un clúster de HDInsight con servidor de R, la siguiente es la convención de nomenclatura para el nodo principal y el nodo perimetral.
   * Nodo principal `CLUSTERNAME-ssh.azurehdinsight.net`
   * Nodo perimetral `CLUSTERNAME-ed-ssh.azurehdinsight.net` 

2. SSH en el nodo perimetral del clúster mediante el patrón de nomenclatura proporcionado en el paso 1. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Una vez conectado, se convierte en un usuario raíz en el clúster. En la sesión SSH, use el comando siguiente:

        sudo su -

4. Descargue el script personalizado para instalar RStudio. Use el comando siguiente:

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Cambie los permisos en el archivo de script personalizado y ejecute el script. Use los comandos siguientes:

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Si utiliza una contraseña SSH al crear un clúster de HDInsight con el servidor de R, puede omitir este paso y continuar en el siguiente. Si en su lugar utiliza una clave SSH para crear el clúster, debe establecer una contraseña para el usuario SSH. Necesita esta contraseña para conectarse a RStudio. Ejecute los comandos siguientes:

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:


7. Cuando se le pida la **contraseña Kerberos actual**, presione **ENTRAR**.  Tenga en cuenta que debe reemplazar `USERNAME` por un usuario SSH de su clúster de HDInsight. Si la contraseña se estableció correctamente, verá el siguiente mensaje:

        passwd: password updated successfully

    Salga de la sesión SSH.

8. Cree un túnel SSH al clúster asignando `ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` en el clúster de HDInsight al equipo cliente. Debe crear un túnel SSH antes de abrir una nueva sesión del explorador.

   * En un cliente de Linux o de Windows con [Cygwin](http://www.redhat.com/services/custom/cygwin/), abra una sesión de terminal y use el comando siguiente:

             ssh -L localhost:8787:localhost:8787 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

       Reemplace **USERNAME** por un usuario SSH para su clúster de HDInsight y reemplace **CLUSTERNAME** por el nombre de su clúster de HDInsight.
       También puede usar una clave SSH en lugar de una contraseña agregando `-i id_rsa_key`.        
   * Si usa un cliente de Windows con PuTTY:

     1. Abra PuTTY y escriba la información de conexión.
     2. En la sección **Category** (Categoría) a la izquierda del cuadro de diálogo, expanda **Connection** (Conexión), **SSH** y, a continuación, seleccione **Tunnels** (Túneles).
     3. Proporcione la siguiente información en el formulario **Options controlling SSH port forwarding** (Opciones que controlan el desvío de puertos SSH):

        * **Source port** : el puerto en el cliente que desea desviar. Por ejemplo, **8787**.
        * **Destino** : el destino que debe asignarse al equipo cliente local. Por ejemplo, **localhost:8787**.

            ![Creación de un túnel de SSH](./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Creación de un túnel de SSH")

     4. Haga clic en **Add** (Agregar) para agregar la configuración y, a continuación, en **Open** (Abrir) para abrir una conexión SSH.
     5. Cuando se le solicite, inicie sesión en el servidor para establecer una sesión de SSH y habilite el túnel.

9. Abra un explorador web y escriba la siguiente dirección URL basada en el puerto especificado para el túnel:

        http://localhost:8787/ 

10. Se le pedirá que escriba el nombre de usuario SSH y la contraseña para conectarse al clúster. Si usó una clave SSH al crear el clúster, debe escribir la contraseña que creó en el paso 5.

    ![Conexión a R Studio](./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Creación de un túnel de SSH")

11. Para probar si la instalación de RStudio fue correcta, puede ejecutar un script de prueba que ejecute trabajos MapReduce y Spark basados en R en el clúster. Para descargar el script de prueba para su ejecución en RStudio, vuelva a la consola SSH y escriba los comandos siguientes:

    *    Si creó un clúster de Hadoop con R, use este comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r
    *    Si creó un clúster de Spark con R, use este comando:

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

12. En RStudio, verá el script de prueba que descargó. Haga doble clic en el archivo para abrirlo, seleccione el contenido del archivo y haga clic en **Ejecutar**. Debería ver la salida en el panel **Consola**:

   ![Prueba de la instalación](./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Prueba de la instalación")

Otra opción sería escribir `source(testhdi.r)` o `source(testhdi_spark.r)` para ejecutar el script.

## <a name="see-also"></a>Consulte también

* [Opciones de contexto de proceso del servidor de R en HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)
* [Opciones de Azure Storage para R Server en HDInsight](hdinsight-hadoop-r-server-storage.md)

