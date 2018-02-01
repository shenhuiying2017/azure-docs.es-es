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
ms.date: 12/14/2017
ms.author: saurinsh
ms.openlocfilehash: c5e6381e7abb18c01031e4168c64cfe9aafec7ae
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planeamiento de clústeres de Hadoop unidos a un dominio de Azure en HDInsight

El clúster de HDInsight estándar es de un único usuario. Es apropiado para la mayor parte de las empresas que tienen equipos de aplicaciones pequeños que generan grandes cargas de trabajo de datos. A medida que aumentó la popularidad de Hadoop, muchas empresas empezaron a usar un modelo en el que los equipos de TI administran clústeres y varios equipos de aplicaciones comparten los clústeres. Por consiguiente, las funcionalidades que implican a clústeres de varios usuarios se encuentran entre las más solicitadas en HDInsight de Azure.

En lugar de generar su propia autenticación y autorización multiusuario, HDInsight usa el proveedor de identidades más popular: Active Directory (AD). La eficaz funcionalidad de seguridad de AD se puede usar para administrar la autenticación multiusuario en HDInsight. Mediante la integración de HDInsight con AD, se puede comunicar con los clústeres mediante sus credenciales de AD. Las máquinas virtuales de HDInsight están unidas a un dominio de AD y esta es la forma en que HDInsight asigna un usuario de AD a un usuario de Hadoop local, de modo que todos los servicios que se ejecutan en HDInsight (Ambari, servidor de Hive, Ranger, servidor Thrift de Spark, etc.) funcionan perfectamente para el usuario autenticado. Después, los administradores pueden crear directivas de autorización seguras con Apache Ranger para proporcionar control de acceso basado en rol para los recursos de HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Integración de HDInsight con Active Directory

Al integrar HDInsight con Active Directory, los nodos de clúster de HDInsight se unen a un dominio de AD. Se configura la seguridad de Kerberos para los componentes de Hadoop en el clúster. Para cada uno de los componentes de Hadoop, se crea una entidad de servicio en Active Directory. También se crea una entidad de seguridad de máquina correspondiente para cada máquina unida al dominio. Estas entidades de servicio y entidades de seguridad de máquina pueden provocar un desorden en su instancia de Active Directory. Como resultado, es necesario proporcionar una unidad organizativa (OU) en Active Directory, donde se colocan estas entidades de seguridad. 

En resumen, es necesario configurar un entorno con:

- Un controlador de dominio de Active Directory con LDAPS configurado.
- Conectividad desde la red virtual de HDInsight al controlador de dominio de Active Directory.
- Una unidad organizativa creada en Active Directory.
- Una cuenta de servicio que tenga permisos para:

    - Crear entidades de servicio en la unidad organizativa.
    - Unir máquinas al dominio y crear entidades de seguridad de máquina en la unidad organizativa.

En la siguiente captura de pantalla se muestra una unidad organizativa creada en contoso.com. Algunas de las entidades de servicio y entidades de seguridad de máquina también se muestran en la captura de pantalla.

![Unidad organizativa de clústeres de HDInsight unidos a un dominio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="two-ways-of-bringing-your-own-active-directory-domain-controllers"></a>Dos formas de usar sus propios controladores de dominio de Active Directory

Existen dos formas de usar sus propios controladores de dominio de Active Directory para crear clústeres de HDInsight unidos a un dominio. 

- **Azure Active Directory Domain Services**: este servicio proporciona un dominio de Active Directory administrado que es totalmente compatible con Windows Server Active Directory. Microsoft se encarga de administrar, aplicar revisiones y supervisar el dominio de Active Directory. Puede implementar el clúster sin preocuparse por mantener los controladores de dominio. Los usuarios, grupos y contraseñas se sincronizan desde su instalación de Microsoft Azure Active Directory, lo que permite a los usuarios iniciar sesión en el clúster con sus credenciales corporativas. Para más información, vea [Configurar clústeres de HDInsight unidos a un dominio con Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

- **Active Directory en máquinas virtuales de IaaS de Azure**: en esta opción, implementa y administra su propio dominio de Windows Server Active Directory en máquinas virtuales de IaaS de Azure. Para más información, vea [Configuración de un entorno de espacio aislado unido a un dominio](./apache-domain-joined-configure.md).

## <a name="next-steps"></a>pasos siguientes
* Para configurar un clúster de HDInsight unido a un dominio, consulte [Configuración de clústeres de HDInsight unidos a un dominio (versión preliminar)](apache-domain-joined-configure.md).
* Para administrar clústeres de HDInsight unidos a un dominio, consulte [Administración de clústeres de HDInsight unidos a dominio (versión preliminar)](apache-domain-joined-manage.md).
* Para configurar directivas de Hive y ejecutar consultas de Hive, consulte [Administración de clústeres de HDInsight unidos a dominio (versión preliminar)](apache-domain-joined-run-hive.md).
* Para ejecutar consultas de Hive mediante SSH en clústeres de HDInsight unidos a un dominio, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
