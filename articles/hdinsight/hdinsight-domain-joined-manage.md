---
title: "Administración de clústeres de HDInsight unidos a dominio - Azure | Microsoft Docs"
description: "Aprenda a administrar clústeres de HDInsight unidos a dominio"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: 6ebc4d2f-2f6a-4e1e-ab6d-af4db6b4c87c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 9b56ce6cc5bdd3b8d48d047751e978cad08598e1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="manage-domain-joined-hdinsight-clusters-preview"></a>Administración de clústeres de HDInsight unidos a dominio (versión preliminar)
Conozca los usuarios y los roles de HDInsight unido a un dominio y cómo administrar clústeres de HDInsight unidos a dominio.

## <a name="users-of-domain-joined-hdinsight-clusters"></a>Usuarios de clústeres de HDInsight unidos a dominio
Un clúster de HDInsight que no está unido a un dominio tiene dos cuentas de usuario que se crean durante la creación del clúster:

* **Administrador de Ambari**: esta cuenta es también conocida como *usuario de Hadoop* o *usuario de HTTP*. Esta cuenta puede usarse para iniciar sesión en Ambari en https://&lt;nombreDeClúster >. azurehdinsight.net. También puede usarse para ejecutar consultas en vistas de Ambari, ejecutar trabajos mediante herramientas externas (es decir, PowerShell, Templeton, Visual Studio) y autenticarse con el controlador ODBC de Hive y herramientas de BI (es decir, Excel, PowerBI o Tableau).
* **Usuario de SSH**: esta cuenta se puede usar con SSH y permite ejecutar comandos de sudo. Tiene privilegios raíz a las máquinas virtuales Linux.

Un clúster de HDInsight unido a dominio tiene tres nuevos usuarios además de administrador de Ambari y usuario de SSH.

* **Administrador de Ranger**: esta cuenta es la cuenta de administrador local de Apache Ranger. No es un usuario de dominio de Active Directory. Esta cuenta se puede usar para configurar directivas y crear otros administradores de usuarios o administradores delegados (para que esos usuarios puedan administrar directivas). De forma predeterminada, el nombre de usuario es *admin* y la contraseña es la misma que la contraseña de administrador de Ambari. En la página de configuración de Ranger se puede actualizar la contraseña.
* **Usuario de dominio administrador de clúster**: esta cuenta es un usuario de dominio de Active Directory designado como administrador de clúster de Hadoop, por ejemplo Ambari y Ranger. Se deben proporcionar las credenciales del usuario durante la creación del clúster. Este usuario tiene los privilegios siguientes:

  * Unir máquinas al dominio y colocarlas en la unidad organizativa que especifique durante la creación del clúster.
  * Crear entidades de servicio dentro de la unidad organizativa que especifique durante la creación del clúster.
  * Crear entradas de DNS inversas.

    Tenga en cuenta que los demás usuarios de AD también tienen estos privilegios.

    Hay algunos puntos de conexión en el clúster (por ejemplo, Templeton) que no están administrados por Ranger, de ahí que no sean seguros. Estos puntos de conexión están bloqueados para todos los usuarios excepto para el usuario de dominio administrador del clúster.
* **Normal**: durante la creación del clúster, puede proporcionar varios grupos de Active Directory. Los usuarios de estos grupos se sincronizarán con Ranger y Ambari. Estos usuarios son usuarios de dominio y solo tendrán acceso a los puntos de conexión administrados por Ranger (por ejemplo, Hiveserver2). Todas las directivas y la auditoría de RBAC serán aplicable a estos usuarios.

## <a name="roles-of-domain-joined-hdinsight-clusters"></a>Roles de clústeres de HDInsight unidos a dominio
Los clústeres de HDInsight unidos a un dominio tienen los siguientes roles:

* Administrador de clústeres
* Operador de clústeres
* Administrador de servicios
* Operador de servicio
* Usuario del clúster

**Para ver los permisos de estos roles:**

1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Roles**.
3. Haga clic en el signo de interrogación azul para ver los permisos:

    ![Permisos de roles de HDInsight unidos a dominio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abrir la interfaz de usuario de administración de Ambari
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Abra el clúster de HDInsight en una hoja. Consulte [Enumeración y visualización de clústeres](hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Haga clic en **Panel** en el menú superior para abrir Ambari.
4. Inicie sesión en Ambari con el nombre de usuario y la contraseña de dominio de administrador del clúster:
5. Haga clic en el menú desplegable **Administrador** de la esquina superior derecha y luego haga clic en **Administrar Ambari**.

    ![Administración de Ambari para HDInsight unido a un dominio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    La interfaz de usuario tiene el siguiente aspecto:

    ![IU de administración de Ambari para HDInsight unido a dominio](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Enumeración de los usuarios de dominio sincronizados desde Active Directory
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Usuarios**. Verá todos los usuarios sincronizados desde Active Directory con el clúster de HDInsight.

    ![IU de administración de Ambari para HDInsight unido a dominio: enumeración de usuarios](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Enumeración de los grupos de dominios sincronizados desde Active Directory
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Grupos**. Verá todos los grupos sincronizados desde Active Directory con el clúster de HDInsight.

    ![IU de administración de Ambari para HDInsight unido a dominio: enumeración de grupos](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configuración de permisos de vistas de Hive
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Vistas**.
3. Haga clic en **HIVE** para mostrar los detalles.

    ![IU de administración de Ambari para HDInsight unido a dominio: vistas de Hive](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Haga clic en el vínculo **Vista de Hive** para configurar vistas de Hive.
5. Desplácese hacia abajo hasta la sección **Permisos**.

    ![IU de administración de Ambari para HDInsight unido a dominio: vistas de Hive, configuración de permisos](./media/hdinsight-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Haga clic en **Agregar usuario** o **Agregar grupo**, y especifique los usuarios o grupos que pueden usar vistas de Hive.

## <a name="configure-users-for-the-roles"></a>Configuración de usuarios para los roles
 Para ver una lista de roles y sus permisos, consulte [Roles de clústeres de HDInsight unidos a dominio](#roles-of-domain---joined-hdinsight-clusters).

1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Roles**.
3. Haga clic en **Agregar usuario** o **Agregar grupo** para asignar usuarios y grupos a roles diferentes.

## <a name="next-steps"></a>Pasos siguientes
* Para configurar un clúster de HDInsight unido a dominio, consulte [Configure Domain-joined HDInsight clusters](hdinsight-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a un dominio).
* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](hdinsight-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
* Para ejecutar consultas de Hive mediante SSH en clústeres de HDInsight unidos a un dominio, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
