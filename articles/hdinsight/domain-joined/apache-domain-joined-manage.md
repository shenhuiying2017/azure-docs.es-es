---
title: "Administración de clústeres de HDInsight unidos a dominio - Azure | Microsoft Docs"
description: "Aprenda a administrar clústeres de HDInsight unidos a dominio"
services: hdinsight
documentationcenter: 
author: bprakash
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
ms.date: 01/11/2018
ms.author: bhanupr
ms.openlocfilehash: 68166be98acc64326a4053b45f0039ae54d930e4
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="manage-domain-joined-hdinsight-clusters"></a>Administración de clústeres de HDInsight unidos a dominio
Conozca los usuarios y los roles de HDInsight unido a un dominio y cómo administrar clústeres de HDInsight unidos a dominio.

## <a name="access-the-clusters-with-enterprise-security-package"></a>Acceda a clústeres con Enterprise Security Package.

Enterprise Security Package (conocido anteriormente como HDInsight Premium) proporciona acceso multiusuario al clúster, donde Active Directory realiza la autenticación con Apache Ranger y ACL de Storage (ADLS ACL). La autorización proporciona límites de seguridad entre varios usuarios y permite que solo los usuarios con privilegios tengan acceso a los datos en función de las directivas de autorización.

El aislamiento de usuarios y la seguridad son importantes para un clúster de HDInsight con Enterprise Security Package. Para cumplir estos requisitos, se bloquea el acceso de SSH al clúster con Enterprise Security Package. En la tabla siguiente se muestran los métodos de acceso recomendados para cada tipo de clúster:

