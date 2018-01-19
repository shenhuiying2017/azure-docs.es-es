---
title: "Configuración de directivas de Hive en HDInsight unido a un dominio - Azure | Microsoft Docs"
description: Aprenda a...
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3fade1e5-c2e1-4ad5-b371-f95caea23f6d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/25/2016
ms.author: saurinsh
ms.openlocfilehash: 35a74ffb6a30fe2ae7db686be5b6774800ce37b1
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="configure-hive-policies-in-domain-joined-hdinsight"></a>Configurar directivas de Hive en HDInsight unido a un dominio
Aprenda a configurar las directivas de Apache Ranger para Hive. En este artículo, cree dos directivas Ranger para restringir el acceso a hivesampletable. hivesampletable viene con los clústeres de HDInsight. Una vez configuradas las directivas, utilice Excel y el controlador ODBC para conectarse a las tablas de Hive en HDInsight.

## <a name="prerequisites"></a>requisitos previos
* Un clúster de HDInsight unido a un dominio. Consulte [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a un dominio).
* Un equipo con Office 2016, Office 2013 Professional Plus, Office 2013 Pro Plus, Excel 365 Standalone u Office 2010 Professional Plus.

## <a name="connect-to-apache-ranger-admin-ui"></a>Conexión a la interfaz de usuario administrador de Apache Ranger
**Para conectarse a la interfaz de usuario administrador de Apache Ranger**

1. En un explorador, conéctese a la interfaz de usuario administrador de Ranger. La dirección URL es https://&lt;nombreDeClúster>.azurehdinsight.net/Ranger/.

   > [!NOTE]
   > Ranger usa credenciales diferentes de clúster de Hadoop. Para evitar que los exploradores usen credenciales almacenadas en caché de Hadoop, use nueva ventana del explorador InPrivate para conectarse a la interfaz de usuario administrador de Ranger.
   >
   >
