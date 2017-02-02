---
title: "¿Cuáles son los diferentes componentes disponibles con un clúster de HDInsight? | Microsoft Docs"
description: "HDInsight admite varias versiones de clúster de Hadoop implementables. Vea las versiones de distribución de Hadoop y HortonWorks Data Platform (HDP) que se admiten."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: saurinsh
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 1761ee404e355d41170a5684a1a0ca6d2629f1e3
ms.openlocfilehash: 5b6819a583f5161854be0f71bbc5164afd57d6f9


---
# <a name="what-are-the-different-hadoop-components-available-with-hdinsight"></a>¿Cuáles son los diferentes componentes de Hadoop disponibles con HDInsight?
Conozca los diferentes niveles de servicio que ofrece HDInsight, así como las versiones de los distintos componentes de Hadoop incluidos con HDInsight.

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard y HDInsight Premium
Azure HDInsight proporciona las ofertas de macrodatos en la nube en dos categorías: **Estándar** y **Premium**. La tabla que aparece debajo de la sección recoge las características que **solo están disponibles con el tipo Premium**. Las características que no aparecen expresamente en esta tabla están disponibles con el tipo Estándar.

> [!NOTE]
> Actualmente, la oferta HDInsight Premium se encuentra en versión preliminar y solo está disponible para los clústeres de Linux.
> 
> 