|Carga de trabajo|Escenario|Modo de acceso|
|--------|--------|-------------|
|Hadoop|Hive: consultas/trabajos interactivos |<ul><li>[Beeline](#beeline)</li><li>[Vista de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC: Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Consultas/trabajos interactivos, PySpark interactivo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin con Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Vista de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC: Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark|Escenarios de Batch: envío de Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interactive|<ul><li>[Beeline](#beeline)</li><li>[Vista de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC: Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Cualquiera|Instalar aplicaciones personalizadas|<ul><li>[Acciones de script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|


El uso de API estándar ayuda desde el punto de vista de la seguridad. Además, obtendrá las siguientes ventajas:

1.  **Administración**: puede administrar el código y automatizar trabajos con las API estándar (Livio, HS2, etc.)
2.  **Auditoría**: con SSH, es imposible auditar qué usuarios accedieron a través de dicho protocolo al clúster. Este no será el caso si los trabajos se construyen a través de puntos de conexión estándar, ya que se ejecutan en el contexto del usuario. 



### <a name="beeline"></a>Uso de BeeLine 
Instale Beeline en la máquina, conéctese a través de la red pública de Internet y use los siguientes parámetros: 

```
- Connection string: -u 'jdbc:hive2://&lt;clustername&gt;.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Si tiene Beeline instalado localmente y se conecta a través de una red virtual de Azure, use los siguientes parámetros: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para buscar el nombre de dominio completo de un nodo principal, use la información del documento Administración de clústeres de HDInsight con la API de REST de Ambari.














## <a name="users-of-domain-joined-hdinsight-clusters"></a>Usuarios de clústeres de HDInsight unidos a dominio
Un clúster de HDInsight que no está unido a un dominio tiene dos cuentas de usuario que se crean durante la creación del clúster:

* **Administrador de Ambari**: esta cuenta es también conocida como *usuario de Hadoop* o *usuario de HTTP*. Esta cuenta puede usarse para iniciar sesión en Ambari en https://&lt;nombreDeClúster >. azurehdinsight.net. También puede usarse para ejecutar consultas en vistas de Ambari, ejecutar trabajos mediante herramientas externas (por ejemplo, PowerShell, Templeton o Visual Studio) y autenticarse con el controlador ODBC de Hive y herramientas de BI (por ejemplo, Excel, PowerBI o Tableau).

Un clúster de HDInsight unido a un dominio tiene tres nuevos usuarios además del administrador de Ambari.

* **Administrador de Ranger**: esta cuenta es la cuenta de administrador local de Apache Ranger. No es un usuario de dominio de Active Directory. Esta cuenta se puede usar para configurar directivas y crear otros administradores de usuarios o administradores delegados (para que esos usuarios puedan administrar directivas). De forma predeterminada, el nombre de usuario es *admin* y la contraseña es la misma que la contraseña de administrador de Ambari. En la página de configuración de Ranger se puede actualizar la contraseña.
* **Usuario de dominio administrador de clúster**: esta cuenta es un usuario de dominio de Active Directory designado como administrador de clúster de Hadoop, por ejemplo Ambari y Ranger. Se deben proporcionar las credenciales del usuario durante la creación del clúster. Este usuario tiene los privilegios siguientes:

  * Unir máquinas al dominio y colocarlas en la unidad organizativa que especifique durante la creación del clúster.
  * Crear entidades de servicio dentro de la unidad organizativa que especifique durante la creación del clúster.
  * Crear entradas de DNS inversas.

    Tenga en cuenta que los demás usuarios de AD también tienen estos privilegios.

    Hay algunos puntos de conexión en el clúster (por ejemplo, Templeton) que no están administrados por Ranger, de ahí que no sean seguros. Estos puntos de conexión están bloqueados para todos los usuarios excepto para el usuario de dominio administrador del clúster.
* **Normal**: durante la creación del clúster, puede proporcionar varios grupos de Active Directory. Los usuarios de estos grupos se sincronizan con Ranger y Ambari. Estos usuarios son usuarios de dominio y solo tienen acceso a los puntos de conexión administrados por Ranger (por ejemplo, Hiveserver2). Todas las directivas y la auditoría de RBAC serán aplicable a estos usuarios.

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

    ![Permisos de roles de HDInsight unidos a dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abrir la interfaz de usuario de administración de Ambari

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Abra el clúster de HDInsight. Consulte [Enumeración y visualización de clústeres](../hdinsight-administer-use-management-portal.md#list-and-show-clusters).
3. Haga clic en **Panel** en el menú superior para abrir Ambari.
4. Inicie sesión en Ambari con el nombre de usuario y la contraseña de dominio de administrador del clúster:
5. Haga clic en el menú desplegable **Administrador** de la esquina superior derecha y luego haga clic en **Administrar Ambari**.

    ![Administración de Ambari para HDInsight unido a un dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    La interfaz de usuario tiene el siguiente aspecto:

    ![IU de administración de Ambari para HDInsight unido a dominio](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Enumeración de los usuarios de dominio sincronizados desde Active Directory
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Usuarios**. Verá todos los usuarios sincronizados desde Active Directory con el clúster de HDInsight.

    ![IU de administración de Ambari para HDInsight unido a dominio: enumeración de usuarios](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Enumeración de los grupos de dominios sincronizados desde Active Directory
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Grupos**. Verá todos los grupos sincronizados desde Active Directory con el clúster de HDInsight.

    ![IU de administración de Ambari para HDInsight unido a dominio: enumeración de grupos](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configuración de permisos de vistas de Hive
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Vistas**.
3. Haga clic en **HIVE** para mostrar los detalles.

    ![IU de administración de Ambari para HDInsight unido a dominio: vistas de Hive](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Haga clic en el vínculo **Vista de Hive** para configurar vistas de Hive.
5. Desplácese hacia abajo hasta la sección **Permisos**.

    ![IU de administración de Ambari para HDInsight unido a dominio: vistas de Hive, configuración de permisos](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Haga clic en **Agregar usuario** o **Agregar grupo**, y especifique los usuarios o grupos que pueden usar vistas de Hive.

## <a name="configure-users-for-the-roles"></a>Configuración de usuarios para los roles
 Para ver una lista de roles y sus permisos, consulte [Roles de clústeres de HDInsight unidos a dominio](#roles-of-domain---joined-hdinsight-clusters).

1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Roles**.
3. Haga clic en **Agregar usuario** o **Agregar grupo** para asignar usuarios y grupos a roles diferentes.

## <a name="next-steps"></a>pasos siguientes
* Para configurar un clúster de HDInsight unido a dominio, consulte [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a un dominio).
* Para configurar directivas de Hive y ejecución de consultas de Hive, consulte [Configure Hive policies for Domain-joined HDInsight clusters](apache-domain-joined-run-hive.md) (Configuración de directivas de los clústeres de HDInsight unidos a dominio).
