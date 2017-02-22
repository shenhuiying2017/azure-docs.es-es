---
title: "Supervisión y administración de clústeres de Azure HDInsight mediante la interfaz de usuario web de Ambari | Microsoft Docs"
description: "Aprenda a usar Ambari para supervisar y administrar clústeres de HDInsight basado en Linux. Con este documento aprende a usar la interfaz de usuario web de Ambari incluida con clústeres de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: e80bf82df28fbce8a1019c6eb07cfcae4cbba930
ms.openlocfilehash: 34f5c92dc565e644bd8c569bfbea65e92ace5a19


---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Administración de clústeres de HDInsight con la interfaz de usuario web de Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica la administración y la supervisión de un clúster de Hadoop al brindar una API de REST y una interfaz de usuario web fácil de usar. Ambari se incluye en los clústeres de HDInsight basado en Linux y, además, se usa para supervisar el clúster y realizar cambios en la configuración.

Con este documento aprende a usar la interfaz de usuario web de Ambari con un clúster de HDInsight.

## <a name="a-idwhatisawhat-is-ambari"></a><a id="whatis"></a> ¿Qué es Ambari?

[Apache Ambari](http://ambari.apache.org) simplifica la administración de Hadoop al proporcionar una interfaz de usuario web fácil de usar que se puede utilizar para aprovisionar, administrar y supervisar clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las [API de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

La interfaz de usuario web de Ambari se proporciona de forma predeterminada con clústeres de HDInsight que usan el sistema operativo Linux.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date). 

## <a name="connectivity"></a>Conectividad

La interfaz de usuario web de Ambari está disponible en el clúster de HDInsight en HTTPS://CLUSTERNAME.azurehdidnsight.net, donde **CLUSTERNAME** es el nombre del clúster. 

> [!IMPORTANT]
> La conexión a Ambari en HDInsight requiere HTTPS. También debe autenticarse en Ambari mediante el nombre de la cuenta de administrador (el valor predeterminado es **admin**) y la contraseña que proporcionó cuando se creó el clúster. 

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

> [!NOTE]
> A pesar de que es posible tener acceso directamente a través de Internet a Ambari para su clúster, algunos vínculos de la interfaz de usuario web de Ambari (como JobTracker) no estarán expuestos en Internet. Por lo tanto, recibirá errores de "servidor no encontrado" al intentar tener acceso a estas características, a menos que utilice un túnel de Shell seguro (SSH) para canalizar el tráfico web para el nodo principal del clúster.

