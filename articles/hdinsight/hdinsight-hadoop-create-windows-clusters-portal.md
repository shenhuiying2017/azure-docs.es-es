---
title: "Creación de clústeres de Hadoop basados en Windows en HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres de Hadoop en HDInsight basados en Windows mediante el portal de Azure."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Creación de clústeres de Hadoop basados en Windows en HDInsight con el portal de Azure

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear un clúster de Hadoop en HDInsight basado en Windows con el portal de Azure. 

La información de este artículo solo se aplica a los clústeres de HDInsight para Windows. Para más información sobre la creación de clústeres basados en Linux, consulte [Creación de clústeres de Hadoop en HDInsight con el portal de Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creación de clústeres
**Para crear un clúster de HDInsight**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo**, en **Inteligencia y análisis** y en **HDInsight**.
3. Escriba o seleccione los valores siguientes:
   
   * **Nombre del clúster**: nombre del clúster.
   * **Suscripción**: seleccione una suscripción de Azure usada para crear este clúster.
   * **Configuración de clúster**:

     * **Tipo de clúster**: en este tutorial, seleccione **Hadoop**.
     * **Sistema operativo**: en este tutorial, seleccione **Windows**.
     * **Versión**: seleccione **Hadoop 2.7.0 (HDI 3.3)**.  Es la versión más reciente para los clústeres de Hadoop basados en Windows.
     * **Nivel de clúster**: en este tutorial, seleccione **Estándar**.

   * **Credenciales**:

     * **Nombre de usuario de inicio de sesión del clúster**: el nombre predeterminado es **administrador**. 
     * **Contraseña de inicio de sesión de clúster**: escriba una contraseña.
     * **Enable Remote Desktop** (Habilitar Escritorio remoto): no es necesario escritorio remoto en este tutorial.

   * **Origen de datos**:

     * **Método de selección**: seleccione **Desde todas las suscripciones**.
     * **Seleccione una cuenta de Storage**: haga clic en **Create new** (Crear nuevo) y luego escriba un nombre para la cuenta de almacenamiento predeterminada.
     * **Contenedor predeterminado**: de forma predeterminada, el contenedor predeterminado usa el mismo nombre que el clúster.
     * **Ubicación**: elija una ubicación cercana a usted.  Esta ubicación la usan tanto el clúster como la cuenta de almacenamiento predeterminada.
   * **Tamaño del clúster**:

     * **Número de nodos de trabajo**: en este tutorial solo se necesita 1 nodo.
   * **Configuraciones avanzadas**: puede omitir esta parte de este tutorial.
   * **Grupo de recursos**: seleccione **Create new** (Crear nuevo) y luego escriba un nombre.

4. Seleccione **Anclar al panel** y luego haga clic en **Crear**. Si selecciona **Anclar a Panel de inicio**, se agrega un icono del clúster en el Panel de inicio de su portal. El clúster tarda entre 15 y 20 minutos en crearse. Use el icono del Panel de control o la entrada **Notificaciones** en la parte izquierda de la página para comprobar el proceso de aprovisionamiento.
5. Cuando termine la creación, haga clic en el icono del clúster desde el panel de inicio para iniciar la hoja del clúster. La hoja de clúster proporciona información esencial sobre el clúster, como el nombre, el grupo de recursos al que pertenece, la ubicación, el sistema operativo, la dirección URL para el panel del clúster, etc.

## <a name="customize-clusters"></a>Personalización de los clústeres
* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md).

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md) : aprenda a empezar a trabajar con su clúster de HDInsight
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md) : aprenda a enviar trabajos a HDInsight mediante programación
* [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](hdinsight-administer-use-management-portal.md)




<!--HONumber=Jan17_HO3-->


