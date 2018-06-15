---
title: 'Habilitar la creación automática de temas en Apache Kafka: Azure HDInsight | Microsoft Docs'
description: Obtenga información sobre cómo configurar Apache Kafka en HDInsight para crear automáticamente los temas. Puede configurar Kafka estableciendo auto.create.topics.enable en true con Ambari o durante la creación del clúster mediante las plantillas de Resource Manager o PowerShell.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 04/18/2018
ms.author: larryfr
ms.openlocfilehash: fa5dd7533259c794671cd16231fd3f530173bfa3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781374"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Configuración de Apache Kafka en HDInsight para crear automáticamente los temas

De forma predeterminada, Kafka en HDInsight no habilita la creación automática de temas. Puede habilitar la creación automática de temas de clústeres existentes con Ambari. También puede habilitar la creación automática de temas al crear un clúster de Kafka mediante una plantilla de Azure Resource Manager.

## <a name="ambari-web-ui"></a>Interfaz de usuario web de Ambari

Para habilitar la creación automática de temas en un clúster existente mediante la interfaz de usuario web de Ambari, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), seleccione el clúster de Kafka.

2. En la __información general del clúster__, seleccione __Panel de clúster__. 

    ![Imagen Azure Portal con el panel de clúster seleccionado](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. A continuación, seleccione __Panel de clúster de HDInsight__. Cuando se le solicite, realice la autenticación mediante las credenciales de inicio de sesión (administrador) del clúster.

    ![Imagen de la entrada del panel de clúster de HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Seleccione el servicio Kafka en la lista de la parte izquierda de la página.

    ![Lista de servicios](./media/apache-kafka-auto-create-topics/service-list.png)

4. Seleccione Configuraciones en el medio de la página.

    ![Pestaña de configuración del servicio](./media/apache-kafka-auto-create-topics/service-config.png)

5. En el campo Filtro, escriba un valor de `auto.create`. 

    ![Imagen del campo de filtro](./media/apache-kafka-auto-create-topics/filter.png)

    La lista de propiedades se filtra y se muestra el valor `auto.create.topics.enable`.

6. Cambie el valor de `auto.create.topics.enable` a `true` y luego seleccione Guardar. Agregue una nota y después vuelva a seleccionar Guardar.

    ![Imagen de la entrada de auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Seleccione el servicio __Kafka__, seleccione __Reiniciar__ y luego seleccione Restart all affected (Reiniciar todos los afectados). Cuando se le solicite, seleccione __Confirm Restart All__ (Confirmar reiniciar todo).

    ![Imagen de selección de reinicio](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> También puede establecer valores de Ambari mediante la API de REST de Ambari. Esto suele ser más difícil, ya que tiene que hacer varias llamadas de REST para recuperar la configuración actual, modificarla, etc. Para más información, vea el documento [Administración de clústeres de HDInsight con la API de REST de Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="resource-manager-templates"></a>Plantillas de Resource Manager

Al crear un clúster de Kafka mediante una plantilla de Azure Resource Manager, puede establecer directamente `auto.create.topics.enable` al agregarla en `kafka-broker`. El siguiente fragmento de código JSON muestra cómo establecer este valor en `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a habilitar la creación automática de temas para Kafka en HDInsight. Para más información sobre cómo trabajar con Kafka, vea los siguientes vínculos:

* [Análisis de los registros de Kafka](apache-kafka-log-analytics-operations-management.md)
* [Réplica de datos entre clústeres de Kafka](apache-kafka-mirroring.md)