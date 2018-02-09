---
title: "Supervisión de HBase con Operations Management Suite (OMS): Azure HDInsight | Microsoft Docs"
description: "Uso de OMS con Azure Log Analytics para supervisar clústeres de HDInsight HBase."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: f78d570cfa8b040cd7673a5e14e6a992511f60bb
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="monitor-hbase-with-operations-management-suite-oms"></a>Supervisión de HBase con Operations Management Suite (OMS)

HDInsight HBase Monitoring usa Azure Log Analytics para recopilar las métricas de rendimiento de HDInsight HBase desde los nodos de clúster de HDInsight. Monitor proporciona visualizaciones y paneles específicos de HBase, las herramientas para buscar las métricas y la capacidad para crear alertas y reglas de supervisión personalizadas. Puede supervisar las métricas de varios clústeres de HDInsight HBase en distintas suscripciones de Azure.

Log Analytics es un servicio de [Operations Management Suite (OMS)](../../operations-management-suite/operations-management-suite-overview.md) que supervisa los entornos local y en la nube para mantener su disponibilidad y rendimiento. Log Analytics recopila los datos generados por los recursos en los entornos local y de nube y mediante otras herramientas de supervisión, para proporcionar análisis entre varios orígenes.

Las [soluciones de administración de Log Analytics](../../log-analytics/log-analytics-add-solutions.md) agregan funcionalidad a OMS, lo que proporciona datos y herramientas de análisis adicionales. Las soluciones de administración de Log Analytics son una colección de reglas de lógica, visualización y adquisición de datos que proporcionan métricas para un área determinada. Una solución también puede definir tipos de registro nuevos que se van a recopilar y estos registros se pueden analizar con búsquedas de registros o con las nuevas características de interfaz de usuario.

