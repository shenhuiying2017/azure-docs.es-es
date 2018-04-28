---
title: 'HDInsight: clústeres de HDInsight unidos a un dominio (Azure)'
description: Aprenda a...
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: omidm
ms.openlocfilehash: 6225bd824e3bcff24b84c79f39ce209f16caafd8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Introducción a la seguridad de Hadoop con clústeres de HDInsight unidos a dominio

Hasta hoy, Azure HDInsight solo admite un único usuario administrador local. Esto funcionaba muy bien para departamentos o equipos de aplicaciones menores. A medida que la popularidad de las cargas de trabajo basadas en Hadoop aumentaba en el sector empresarial, fue creciendo la necesidad de funcionalidades de tipo empresarial, tales como la autenticación basada en Active Directory, la compatibilidad de múltiples usuarios y el control de acceso basado en roles. Mediante los clústeres de HDInsight unidos a dominio, puede crear un clúster de HDInsight unido a un dominio de Active Directory y configurar una lista de empleados de la empresa que pueden autenticarse mediante Azure Active Directory para iniciar sesión en el clúster de HDInsight. Ninguna persona fuera de la empresa puede iniciar sesión o acceder al clúster de HDInsight. El administrador de organización puede configurar el control de acceso basado en roles para la seguridad de Hive con [Apache Ranger](http://hortonworks.com/apache/ranger/), que solo restringe el acceso a datos en la medida que sea necesario. Por último, el administrador puede auditar el acceso a los datos por parte de los empleados, y los cambios realizados en las directivas de control de acceso, con lo que se consigue un alto grado de control de los recursos corporativos.

> [!NOTE]
> Las nuevas características que se describen en este artículo solo están disponibles en los siguientes tipos de clúster: Hadoop, Spark e Interactive Query. Las demás cargas de trabajo, como HBase, Storm y Kafka, se habilitarán en futuras versiones.

> [!IMPORTANT]
> Oozie no está habilitado en HDInsight unido a un dominio.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="benefits"></a>Ventajas
La seguridad de la empresa se basa en cuatro grandes pilares: seguridad del perímetro, autenticación, autorización y cifrado.

![Pilares de las ventajas de los clústeres de HDInsight de dominio unido](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Seguridad del perímetro
La seguridad del perímetro en HDInsight se logra con redes virtuales y el servicio de puerta de enlace. En la actualidad, un administrador de empresa puede crear un clúster de HDInsight dentro de una red virtual y usar Grupos de seguridad de red (reglas de firewall de entrada o salida) para restringir el acceso a la red virtual. Solo las direcciones IP definidas en las reglas de firewall de entrada podrán comunicarse con el clúster de HDInsight, lo que proporciona la seguridad del perímetro. Otro nivel de seguridad del perímetro se realiza mediante el servicio de puerta de enlace. La puerta de enlace es el servicio que actúa como primera línea de defensa para cualquier solicitud entrante al clúster de HDInsight. Acepta la solicitud, la valida y, solo entonces, permite que la solicitud pase a otros nodos del clúster, lo que proporciona la seguridad del perímetro para otros nodos de datos y de nombres del clúster.

### <a name="authentication"></a>Autenticación
Un administrador de empresa puede crear un clúster de HDInsight unido a dominio en una [red virtual](https://azure.microsoft.com/services/virtual-network/). Los nodos del clúster de HDInsight se unirán al dominio administrado por la empresa. Esto se logra mediante el uso de [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Todos los nodos del clúster se unen a un dominio que administra la empresa. Con esta configuración, los empleados de la empresa pueden iniciar sesión en los nodos del clúster con sus credenciales de dominio. También pueden usar sus credenciales de dominio para autenticar con otros puntos de conexión aprobados tales como Hue, Ambari Views, ODBC, JDBC, PowerShell y las API de REST para interactuar con el clúster. El administrador tiene control total sobre la limitación del número de usuarios que interactúan con el clúster mediante estos puntos de conexión.

### <a name="authorization"></a>Autorización
Un procedimiento recomendado seguido por la mayoría de las empresas es que no todos los empleados tengan acceso a todos los recursos de empresa. Asimismo, con esta versión, el administrador puede definir directivas de control de acceso basado en roles para los recursos del clúster. Por ejemplo, puede configurar el administrador [Apache Ranger](http://hortonworks.com/apache/ranger/) para establecer las directivas de control de acceso para Hive. Esta capacidad garantiza que los empleados solo puedan acceder a los datos que necesitan para tener éxito en sus trabajos. El acceso SSH al clúster también está restringido a solo el administrador.

### <a name="auditing"></a>Auditoría
Junto con la protección de los recursos del clúster de HDInsight contra usuarios no autorizados y la protección de los datos, la auditoría de todos los accesos a los recursos del clúster y los datos es necesaria para realizar el seguimiento de los accesos no autorizados o accidentales a los recursos. El administrador puede ver y notificar todos los accesos a los recursos y los datos del clúster de HDInsight. El administrador también puede ver y notificar todos los cambios en las directivas de control de acceso en los puntos de conexión de Ranger Apache compatibles. Un clúster de HDInsight unido a dominio utiliza la familiar interfaz de usuario de Apache Ranger para buscar registros de auditoría. En el back-end, usa Ranger [Apache Solr](http://hortonworks.com/apache/solr/) para almacenar y buscar los registros.

### <a name="encryption"></a>Cifrado
La protección de los datos es importante para cubrir los requisitos de cumplimiento y seguridad de la organización y, junto con la restricción del acceso a datos por parte de empleados no autorizados, también se deben proteger mediante su cifrado. Los almacenes de datos para clústeres de HDInsight, Azure Storage Blob y Azure Data Lake Storage admiten el [cifrado de datos](../../storage/common/storage-service-encryption.md) en reposo en el lado servidor transparente. Los clústeres de HDInsight seguros funcionarán perfectamente con esta funcionalidad de cifrado de los datos en reposo en el lado servidor.

## <a name="next-steps"></a>Pasos siguientes
* Para configurar un clúster de HDInsight unido a dominio, consulte [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a un dominio).
* Para administrar un clúster de HDInsight unido a dominio, consulte [Manage Domain-joined HDInsight clusters](apache-domain-joined-manage.md) (Administración de clústeres de HDInsight unidos a un dominio).
* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
* Para ejecutar consultas de Hive mediante SSH en clústeres de HDInsight unidos a un dominio, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Para usar VSCode para establecer un vínculo a un clúster unido a un dominio, vea [Link to domain joined cluster with VSCode](../hdinsight-for-vscode.md#linkcluster) (Vinculación a un clúster unido a un dominio con VSCode).
* Para usar IntelliJ para establecer un vínculo a un clúster unido a un dominio, vea [Link to domain joined cluster with IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#linkcluster) (Vinculación a un clúster unido a un dominio con IntelliJ).
* Para usar Eclipse para establecer un vínculo a un clúster unido a un dominio, vea [Link to domain joined cluster with VSCode](../spark/apache-spark-eclipse-tool-plugin.md#linkcluster) (Vinculación a un clúster unido a un dominio con Eclipse).
