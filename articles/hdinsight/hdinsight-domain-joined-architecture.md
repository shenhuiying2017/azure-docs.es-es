---
title: Arquitectura Azure HDInsight unida a un dominio| Microsoft Docs
description: "Aprenda a planear clústeres de HDInsight unidos a un dominio."
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4ba44128ec19d3937643ac934ca3e787cb9819a3
ms.openlocfilehash: 690ba97d2b0634548a0ec424d441ad34d70667b9
ms.lasthandoff: 02/27/2017


---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planeamiento de clústeres de Hadoop unidos a un dominio de Azure en HDInsight

Tradicionalmente, Hadoop es un clúster de un solo usuario. Es apropiado para la mayor parte de las empresas que tienen equipos de aplicaciones pequeños que generan grandes cargas de trabajo de datos. A medida que aumenta la popularidad de Hadoop, muchas empresas empiezan a usar un modelo en el que los equipos de TI administran clústeres y varios equipos de aplicaciones comparten los clústeres. Por consiguiente, las funcionalidades que implican a clústeres de varios usuarios se encuentran entre las más solicitadas en HDInsight de Azure.

En lugar de generar su propia autenticación y autorización multiusuario, HDInsight usa el proveedor de identidades más popular, Azure Active Directory (Azure AD). La eficaz funcionalidad de seguridad de Azure AD se puede usar para administrar la autorización multiusuario en HDInsight. Mediante la integración de HDInsight con Azure AD, se puede comunicar con los clústeres mediante sus credenciales de Azure AD. HDInsight asigna un usuario de Azure AD a un usuario de Hadoop local, de modo que todos los servicios que se ejecutan en HDInsight (Ambari, servidor de Hive, Ranger, servidor Thrift de Spark, etc.) funcionan perfectamente para el usuario autenticado.

## <a name="integrate-hdinsight-with-azure-ad"></a>Integración de HDInsight con Azure Apps

Mediante la integración de HDInsight con Azure AD, los nodos del clúster de HDInsight se unen al dominio de Azure AD. HDInsight crea entidades de servicio para los servicios de Hadoop que se ejecutan en el clúster y las coloca en una unidad organizativa (OU) especificada de Azure AD. HDInsight también crea asignaciones de DNS inverso en el dominio de Azure AD para las direcciones IP de los nodos que se unen al dominio.

Esta configuración se puede lograr mediante el uso de varias arquitecturas. Puede elegir entre las siguientes opciones.

**HDInsight integrado con Azure AD que se ejecuta en Azure IaaS**

Esta es la arquitectura más sencilla para integrar HDInsight con Azure AD. El controlador de dominio de Azure AD se ejecuta en una máquina virtual, o en varias, en Azure. Normalmente, estas máquinas virtuales se encuentran en una red virtual. Configure otra red virtual para el clúster de HDInsight. Para que HDInsight tenga una línea de visión a Azure AD, es preciso emparejar estas redes virtuales mediante el uso del [emparejamiento de VNet a VNet](../virtual-network/virtual-networks-create-vnetpeering-arm-portal.md).

![Topología de clúster de HDInsight de unión a dominio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_1.png)

> [!NOTE]
> En esta arquitectura, Azure Data Lake Store no se puede usar con el clúster de HDInsight.


Requisitos previos de Azure AD:

* Se debe crear una [unidad organizativa](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), en la que se colocan las máquinas virtuales del clúster de HDInsight y las entidades de servicio que usa el clúster.
* [Protocolos ligeros de acceso a directorios](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) (LDAP) deben configurarse para comunicarse con Azure AD. El certificado que se usa para instalar LDAPS debe ser un certificado real (no uno autofirmado).
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

**HDInsight integrado con una instancia de Active Directory local a través de VPN**

Esta arquitectura es similar a HDInsight integrado con Azure AD que se ejecuta en Azure IaaS. La única diferencia es que la instancia de Azure AD es local y la línea de visión de HDInsight a Azure AD es a través de una [conexión VPN desde Azure a una red local](../expressroute/expressroute-introduction.md).

![Topología de clúster de HDInsight de unión a dominio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_3.png)

> [!NOTE]
> En esta arquitectura, Azure Data Lake Store no se puede usar con el clúster de HDInsight.

Requisitos previos de Azure AD:

