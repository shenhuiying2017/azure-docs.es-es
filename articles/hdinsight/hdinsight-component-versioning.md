---
title: 'Componentes y versiones de Hadoop: Azure HDInsight | Microsoft Docs'
description: "Conozca los componentes y las versiones de Hadoop en HDInsight y los niveles de servicio disponibles en esta distribución de nube de HortonWorks Data Platform."
keywords: "versiones de Hadoop, componentes del ecosistema de Hadoop, componentes de Hadoop, cómo comprobar la versión de Hadoop"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f6f50906766408226ad3ab9fdfd67b4707961b9b
ms.contentlocale: es-es
ms.lasthandoff: 05/17/2017

 

---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>¿Cuáles son los componentes y versiones de Hadoop disponibles con HDInsight?

Aprenda sobre los niveles de servicio Estándar y Premium, así como sobre los componentes del ecosistema Hadoop y las versiones incluidas en Azure HDInsight. Además, obtenga información sobre cómo comprobar las versiones de componentes de Hadoop en HDInsight. 

Cada versión de HDInsight es una distribución de nube de una versión de HortonWorks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componentes de Hadoop disponibles con las distintas versiones de HDInsight
HDInsight de Azure es compatible con varias versiones de clústeres de Hadoop que se pueden implementar en cualquier momento. Cada versión crea una versión específica de la distribución HortonWorks Data Platform (HDP) y un conjunto de componentes que están incluidos en esa distribución. La versión de clúster predeterminada que usa Azure HDInsight actualmente es la 3.5 y, desde el 17 de febrero de 2017, basada en HDP 2.5.

En la tabla siguiente se detallan las versiones de componente asociadas a las versiones de clúster de HDInsight: 

> [!NOTE]
> La versión predeterminada del servicio pueden cambiar sin previo aviso. Se recomienda especificar la versión al crear clústeres con el SDK de .NET, Azure PowerShell y la CLI de Azure, si tiene una dependencia de versiones. 
>
>


