---
title: "Instalación de aplicaciones de Hadoop de terceros en Azure HDInsight | Microsoft Docs"
description: Aprenda a instalar aplicaciones de Hadoop de terceros en Azure HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/16/2018
ms.author: jgao
ms.openlocfilehash: cfaad24e7bf1c38f3be1e13c88fc932be0bd502c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Instalación de aplicaciones de Hadoop de terceros en Azure HDInsight

Aprenda a instalar una aplicación de Hadoop de terceros en Azure HDInsight. Para obtener instrucciones sobre cómo instalar su propia aplicación, consulte [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md).

Una aplicación de HDInsight es una aplicación que los usuarios pueden instalar en un clúster de HDInsight. Estas aplicaciones puede desarrollarlas Microsoft, fabricantes de software independientes (ISV) o el propio usuario.  

En la lista siguiente se muestran las aplicaciones publicadas:

* **AtScale Intelligence Platform** activa su clúster de HDInsight en un servidor OLAP de escalabilidad horizontal. La aplicación permite consultar miles de millones de filas de datos de forma interactiva mediante las herramientas de BI, desde Microsoft Excel, Power BI y Tableau Software hasta QlikView.
* **Cask CDAP para HDInsight** proporciona la primera plataforma de integración unificada para macrodatos que reduce el tiempo de producción de aplicaciones de datos y lagos de datos en un 80 %. Esta aplicación solo admite con clústeres Standard HBase 3.4.
* **DATAIKU DDS on HDInsight** permite a los profesionales de datos crear prototipos, compilar e implementar servicios muy específicos que transforman datos sin procesar en predicciones de negocio de gran impacto.
* **H2O Artificial Intelligence para HDInsight (Beta)** H2O Sparkling Water admite los siguientes algoritmos distribuidos: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection y Autoencoders.
* **Plataforma de análisis de Kyligence** La plataforma de análisis de Kyligence (KAP) es un almacén de datos preparado para la empresa con tecnología Apache Kylin y Apache Hadoop; proporciona la latencia de consultas en fracciones de segundo en el conjunto de datos de gran escala y simplifica el análisis de datos para usuarios empresariales y analistas. 
* **Paxata Self-service Data Preparation**
* **Servidor de trabajo de Spark para KNIME Spark ejecutor** El servidor de trabajo de Spark para KNIME Spark Executor se utiliza para conectar la plataforma de análisis de KNIME a clústeres de HDInsight.
* **Streamsets Data Collector para HDnsight** proporciona un entorno de desarrollo integrado (IDE) con multitud de características que permite diseñar, probar, implementar y administrar canalizaciones de ingesta todos con todos que crean una malla de datos de transmisión y por lotes, e incluyen varias de transformaciones internas de transmisión (sin tener que escribir código personalizado). 
* **[Trifacta](http://www.trifacta.com/)** permite a los ingenieros de datos y analistas explorar y preparar los distintos datos de hoy en día más eficazmente gracias al aprendizaje automático, que proporcionar una experiencia de usuario, un flujo de trabajo y una arquitectura innovadores.
* **WANdisco Fusion HDI App** permite una conectividad coherente y continua a los datos a medida que cambian, dondequiera que se encuentren. Proporciona acceso a los datos en cualquier momento y lugar sin tiempos de inactividad ni interrupciones.

Para las instrucciones proporcionadas en este artículo se usa Azure Portal. También puede exportar la plantilla de Azure Resource Manager desde el portal u obtener una copia de dicha plantilla de proveedores y usar Azure PowerShell y CLI de Azure para implementarla.  Vea [Creación de clústeres de Hadoop en HDInsight con plantillas de Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>requisitos previos
Si desea instalar aplicaciones de HDInsight en un clúster de HDInsight existente, debe tener un clúster de HDInsight. Para crearlo, consulte [Crear clúster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). También puede instalar aplicaciones de HDInsight al crear un clúster de HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalación de aplicaciones en clústeres existentes
En el siguiente procedimiento, se muestra cómo instalar aplicaciones de HDInsight en un clúster de HDInsight existente.

**Instalación de una aplicación HDInsight**

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda.
3. Haga clic en un clúster de HDInsight.  Si no tiene ninguno, debe crearlo primero.  Consulte [Crear clúster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Haga clic en **Aplicaciones** en la categoría **Configuraciones**. Verá una lista de aplicaciones instaladas. Si no encuentra aplicaciones, significa que no hay aplicaciones para esta versión del clúster de HDInsight.
   
    ![Menú del portal de aplicaciones de HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Haga clic en **Agregar** en el menú. Verá una lista de las aplicaciones de HDInsight existentes.
   
    ![Aplicaciones disponibles para aplicaciones de HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Haga clic en una de las aplicaciones disponibles y siga las instrucciones para aceptar los términos legales.

En el portal aparece una notificación donde puede comprobar el estado de la instalación (haga clic en el icono de campana que encontrará en la parte superior del portal). Una vez instalada la aplicación, aparece en la lista Aplicaciones instaladas.

## <a name="install-applications-during-cluster-creation"></a>Instalación de aplicaciones durante la creación del clúster
Tiene la opción de instalar aplicaciones de HDInsight al crear un clúster. Durante el proceso, se instalan aplicaciones de HDInsight una vez que se haya creado el clúster y se encuentre en ejecución. Para instalar aplicaciones durante la creación del clúster mediante Azure Portal, use la opción --Personalizar-- en lugar de la opción predeterminada --Creación rápida--.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista de las aplicaciones de HDInsight instaladas y sus propiedades
El portal muestra una lista de las aplicaciones de HDInsight instaladas para un clúster y las propiedades de cada una.

**Lista de las aplicaciones de HDInsight y visualización de sus propiedades**

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda. 
3. Haga clic en un clúster de HDInsight.
4. En **Configuración**, haga clic en **Aplicaciones** (en la categoría **Configuración**). Las aplicaciones instaladas se muestran a la derecha. 
   
    ![Aplicaciones instaladas en aplicaciones de HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Haga clic en una de las aplicaciones instaladas para mostrar la propiedad. En la propiedad se indican los siguientes elementos:
   
   * Nombre de la aplicación: el nombre de la aplicación.
   * Estado: el estado de la aplicación. 
   * Página web: la dirección URL de la aplicación web que ha implementado en el nodo perimetral. La credencial coincide con las credenciales de usuario HTTP que ha configurado para el clúster.
   * Punto de conexión HTTP: la credencial coincide con las credenciales de usuario HTTP que ha configurado para el clúster. 
   * Punto de conexión SSH: puede usar SSH para conectarse con el nodo perimetral. Las credenciales de SSH coinciden con las credenciales de usuario SSH que ha configurado para el clúster. Para más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Para eliminar una aplicación, haga clic con el botón derecho en ella y haga clic en **Eliminar** en el menú contextual.

## <a name="connect-to-the-edge-node"></a>Conexión con el nodo perimetral
Puede conectar con el nodo perimetral mediante HTTP y SSH. Encontrará la información de punto de conexión en el [portal](#list-installed-hdinsight-apps-and-properties). Para más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Las credenciales del punto de conexión HTTP son las credenciales de usuario HTTP que ha configurado para el clúster de HDInsight; las del punto de conexión SSH son las credenciales SSH que ha configurado para el clúster de HDInsight.

## <a name="troubleshoot"></a>Solución de problemas
Consulte [Solución de problemas de instalación](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>pasos siguientes
* [Instalación de aplicaciones de HDInsight personalizadas](hdinsight-apps-install-custom-applications.md): aprenda a implementar en HDInsight una aplicación de HDInsight no publicada.
* [Publicación de aplicaciones de HDInsight en Azure Marketplace](hdinsight-apps-publish-applications.md): aprenda a publicar aplicaciones de HDInsight personalizadas en Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: instalación de una aplicación de HDInsight): aprenda a definir aplicaciones de HDInsight.
* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md): aprenda a usar acciones de script para instalar otras aplicaciones.
* [Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): aprenda a llamar a plantillas de Resource Manager para crear clústeres de HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Utilización de nodos perimetrales vacíos en HDInsight): aprenda a usar un nodo perimetral vacío para acceder a los clústeres de HDInsight, probar aplicaciones de este y hospedarlas.