[Insight & Analytics](https://azure.microsoft.com/pricing/details/insight-analytics/) se compiló en base a la plataforma de Log Analytics. Puede elegir usar las funcionalidades de Log Analytics y pagar por los GB introducidos en el servicio o cambiar su área de trabajo a la capa de Insight and Analytics y pagar por nodo administrado por el servicio. Insight & Analytics ofrece un superconjunto de las funcionalidades que ofrece Log Analytics. La solución HBase Monitoring está disponible con Log Analytics o Insight & Analytics.

Cuando se proporciona una solución de HDInsight HBase Monitoring, crea un área de trabajo de OMS. Cada área de trabajo es un entorno de Log Analytics único, con su propio repositorio de datos, sus propios orígenes de datos y sus propias soluciones. Puede crear varias áreas de trabajo en su suscripción para admitir varios entornos, como los entornos de producción y prueba.

## <a name="provision-hdinsight-hbase-monitoring"></a>Aprovisionamiento de HDInsight HBase Monitoring

1. Inicie sesión en [Azure Portal](https://portal.azure.com) mediante la suscripción de Azure.
2. En el panel **Nuevo**, en **Marketplace**, seleccione **Supervisión y administración**.
3. En el panel **Supervisión y administración**, seleccione **Ver todo**.

    ![Panel Supervisión y administración](./media/apache-hbase-monitor-with-oms/monitoring-management-blade.png)  

4. En la lista, busque la banda **Soluciones de administración**. A la derecha de **Soluciones de administración**, seleccione **Más**.

    ![Panel Supervisión y administración](./media/apache-hbase-monitor-with-oms/management-solutions.png) 

5. En el panel **Soluciones de administración**, seleccione la solución de administración HDInsight HBase Monitoring para agregar a un área de trabajo.

    ![Panel Soluciones de administración](./media/apache-hbase-monitor-with-oms/hbase-solution.png)  
6. En el panel de soluciones de administración, revise la información sobre la solución de administración y seleccione **Crear**. 
7. En el panel del *nombre de la solución de administración*, seleccione un área de trabajo existente para asociarlo con la solución de administración, o bien cree un área de trabajo de OMS nueva y selecciónela.
8. Cambie la configuración de área de trabajo de la suscripción de Azure, el grupo de recursos y la ubicación según corresponda. 
    ![área de trabajo de solución](./media/apache-hbase-monitor-with-oms/solution-workspace.png)  
9. Seleccione **Crear**.  
10. Para usar esta solución de administración nueva en su área de trabajo, vaya a **Log Analytics** > ***nombre del área de trabajo*** > **Soluciones**. En la lista se muestra una entrada para la solución de administración. Seleccione la entrada para navegar a la solución.

    ![Soluciones de Log Analytics](./media/apache-hbase-monitor-with-oms/log-analytics-solutions.png)  

11. Aparece el panel de la solución HDInsight HBase Monitoring.

    ![Panel Soluciones HDInsight HBase](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

12. Los iconos de Resumen no muestran ningún dato porque todavía no se configuró el clúster de HDInsight HBase para que envíe los datos a Log Analytics.

## <a name="connect-hdinsight-hbase-cluster-to-log-analytics"></a>Conexión de clúster de HDInsight HBase a Log Analytics

Para usar las herramientas que proporciona HDInsight HBase Monitoring, debe configurar el clúster para que transmita las métricas del servidor de región, los nodos principales y los nodos de ZooKeeper a Log Analytics. Esta configuración se realiza mediante la ejecución de una acción de script en el clúster de HDInsight HBase.

### <a name="get-oms-workspace-id-and-workspace-key"></a>Obtención del identificador y la clave del área de trabajo de OMS

Necesitará el identificador y la clave del área de trabajo de OMS para habilitar los nodos en el clúster para realizar la autenticación con Log Analytics. Para obtener estos valores:

1. Desde el panel HBase Monitoring en Azure Portal, seleccione Información general.

    ![Panel de solución HBase Monitoring](./media/apache-hbase-monitor-with-oms/hdinsight-hbase-solution.png) 

2. Seleccione el portal de OMS para abrirlo en una ventana o una pestaña del explorador.

    ![Selección del portal de OMS](./media/apache-hbase-monitor-with-oms/select-oms-portal.png) 

3. En la página principal del portal de OMS, seleccione Configuración.

    ![Portal de OMS](./media/apache-hbase-monitor-with-oms/oms-portal-settings.png) 

4. Seleccione Origen conectado, Servidores Linux.

    ![Selección de Origen conectado y, luego, Servidores Linux](./media/apache-hbase-monitor-with-oms/select-linux-servers.png) 

5. Anote los valores de la clave principal y del identificador del área de trabajo que aparecen, porque los necesitará en la acción de script.

### <a name="run-the-script-action"></a>Ejecución de la acción de script

Para habilitar la recopilación de datos del clúster de HDInsight HBase, ejecute una acción de script en todos los nodos del clúster:

1. Vaya al panel correspondiente al clúster de HDInsight HBase en Azure Portal.
2. Seleccione **Acciones de script**.

    ![Acciones de script](./media/apache-hbase-monitor-with-oms/script-actions.png) 

3. Seleccione **Enviar nuevo**.

    ![Enviar nuevo](./media/apache-hbase-monitor-with-oms/script-actions-submit-new.png)  

4. En la acción **Enviar script**, establezca el tipo de script en `"- Custom"`.
5. Proporcione un nombre para este script.
6. En **URI de script de Bash**, pegue el URI siguiente:

        https://raw.githubusercontent.com/hdinsight/HDInsightOMS/master/monitoring/script2.sh 

7. En los **tipos de nodo**, seleccione las tres opciones (**Principal**, **Región**, **ZooKeeper**).
8. En el cuadro de texto **Parámetros**, escriba la clave y el identificador del área de trabajo, con cada valor entre comillas y separados por un espacio.

        "<Workspace ID>" "<Workspace Key>"

9. Seleccione **Conservar esta acción de script** para volver a ejecutar la acción cuando se agreguen nodos nuevos al clúster.

    ![Configuración de la acción de script](./media/apache-hbase-monitor-with-oms/submit-script-action.png)  

10. Seleccione **Crear**.
11. La acción de script tarda unos minutos en completarse. Puede supervisar su estado en el panel Acciones de script.

    ![Historial de acciones de script](./media/apache-hbase-monitor-with-oms/script-action-running.png)  

12. Cuando se complete la acción de script, debería ver una marca de verificación verde junto al nombre del script en la lista.

    ![Acción de script completada](./media/apache-hbase-monitor-with-oms/script-action-done.png)  

## <a name="view-the-hdinsight-hbase-monitoring-solution"></a>Vista de la solución HDInsight HBase Monitoring

Una vez que se completa la acción de script, debería ver los datos en la solución de supervisión en cuestión de minutos.

1. Dentro de Azure Portal, vaya al panel de la solución de HDInsight HBase.
2. Seleccione la pestaña **Información general**.
3. En **Resumen**, verá un icono que indica la cantidad de servidores de regiones que se supervisan.

    ![Icono de resumen de supervisión](./media/apache-hbase-monitor-with-oms/monitoring-summary-tile.png)  

4. Seleccione el icono que indica el número de servidores de regiones. Aparece el panel principal.
5. Este panel proporciona acceso a las estadísticas sobre las regiones, el número de registros de escritura previa (WAL) en uso, una colección de búsquedas de Log Analytics (como para los registros de servidores de regiones, los registros de Phoenix y las excepciones) y una colección de gráficos populares para visualizar las métricas pertinentes. 

    ![Panel principal](./media/apache-hbase-monitor-with-oms/main-dashboard.png)  

6. La selección de cualquiera de estas opciones se desglosará en la vista de búsqueda de registros donde puede restringir la consulta y explorar los datos con más detalle.

## <a name="next-steps"></a>pasos siguientes

* [Creación de alertas en Log Analytics (OMS)](../../log-analytics/log-analytics-alerts-creating.md)
* [Búsqueda de datos con búsquedas de registros en Azure Log Analytics](../../log-analytics/log-analytics-log-searches.md).
