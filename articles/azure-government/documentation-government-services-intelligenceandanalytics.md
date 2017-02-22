---
title: "Inteligencia y análisis de Azure Government | Microsoft Docs"
description: "Esto proporciona una comparación de funciones e instrucciones sobre cómo desarrollar aplicaciones para la administración de Azure"
services: Azure-Government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
editor: 
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: 0302a44bb42200c07a313bf2862479c1722af8c4
ms.openlocfilehash: 2a8769f7a611fb22e39db166d3416728f57cbbf4


---
# <a name="azure-government-intelligence--analytics"></a>Inteligencia y análisis de Azure Government
En este artículo se describen los servicios, las variaciones y las consideraciones de análisis e inteligencia para el entorno de Azure Government.

## <a name="azure-hdinsight"></a>HDInsight de Azure
HDInsight está disponible en Azure Government con carácter general.

### <a name="variations"></a>Variaciones
Las siguientes características de HDInsight no están actualmente disponibles en Azure Government.

* HDInsight no está disponible en Windows.
* Azure Data Lake Store no está actualmente disponible en Azure Government. Azure Blob Storage es la única opción de almacenamiento disponible en este momento.
* La integración de Azure Active Directory Domain Services no está aún está disponible. Para crear clústeres de Hadoop seguros sin Active Directory Domain Services, seleccione uno de los siguientes escenarios de instalación:

1. HDINSIGHT INTEGRADO CON ACTIVE DIRECTORY EJECUTÁNDOSE EN AZURE IAAS

  Esta es con diferencia la arquitectura más sencilla para la integración de HDInsight con Active Directory. A continuación se proporciona el diagrama de arquitectura. En esta arquitectura, tendrá el controlador de dominio de Active Directory ejecutándose en una (o varias) máquinas virtuales en Azure. Normalmente, estas máquinas virtuales estarán dentro de una red virtual. Puede configurar una nueva red virtual dentro de la que puede colocar su clúster de HDInsight. Para que HDInsight tenga una línea de visión a Active Directory, tendrá que emparejar estas redes virtuales mediante [Emparejamiento de red virtual mediante Azure Portal] (https://docs.microsoft.com/es-es/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). 
  
  Requisitos previos que deben estar configurados en Active Directory
     * Debe crearse una unidad organizativa, en la que colocará las máquinas virtuales del clúster de HDInsight y las entidades de servicio utilizadas por el clúster. 
     * Debe configurarse LDAPS para facilitar la comunicación con Active Directory. El certificado usado para instalar LDAPS debe ser un certificado real (no un certificado autofirmado).
     * Deben crearse zonas DNS inversas en el dominio para el intervalo de direcciones IP de la subred HDI.
     * Se necesita una cuenta de servicio, o una cuenta de usuario, que se usarán para crear el clúster de HDInsight. Esta cuenta debe tener los siguientes permisos:
        * Permisos para crear objetos de entidad de servicio y objetos de equipo dentro de la unidad organizativa.
        * Permisos para crear reglas de proxy DNS inversas.
        * Permisos para unir máquinas al dominio de Active Directory.
        
2. HDINSIGHT INTEGRADO CON UNA INSTANCIA DE ACTIVE DIRECTORY LOCAL A TRAVÉS DE LA CONFIGURACIÓN DE VPN
  
  Esta arquitectura es como la arquitectura núm. 1. La única diferencia es que, en este caso, su instancia de Active Directory es local y la línea de visión de HDInsight a Active Directory es a través de una [conexión VPN de Azure a la red local] (https://docs.microsoft.com/es-es/azure/expressroute/expressroute-introduction). A continuación se muestra el diagrama de arquitectura para esta instalación. 
  
  Requisitos previos que deben estar configurados en Active Directory local
     * Debe crearse una unidad organizativa, en la que colocará las máquinas virtuales del clúster de HDInsight y las entidades de servicio utilizadas por el clúster.
     * Debe configurarse LDAPS para facilitar la comunicación con Active Directory. El certificado usado para instalar LDAPS debe ser un certificado real (no un certificado autofirmado).
     * Deben crearse zonas DNS inversas en el dominio para el intervalo de direcciones IP de la subred HDI.
     * Se necesita una cuenta de servicio, o una cuenta de usuario, que se usarán para crear el clúster de HDInsight. Esta cuenta debe tener los siguientes permisos:
        * Permisos para crear objetos de entidad de servicio y objetos de equipo dentro de la unidad organizativa.
        * Permisos para crear reglas de proxy DNS inversas.
        * Permisos para unir máquinas al dominio de Active Directory.


Las direcciones URL para Log Analytics son diferentes en Azure Government:

| Tipo de servicio | Azure Public | Azure Government |
| --- | --- | --- |
| Clúster de HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Para obtener más información, consulte [Azure HDInsight public documentation](../hdinsight/hdinsight-hadoop-introduction.md) (Documentación pública de Azure HDInsight).


## <a name="next-steps"></a>Pasos siguientes
Para obtener información complementaria y actualizaciones, suscríbase al <a href="https://blogs.msdn.microsoft.com/azuregov/">blog de Microsoft Azure Government</a>.



<!--HONumber=Jan17_HO2-->


