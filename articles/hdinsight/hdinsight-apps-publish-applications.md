---
title: "Publicación de aplicaciones de HDInsight | Microsoft Docs"
description: Aprenda a crear y publicar aplicaciones de HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/18/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 35a6c06bc4850f3fcfc6221d62998465f3b38251


---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publicación de aplicaciones de HDInsight en Azure Marketplace
Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight basado en Linux. Estas aplicaciones puede desarrollarlas Microsoft, fabricantes de software independientes (ISV) o el propio usuario. En este artículo, obtendrá información sobre cómo publicar una aplicación de HDInsight en Azure Marketplace.  Para obtener información general acerca de cómo publicar en Azure Marketplace, consulte [Publicación de una oferta en Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Las aplicaciones de HDInsight usan el modelo *Traiga su propia licencia (BYOL)* , donde el proveedor de la aplicación es el responsable de la concesión de licencias de la aplicación a los usuarios finales y Azure solo cobra a los usuarios finales los recursos que crean, como el clúster de HDInsight y sus máquinas virtuales o nodos. Actualmente, la facturación de la aplicación en sí no se realiza a través de Azure.

Artículo relacionado con otra aplicación de HDInsight:

* [Install HDInsight applications](hdinsight-apps-install-applications.md)(Instalación de aplicaciones de HDInsight): aprenda a instalar una aplicación de HDInsight en sus clústeres.
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a instalar y probar aplicaciones personalizadas de HDInsight.

## <a name="prerequisites"></a>Requisitos previos
Para enviar una aplicación personalizada a Marketplace, es preciso haberla creado y probado. Consulte los artículos siguientes:

* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a instalar y probar aplicaciones personalizadas de HDInsight.

También es preciso haber registrado una cuenta de desarrollador. Consulte [Publicación de una oferta en Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) y [Crear una cuenta de desarrollador de Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Definición de la aplicación
La publicación de aplicaciones en Azure Marketplace conlleva dos pasos.  En primer lugar, es preciso definir un archivo **createUiDef.json** para indicar con que clústeres es compatible la aplicación y, después, publicar la plantilla desde el Portal de Azure. Este es un archivo createUiDef.json de ejemplo.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| Campo | Description | Valores posibles |
| --- | --- | --- |
| types |Los tipos de clúster con los que es compatible la aplicación. |Hadoop, HBase, Storm y Spark (o cualquier combinación de éstos) |
| tiers |Los niveles de clúster con los que es compatible la aplicación. |Standard, Premium (o ambos) |
| versions |Los tipos de clúster de HDInsight con los que es compatible la aplicación. |3.4 |

## <a name="package-application"></a>Empaquetado de aplicación
Cree un archivo zip que contenga todos los archivos requeridos para instalar aplicaciones de HDInsight. Dicho archivo zip lo necesitará en [Publicación de la aplicación](#publish-application).

* [createUiDefinition.json](#define-application).
* mainTemplate.json. En [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md), puede encontrar un ejemplo.
  
  > [!IMPORTANT]
  > El nombre de los nombres de script de instalación de aplicación debe ser único para un clúster determinado y tener el formato siguiente. Además, cualquier acción de script de instalación y desinstalación debe ser idempotente, lo que significa que los scripts se pueden llamar de forma repetida y el resultado siempre será el mismo.
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > Tenga en cuenta que el nombre del script consta de tres partes:
  > 
  > 1. El prefijo del nombre del script, que incluirá el nombre de la aplicación o un nombre relevante para la aplicación.
  > 2. Un signo "-", para mejorar la legibilidad.
  > 3. Una función de cadena única con el nombre de la aplicación como parámetro.
  > 
  > El ejemplo anterior se acaba convirtiendo en: hue-install-v0-4wkahss55hlas en la lista de acciones de script persistentes. Para ver una carga de JSON de ejemplo, diríjase a [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 
  > 
* Todos los scripts requeridos.

> [!NOTE]
> Los archivos de aplicación (incluidos los archivos de aplicación web, en caso de que haya) se pueden ubicar en cualquier punto de conexión con acceso público.
> 
> 

## <a name="publish-application"></a>Publicación de la aplicación
Para publicar una aplicación de HDInsight, siga estos pasos:

1. Inicie sesión en el [Portal de publicación de Azure](https://publish.windowsazure.com/).
2. Haga clic en **Plantillas de solución** para crear una plantilla de solución.
3. Escriba un título y haga clic en **Create a new solution template** (Crear una plantilla de solución).
4. Haga clic en **Create Dev Center account and join the Azure program** (Crear cuenta del Centro de desarrollo y unirse al programa de Azure) para registrar una compañía, en caso de que no lo haya hecho.  Consulte [Crear una cuenta de desarrollador de Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Haga clic en **Define some Topologies to get Started**(Definir topologías para empezar). Una plantilla de solución es una "matriz" para todas sus topologías. Puede definir varias topologías en una oferta o plantilla de solución. Cuando se inserta una oferta en un entorno de ensayo, se inserta con todas sus topologías. 
6. Especifique el nombre de una topología y haga clic en el signo más.
7. Especifique una nueva versión y haga clic en el signo más.
8. Cargue el archivo zip preparado en [Empaquetado de aplicación](#package-application).  
9. Haga clic en **Request Certification**(Solicitar certificado). El equipo de certificación de Microsoft revisará los archivos y certificará la topología.

## <a name="next-steps"></a>Pasos siguientes
* [Install HDInsight applications](hdinsight-apps-install-applications.md)(Instalación de aplicaciones de HDInsight): aprenda a instalar una aplicación de HDInsight en sus clústeres.
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a utilizar acciones de script para instalar otras aplicaciones.
* [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): aprenda a llamar a plantillas de Resource Manager para crear clústeres de HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Utilización de nodos perimetrales vacíos en HDInsight): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.




<!--HONumber=Dec16_HO2-->


