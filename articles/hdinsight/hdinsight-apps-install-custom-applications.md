<properties
   	pageTitle="Instalación de aplicaciones de Hadoop en HDInsight | Microsoft Azure"
   	description="Obtenga información acerca de cómo instalar aplicaciones de HDInsight en aplicaciones de HDInsight."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="05/18/2016"
   	ms.author="jgao"/>

# Instalación de aplicaciones de HDInsight personalizadas

Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight basado en Linux. Estas aplicaciones puede desarrollarlas Microsoft, fabricantes de software independientes (ISV) o el propio usuario. En este artículo, aprenderá a instalar una aplicación de HDInsight que no se ha publicado en el Portal de Azure en HDInsight. La aplicación que instalará es [Hue](http://gethue.com/).

Otros artículos relacionados:

- [Publish HDInsight applications into the Azure Marketplace](hdinsight-apps-publish-applications.md) (Publicar aplicaciones de HDInsight en Azure Marketplace): obtenga información acerca de cómo publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalar una aplicación de HDInsight): obtenga información acerca de cómo definir aplicaciones de HDInsight.

 
## Requisitos previos

Si desea instalar aplicaciones de HDInsight en un clúster de HDInsight existente, debe tener un clúster de HDInsight. Para crearlo, consulte [Crear clúster](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). También puede instalar aplicaciones de HDInsight al crear un clúster de HDInsight.


## Instalación de una aplicación de HDInsight

Las aplicaciones de HDInsight pueden instalarse cuando se crea un clúster o en un clúster de HDInsight existente. Para definir plantillas de ARM, consulte [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalar una aplicación de HDInsight).

Los archivos necesarios para implementar esta aplicación (Hue):

- [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): la plantilla de ARM para instalar una aplicación HDInsight. Consulte [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalar una aplicación de HDInsight) para desarrollar su propia plantilla de ARM.
- [hue-install\_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): la acción de script que se llama la plantilla de ARM para configurar el nodo del borde. 
- [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): el archivo binario de Hue que se llama desde hui install\_v0.sh. 
- [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): el archivo binario de Hue que se llama desde hui install\_v0.sh. 
- [webwasb tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): una aplicación web de ejemplo (Tomcat) que se llama desde hui install\_v0.sh.

**Para instalar Hue en un clúster de HDInsight existente**

1. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de ARM en el Portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/es-ES/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botón abre una plantilla de ARM en el Portal de Azure. La plantilla de ARM se encuentra en [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue). Para obtener información acerca de cómo escribir esta plantilla de ARM, consulte [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalar una aplicación de HDInsight).
    
2. En la hoja **Parámetros**, escriba lo siguiente:

    - **ClusterName**: escriba el nombre del clúster en el que desea instalar la aplicación. Debe ser un clúster existente.
    
3. Haga clic en **Aceptar** para guardar los parámetros.
4. En la hoja **Implementación personalizada**, escriba **Grupo de recursos**. El grupo de recursos es un contenedor que agrupa el clúster, la cuenta de almacenamiento dependiente y otros recursos. Es preciso usar el mismo grupo de recursos que el clúster.
5. Haga clic en **Condiciones legales** y luego en **Crear**.
6. Compruebe que la casilla **Anclar al panel** está activada y haga clic en **Crear**. El estado de la instalación se puede ver desde el icono anclado al panel del portal y la notificación del portal (haga clic en el icono de campana de la parte superior del portal). La aplicación tarda aproximadamente 10 minutos en instalarse.

**Para instalar Hue durante la creación de un clúster**

1. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de ARM en el Portal de Azure. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/es-ES/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Este botón abre una plantilla de ARM en el Portal de Azure. La plantilla de ARM que se usa en este tutorial se encuentra en un contenedor de blobs público, [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json). Para obtener información acerca de cómo escribir esta plantilla de ARM, consulte [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalar una aplicación de HDInsight).

2. Siga las instrucciones para crear un clúster e instalar Hue. Para más información acerca de cómo crear clústeres basados en HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## Validación de la instalación

El estado de la aplicación se puede comprobar en el Portal de Azure para validar la instalación de la aplicación. Además, también se pueden validar todos los puntos de conexión HTTP que han aparecido según lo previsto y la página web, si hay:

**Para abrir el portal de Hue**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda. Si no lo ve, haga clic en **Examinar** y, después, en **Clústeres de HDInsight**.
3. Haga clic en el clúster en el que instaló la aplicación.
4. En la hoja **Configuración**, haga clic en **Aplicaciones** en la categoría **General**. Verá **hue** en la hoja **Aplicaciones instaladas**.
5. Haga clic en **hue** en la lista para ver las propiedades.  
6. Haga clic en el vínculo de Página web para validar el sitio web; abra el punto de conexión HTTP en un explorador para validar la interfaz de usuario web de Hue y abra el punto de conexión SSH mediante [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md) u otros [clientes SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
 
## Solución de problemas de instalación

Consulte la sección [Solución de problemas](hdinsight-apps-install-applications.md#troubleshoot) de Instalación de aplicaciones de HDInsight.


## Pasos siguientes

- [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx) (MSDN: instalar una aplicación de HDInsight): obtenga información acerca de cómo desarrollar plantillas de ARM para implementar aplicaciones de HDInsight.
- [Publish HDInsight applications into the Azure Marketplace](hdinsight-apps-publish-applications.md) (Publicar aplicaciones de HDInsight en Azure Marketplace): obtenga información acerca de cómo publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
- [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md): obtenga información acerca de cómo usar una acción de script para instalar aplicaciones adicionales.
- [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): obtenga información general acerca de cómo llamar a plantillas de ARM para crear clústeres de HDInsight.

<!---HONumber=AcomDC_0518_2016-->