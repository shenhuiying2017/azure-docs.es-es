---
title: 'Componentes y versiones de Hadoop: Azure HDInsight | Microsoft Docs'
description: "Conozca los componentes y las versiones de Hadoop en HDInsight y los niveles de servicio disponibles en esta distribución de nube de Hortonworks Data Platform."
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
ms.date: 01/09/2018
ms.author: bprakash
ms.openlocfilehash: 35ded349e9ced6463ece876ca1fd92423d20c625
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>¿Cuáles son los componentes y versiones de Hadoop disponibles con HDInsight?

Aprenda sobre el paquete de seguridad de la empresa, así como sobre los componentes del ecosistema Apache Hadoop y las versiones incluidas en Microsoft Azure HDInsight. Además, obtenga información sobre cómo comprobar las versiones de componentes de Hadoop en HDInsight. 

Cada versión de HDInsight es una distribución de nube de una versión de Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componentes de Hadoop disponibles con las distintas versiones de HDInsight
HDInsight de Azure es compatible con varias versiones de clústeres de Hadoop que se pueden implementar en cualquier momento. Cada versión crea una versión específica de la distribución HDP y un conjunto de componentes que están incluidos en esa distribución. A partir del 17 de febrero de 2017, la versión de clúster predeterminada que usa Azure HDInsight actualmente es la 3.5 y se basa en HDP 2.5.

En la tabla siguiente se enumeran las versiones de componente asociadas a las versiones de clúster de HDInsight: 

> [!NOTE]
> La versión predeterminada del servicio HDInsight puede cambiar sin previo aviso. Si tiene una dependencia de la versión, especifique la versión de HDInsight al crear clústeres con el SDK de .NET con Azure PowerShell y la CLI de Azure.

