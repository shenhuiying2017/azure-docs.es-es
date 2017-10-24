---
title: "Administración de permisos de usuario en los niveles de archivo y carpeta: Azure HDInsight | Microsoft Docs"
description: "Cómo administrar los permisos de archivos y carpetas en los clústeres de HDInsight unidos a un dominio."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 42d617ffeb8c2fee6be6d747b39d80b09774a1c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>Administración de permisos de usuario en los niveles de archivo y carpeta

[Clústeres de HDInsight unidos a un dominio](hdinsight-domain-joined-introduction.md): utilice una autenticación sólida con usuarios de Azure Active Directory (Azure AD) y también las directivas de *control de acceso basado en rol* (RBAC) para varios servicios, como YARN y Hive. Si el almacén de datos predeterminado del clúster es Azure Storage o Windows Azure Storage Blobs (WASB), también puede aplicar permisos a nivel de carpeta y archivo. Puede usar Apache Ranger para controlar el acceso de los usuarios y grupos sincronizados de Azure AD a los archivos del clúster.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

La instancia de Apache Ranger para los clústeres de HDInsight unidos a un dominio tiene preconfigurado el servicio Ranger-WASB. El servicio Ranger-WASB es un motor de administración de políticas similar a Ranger-HDFS, pero con un cumplimiento distinto de las directivas de acceso de Ranger. En el servicio Ranger-WASB, si una solicitud de recursos entrante no tiene una directiva de Ranger de conciliación, la respuesta predeterminada es DENY. El servicio Ranger no pasa la comprobación de permisos a WASB.

## <a name="permission-and-policy-model"></a>Modelo de directivas y permisos

Las solicitudes de acceso a recursos se evalúan con el siguiente flujo:

![Flujo de evaluación de directivas de Apache Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

Primero se evalúan las reglas DENY, seguidas de las reglas ALLOW. Al final de la conciliación, si las directivas no coinciden, se devuelve un resultado DENY.

### <a name="user-variable"></a>Variable USER

Puede usar la variable `{USER}` al asignar directivas a un usuario para que acceda a un subdirectorio `/{username}`; por ejemplo:

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

La directiva anterior concede a los usuarios acceso a su propia subcarpeta en el directorio `/app-logs/`. Este es el aspecto de la directiva en la interfaz de usuario de Ranger:

![Ejemplo de uso de la variable USER](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Ejemplos de modelo de directivas

En la tabla siguiente se enumeran algunos ejemplos de cómo funciona el modelo de directiva:

| Directiva de Ranger | Sistema de archivos existente | Solicitud de usuario | Resultado |
| -- | -- | -- | -- |
| /data/finance/, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW: la carpeta intermedia "finance" se crea a raíz de la comprobación del antecesor |
| /data/finance/, bob, WRITE | /data | alice, Create file /data/finance/mydatafile.txt | DENY: ninguna directiva de conciliación |
| /data/finance*, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | ALLOW: en este caso, la directiva recursiva opcional (`*`) está presente; vea los [caracteres comodín](#wildcards) |
| /data/finance/mydatafile.txt, bob, WRITE | /data | bob, Create file /data/finance/mydatafile.txt | DENY: no se pudo comprobar el antecesor en "/data" porque no hay ninguna directiva |
| /data/finance/mydatafile.txt, bob, WRITE | /data/finance | bob, Create file /data/finance/mydatafile.txt | DENY: ninguna directiva para la comprobación del antecesor en "/data/finance" |

Se requieren permisos en el nivel de carpeta o de archivo, según el tipo de operación. Por ejemplo, una llamada "read/open" requiere acceso de lectura a nivel de archivo, mientras que una llamada "create" requiere permisos a nivel de la carpeta antecesora.

### <a name="wildcards-"></a>Caracteres comodín (*)

Cuando un carácter comodín (`*`) está presente en la ruta de acceso de una directiva, dicho carácter se aplica a esa ruta de acceso y a su subárbol completo. Esta recursividad es lo mismo que usar `recurse-flag`. En Ranger-WASB, el carácter comodín indica recursividad y coincidencia parcial de nombres.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Administración de permisos a nivel de archivo y carpeta con Apache Ranger

Si no lo ha hecho aún, siga [estas instrucciones](hdinsight-domain-joined-configure.md) para aprovisionar un nuevo clúster unido a un dominio.

Abra Ranger-WASB; para ello, examine `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`. Escriba el nombre de usuario y la contraseña del administrador del clúster que definió cuando creó el clúster.

Después de iniciar sesión, verá el panel de Ranger:

![Panel de Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

Para ver los permisos actuales de archivos y carpetas de la cuenta de Azure Storage asociada del clúster, haga clic en el vínculo ***CLUSTERNAME*_wasb** en el cuadro de control de WASB.

![Panel de Ranger](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

Se muestra la lista de directivas actuales. Como punto inicial, se incluyen varias directivas típicas; consulte los detalles de cada directiva para ver ejemplos de uso.

En cada directiva, puede ver si la directiva está habilitada, si el registro de auditoría está configurado y qué grupos y usuarios hay asignados. Hay dos botones de acción para cada directiva: Edit (Editar) y Delete (Eliminar).

![Lista de directivas](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Adición de una nueva directiva

1. En la parte superior derecha de la página de directivas de WASB, seleccione **Add New Policy** (Agregar nueva directiva).

    ![Add New Policy](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. Escriba un texto descriptivo en **Policy Name** (Nombre de la directiva). Especifique la información de Azure **Storage Account** (Cuenta de Azure Storage) para el clúster (*NOMBRE_CUENTA*.blob.core.windows.net) y de **Storage Account Container** (Contenedor de la cuenta de almacenamiento), que debe coincidir con lo que especificó al crear el clúster. Escriba el texto correspondiente en **Relative Path** (Ruta de acceso relativa) en relación con el clúster para la carpeta o el archivo a los que se accede.

    ![Formulario de nueva directiva](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Debajo del formulario, especifique las **condiciones permitidas** para este nuevo recurso. Seleccione los usuarios y grupos correspondientes y establezca sus permisos. En el ejemplo siguiente, se permite que todos los usuarios del grupo `sales` tengan acceso de lectura/escritura.

    ![Permitir ventas](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Seleccione **Guardar**.

### <a name="example-policy-conditions"></a>Ejemplo de condiciones de directiva

El [flujo de evaluación de directivas](#permission-and-policy-model) de Apache Ranger permite especificar cualquier combinación de condiciones admitidas y denegadas que satisfaga sus necesidades. Estos son algunos ejemplos:

1. Permitir a todos los usuarios de ventas, pero no a los que están en prácticas:

    ![Permitir a los usuarios de ventas y denegar a los que están en prácticas](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Permitir a todos los usuarios de ventas y denegar a todos los que están en prácticas, salvo a un usuario en prácticas llamado "hiveuser3", que debe tener acceso de lectura:

    ![Permitir a los usuarios de ventas y denegar a los que están en prácticas, salvo a hiveuser3](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de directivas de Hive en HDInsight unido a un dominio](hdinsight-domain-joined-run-hive.md)
* [Administración de clústeres de HDInsight unidos a dominio](hdinsight-domain-joined-manage.md)
* [Administración de Ambari: autorización de usuarios en Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

