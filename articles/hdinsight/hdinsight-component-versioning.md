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
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 54254203b79c6d69a2febc5987b0a24077a84939
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

 

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
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, vea [Windows retirement on HDInsight](#hdi-version-33-nearing-retirement-date) (Retirada de Windows en HDInsight). 

### <a name="release-notes"></a>Notas de la versión

Consulte [Notas de la versión de HDInsight](hdinsight-release-notes.md) para conocer otras notas de las últimas versiones de HDInsight.

## <a name="supported-hdinsight-versions"></a>Versiones compatibles de HDInsight
En la siguiente tabla se enumeran las versiones de HDInsight que está disponibles actualmente, las versiones correspondientes de la distribución Hortonworks Data Platform que usan y sus fechas de lanzamiento. Si se conocen, también se proporcionan las fechas de expiración y retirada. Tenga en cuenta la siguiente información de versión:

* Los clústeres de alta disponibilidad con dos nodos principales se implementan de forma predeterminada para los clústeres de HDInsight 2.1 y versiones posteriores. No están disponibles para los clústeres de HDInsight 1.6.
* Cuando expira el soporte técnico de una versión concreta, es posible que no esté disponible en el Portal de Azure. En la tabla siguiente se indica qué versiones están disponibles en el Portal de Azure clásico. Las versiones de clúster seguirán estando disponibles con el parámetro `Version` en el comando [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) de Windows PowerShell y en el SDK de .NET hasta la fecha de retirada.

| Versión de HDInsight | Versión de HDP | SISTEMA OPERATIVO DE LA MÁQUINA VIRTUAL | Alta disponibilidad | Fecha de lanzamiento | Disponible en el Portal de Azure | Fecha de expiración del soporte técnico | Fecha de retirada |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 |HDP 2.6 |Ubuntu 16 |Sí |06/04/2017 |Sí | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |Sí |30/9/2016 |Sí |05/07/2017 |31/05/2018 |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Sí |29/03/2016 |Sí |29/12/2016 |9/1/2018 |
| HDI 3.3 |HDP 2.3 |Windows Server 2012R2 |Sí |12/02/2015 |Sí |27/06/2016 |07/31/2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |Sí |12/02/2015 |Sí |27/06/2016 |31/07/2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012R2 |Sí |18/02/2015 |No |01/03/2016 |01/04/2017 |
| HDI 3,1 |HDP 2,1 |Windows Server 2012R2 |Sí |24/06/2014 |No |18/05/2015 |30/06/2016 |
| HDI 3,0 |HDP 2,0 |Windows Server 2012R2 |Sí |11/02/2014 |No |17/09/2014 |30/06/2015 |
| HDI 2,1 |HDP 1,3 |Windows Server 2012R2 |Sí |28/10/2013 |No |12/05/2014 |31/05/2015 |
| HDI 1.6 |HDP 1.1 | |No |28/10/2013 |No |26/04/2014 |31/05/2015 |

## <a name="hdinsight-windows-retirement"></a>Retirada de Windows de HDInsight

Microsoft Azure HDInsight (HDI) versión 3.3 fue la última versión de HDInsight en Windows y se retirará el 31 de julio de 2018. Si tiene algún clúster HDI en Windows (3.3 o anterior), debe migrar a HDInsight en Linux (HDI 3.5 o posterior) antes del 31 de julio de 2018 para conservar la capacidad de crear clústeres HDI o cambiar su tamaño. La compatibilidad con HDI 3.3 en Windows expiró el 27 de junio de 2016. 
 
A partir de HDInsight versión 3.4, Microsoft ha lanzado HDInsight únicamente en el sistema operativo Linux. Como resultado, algunos de los componentes de HDInsight solo están disponibles para Linux: Apache Ranger, Kafka, Hive interactivo, Spark, Aplicaciones de HDInsight y Azure Data Lake Store como sistema de archivos principal. Las versiones futuras de HDInsight solo estarán disponibles en el sistema operativo Linux. No habrá ninguna versión futura de HDInsight en el sistema operativo Windows.

### <a name="faqs"></a>Preguntas más frecuentes

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>¿Cuál es la escala de tiempo para la retirada de HDInsight en Windows?
El 31 de julio de 2018 es la fecha de retirada de HDInsight en Windows. Si la fecha de retirada planeada para su región es diferente, se le notificará de forma individual. 

### <a name="what-will-be-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>¿Cuál será el impacto de la retirada de HDInsight en Windows para los clientes existentes?
* Después de la fecha de retirada no será posible crear un nuevo HDI en el clúster de Windows.
* Después de la fecha de retirada no será posible cambiar el tamaño de un HDInsight existente en el clúster de Windows. 
 * Las versiones futuras de HDInsight solo estarán disponibles en el sistema operativo Linux. No habrá ninguna versión futura de HDInsight en el sistema operativo Windows.
* Tenga en cuenta que la compatibilidad con HDInsight 3.3 expiró el 27 de junio de 2016. Por lo tanto, no hay soporte técnico ni correcciones de errores para HDInsight 3.3 o versiones anteriores. 
 
### <a name="which-versions-of-hdinsight-on-windows-are-impacted"></a>¿Qué versiones de HDInsight en Windows se ven afectadas?
Azure HDInsight versión 3.3 fue la última versión de HDInsight para Windows. Cualquier clúster de HDInsight que se ejecute en Windows (3.3 o anterior) debe migrarse a HDInsight en Linux (3.5 o posterior) antes de la fecha de retirada para conservar la capacidad de crear nuevos clústeres HDI o cambiar el tamaño de los existentes. 

### <a name="what-do-i-need-to-do"></a>¿Qué tengo que hacer?
Vea [este](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) documento para migrar clústeres de HDInsight basados en Windows a un clúster de HDInsight basado en Linux compatible antes del 31 de julio de 2018. [Aquí](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions) encontrará más información sobre las versiones de HDI compatibles. 

### <a name="where-do-i-find-the-cluster-os-type"></a>¿Dónde se encuentra el tipo de sistema operativo del clúster?
En el portal de Azure, vaya a la página de información general del clúster de HDInsight. En esa página encontrará el tipo de clúster en Información esencial. Ahí se indicará el tipo de sistema operativo del clúster. 

### <a name="what-will-be-the-impact-to-my-hdinsight-windows-cluster-if-i-cant-migrate-to-a-linux-based-cluster-by-jul-31st-2018"></a>¿Cuál será el impacto en el clúster de HDInsight en Windows si no se puede migrar a un clúster basado en Linux antes del 31 de julio de 2018?
El clúster de HDInsight en Windows se ejecutará tal cual, pero no podrá crear un nuevo clúster de HDInsight en Windows ni cambiar el tamaño de un HDInsight existente en el clúster de Windows. 

### <a name="my-cluster-has-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>El clúster tiene dependencia de .NET. ¿Cómo se resuelve esta dependencia en Linux?
[Mono](http://www.mono-project.com/), una implementación de código abierto de .NET, está disponible en los clústeres de HDInsight en Linux. Vea [este](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) documento para obtener más detalles. 

### <a name="i-am-a-new-customer-trying-to-create-an-hdinsight-windows-based-cluster-however-i-dont-see-the-option-in-the-azure-portal-or-i-am-unable-to-create-this-from-powershell-or-sdk-how-can-i-create-an-hdi-windows-based-cluster"></a>Soy un nuevo cliente que intenta crear un clúster de HDInsight basado en Windows, pero no veo la opción en el portal de Azure o no puedo crearlo desde PowerShell ni el SDK. ¿Cómo se puede crear un clúster HDI basado en Windows?
A partir del 3 de julio de 2017, solo los clientes existentes de HDInsight basado en Windows pueden crear nuevos clústeres HDI basados en Windows (hasta la fecha de retirada). Se recomienda crear un clúster HDI basado en Linux. 

### <a name="is-there-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>¿La migración de HDInsight en Windows a HDInsight en Linux afecta en algo al precio?
No, el precio es el mismo para HDInsight en cualquier sistema operativo. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-hdinsight-only-on-the-linux-os"></a>¿Cuáles son las ventajas para el cliente de la migración a HDInsight solo en el sistema operativo Linux?
* Un tiempo de comercialización más rápido para tecnologías de macrodatos de código abierto a través del servicio HDInsight
* Una gran comunidad y ecosistema de soporte técnico
* Mayor posibilidad de aprovechar el desarrollo activo mediante la comunidad de código abierto para Hadoop y tecnologías de macrodatos más recientes

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-that-available-in-hdinsight-on-windows"></a>¿HDInsight en Linux proporciona funcionalidad adicional más allá de la disponible en HDInsight en Windows?
A partir de HDInsight versión 3.4, MSFT ha lanzado HDInsight únicamente en el sistema operativo Linux. Como resultado, algunos de los componentes de HDInsight solo están disponibles para Linux: Apache Ranger, Kafka, Hive interactivo, Spark, Aplicaciones de HDInsight y Azure Data Lake Store como sistema de archivos principal. 

## <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>El contrato de nivel de servicio para las versiones de clúster de HDInsight
El Acuerdo de Nivel de Servicio se define en términos de **plazo de soporte técnico**. Un plazo de soporte técnico se refiere al período durante el cual la versión del clúster de HDInsight puede recibir soporte técnico por parte del Soporte técnico y el servicio al cliente de Microsoft. Si la versión tiene una **fecha de expiración de soporte técnico** anterior a la fecha actual, el clúster de HDInsight está fuera del servicio de soporte técnico. En la tabla anterior se puede consultar una lista de las versiones de clúster de HDInsight con soporte técnico. La fecha de expiración de una versión determinada (X) de HDInsight (una vez que hay disponible una versión más reciente X+1) se calcula como la fecha más tardía de las dos siguientes:  

* Fórmula 1: agregue 180 días a la fecha en la que se lanzó la versión X del clúster de HDInsight.
* Fórmula 2: agregue 90 días a la fecha en la que la versión X+1 (versión subsiguiente después de X) del clúster de HDInsight se puso a su disposición en el Portal.

La **fecha de retirada** es la fecha tras la cual no se puede crear la versión del clúster en HDInsight. A partir del 31 de julio de 2017, no se puede cambiar el tamaño de un clúster después de su fecha de retirada. 

> [!NOTE]
> Los clústeres de HDInsight basados en Windows (incluidas las versiones 2.1, 3.0, 3.1, 3.2 y 3.3) se ejecutan en el SO invitado de Azure Familia 4 que usa la versión de 64 bits de Windows Server 2012 R2 y admite .NET Framework 4.0, 4.5, 4.5.1 y 4.5.2.
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de la versión de HortonWorks asociadas con las versiones de HDInsight
* La versión 3.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html). 
* La versión 3.5 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Este clúster de Hadoop es el **predeterminado** que se crea al usar el portal.
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

Azure HDInsight proporciona las ofertas de macrodatos en la nube en dos categorías: **Estándar** y **Premium**. La tabla de la siguiente sección indica las características que **solo están disponibles con el tipo Premium**. Las características que no aparecen expresamente en esta tabla están disponibles con el tipo Estándar.

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

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuración de nodo predeterminada y tamaños de máquina virtual para clústeres
En las tablas siguientes se indican los tamaños de máquina virtual predeterminados para clústeres de HDInsight:

> [!IMPORTANT]
> Si necesita más de 32 nodos de trabajo en un clúster, tiene que seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
>

* Todas las regiones, excepto Sur de Brasil y Japón Occidental:

  | Tipo de clúster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Principal: tamaño de máquina virtual predeterminado |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | Principal: tamaños de máquina virtual recomendados |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2 |A3, A4, A5 |D12 v2, v2 D13 y D14 v2 |D12 v2, v2 D13 y D14 v2 |
  | Trabajo: tamaño de máquina virtual predeterminado |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Trabajo: tamaños de máquina virtual recomendados |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2 |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |
  | Zookeeper: tamaño de máquina virtual predeterminado | |A3 |A2 | | |
  | Zookeeper: tamaños de máquina virtual recomendados | |A3, A4, A5 |A2, A3, A4 | | |
  | Perimetral: tamaño de máquina virtual predeterminado | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Perimetral: tamaño de máquina virtual recomendado | | | | |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |
* Solo Sur de Brasil y Japón Occidental (ningún tamaño v2 aquí):

  | Tipo de clúster | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | Principal: tamaño de máquina virtual predeterminado |D3 |D3 |A3 |D12 |D12 |
  | Principal: tamaños de máquina virtual recomendados |D3, D4, D12 |D3, D4, D12 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Trabajo: tamaño de máquina virtual predeterminado |D3 |D3 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Trabajo: tamaños de máquina virtual recomendados |D3, D4, D12 |D3, D4, D12 |D3, D4, D12 |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |
  | Zookeeper: tamaño de máquina virtual predeterminado | |A2 |A2 | | |
  | Zookeeper: tamaños de máquina virtual recomendados | |A2, A3, A4 |A2, A3, A4 | | |
  | Perimetral: tamaños de máquina virtual predeterminados | | | | |Windows: D12; Linux: D4 |
  | Perimetral: tamaños de máquina virtual recomendados | | | | |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |

> [!NOTE]
> Nota: Principal se conoce como *Nimbus* para el tipo de clúster de Storm. Trabajo se conoce como *Región* en el tipo de clúster de HBase y como *Supervisor* en el tipo de clúster de Storm.

## <a name="next-steps"></a>Pasos siguientes
- [Configuración de clúster para Hadoop, Spark, etc. en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabajo en Hadoop en HDInsight desde un equipo Windows](hdinsight-hadoop-windows-tools.md)

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

