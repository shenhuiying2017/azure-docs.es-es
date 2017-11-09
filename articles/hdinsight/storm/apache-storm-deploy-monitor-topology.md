---
title: "Implementación y administración de topologías de Apache Storm en HDInsight | Microsoft Docs"
description: "Aprenda a implementar, supervisar y administrar topologías de Apache Storm mediante el panel de Storm en HDInsight. Utilice herramientas de Hadoop para Visual Studio"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5e542072-f014-42aa-82d6-2694a76df520
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 92c1a02cd7d435809914e7f5bb43b2f8d6aa0cdb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-windows-based-hdinsight"></a>Implementación y administración de topologías de Apache Storm en HDInsight basado en Windows

El panel de Storm permite implementar y ejecutar fácilmente topologías de Apache Storm en el clúster de HDInsight mediante el explorador web. También puede utilizar el panel para supervisar y administrar topologías de ejecución. Si utiliza Visual Studio, las herramientas de HDInsight para Visual Studio proporcionan características similares en Visual Studio.

El panel de Storm y las características de Storm de las herramientas de HDInsight dependen de la API de REST de Storm, que se puede usar para crear sus propias soluciones de supervisión y administración.

> [!IMPORTANT]
> Para los pasos de este documento se necesita un clúster de Storm en HDInsight que usa Windows como sistema operativo. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Para más información sobre la implementación y la administración de topologías de Storm con un clúster de HDInsight que usa Linux, consulte [Implementación y administración de topologías de Apache Storm en HDInsight basado en Linux](apache-storm-deploy-monitor-topology-linux.md).

## <a name="prerequisites"></a>Requisitos previos

* **Apache Storm en HDInsight**: consulte [Introducción a Apache Storm en HDInsight](apache-storm-tutorial-get-started-linux.md) para conocer los pasos para la creación de un clúster.

* Para el **panel de Storm**: un explorador web moderno que admite HTML5.

* Para **Visual Studio** : Azure SDK 2.5.1 o versiones más recientes y las herramientas de HDInsight para Visual Studio. Consulte [Introducción a las herramientas de HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) para Visual Studio para instalar y configurar las herramientas de HDInsight para Visual Studio.

    Una de las siguientes versiones de Visual Studio:

  * Visual Studio 2012 con la actualización 4

  * Visual Studio 2013 con la actualización 4 o Visual Studio Community 2013

  * Visual Studio 2015 (cualquier edición)

  * Visual Studio 2017 (cualquier edición)

## <a name="storm-dashboard"></a>panel de Storm

El panel de Storm es una página web disponible en el clúster de Storm. La dirección URL es **https://&lt;clustername>.azurehdinsight.net/**, donde **clustername** es el nombre del clúster de Storm en HDInsight.

En la parte superior del panel de Storm, seleccione **Enviar topología**. Siga las instrucciones de la página para ejecutar una topología de muestra o cargar y ejecutar una topología que haya creado.

![la página de envío de topología][storm-dashboard-submit]

### <a name="storm-ui"></a>UI de Storm

En el panel de Storm, seleccione el vínculo **IU de Storm** . Se muestra información sobre el clúster, así como las topologías que estén en ejecución.

![la interfaz de usuario de storm][storm-dashboard-ui]

> [!NOTE]
> Con algunas versiones de Internet Explorer, es posible que descubra que la interfaz de usuario de Strom no se actualiza después de visitarla por primera vez. Por ejemplo, puede que no se muestren las topologías nuevas que haya enviado o puede que se muestre una topología como activa, aunque anteriormente la desactivase. Microsoft conoce este problema y está trabajando para conseguir una solución.

#### <a name="main-page"></a>Página principal

La página principal de la interfaz de usuario de Storm ofrece la siguiente información:

* **Resumen del clúster**: información básica sobre el clúster de Storm.

* **Resumen de las topologías**: una lista de las topologías en ejecución. Use los vínculos de esta sección para obtener más información sobre las topologías específicas.

* **Resumen de supervisor**: información acerca del supervisor de Storm.

* **Configuración de Nimbus**: configuración de Nimbus del clúster.

#### <a name="topology-summary"></a>Resumen de las topologías

Si selecciona un vínculo desde la sección **Resumen de la topología** , se mostrará la siguiente información sobre la topología.

* **Resumen de la topología**: información básica sobre la topología.