| Característica de HDInsight Premium | Description |
| --- | --- |
| Usuarios de clústeres de HDInsight unidos a dominio |Una clústeres de HDInsight a dominios de Azure Active Directory (AAD) para obtener una seguridad de nivel empresarial. Ahora puede configurar una lista de empleados de su empresa que pueden autenticarse a través de Azure Active Directory para iniciar sesión en el clúster de HDInsight. El administrador de la empresa también puede configurar el control de acceso basado en roles para la seguridad de Hive con [Apache Ranger](http://hortonworks.com/apache/ranger/), que restringe el acceso a datos solo en la medida que sea necesario. Finalmente, el administrador puede auditar el acceso a los datos por parte de los empleados, y los cambios realizados en las directivas de control de acceso, con lo que se consigue un alto grado de control de los recursos corporativos. Para obtener más información, consulte [Configure domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a dominio). |

### <a name="cluster-types-supported-for-premium"></a>Tipos de clúster compatibles con Premium
En la tabla siguiente se recoge el tipo de clúster de HDInsight y la matriz de compatibilidad Premium.

| Tipo de clúster | Estándar | Premium |
| --- | --- | --- |
| Hadoop |yes |Sí (solo HDInsight 3.5) |
| Spark |Sí |No |
| HBase |Sí |No |
| Storm |Sí |No |
| Interactive Hive (versión preliminar) |Sí |No |
| R Server (versión preliminar de R Server) |Sí |No |

Esta tabla se actualizará cuando se incluyan nuevos tipos de clúster en HDInsight Premium.

### <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio
Para obtener información sobre los precios y el contrato de nivel de servicio de HDInsight Premium, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componentes de Hadoop disponibles con las distintas versiones de HDInsight
HDInsight de Azure es compatible con varias versiones de clústeres de Hadoop que se pueden implementar en cualquier momento. Cada versión crea una versión específica de la distribución HortonWorks Data Platform (HDP) y un conjunto de componentes que están incluidos en esa distribución. En la tabla siguiente se detallan las versiones de componente asociadas a las versiones de clúster de HDInsight. Tenga en cuenta que la versión de clúster predeterminada que usa HDInsight de Azure actualmente es la 3.4 y, desde el 14/09/2016, basada en HDP 2.4.

> [!NOTE]
> La versión predeterminada del servicio pueden cambiar sin previo aviso. Se recomienda especificar la versión al crear clústeres con el SDK de .NET, Azure PowerShell y la CLI de Azure, si tiene una dependencia de versiones. 
> 
> 

| Componente | Versión de HDInsight 3.5 | HDInsight versión 3.4 (predeterminada) | Versión de HDInsight 3.3 | HDInsight versión 3.2 | Versión de HDInsight 3.1 | HDInsight versión 3.0 |
| --- | --- | --- | --- | --- | --- | --- |
| Hortonworks Data Platform |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop y YARN |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 | |
| Apache Pig |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive y HCatalog |1.2.1.2.5 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| HBase Apache |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 | |
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 | |
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 | |
| Apache Phoenix |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 | |
| Spark de Apache |1.6.2 y 2.0 (solo Linux) |1.6.0 (solo Linux) |1.5.2 (solo Linux/compilación experimental) |1.3.1 (solo Windows) | | |

**Obtención de información sobre las versiones actuales de componentes**

Las versiones de los componentes asociados a las versiones de clúster de HDInsight pueden cambiar en futuras actualizaciones de HDInsight. Una forma de determinar los componentes disponibles y comprobar las versiones que se están usando para un clúster es usar la API REST de Ambari. El comando **GetComponentInformation** se puede usar para recuperar información acerca de un componente del servicio. Para obtener más información, consulte la [documentación de Ambari][ambari-docs]. Otra forma de obtener esta información es iniciar sesión en un clúster mediante el Escritorio remoto y examinar directamente el contenido del directorio C:\apps\dist\".

**Notas de la versión**

Consulte [Notas de la versión de HDInsight](hdinsight-release-notes.md) para conocer otras notas de las últimas versiones de HDInsight.

## <a name="supported-hdinsight-versions"></a>Versiones compatibles de HDInsight
En la siguiente tabla se enumeran las versiones de HDInsight que está disponibles actualmente, las versiones correspondientes de la distribución Hortonworks Data Platform que usan y sus fechas de lanzamiento. Si se conocen, también se proporcionan también las fechas de expiración y desuso. Tenga en cuenta lo siguiente:

* Los clústeres de alta disponibilidad con dos nodos principales se implementan de forma predeterminada para los clústeres de HDInsight 2.1 y versiones posteriores. No están disponibles para los clústeres de HDInsight 1.6.
* Cuando expira el soporte técnico de una versión concreta, es posible que no esté disponible en el Portal de Azure. En la tabla siguiente se indica qué versiones están disponibles en el Portal de Azure clásico. Las versiones de clúster seguirán estando disponibles usando el parámetro `Version` en el comando [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) y en el SDK de .NET hasta la fecha de degradación.

| Versión de HDInsight | Versión de HDP | SISTEMA OPERATIVO DE LA MÁQUINA VIRTUAL | Alta disponibilidad | Fecha de lanzamiento | Disponible en el Portal de Azure | Fecha de expiración del soporte técnico | Fecha de desuso |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |Sí |30/9/2016 |Sí | | |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Sí |29/03/2016 |Sí |29/12/2016 |9/1/2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS o Windows Server 2012R2 |Sí |12/02/2015 |Sí |27/06/2016 |31/07/2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012R2 |Sí |18/02/2015 |Sí |01/03/2016 |01/04/2017 |
| HDI 3,1 |HDP 2,1 |Windows Server 2012R2 |Sí |24/06/2014 |No |18/05/2015 |30/06/2016 |
| HDI 3,0 |HDP 2,0 |Windows Server 2012R2 |Sí |11/02/2014 |No |17/09/2014 |30/06/2015 |
| HDI 2,1 |HDP 1,3 |Windows Server 2012R2 |Sí |28/10/2013 |No |12/05/2014 |31/05/2015 |
| HDI 1.6 |HDP 1.1 | |No |28/10/2013 |No |26/04/2014 |31/05/2015 |

##<a name="hdi-version-32-and-33-nearing-deprecation-date"></a>HDI, versión 3.2 y 3.3 cerca de la fecha de desuso
La compatibilidad con el clúster de HDI 3.2 expiró el 01/03/2016 y dejará de utilizarse en 04/01/2017. La compatibilidad con el clúster de HDI 3.3 expiró el 27/06/2016 y dejará de utilizarse en 31/07/2017. Si tiene un clúster de HDI 3.2 o HDI 3.3, actualice el clúster a HDI 3,5 (última versión) pronto. 

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>El contrato de nivel de servicio para las versiones de clúster de HDInsight
El SLA se define en términos de "plazo de soporte técnico". Un plazo de soporte técnico se refiere al período durante el cual la versión del clúster de HDInsight puede recibir soporte técnico por parte del Soporte técnico y el servicio al cliente de Microsoft. Un clúster de HDInsight está fuera del plazo de soporte técnico si su versión cuenta con una **fecha de expiración de soporte técnico** anterior a la fecha actual. En la tabla anterior se puede consultar una lista de las versiones de clúster de HDInsight con soporte técnico. La fecha de expiración de una versión determinada (X) de HDInsight (una vez que hay disponible una versión más reciente X+1) se calcula como la fecha más tardía de las dos siguientes:  

* Fórmula 1: agregue 180 días a la fecha en la que se lanzó la versión X del clúster de HDInsight.
* Fórmula 2: agregue 90 días a la fecha en la que la versión X+1 (versión subsiguiente después de X) del clúster de HDInsight se puso a su disposición en el Portal.

La **fecha de desuso** es la fecha tras la cual no se puede crear la versión del clúster en HDInsight.

> [!NOTE]
> Los clústeres de HDInsight basados en Windows (incluidas las versiones 2.1, 3.0, 3.1, 3.2 y 3.3) se ejecutan en el SO invitado de Azure Familia 4 que usa la versión de 64 bits de Windows Server 2012 R2 y admite .NET Framework 4.0, 4.5, 4.5.1 y 4.5.2.
> 
> 

##<a name="hdinsight-deprecation-on-windows"></a>Degradación de HDInsight en Windows
A partir de HDI, versión 3.4, hemos publicado HDInsight solo en el sistema operativo Linux. Algunas de las ofertas de HDInsight están disponibles solo para Linux: Apache Ranger, aplicaciones de HDInsight, Azure Data Lake Store como FS principal, etc. Esto tiene varias ventajas para los clientes

* Podemos incorporar la tecnología de macrodatos de código abierto al mercado a través del servicio HDInsight
* Existe una gran comunidad y ecosistema de soporte técnico
* Desarrollo activo mediante la comunidad de código abierto para Hadoop y tecnologías de macrodatos más recientes 
* El servicio HDInsight puede centrarse más en la tecnología de código abierto de macrodatos 

Para inversiones continuas en las tecnologías de macrodatos de código abierto, las versiones futuras de HDInsight estarán disponibles solo en el sistema operativo Linux. No habrá ninguna versión futura de HDInsight en el sistema operativo Windows. La última versión de HDInsight en Windows fue HDI 3.3. La compatibilidad con HDI 3.3 expiró el 27/06/2016 y dejará de utilizarse en 31/07/2017. Consulte [esto](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) para realizar la migración desde un clúster de HDInsight basado en Windows a un clúster basado en Linux.


## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de la versión de HortonWorks asociadas con las versiones de HDInsight
* La versión 3.4 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). Este es el clúster de Hadoop **predeterminado** que se crea al usar el portal.
* La versión 3.3 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).
  
  * Las notas de la versión de Apache Storm están disponibles [aquí](https://storm.apache.org/2015/11/05/storm0100-released.html).
  * Las notas de la versión de Apache Hive están disponibles [aquí](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).
* La versión 3.2 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.2][hdp-2-2].  
  
  * Notas de la versión de componentes específicos de Apache: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) y [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* La versión 3.1 de HDInsight utiliza una distribución de Hadoop que se basa en [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Los clústeres de HDInsight 3.1 creados antes del 7/11/2014 se basaban en [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* La versión 3.0 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.0][hdp-2-0-8].
* La versión 2.1 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.3][hdp-1-3-0].
* La versión 1.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.1][hdp-1-1-0].

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



<!--HONumber=Dec16_HO2-->