| Componente | HDInsight versión 3.6 | HDInsight versión 3.5 (predeterminada) | Versión de HDInsight 3.4 | Versión de HDInsight 3.3 | HDInsight versión 3.2 | Versión de HDInsight 3.1 | HDInsight versión 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop y YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive y HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez-Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| HBase Apache |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Spark de Apache |2.1.0 (solo Linux) |1.6.2 y 2.0 (solo Linux) |1.6.0 (solo Linux) |1.5.2 (solo Linux/compilación experimental) |1.3.1 (solo Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|


## <a name="how-to-check-current-hadoop-component-version-information"></a>Procedimiento para comprobar la información de la versión de los componentes actuales de Hadoop

Las versiones de los componentes del ecosistema de Hadoop asociados a las versiones de los clústeres de HDInsight pueden cambiar en futuras actualizaciones de HDInsight. Para comprobar los componentes de Hadoop disponibles y comprobar las versiones que se están usando para un clúster, use la API REST de Ambari. El comando **GetComponentInformation** recupera información sobre componentes de servicio. Para obtener más información, consulte la [documentación de Ambari][ambari-docs].

Para los clústeres basados en Windows: otra forma de obtener las versiones de los componentes es iniciar sesión en un clúster mediante el Escritorio remoto y examinar directamente el contenido del directorio C:\apps\dist\".

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte la sección sobre la [puesta en desuso de HDInsight 3.3](#hdi-version-33-nearing-deprecation-date). 

### <a name="release-notes"></a>Notas de la versión

Consulte [Notas de la versión de HDInsight](hdinsight-release-notes.md) para conocer otras notas de las últimas versiones de HDInsight.

## <a name="supported-hdinsight-versions"></a>Versiones compatibles de HDInsight
En la siguiente tabla se enumeran las versiones de HDInsight que está disponibles actualmente, las versiones correspondientes de la distribución Hortonworks Data Platform que usan y sus fechas de lanzamiento. Si se conocen, también se proporcionan también las fechas de expiración y desuso. Tenga en cuenta la siguiente información de versión:

* Los clústeres de alta disponibilidad con dos nodos principales se implementan de forma predeterminada para los clústeres de HDInsight 2.1 y versiones posteriores. No están disponibles para los clústeres de HDInsight 1.6.
* Cuando expira el soporte técnico de una versión concreta, es posible que no esté disponible en el Portal de Azure. En la tabla siguiente se indica qué versiones están disponibles en el Portal de Azure clásico. Las versiones de clúster seguirán estando disponibles usando el parámetro `Version` en el comando [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) de Windows PowerShell y en el SDK de .NET hasta la fecha de degradación.

| Versión de HDInsight | Versión de HDP | SISTEMA OPERATIVO DE LA MÁQUINA VIRTUAL | Alta disponibilidad | Fecha de lanzamiento | Disponible en el Portal de Azure | Fecha de expiración del soporte técnico | Fecha de desuso |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 |HDP 2.6 |Ubuntu 16 |Sí |06/04/2017 |Sí | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |Sí |30/9/2016 |Sí |05/07/2017 |31/05/2018 |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Sí |29/03/2016 |Sí |29/12/2016 |9/1/2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS o Windows Server 2012R2 |Sí |12/02/2015 |Sí |27/06/2016 |31/07/2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012R2 |Sí |18/02/2015 |No |01/03/2016 |01/04/2017 |
| HDI 3,1 |HDP 2,1 |Windows Server 2012R2 |Sí |24/06/2014 |No |18/05/2015 |30/06/2016 |
| HDI 3,0 |HDP 2,0 |Windows Server 2012R2 |Sí |11/02/2014 |No |17/09/2014 |30/06/2015 |
| HDI 2,1 |HDP 1,3 |Windows Server 2012R2 |Sí |28/10/2013 |No |12/05/2014 |31/05/2015 |
| HDI 1.6 |HDP 1.1 | |No |28/10/2013 |No |26/04/2014 |31/05/2015 |

## <a name="hdi-version-33-nearing-deprecation-date"></a>HDI versión 3.3 cerca de la fecha de desuso
La compatibilidad con el clúster de HDI 3.3 expiró el 27/06/2016 y dejará de utilizarse en 31/07/2017. Si tiene un clúster de HDI 3.3, actualícelo a HDI 3.5 o HDI 3.6 cuanto antes. Las fechas de desuso de HDI 3.3 para Windows pueden variar en función de la región. Si la fecha de desuso planeada de la región es diferente, se le notificará por separado.

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>El contrato de nivel de servicio para las versiones de clúster de HDInsight
El Acuerdo de Nivel de Servicio se define en términos de **plazo de soporte técnico**. Un plazo de soporte técnico se refiere al período durante el cual la versión del clúster de HDInsight puede recibir soporte técnico por parte del Soporte técnico y el servicio al cliente de Microsoft. Un clúster de HDInsight está fuera del plazo de soporte técnico si su versión cuenta con una **fecha de expiración de soporte técnico** anterior a la fecha actual. En la tabla anterior se puede consultar una lista de las versiones de clúster de HDInsight con soporte técnico. La fecha de expiración de una versión determinada (X) de HDInsight (una vez que hay disponible una versión más reciente X+1) se calcula como la fecha más tardía de las dos siguientes:  

* Fórmula 1: agregue 180 días a la fecha en la que se lanzó la versión X del clúster de HDInsight.
* Fórmula 2: agregue 90 días a la fecha en la que la versión X+1 (versión subsiguiente después de X) del clúster de HDInsight se puso a su disposición en el Portal.

La **fecha de desuso** es la fecha tras la cual no se puede crear la versión del clúster en HDInsight. A partir del 31 de julio de 2017, no podrá cambiar el tamaño de un clúster después de su fecha de desuso. 

> [!NOTE]
> Los clústeres de HDInsight basados en Windows (incluidas las versiones 2.1, 3.0, 3.1, 3.2 y 3.3) se ejecutan en el SO invitado de Azure Familia 4 que usa la versión de 64 bits de Windows Server 2012 R2 y admite .NET Framework 4.0, 4.5, 4.5.1 y 4.5.2.
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de la versión de HortonWorks asociadas con las versiones de HDInsight
* La versión 3.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html). 
* La versión 3.5 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Este es el clúster de Hadoop **predeterminado** que se crea al usar el portal.
* La versión 3.4 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). 
* La versión 3.3 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * Las notas de la versión de Apache Storm están disponibles [aquí](https://storm.apache.org/2015/11/05/storm0100-released.html).
  * Las notas de la versión de Apache Hive están disponibles [aquí](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).
* La versión 3.2 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.2][hdp-2-2].  

  * Notas de la versión de componentes específicos de Apache: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) y [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* La versión 3.1 de HDInsight utiliza una distribución de Hadoop que se basa en [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Los clústeres de HDInsight 3.1 creados antes del 7/11/2014 se basaban en [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* La versión 3.0 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.0][hdp-2-0-8].
* La versión 2.1 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.3][hdp-1-3-0].
* La versión 1.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard y HDInsight Premium

Azure HDInsight proporciona las ofertas de macrodatos en la nube en dos categorías: **Estándar** y **Premium**. La tabla que aparece debajo de la sección recoge las características que **solo están disponibles con el tipo Premium**. Las características que no aparecen expresamente en esta tabla están disponibles con el tipo Estándar.

> [!NOTE]
> Actualmente, la oferta HDInsight Premium se encuentra en versión preliminar y solo está disponible para los clústeres de Linux.
>
>

| Característica de HDInsight Premium | Description |
| --- | --- |
| Usuarios de clústeres de HDInsight unidos a dominio |Una clústeres de HDInsight a dominios de Azure Active Directory (AAD) para obtener una seguridad de nivel empresarial. Ahora puede configurar una lista de empleados de su empresa que pueden autenticarse a través de Azure Active Directory para iniciar sesión en el clúster de HDInsight. El administrador de la empresa también puede configurar el control de acceso basado en roles para la seguridad de Hive con [Apache Ranger](http://hortonworks.com/apache/ranger/), que restringe el acceso a datos solo en la medida que sea necesario. Finalmente, el administrador puede auditar el acceso a los datos por parte de los empleados, y los cambios realizados en las directivas de control de acceso, con lo que se consigue un alto grado de control de los recursos corporativos. Para obtener más información, consulte [Configure domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a dominio). |

### <a name="cluster-types-supported-for-hdinsight-premium"></a>Tipos de clúster compatibles con HDInsight Premium
En la tabla siguiente se recoge el tipo de clúster de HDInsight y la matriz de compatibilidad Premium.

| Tipo de clúster | Estándar | Premium (vista previa) |
| --- | --- | --- |
| Hadoop |Sí |Sí (solo HDInsight 3.5 y 3.6) |
| Spark |Sí |No |
| HBase |Sí |No |
| Storm |Sí |No |
| R Server  |Sí |No |
| Interactive Hive (versión preliminar) |Sí |No |
| Kafka (Versión preliminar)|Sí|No| 

Esta tabla se actualizará cuando se incluyan nuevos tipos de clúster en HDInsight Premium.

### <a name="features-not-supported-for-hdinsight-premium"></a>Características no compatibles con HDInsight Premium

Las siguientes características no son compatibles en estos momentos con clústeres de HDInsight Premium.

* **Sin compatibilidad con Azure Data Lake Store como almacenamiento principal**. Todavía puede usar Azure Data Lake Store como almacenamiento complementario con clústeres de HDInsight Premium.

### <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio
Para obtener información sobre los precios y el contrato de nivel de servicio de HDInsight Premium, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