* **Acciones de topología**: acciones de administración que puede realizar para la topología.

  * **Activar**: reanuda el procesamiento de una topología desactivada.

  * **Desactivar**: pausa una topología en ejecución.

  * **Reequilibrar**: ajusta el paralelismo de la topología. Debe volver a equilibrar las topologías en ejecución después de haber cambiado el número de nodos del clúster. Esto permite que la topología ajuste el paralelismo para compensar el mayor o menor número de nodos del clúster.

      Para obtener más información, consulte [Understanding the parallelism of a Storm topology (Descripción del paralelismo de una topología de Storm) (http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

  * **Eliminar**: finaliza una topología de Storm tras el tiempo de espera especificado.

* **Estadísticas de topología**: estadísticas sobre la topología. Use los vínculos de la columna **Ventana** para establecer el plazo de tiempo para las entradas restantes en la página.

* **Spouts**: los spouts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de spouts específicos.

* **Bolts**: los bolts que usa la topología. Use los vínculos de esta sección para obtener más información acerca de bolts específicos.

* **Configuración de la topología**: la configuración de la topología seleccionada.

#### <a name="spout-and-bolt-summary"></a>Resumen de spouts y bolts

Si se selecciona un spout en la sección **Spouts** o **Bolts**, se muestra la siguiente información sobre el elemento seleccionado:

* **Resumen de componentes**: información básica acerca del spout o bolt.

* **Estadísticas de spouts/bolts**: estadísticas sobre el spout o bolt. Use los vínculos de la columna **Ventana** para establecer el plazo de tiempo para las entradas restantes en la página.

* **Estadísticas de entrada** (solo bolt): información sobre las secuencias de entrada consumidas por el bolt.

* **Estadísticas de salida**: información sobre las secuencias emitidas por este spout o bolt

* **Ejecutores**: información sobre las instancias del spout o bolt. Seleccione la entrada **Puerto** de un ejecutor específico para ver un registro de información de diagnóstico generado por esta instancia.

* **Errores**: cualquier información de error de este spout o bolt.

## <a name="hdinsight-tools-for-visual-studio"></a>Herramientas de HDInsight para Visual Studio

Las herramientas de HDInsight puede utilizarse para enviar las topologías de C# o híbridas al clúster de Storm. Los pasos siguientes usan una aplicación de muestra. Para obtener información sobre cómo crear sus propias topologías con las herramientas de HDInsight, consulte [Desarrollo de las topologías de C# mediante las herramientas de HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Utilice los siguientes pasos para implementar una muestra en el clúster de Storm en HDInsight y, a continuación, ver y administrar la topología.

1. Si todavía no tiene instalada la versión más reciente de las herramientas de HDInsight para Visual Studio, consulte [Introducción al uso de las herramientas de HDInsight para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

2. Abra Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.

3. En el cuadro de diálogo **Nuevo proyecto**, expanda **Instalado** > **Plantillas** y seleccione **HDInsight**. En la lista de plantillas, seleccione **Muestra de Storm**. En la parte inferior del cuadro de diálogo, escriba un nombre para la aplicación.

    ![imagen](./media/apache-storm-deploy-monitor-topology/sample.png)

4. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto y seleccione **Submit to Storm on HDInsight** (Enviar a Storm en HDInsight).

   > [!NOTE]
   > Si se le solicita, introduzca las credenciales de inicio de sesión de su suscripción de Azure. Si tiene más de una suscripción, inicie sesión en la que contenga el clúster de Storm en HDInsight.

5. Seleccione el clúster de Storm en HDInsight desde el menú desplegable **Storm Cluster** (Clúster de Storm y seleccione **Submit** (Enviar). Puede supervisar si el envío es correcto mediante la ventana **Salida** .

6. Cuando la topología se envíe correctamente, deben aparecer las **topologías de Storm** del clúster. Seleccione la topología de la lista para ver información acerca de la topología de ejecución.

    ![supervisión de visual studio](./media/apache-storm-deploy-monitor-topology/vsmonitor.png)

   > [!NOTE]
   > También puede ver las **topologías de Storm** en el **Explorador de servidores** expandiendo **Azure** > **HDInsight** y, después, haciendo clic con el botón derecho en un clúster de Storm en HDInsight y seleccionando **View Storm Topologies** (Ver topologías de Storm).

    Seleccione la forma de los spouts o bolts para ver información sobre estos componentes. Se abrirá una ventana nueva para cada elemento seleccionado.

   > [!NOTE]
   > El nombre de la topología es el nombre de clase de la topología (en este caso, `HelloWord`) con una marca de tiempo adjunta.

7. Desde la vista **Topology Summary** (Resumen de la topología), seleccione **Kill** (Terminar) para detener la topología.

   > [!NOTE]
   > Las topologías de Storm continúan ejecutándose hasta que se detengan o se elimine el clúster.


## <a name="rest-api"></a>API de REST

La interfaz de usuario de Storm se basa en la API de REST, lo que permite realizar una funcionalidad similar de administración y supervisión mediante la API de REST. Puede usar la API de REST para crear herramientas personalizadas para administrar y supervisar las topologías de Storm.

Para más información, vea la [API de REST de la IU de Storm](https://github.com/apache/storm/blob/0.9.3-branch/STORM-UI-REST-API.md). La siguiente información es específica para usar la API de REST con Apache Storm en HDInsight.

### <a name="base-uri"></a>URI base

El URI de base para la API de REST en los clústeres de HDInsight es **https://&lt;clustername>.azurehdinsight.net/stormui/api/v1/**, donde **clustername** es el nombre del clúster de Storm en HDInsight.

### <a name="authentication"></a>Autenticación

Las solicitudes a la API de REST deben usar la **autenticación básica**; use el nombre y la contraseña de administrador del clúster de HDInsight.

> [!NOTE]
> Dado que la autenticación básica se envía mediante texto no cifrado, **siempre** debe usar HTTPS para proteger las comunicaciones con el clúster.

### <a name="return-values"></a>Valores devueltos

La información que se devuelve de la API de REST solo se puede utilizar desde dentro del clúster o de máquinas virtuales en la misma instancia de Azure Virtual Network que el clúster. Por ejemplo, no se puede obtener el acceso desde Internet al nombre de dominio completo (FQDN) devuelto para servidores de Zookeeper.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido cómo implementar y supervisar las topologías mediante el panel Storm, aprenda cómo:

* [Ejecutar topologías de C# mediante las herramientas de HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)

* [Desarrollar topologías basadas en Java con Maven](apache-storm-develop-java-topology.md)

Para obtener una lista con más topologías de ejemplo, consulte [Topologías de ejemplo para Storm en HDInsight](apache-storm-example-topology.md).

[hdinsight-dashboard]: ./media/apache-storm-deploy-monitor-topology/dashboard-link.png
[storm-dashboard-submit]: ./media/apache-storm-deploy-monitor-topology/submit.png
[storm-dashboard-ui]: ./media/apache-storm-deploy-monitor-topology/storm-ui-summary.png