Para obtener información sobre cómo crear un túnel SSH para trabajar con Ambari, consulte [Uso de la tunelización SSH para acceder a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interfaz de usuario web de Ambari

Cuando se conecte a la interfaz de usuario web de Ambari, se le pedirá que se autentique en la página. Use el nombre de usuario y la contraseña de administrador del clúster (valor predeterminado, Admin) que usó durante la creación del clúster.

Cuando se abra la página, observe la barra que se encuentra en la parte superior. Contiene la siguiente información y controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo de Ambari** : abre el panel, que puede usarse para supervisar el clúster.

* **Cluster name # ops** : muestra el número de operaciones de Ambari en curso. Seleccione el nombre del clúster o **# ops** para ver una lista de las operaciones en segundo plano.

* **# alerts** : alertas de advertencia o alertas críticas del clúster, si las hay. Al seleccionar esto se muestra una lista de alertas.

* **Dashboard** : muestra el panel.

* **Services** : información y ajustes de configuración de los servicios en el clúster.

* **Hosts** : información y ajustes de configuración de los nodos del clúster.

* **Alerts** : un registro de información, advertencias y alertas críticas.

* **Admin** : servicios/pila de software que están instalados en el clúster, información de la cuenta de servicio y seguridad Kerberos.

* **Botón Admin** : administración de Ambari, configuración del usuario y cierre de sesión.

## <a name="monitoring"></a>Supervisión

### <a name="alerts"></a>Alertas

Ambari proporciona muchas alertas, que tendrán uno de los siguientes estados:

* **OK (CORRECTO)**
* **Warning (ADVERTENCIA)**
* **CRITICAL (CRÍTICA)**
* **UNKNOWN (DESCONOCIDO)**

Las alertas, con la excepción de **OK**, hacen que la entrada **# alerts** en la parte superior de la página muestre el número de alertas. Seleccione esta entrada para ver las alertas y sus estados.

Las alertas se organizan en varios grupos predeterminados, que se pueden ver desde la página **Alertas** .

![página de alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Para administrar los grupos, use el menú **Actions** (Acciones) y seleccione **Manage Alert Groups** (Administración de grupos de alertas). Esta acción le permite modificar los grupos existentes o crear grupos nuevos.

![administrar cuadro de diálogo de grupos de alertas](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

También puede administrar métodos de alerta y crear notificaciones de alerta a partir del menú **Actions** (Acciones) seleccionando __Manage Alert Notifications__ (Administrar notificaciones de alerta). Esto muestra las notificaciones actuales y le permite crear nuevas notificaciones. Se pueden enviar notificaciones a través de **CORREO ELECTRÓNICO** o **SNMP** cuando se producen combinaciones de alerta/gravedad específicas. Por ejemplo, puede enviar una alerta cuando alguna de las alertas del grupo **YARN Default** (Predeterminado de YARN) está definida en **Critical** (Crítica).

![cuadro de diálogo Crear alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por último, al seleccionar __Manage Alert Settings__ (Administrar configuración de alerta) en el menú __Actions__ (Acciones) podrá probar el número de veces que debe aparecer una alerta antes de enviar una notificación. Esto se puede utilizar para evitar notificaciones para errores transitorios, como cuando hay un error en un servicio en un nodo principal y se reinicia en el otro nodo principal.

### <a name="cluster"></a>Clúster

La pestaña **Metrics** (Métricas) del panel contiene una serie de widgets que facilitan la supervisión del estado del clúster de un solo vistazo. Varios widgets, como **CPU Usage**(Uso de CPU), proporcionan información adicional al hacer clic en ellos.

![panel con métricas](./media/hdinsight-hadoop-manage-ambari/metrics.png)

La pestaña **Heatmaps** (Mapas térmicos) muestran las métricas como mapas térmicos coloreados, desde el verde hasta el rojo.

![panel con mapas térmicos](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obtener información más detallada sobre los nodos dentro del clúster, seleccione **Hosts**y, a continuación, seleccione el nodo específico que le interesa.

![detalles del host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Services

La barra lateral **Services** (Servicios) del panel proporciona información rápida del estado de los servicios que se ejecutan en el clúster. Se usan varios iconos para indicar el estado o las acciones que se debieran realizar, como un símbolo de reciclaje de color amarillo si es necesario reciclar un servicio.

![barra lateral de servicios](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Los servicios mostrados difieren entre las versiones y los tipos de clúster de HDInsight. Los servicios mostrados aquí pueden ser diferentes de los servicios que se muestran para el clúster.

Al seleccionar un servicio se muestra información más detallada sobre el mismo.

![información de resumen de servicio](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Vínculos rápidos

Algunos servicios muestran un vínculo **Quick Links** (Vínculos rápidos) en la parte superior de la página. Este vínculo puede usarse para tener acceso a interfaces de usuario web específicas del servicio, como:

* **Job History** : el historial de trabajos de MapReduce.
* **Resource Manager** : la interfaz de usuario del administrador de recursos de YARN.
* **NameNode** : la interfaz de usuario de NameNode del Sistema de archivos distribuido de Hadoop (HDFS).
* **Oozie Web UI** : interfaz de usuario de Oozie.

Seleccione cualquiera de estos vínculos para abrir una pestaña nueva del explorador, donde aparecerá la página seleccionada.

> [!NOTE]
> Al seleccionar un vínculo **Vínculos rápidos** para cualquier servicio se generará un error de "servidor no encontrado", a menos que use un túnel de Capa de sockets seguros (SSL) para autorizar el tráfico web al clúster. Esto es porque las aplicaciones web que se usan para mostrar esta información no se exponen en Internet.
> 
> Para obtener información sobre cómo usar un túnel SSL con HDInsight, consulte [Uso de la tunelización SSH para acceder a la interfaz de usuario web de Ambari, ResourceManager, JobHistory, NameNode, Oozie y otras interfaces de usuario web](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Administración

### <a name="ambari-users-groups-and-permissions"></a>Usuarios, grupos y permisos de Ambari

Se puede trabajar con usuarios, grupos y permisos cuando se usa un clúster de HDInsight [unidos a un dominio](hdinsight-domain-joined-introduction.md). Para más información sobre el uso de la interfaz de usuario de administración de Ambari en un clúster unidos a un dominio, consulte [Manage domain-joined HDInsight clusters](hdinsight-domain-joined-introduction.md) (Administración de clústeres de HDInsight unidos a dominio).

> [!WARNING]
> No cambie la contraseña del guardián Ambari (hdinsightwatchdog) en el clúster de HDInsight basado en Linux. El cambio de la contraseña impide usar acciones de script o realizar operaciones de escalado con el clúster.

### <a name="hosts"></a>Hosts

La página **Hosts** muestra todos los hosts existentes en el clúster. Siga estos pasos para administrar los hosts.

![página de hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> No se debe agregar, retirar o volver a programar un host con los clústeres de HDInsight.

1. Seleccione los hosts que desee administrar.

2. Use el menú **Actions** para seleccionar la acción que desea realizar:
   
   * **Start all components** : inicie todos los componentes en el host.

   * **Stop all components** : detenga todos los componentes en el host.

   * **Restart all components** : detenga e inicie todo los componentes en el host.

   * **Turn on maintenance mode** : suprime las alertas del host. Esta opción debe habilitarse si realiza acciones que generan alertas, como reiniciar un servicio en el que se basan servicios en ejecución.

   * **Turn off maintenance mode** : devuelve el host a su estado de alerta normal.

   * **Stop** : detiene DataNode o NodeManagers en el host.

   * **Start** : inicia DataNode o NodeManagers en el host.

   * **Restart** : detiene e inicia DataNode o NodeManagers en el host.

   * **Decommission** : quita un host del clúster.
     
     > [!NOTE]
     > No use esta acción en clústeres de HDInsight.

   * **Recommission** : agrega un host anteriormente retirado al clúster.
     
     > [!NOTE]
     > No use esta acción en clústeres de HDInsight.

### <a name="a-idserviceaservices"></a><a id="service"></a>Servicios

En la página **Dashboard** (Panel) o **Services** (Servicios), use el botón **Actions** (Acciones) que se encuentra en la parte inferior de la lista de servicios para detener e iniciar todos los servicios.

![Service Actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Aunque **Add Service** (Agregar servicio) aparece en este menú, no debe usarse para agregar servicios al clúster de HDInsight. Se deben agregar nuevos servicios deben mediante una acción de script durante el aprovisionamiento del clúster. Para obtener más información sobre el uso de las acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

A pesar de que el botón **Actions** puede reiniciar todos los servicios, con frecuencia se desea iniciar, detener o reiniciar un servicio específico. Use los siguientes pasos para realizar acciones sobre un servicio individual:

1. En la página **Dashboard** (Panel) o **Services** (Servicios), seleccione un servicio.

2. En la parte superior de la pestaña **Summary** (Resumen), use el botón **Service Actions** (Acciones de servicio) y seleccione la acción que se realizará. Con esto se reinicia el servicio en todos los nodos.
   
    ![acción de servicio](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)
   
   > [!NOTE]
   > Reiniciar algunos servicios mientras el clúster está en ejecución puede generar alertas. Para evitarlo, puede usar el botón **Service Actions** (Acciones de servicio) para habilitar el **modo de mantenimiento** del servicio antes de reiniciar.

3. Una vez que se selecciona una acción, la entrada **# op** que aparece en la parte superior de la página aumenta para mostrar que se está produciendo una operación en segundo plano. La lista de operaciones en segundo plano aparecerá si está configurada de ese modo.
   
   > [!NOTE]
   > Si habilitó el **modo de mantenimiento** para el servicio, recuerde deshabilitarlo con el botón **Service Actions** (Acciones de servicio) una vez finalizada la operación.

Para configurar un servicio, use los siguientes pasos:

1. En la página **Dashboard** (Panel) o **Services** (Servicios), seleccione un servicio.

2. Seleccione la pestaña **Configs** (Configuraciones). Aparecerá la configuración actual. También aparecerá una lista de las configuraciones anteriores.
   
    ![configuraciones](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use los campos que aparecen para modificar la configuración y, a continuación, seleccione **Save**(Guardar). O bien, seleccione una configuración anterior y, a continuación, seleccione **Make current** (Convertir en actual) para volver a la configuración anterior.

## <a name="ambari-views"></a>Vistas de Ambari

Las vistas de Ambari permiten a los desarrolladores conectar elementos de interfaz de usuario a la interfaz de usuario web de Ambari mediante el [marco de vistas de Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight proporciona las siguientes vistas con los tipos de clúster de Hadoop:

* Administrador de colas de Yarn: el administrador de colas proporciona una interfaz de usuario sencilla para ver y modificar colas de YARN.

* Vista de Hive: la vista de Hive permite ejecutar consultas de Hive directamente desde el explorador web. Puede guardar consultas, ver resultados, guardar resultados en el almacenamiento del clúster o descargar los resultados en el sistema local. Para más información sobre el uso de vistas de Hive, consulte [Uso de vistas de Hive con HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).

* Vista de Tez: la vista de Tez permite comprender mejor y optimizar los trabajos ya que proporciona información sobre cómo se ejecutan los trabajos de Tez y qué recursos se emplean en el trabajo.




<!--HONumber=Feb17_HO2-->


