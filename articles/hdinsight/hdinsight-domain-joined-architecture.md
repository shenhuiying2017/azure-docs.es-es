---
title: Arquitectura Azure HDInsight unida a un dominio | Microsoft Docs
description: "Aprenda a planear clústeres de HDInsight unidos a un dominio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 7e34f47f09466a40993b4cc797ff1cad2bdaeafe
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017

---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planeamiento de clústeres de Hadoop unidos a un dominio de Azure en HDInsight

Tradicionalmente, Hadoop es un clúster de un solo usuario. Es apropiado para la mayor parte de las empresas que tienen equipos de aplicaciones pequeños que generan grandes cargas de trabajo de datos. A medida que aumenta la popularidad de Hadoop, muchas empresas empiezan a usar un modelo en el que los equipos de TI administran clústeres y varios equipos de aplicaciones comparten los clústeres. Por consiguiente, las funcionalidades que implican a clústeres de varios usuarios se encuentran entre las más solicitadas en HDInsight de Azure.

En lugar de generar su propia autenticación y autorización multiusuario, HDInsight usa el proveedor de identidades más popular: Active Directory (AD). La eficaz funcionalidad de seguridad de AD se puede usar para administrar la autorización multiusuario en HDInsight. Mediante la integración de HDInsight con AD, se puede comunicar con los clústeres mediante sus credenciales de AD. HDInsight asigna un usuario de AD a un usuario de Hadoop local, de modo que todos los servicios que se ejecutan en HDInsight (Ambari, servidor de Hive, Ranger, servidor Thrift de Spark, etc.) funcionan perfectamente para el usuario autenticado.

## <a name="integrate-hdinsight-with-ad-and-ad-on-iaas-vm"></a>Integración de HDInsight con AD y AD en la máquina virtual de IaaS

Al integrar HDInsight con Azure AD o AD, los nodos del clúster de HDInsight son unidos a un dominio para un dominio. HDInsight crea entidades de servicio para los servicios de Hadoop que se ejecutan en el clúster y las coloca en una unidad organizativa (OU) especificada de Azure AD o AD en una máquina virtual de IaaS. HDInsight también crea asignaciones de DNS inverso en el dominio para las direcciones IP de los nodos que se unen al dominio.

Esta configuración se puede lograr mediante el uso de varias arquitecturas. Puede elegir entre las siguientes opciones.

**HDInsight integrado con AD que se ejecuta en Azure IaaS**

Esta es la arquitectura más sencilla para la integración de HDInsight con Active Directory. El controlador de dominio de AD se ejecuta en una máquina virtual, o en varias, en Azure. Normalmente, estas máquinas virtuales se encuentran en una red virtual. Configure otra red virtual para el clúster de HDInsight. Para que HDInsight tenga una línea de visión a Active Directory, es preciso emparejar estas redes virtuales mediante el uso del [emparejamiento de VNet a VNet](../virtual-network/virtual-network-create-peering.md). Si crea el Active Directory en ARM, a continuación, puede crear el Active Directory y HDInsight en la misma red virtual y no tiene que hacer el emparejamiento. 

![Topología de clúster de HDInsight de unión a dominio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> En esta arquitectura, Azure Data Lake Store no se puede usar con el clúster de HDInsight.


Requisitos previos de Active Directory:

* Se debe crear una [unidad organizativa](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), en la que se colocan las máquinas virtuales del clúster de HDInsight y las entidades de servicio que usa el clúster.
* [Protocolo ligero de acceso a directorios](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAP) debe configurarse para comunicarse con AD. El certificado que se usa para instalar LDAPS debe ser un certificado real (no uno autofirmado).
* Deben crearse zonas de DNS inverso en el dominio para el intervalo de direcciones IP de la subred de HDInsight (por ejemplo, 10.2.0.0/24 en la imagen anterior).
* Se necesita una cuenta de servicio o una cuenta de usuario. Utilice esta cuenta para crear el clúster de HDInsight. Esta cuenta debe tener los siguientes permisos:

    - Permisos para crear objetos de entidad de servicio y objetos de equipo en la unidad organizativa
    - Permisos para crear reglas de proxy de DNS inverso
    - Permisos para unir equipos al dominio de Active Directory

**HDInsight integrado con una instancia de Azure AD solo en la nube**

Para una instancia de Azure AD solo en la nube, configure un controlador de dominio para que HDInsight se pueda integrar con Azure AD. Esto se logra mediante el uso de [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md) (Azure AD DS). Azure AD DS crea equipos del controlador de dominio en la nube y les proporciona direcciones IP. Crea dos controladores de dominio para lograr alta disponibilidad.

Actualmente, Azure AD DS solo existe en redes virtuales clásicas. Solo se puede acceder a él mediante el Portal de Azure clásico. La red virtual de HDInsight existe en Azure Portal, que es preciso emparejar con la red virtual clásica mediante el emparejamiento entre redes virtuales.

> [!NOTE]
> El emparejamiento entre una red virtual clásica y una red virtual de Azure Resource Manager requiere que las dos redes virtuales estén en la misma región y en la misma suscripción de Azure.

![Topología de clúster de HDInsight de unión a dominio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Requisitos previos de Azure AD:

* Se debe crear una [unidad organizativa](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), en la que se colocan las máquinas virtuales del clúster de HDInsight y las entidades de servicio que usa el clúster.
* Al configurar Azure AD DS, se debe configurar [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). El certificado que se usa para instalar LDAPS debe ser un certificado real (no uno autofirmado).
* Deben crearse zonas de DNS inverso en el dominio para el intervalo de direcciones IP de la subred de HDInsight (por ejemplo, 10.2.0.0/24 en la imagen anterior).
* Deben sincronizarse [hash de contraseña](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) de Azure AD a Azure AD DS.
* Se necesita una cuenta de servicio o una cuenta de usuario. Utilice esta cuenta para crear el clúster de HDInsight. Esta cuenta debe tener los siguientes permisos:

    - Permisos para crear objetos de entidad de servicio y objetos de equipo en la unidad organizativa
    - Permisos para crear reglas de proxy de DNS inverso
    - Permisos para unir equipos al dominio de Azure AD

## <a name="next-steps"></a>Pasos siguientes
* Para configurar un clúster de HDInsight unido a un dominio, consulte [Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar)](hdinsight-domain-joined-configure.md).
* Para administrar clústeres de HDInsight unidos a un dominio, consulte [Administración de clústeres de HDInsight unidos a dominio (versión preliminar)](hdinsight-domain-joined-manage.md).
* Para configurar directivas de Hive y ejecutar consultas de Hive, consulte [Administración de clústeres de HDInsight unidos a dominio (versión preliminar)](hdinsight-domain-joined-run-hive.md).
* Para ejecutar consultas de Hive mediante SSH en clústeres de HDInsight unidos a un dominio, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