2. Inicie sesión con el nombre de usuario y la contraseña del dominio del administrador de clúster:

    ![Página principal de Ranger unido a un dominio de HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    Actualmente, Ranger solo funciona con Yarn y Hive.

## <a name="create-domain-users"></a>Creación de usuarios del dominio
En [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md#optional-create-ad-users-and-groups) (Configuración de clústeres de HDInsight unidos a un dominio), ha creado hiveruser1 y hiveuser2. Para este tutorial usará las dos cuentas de usuario.

## <a name="create-ranger-policies"></a>Creación de directivas de Ranger
En esta sección, creará dos directivas Ranger para acceder a hivesampletable. Se concede el permiso select en un conjunto diferente de columnas. Ambos usuarios se crearon en [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md#optional-create-ad-users-and-groups) (Configuración de clústeres de HDInsight unidos a un dominio).  En la siguiente sección probará las dos directivas en Excel.

**Para crear directivas de Ranger**

1. Abra la interfaz de usuario administrador de Ranger. Consulte el artículo sobre la [conexión a la interfaz de usuario administrador de Apache Ranger](#connect-to-apache-ranager-admin-ui).
2. Haga clic en **&lt;nombreDeClúster>_hive**, en **Hive**. Deben aparecer dos directivas preconfiguradas.
3. Haga clic en **Agregar nueva directiva**, y escriba los siguientes valores:

   * Nombre de directiva: read-hivesampletable-all
   * Base de datos de Hive: default
   * tabla: hivesampletable
   * Columna de Hive: *
   * Seleccionar usuario: hiveuser1
   * Permisos: select

     ![Configuración de políticas de Hive para Ranger unido a un dominio de HDInsight](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png).

     > [!NOTE]
     > Si un usuario de dominio no se rellena en Seleccionar usuario, espere unos instantes para que Ranger se sincronice con AAD.
     >
     >
4. Haga clic en **Agregar** para guardar la directiva.
5. Repita los dos últimos pasos para crear otra directiva con las siguientes propiedades:

   * Nombre de directiva: read-hivesampletable-devicemake
   * Base de datos de Hive: default
   * tabla: hivesampletable
   * Columna de Hive: clientid, devicemake
   * Seleccionar usuario: hiveuser2
   * Permisos: select

## <a name="create-hive-odbc-data-source"></a>Creación de un origen de datos de Hive ODBC
Las instrucciones se pueden encontrar en el artículo sobre la [creación de origen de datos ODBC en Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).  

 | Propiedad  |DESCRIPCIÓN |
 | --- | --- |
 | Data Source Name | Asigne un nombre al origen de datos |
 | Host | Escriba &lt;NombreClústerHDInsight>.azurehdinsight.net. Por ejemplo, myHDICluster.azurehdinsight.net |
 | Port | Use **443**. (Este puerto se ha cambiado de 563 a 443). |
 | Base de datos | Use el **valor predeterminado** |
 | Hive Server Type | Seleccione **Hive Server 2** |
 | Mechanism | Seleccione **Azure HDInsight Service** |
 | HTTP Path | Deje este parámetro en blanco. |
 | User Name | Escriba hiveuser1@contoso158.onmicrosoft.com. Actualice el nombre de dominio si es diferente. |
 | Password | Escriba la contraseña de hiveuser1. |

Haga clic **Prueba** antes de guardar el origen de datos.

## <a name="import-data-into-excel-from-hdinsight"></a>Importación de datos en Excel desde HDInsight
En la última sección ha configurado dos directivas.  hiveuser1 tiene el permiso select en todas las columnas y hiveuser2, en dos columnas. En esta sección se suplanta a los dos usuarios para importar datos a Excel.

1. Abra un libro de Excel nuevo o existente.
2. En la pestaña **Datos**, haga clic en **From Other Data Sources** (Desde otros orígenes de datos) y en **Desde el Asistente para la conexión de datos** para iniciar el **Asistente para la conexión de datos**.

    ![Abrir el Asistente para la conexión de datos][img-hdi-simbahiveodbc.excel.dataconnection]
3. Seleccione **DSN ODBC** como origen de datos y haga clic en **Siguiente**.
4. En los orígenes de datos ODBC, seleccione el nombre del origen de datos creado en el paso anterior y haga clic en **Siguiente**.
5. Vuelva a escribir la contraseña para el clúster en el asistente y haga clic en **Aceptar**. Espere a que se abra el cuadro de diálogo **Seleccionar base de datos y tabla** . Esta operación puede tardar unos segundos.
6. Seleccione **hivesampletable** y haga clic en **Siguiente**.
7. Haga clic en **Finalizar**
8. En el cuadro de diálogo **Importar datos** , puede cambiar o especificar la consulta. Para ello, haga clic en **Propiedades**. Esta operación puede tardar unos segundos.
9. Haga clic en la pestaña **Definición**. El texto del comando es:

       SELECT * FROM "HIVE"."default"."hivesampletable"

   Según las directivas de Ranger definidas, hiveuser1 tiene el permiso select en todas las columnas.  Por lo que esta consulta funciona con las credenciales del hiveuser1, pero no con las credenciales del hiveuser2.

   ![Propiedades de conexión][img-hdi-simbahiveodbc-excel-connectionproperties]
10. Haga clic en **Aceptar** para cerrar el cuadro de diálogo Propiedades de conexión.
11. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Importar datos**.  
12. Vuelva a escribir la contraseña de hiveuser1 y haga clic en **Aceptar**. La importación de los datos a Excel tarda algunos segundos. Cuando termine, verá 11 columnas de datos.

Para probar la segunda directiva (read-hivesampletable-devicemake) que creó en la última sección

1. Agregue una nueva hoja de Excel.
2. Siga el último procedimiento para importar los datos.  El único cambio que deberá hacer es usar las credenciales de hiveuser2 en lugar de las de hiveuser1. Esto genera un error, ya que hiveuser2 solo tiene permiso para ver dos columnas. Se producirá el error siguiente:

        [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
3. Siga el mismo procedimiento para importar los datos. Esta vez, utilice las credenciales de hiveuser2 y modifique la instrucción select de:

        SELECT * FROM "HIVE"."default"."hivesampletable"

    a:

        SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"

    Cuando termine, verá dos columnas de datos importados.

## <a name="next-steps"></a>pasos siguientes
* Para configurar un clúster de HDInsight unido a dominio, consulte [Configure Domain-joined HDInsight clusters](apache-domain-joined-configure.md) (Configuración de clústeres de HDInsight unidos a un dominio).
* Para administrar un clúster de HDInsight unido a dominio, consulte [Manage Domain-joined HDInsight clusters](apache-domain-joined-manage.md) (Administración de clústeres de HDInsight unidos a un dominio).
* Para ejecutar consultas de Hive mediante SSH en clústeres de HDInsight unidos a un dominio, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined).
* Para conectar Hive mediante Hive JDBC, consulte [Conexión a Hive en HDInsight de Azure con el controlador JDBC de Hive](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)
* Para conectar Excel a Hadoop con Hive ODBC, consulte [Conexión de Excel a Hadoop con el controlador ODBC de Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* Para conectar Excel a Hadoop con Power Query, consulte [Conexión de Excel a Hadoop con Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md)
