---
title: "Instalación de aplicación publicada: StreamSets Data Collector - Azure HDInsight | Microsoft Docs"
description: "Instale y use la aplicación de Hadoop de terceros StreamSets Data Collector."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 90775452c58457ae8ecc73687a375606474158f5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---streamsets-data-collector"></a>Instalación de aplicación publicada: StreamSets Data Collector

En este artículo se describe cómo instalar y ejecutar la aplicación de Hadoop publicada [StreamSets Data Collector](https://streamsets.com/) en Azure HDInsight. Para información general de la plataforma de aplicaciones HDInsight y una lista de las aplicaciones publicadas de fabricantes de software independiente (ISV) disponibles, consulte [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md). Para obtener instrucciones sobre cómo instalar su propia aplicación, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>Información sobre StreamSets Data Collector

StreamSets Data Collector se implementa sobre una aplicación Azure HDInsight. StreamSets Data Collector proporciona un entorno de desarrollo integrado (IDE) completo que permite diseñar, probar, implementar y administrar las canalizaciones de ingesta universal. Estas canalizaciones pueden crear una malla de datos de flujo y por lotes, e incluyen una variedad de transformaciones internas de flujo, sin tener que escribir código personalizado.

StreamSets Data Collector permite crear flujos de datos con mediante diversos componentes de macrodatos como HDFS, Kafka, Solr, Hive, HBASE y Kudu. Una vez que StreamSets Data Collector se ejecuta en un servidor perimetral, o en el clúster de Hadoop, se obtiene supervisión en tiempo real tanto de las anomalías de los datos como de las operaciones de flujo de datos. Esta supervisión incluye las alertas basadas en umbrales, la detección de anomalías y la corrección automática de registros de errores.

StreamSets Data Collector está diseñado para aislar de manera lógica cada fase de una canalización, por lo que puede cumplir con los requisitos de negocio nuevos al colocarla en procesadores y conectores nuevos sin necesidad de codificación y con un tiempo de inactividad mínimo.

### <a name="streamsets-resource-links"></a>Vínculos de recursos de StreamSets

* [Documentación](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Tutoriales](https://github.com/streamsets/tutorials)
* [Foro de soporte técnico Developer](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Slack Public StreamSets Channel](https://streamsetters.slack.com/) (Canal Slack Public StreamSets)
* [Código fuente](https://github.com/streamsets)

## <a name="prerequisites"></a>requisitos previos

Para instalar esta aplicación en un clúster de HDInsight nuevo o en un clúster existente, debe tener la configuración siguiente:

* Niveles de clúster: estándar o Premium
* Versiones del clúster: 3.5 y posteriores

## <a name="install-the-streamsets-data-collector-published-application"></a>Instalación de la aplicación publicada StreamSets Data Collector

Para instrucciones paso a paso sobre cómo instalar esta aplicación y otras aplicaciones de ISV disponibles, lea [Instalación de aplicaciones de Hadoop de terceros](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Inicio de StreamSets Data Collector

1. Después de la instalación, puede iniciar StreamSets desde su clúster en Azure Portal en el panel **Configuración** y, luego, seleccione **Aplicaciones** en la categoría **General**. En el panel Aplicaciones instaladas aparecen las aplicaciones instaladas.

    ![Aplicación StreamSets instalada](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Cuando seleccione StreamSets Data Collector, verá un vínculo a la página web y la ruta de acceso del punto de conexión SSH. Seleccione el vínculo WEBPAGE.

3. En el cuadro de diálogo Iniciar sesión, use estas credenciales para iniciar sesión: `admin` y `admin`.

4. En la página de introducción, haga clic en **Create pipeline** (Crear canalización).

    ![Crear canalización](./media/hdinsight-apps-install-streamsets/get-started.png)

5. En la ventana New Pipeline (Canalización nueva), escriba un nombre para la canalización ("Hola mundo"), puede escribir una descripción y seleccionar **Save** (Guardar).

6. Aparece la consola de Data Collector. El panel de propiedades muestra las propiedades de la canalización.
 
    ![Consola Data Collector](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Ya está listo para seguir el [tutorial de StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). El tutorial proporciona instrucciones detalladas para crear la primera canalización.

## <a name="next-steps"></a>pasos siguientes

* [Documentación de StreamSets Data Collector](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
* [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.