* Se debe crear una [unidad organizativa](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), en la que se colocan las máquinas virtuales del clúster de HDInsight y las entidades de servicio que usa el clúster.
* [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md) debe estar configurado para comunicarse con Azure AD. El certificado que se usa para instalar LDAPS debe ser un certificado real (no uno autofirmado).
* Deben crearse zonas de DNS inverso en el dominio para el intervalo de direcciones IP de la subred de HDInsight (por ejemplo, 10.2.0.0/24 en la imagen anterior).
* Se necesita una cuenta de servicio o una cuenta de usuario. Utilice esta cuenta para crear el clúster de HDInsight. Esta cuenta debe tener los siguientes permisos:

    - Permisos para crear objetos de entidad de servicio y objetos de equipo en la unidad organizativa
    - Permisos para crear reglas de proxy de DNS inverso
    - Permisos para unir equipos al dominio de Azure AD

**HDInsight integrado con una instancia de Active Directory local sincronizada con Azure AD**

Esta arquitectura es similar a HDInsight integrado con una instancia de Azure AD solo en la nube. La única diferencia es que la instancia de Active Directory local se sincroniza con Azure AD. Configure un controlador de dominio en la nube para que HDInsight se pueda integrar con Azure AD. Esto se logra mediante el uso de [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md). Azure AD DS crea equipos del controlador de dominio en la nube y les proporciona direcciones IP. Crea dos controladores de dominio para lograr alta disponibilidad.

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
    - Permisos para unir equipos al dominio de Active Directory

**HDInsight integrado con un valor no predeterminado de Azure AD (se recomienda solo para desarrollo y pruebas)**

Esta arquitectura es similar a HDInsight integrado con una instancia de Azure AD solo en la nube. En la mayoría de las empresas, el acceso de administrador a Azure AD está restringido a determinados usuarios. Por consiguiente, cuando se desea realizar una prueba de concepto o probar a crear un clúster unido a un dominio, es posible que sea conveniente crear una instancia de Azure AD en la suscripción, en lugar de esperar a que un administrador configure los requisitos previos en Azure AD. Dado que ha creado esta instancia de Azure AD, tiene permisos totales en Azure AD para configurar Azure AD DS.

Azure AD DS crea equipos del controlador de dominio en la nube y les proporciona direcciones IP. Crea dos controladores de dominio para lograr alta disponibilidad.

Azure AD DS solo existe en las redes virtuales clásicas, por lo que necesita tener acceso al Portal de Azure clásico y debe crear una red virtual clásica para configurar Azure AD DS. La red virtual de HDInsight existe en Azure Portal, que es preciso emparejar con la red virtual clásica mediante el emparejamiento entre redes virtuales.

> [!NOTE]
> El emparejamiento entre las redes virtuales clásica y de Azure Resource Manager requiere que las dos redes virtuales estén en la misma región y en la misma suscripción de Azure.

![Topología de clúster de HDInsight de unión a dominio](./media/hdinsight-domain-joined-architecture/hdinsight-domain-joined-architecture_2.png)

Requisitos previos de Azure AD:

* Se debe crear una [unidad organizativa](../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md), en la que se colocan las máquinas virtuales del clúster de HDInsight y las entidades de servicio que usa el clúster.
* Al configurar Azure AD DS, se debe configurar [LDAPS](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). Para configurar LDAPS puede crear un [certificado autofirmado](../active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap.md). Sin embargo, para usar un certificado autofirmado, debe solicitar una excepción de <a href="mailto:hdipreview@microsoft.com">hdipreview@microsoft.com</a>.
* Deben crearse zonas de DNS inverso en el dominio para el intervalo de direcciones IP de la subred de HDInsight (por ejemplo, 10.2.0.0/24 en la imagen anterior).
* Deben sincronizarse [hash de contraseña](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) de Azure AD a Azure AD DS.
* Se necesita una cuenta de servicio o una cuenta de usuario. Utilice esta cuenta para crear el clúster de HDInsight. Esta cuenta debe tener los siguientes permisos:

    - Permisos para crear objetos de entidad de servicio y objetos de equipo en la unidad organizativa
    - Permisos para crear reglas de proxy de DNS inverso
    - Permisos para unir equipos al dominio de Azure Active Directory

## <a name="next-steps"></a>Pasos siguientes
* Para configurar un clúster de HDInsight unido a un dominio, consulte [Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar)](hdinsight-domain-joined-configure.md).
* Para administrar clústeres de HDInsight unidos a un dominio, consulte [Administración de clústeres de HDInsight unidos a dominio (versión preliminar)](hdinsight-domain-joined-manage.md).
* Para configurar directivas de Hive y ejecutar consultas de Hive, consulte [Administración de clústeres de HDInsight unidos a dominio (versión preliminar)](hdinsight-domain-joined-run-hive.md).
* Para ejecutar consultas de Hive mediante SSH en clústeres de HDInsight unidos a un dominio, consulte [Uso de SSH con HDInsight (Hadoop) desde Windows, Linux, Unix u OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