| Componente | HDInsight 3.6 (predeterminado) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop y YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive y HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| HBase Apache |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Spark de Apache |2.1.0 (solo Linux) |1.6.2 y 2.0 (solo Linux) |1.6.0 (solo Linux) |1.5.2 (solo la compilación experimental de Linux) |1.3.1 (solo Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Comprobación de la información de la versión de los componentes actuales de Hadoop

Las versiones de los componentes del ecosistema de Hadoop asociados a las versiones de los clústeres de HDInsight pueden cambiar en futuras actualizaciones de HDInsight. Para comprobar los componentes de Hadoop disponibles y comprobar las versiones que se están usando para un clúster, use la API REST de Ambari. El comando **GetComponentInformation** recupera información sobre componentes de servicio. Para obtener más información, consulte la [documentación de Ambari][ambari-docs].

Para los clústeres de Windows, otra forma de obtener las versiones de los componentes es iniciar sesión en un clúster mediante el Escritorio remoto y examinar directamente el contenido del directorio C:\apps\dist\.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las versiones posteriores. Para más información, vea [Windows retirement on HDInsight](#hdinsight-windows-retirement) (Retirada de Windows en HDInsight).

### <a name="release-notes"></a>Notas de la versión

Consulte [Notas de la versión de HDInsight](hdinsight-release-notes.md) para conocer otras notas de las últimas versiones de HDInsight.

## <a name="supported-hdinsight-versions"></a>Versiones compatibles de HDInsight
Las tablas siguientes enumeran las versiones de HDInsight. Las versiones de HDP que corresponden a cada versión de HDInsight se muestran junto con las fechas de lanzamiento del producto. Si se conocen, también se proporcionan las fechas de expiración y retirada.

### <a name="available-versions"></a>Versiones disponibles

En la tabla siguiente se enumeran las versiones de HDInsight que están disponibles en Azure Portal, así como otros métodos de implementación como PowerShell y .NET SDK.

| Versión de HDInsight | Versión de HDP | SISTEMA OPERATIVO DE LA MÁQUINA VIRTUAL | Fecha de lanzamiento | Fecha de expiración del soporte técnico | Fecha de retirada | Alta disponibilidad |  Disponibilidad en Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4 de abril de 2017 | | |Sí |Sí |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 de septiembre de 2016 |5 de septiembre de 2017 |31 de mayo de 2018 |Sí |Sí |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 de diciembre de 2015 |27 de junio de 2016 |31 de julio de 2018 |Sí |Sin  |

> [!NOTE]
> Cuando expira la compatibilidad de una versión, puede dejar de estar disponible en Microsoft Azure Portal. Sin embargo, las versiones de clúster seguirán estando disponibles con el parámetro `Version` en el comando [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) de Windows PowerShell y en el SDK de .NET hasta la fecha de retirada de la versión.
>

### <a name="retired-versions"></a>Versiones retiradas

En la tabla siguiente se enumeran las versiones de HDInsight que **no** están disponibles en Azure Portal.

| Versión de HDInsight | Versión de HDP | SISTEMA OPERATIVO DE LA MÁQUINA VIRTUAL | Fecha de lanzamiento | Fecha de expiración del soporte técnico | Fecha de retirada | Alta disponibilidad |  Disponibilidad en Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 de marzo de 2016 |29 de diciembre de 2016 |9 de enero de 2018 |Sí |Sin  |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 de diciembre de 2015 |27 de junio de 2016 |31 de julio de 2017 |Sí |Sin  |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS o Windows Server 2012 R2 |18 de febrero de 2015 |1 de marzo de 2016 |1 de abril de 2017 |Sí |Sin  |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24 de junio de 2014 |18 de mayo de 2015 |30 de junio de 2016 |Sí |Sin  |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11 de febrero de 2014 |17 de septiembre de 2014 |30 de junio de 2015 |Sí |Sin  |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28 de octubre de 2013 |12 de mayo de 2014 |31 de mayo de 2015 |Sí |Sin  |
| HDInsight 1.6 |HDP 1.1 | |28 de octubre de 2013 |26 de abril de 2014 |31 de mayo de 2015 |Sin  |Sin  |

> [!NOTE]
> Los clústeres de alta disponibilidad con dos nodos principales se implementan de forma predeterminada para los clústeres de HDInsight 2.1 y versiones posteriores. No están disponibles para los clústeres de HDInsight 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Paquete de seguridad de la empresa para HDInsight

Azure HDInsight es un paquete opcional que puede agregar en su clúster de HDInsight como parte del flujo de trabajo de creación del clúster. El paquete de seguridad de la empresa admite:

- Integración con Active Directory para autenticación.

    En el pasado, solo se podían crear clústeres de HDInsight con un usuario administrador local y un usuario de SSH local. El usuario administrador local podía acceder a todos los archivos, carpetas, tablas y columnas.  Con el paquete de seguridad de la empresa, puede habilitar el control de acceso basado en rol mediante la integración de clústeres de HDInsight con su propia instancia de Active Directory, incluyendo Active Directory local, Azure Active Directory Domain Services o Active Directory en una máquina virtual de IaaS. El administrador de dominio del clúster puede conceder a los usuarios permiso para utilizar sus propias credenciales corporativas (dominio) de nombre de usuario y contraseña para acceder al clúster. 

    Para más información, consulte:

    - [Introducción a la seguridad de Hadoop con clústeres de HDInsight unidos a dominio](./domain-joined/apache-domain-joined-introduction.md)
    - [Planeamiento de clústeres de Hadoop unidos a un dominio de Azure en HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Configuración de un entorno de espacio aislado unido a un dominio](./domain-joined/apache-domain-joined-configure.md)
    - [Configurar clústeres de HDInsight unidos a un dominio con Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorización de datos

    - Integración con Apache Ranger para la autorización de Hive, Spark SQL y colas de Yarn.
    - Puede establecer un control de acceso en archivos y carpetas.

    Para más información, consulte:

    - [Configuración de directivas de Hive en HDInsight unido a un dominio](./domain-joined/apache-domain-joined-run-hive.md)

- Vea los registros de auditoría para supervisar accesos y las directivas configuradas. 

### <a name="supported-cluster-types"></a>Tipos de clúster compatibles

Actualmente, solo los siguientes tipos de clúster admiten el paquete de seguridad de la empresa:

- Hadoop (solo HDInsight 3.6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-store"></a>Compatibilidad con Almacén de Azure Data Lake

El paquete de seguridad de la empresa permite usar Azure Data Lake Store como almacenamiento principal y como almacenamiento complementario.

### <a name="pricing-and-sla"></a>Precios y contrato de nivel de servicio
Para obtener información sobre los precios y el Acuerdo de Nivel de Servicio del paquete de seguridad de la empresa, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Retirada de Windows de HDInsight
Microsoft Azure HDInsight 3.3 fue la última versión de HDInsight en Windows. La fecha de retirada de HDInsight en Windows es el 31 de julio de 2018. Si tiene clústeres de HDInsight en Windows 3.3 o en alguna versión anterior, debe migrar a HDInsight en Linux (HDInsight 3.5 o posterior) antes del 31 de julio de 2018. La migración al sistema operativo Linux permite conservar la capacidad de crear clústeres de HDInsight o de cambiar su tamaño. La compatibilidad con HDInsight 3.3 en Windows expiró el 27 de junio de 2016.

A partir de HDInsight versión 3.4, Microsoft ha lanzado HDInsight únicamente en el sistema operativo Linux. Como resultado, algunos de los componentes de HDInsight solo están disponibles para Linux. Entre ellos se incluyen las aplicaciones Apache Ranger, Kafka, Interactive Query, Spark y HDInsight, y Azure Data Lake Store como sistema de archivos principal. Las versiones futuras de HDInsight solo estarán disponibles en el sistema operativo Linux. No habrá ninguna versión futura de HDInsight en Windows. 

## <a name="faqs"></a>Preguntas más frecuentes

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>¿Cuál es la escala de tiempo para la retirada de HDInsight en Windows?
El 31 de julio de 2018 es la fecha de retirada de HDInsight en Windows. Si la fecha de retirada planeada para su región es diferente, se le notificará de forma individual. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>¿Cuál es el impacto de la retirada de HDInsight en Windows para los clientes existentes?
Después de retirar HDInsight en Windows, no puede crear ningún clúster de HDInsight en Windows ni cambiar el tamaño de un clúster existente de este tipo. La compatibilidad con HDInsight 3.3 expiró el 27 de junio de 2016. Por lo tanto, no hay soporte técnico ni correcciones de errores para HDInsight 3.3 o versiones anteriores. Las versiones futuras de HDInsight solo estarán disponibles en el sistema operativo Linux. No habrá ninguna versión futura de HDInsight en Windows.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>¿Qué versiones de HDInsight en Windows se ven afectadas?
Azure HDInsight 3.3 es la última versión de HDInsight para Windows. Antes de la retirada de HDInsight en Windows, todos los clústeres de HDInsight 3.3 en Windows o de versiones anteriores se deben migrar a HDInsight 3.5 o posterior en Linux. La migración de los clústeres a HDInsight en Linux permite conservar la capacidad de crear clústeres o cambiar el tamaño de los clústeres existentes. 

### <a name="what-do-i-need-to-do"></a>¿Qué tengo que hacer?
Migre los clústeres de HDInsight en Windows a un clúster de HDInsight en Linux compatible antes del 31 de julio de 2018. Obtenga más información en el [documento de migración de HDInsight](hdinsight-migrate-from-windows-to-linux.md). Para obtener información detallada sobre las versiones de Azure HDInsight, consulte la lista de [versiones compatibles](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>¿Dónde se encuentra el tipo de sistema operativo del clúster?
En Azure Portal, vaya a la página de información general de Clústeres de HDInsight y busque **Tipo de clúster** en **Información esencial**. Los tipos de sistema operativo de clúster se enumeran en esa página. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>No se puede migrar a un clúster de HDInsight en Linux antes del 31 de julio de 2018. ¿Cuál es el impacto en el clúster de HDInsight en Windows?
El clúster de HDInsight en Windows se ejecuta con normalidad, pero no puede crear ningún clúster de HDInsight en Windows ni cambiar el tamaño de un clúster existente de este tipo. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>El clúster tiene una dependencia de .NET. ¿Cómo se resuelve esta dependencia en Linux?
Puede resolver la dependencia de clústeres de Linux mediante el [proyecto de Mono](http://www.mono-project.com/). Esta implementación de código abierto de .NET está disponible para los clústeres de HDInsight en Linux. Obtenga más información en el [documento de migración de HDInsight](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Soy un nuevo cliente de HDInsight en Windows. ¿Cómo se puede crear un clúster de HDInsight en Windows?
A partir del 3 de julio de 2017, solo los clientes existentes de HDInsight en Windows pueden crear clústeres de HDInsight en Windows. Los nuevos clientes no pueden crear un clúster de HDInsight en Windows en Azure Portal con PowerShell o el SDK. Se recomienda que los nuevos clientes creen un clúster de HDInsight en Linux. Los clientes existentes pueden crear clústeres de HDInsight en Windows hasta la fecha de retirada de esta característica. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>¿La migración de HDInsight en Windows a HDInsight en Linux afecta en algo al precio?
No, el precio es el mismo para HDInsight en cualquier sistema operativo. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>¿Cuáles son las ventajas para el cliente asociadas con la migración a HDInsight solo en Linux?
* Un tiempo de comercialización más rápido para tecnologías de macrodatos de código abierto a través del servicio HDInsight
* Una gran comunidad y ecosistema de soporte técnico
* Posibilidad de aprovechar el desarrollo activo mediante la comunidad de código abierto para Hadoop y otras tecnologías de macrodatos

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>¿HDInsight en Linux proporciona funcionalidad adicional más allá de la disponible en HDInsight en Windows?
A partir de HDInsight versión 3.4, Microsoft ha lanzado HDInsight únicamente en el sistema operativo Linux. Como resultado, algunos de los componentes de HDInsight solo están disponibles para Linux. Entre ellos se incluyen las aplicaciones Apache Ranger, Kafka, Interactive Query, Spark y HDInsight, y Azure Data Lake Store como sistema de archivos principal. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Contrato de nivel de servicio para las versiones de clúster de HDInsight
El contrato de nivel de servicio (SLA) de define en términos de _plazo de soporte técnico_. Un plazo de soporte técnico se refiere al período durante el cual la versión del clúster de HDInsight puede recibir soporte técnico por parte del Soporte técnico y el servicio al cliente de Microsoft. Si la versión tiene una _fecha de expiración de soporte técnico_ que ya ha pasado, el clúster de HDInsight está fuera del servicio de soporte técnico. Para más información sobre las versiones compatibles, vea la lista de [versiones compatibles de clústeres de HDInsight](hdinsight-migrate-from-windows-to-linux.md). La fecha de expiración del soporte técnico de una versión determinada X de HDInsight (una vez que hay disponible una versión más reciente X+1) se calcula como la fecha más tardía de las dos siguientes:  

* Fórmula 1: agregue ciento ochenta días a la fecha en la que se lanzó la versión X del clúster de HDInsight.
* Fórmula 2: agregue noventa días a la fecha en la que la versión del clúster de HDInsight x+1 se encuentra disponible en Azure Portal.

La _fecha de retirada_ es la fecha tras la cual no se puede crear la versión del clúster en HDInsight. A partir del 31 de julio de 2017, no se puede cambiar el tamaño de un clúster de HDInsight después de su fecha de retirada. 

> [!NOTE]
> Los clústeres de HDInsight en Windows (incluidas las versiones 2.1, 3.0, 3.1, 3.2 y 3.3) se ejecutan en el SO invitado de Azure Familia 4 que usa la versión de 64 bits de Windows Server 2012 R2. El SO invitado de Azure Familia 4 es compatible con las versiones .NET Framework 4.0, 4.5, 4.5.1 y 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de la versión de HortonWorks asociadas con las versiones de HDInsight

En la sección se proporcionan vínculos a las notas de la versión para las distribuciones de Hortonworks Data Platform y los componentes de Apache usados con HDInsight.
* La versión 3.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* La versión 3.5 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). El clúster de HDInsight 3.5 es el clúster de Hadoop _predeterminado_ que se crea en Azure Portal.
* La versión 3.4 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* La versión 3.3 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * Las [notas de la versión de Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) están disponibles en el sitio web de Apache.
  * Las [notas de la versión de Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) están disponibles en el sitio web de Apache.
* La versión 3.2 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.2][hdp-2-2].

  * Las notas de la versión de los componentes específicos de Apache se encuentran disponibles como sigue: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) y [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* El clúster de HDInsight 3.1 utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Los clústeres de HDInsight 3.1 creados antes 7 de noviembre de 2014 se basaban en [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* La versión 3.0 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 2.0][hdp-2-0-8].
* La versión 2.1 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.3][hdp-1-3-0].
* La versión 1.6 del clúster de HDInsight utiliza una distribución de Hadoop basada en [Hortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Configuración de nodo predeterminada y tamaños de máquina virtual para clústeres
En las tablas siguientes se indican los tamaños de máquina virtual predeterminados para clústeres de HDInsight.

> [!IMPORTANT]
> Si necesita más de 32 nodos de trabajo en un clúster, tiene que seleccionar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
> 
> 

* Todas las regiones, excepto Sur de Brasil y Japón Occidental:

  | Tipo de clúster | Hadoop | hbase | Interactive Query | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Principal: tamaño de máquina virtual predeterminado |D3 v2 |D3 v2 | D13, D14 |A3 |D12 v2 |D12 v2 |
  | Principal: tamaños de máquina virtual recomendados |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2  | D13, D14 |A3, A4, A5 |D12 v2, v2 D13 y D14 v2 |D12 v2, v2 D13 y D14 v2 |
  | Trabajo: tamaño de máquina virtual predeterminado |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Trabajo: tamaños de máquina virtual recomendados |D3 v2, D4 v2 y D12 v2 |D3 v2, D4 v2 y D12 v2  | D13, D14 |D3 v2, D4 v2 y D12 v2 |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |
  | Zookeeper: tamaño de máquina virtual predeterminado | |A3 | |A2 | | |
  | Zookeeper: tamaños de máquina virtual recomendados | |A3, A4, A5 | | A2, A3, A4 | | |
  | Perimetral: tamaño de máquina virtual predeterminado | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Perimetral: tamaño de máquina virtual recomendado | | | | | |Windows: D12 v2, v2 D13 y D14 v2; Linux: D4 v2, D12 v2, v2 D13 y D14 v2 |
* Solo Sur de Brasil y Japón Occidental (ningún tamaño v2):

  | Tipo de clúster | Hadoop | hbase | Interactive Query |Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Principal: tamaño de máquina virtual predeterminado |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | Principal: tamaños de máquina virtual recomendados |D3, D4, D12 |D3, D4, D12  | D13, D14 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Trabajo: tamaño de máquina virtual predeterminado |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Trabajo: tamaños de máquina virtual recomendados |D3, D4, D12 |D3, D4, D12  | D13, D14 |D3, D4, D12 |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |
  | Zookeeper: tamaño de máquina virtual predeterminado | |A2 | | A2 | | |
  | Zookeeper: tamaños de máquina virtual recomendados | |A2, A3, A4 | |A2, A3, A4 | | |
  | Perimetral: tamaños de máquina virtual predeterminados | | | | | |Windows: D12; Linux: D4 |
  | Perimetral: tamaños de máquina virtual recomendados | | | | | |Windows: D12, D13 y D14; Linux: D4, D12, D13 y D14 |

> [!NOTE]
> - Nota: Principal se conoce como *Nimbus* para el tipo de clúster de Storm.
> - El trabajo se conoce como *Supervisor* para el tipo de clúster de Storm.
> - El trabajo se conoce como *Región* para el tipo de clúster de HBase.

## <a name="next-steps"></a>pasos siguientes
- [Configuración de clúster para Hadoop, Spark, etc. en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Trabajo en Hadoop en HDInsight desde un equipo Windows](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

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
