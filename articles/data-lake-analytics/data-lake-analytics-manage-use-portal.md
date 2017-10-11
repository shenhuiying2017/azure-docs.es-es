---
title: "Administración de Azure Data Lake Analytics con el portal de Azure | Microsoft Docs"
description: "Aprenda a administrar cuentas, orígenes de datos, usuarios y trabajos de Análisis de Data Lake."
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: a0e045f1-73d6-427f-868d-7b55c10f811b
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: e49d1a0e0ccc6567d0a6841817667717ff5dba76
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="manage-azure-data-lake-analytics-by-using-the-azure-portal"></a>Administración de Azure Data Lake Analytics con el portal de Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Aprenda a administrar cuentas, orígenes de datos, usuarios y trabajos de Azure Data Lake Analytics mediante el portal de Azure. Para ver los temas de administración sobre el uso de otras herramientas, haga clic en una pestaña de la parte superior de la página.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Administración de cuentas de Data Lake Analytics

### <a name="create-an-account"></a>Crear una cuenta

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Nuevo** > **Inteligencia y análisis** > **Data Lake Analytics**.
3. Seleccione los valores de los siguientes elementos: 
   1. **Nombre**: nombre de la cuenta de Azure Data Lake Analytics.
   2. **Suscripción**: suscripción de Azure usada para la cuenta.
   3. **Grupo de recursos**: grupo de recursos de Azure en el que se va a crear la cuenta. 
   4. **Ubicación**: centro de datos de Azure para la cuenta de Data Lake Analytics. 
   5. **Data Lake Store**: almacén predeterminado que se va a usar para la cuenta de Data Lake Analytics. Las cuentas de Azure Data Lake Store y de Data Lake Analytics deben estar en la misma ubicación.
4. Haga clic en **Crear**. 

### <a name="delete-a-data-lake-analytics-account"></a>Eliminar una cuenta de Data Lake Analytics

Para eliminar una cuenta de Data Lake Analytics, elimine la cuenta de Data Lake Store predeterminada.

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Hacer clic en **Eliminar**.
3. Escriba el nombre de la cuenta.
4. Hacer clic en **Eliminar**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Administración de orígenes de datos

Data Lake Analytics admite los siguientes orígenes de datos:

* Data Lake Store
* Almacenamiento de Azure

Puede usar el explorador de datos para examinar los orígenes de datos y realizar operaciones básicas de administración de archivos. 

### <a name="add-a-data-source"></a>Agregar un origen de datos

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Orígenes de datos**.
3. Haga clic en **Agregar origen de datos**.
    
   * Para agregar una cuenta de Data Lake Store, necesita el nombre de la cuenta y acceso a ella para poder realizar consultas.
   * Para agregar Azure Blob Storage, necesita la cuenta y la clave de almacenamiento. Para encontrarlas, vaya a la cuenta de almacenamiento en el portal.

## <a name="set-up-firewall-rules"></a>Configurar reglas de firewall

Puede usar Data Lake Analytics para bloquear aún más el acceso a la cuenta de Data Lake Analytics en el nivel de red. Puede habilitar un firewall, especificar una dirección IP o definir un intervalo de direcciones IP para los clientes de confianza. Una vez habilitadas estas medidas, solo los clientes con direcciones IP del intervalo definido pueden conectarse al almacén.

Si otros servicios de Azure, como Azure Data Factory o las máquinas virtuales, se conectan a la cuenta de Data Lake Analytics, asegúrese de que **Permitir los servicios de Azure** esté **Activado**. 

### <a name="set-up-a-firewall-rule"></a>Configurar una regla de firewall

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. En el menú de la izquierda, haga clic en **Firewall**.

## <a name="add-a-new-user"></a>Agregar un nuevo usuario

Puede usar el **Asistente para agregar usuario** para aprovisionar fácilmente nuevos usuarios de Data Lake.

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. A la izquierda, en **Introducción**, haga clic en **Asistente para agregar usuario**.
3. Seleccione un usuario y luego haga clic en **Seleccionar**.
4. Seleccione un rol y luego haga clic en **Seleccionar**. Para configurar un nuevo desarrollador que use Azure Data Lake, seleccione el rol **Desarrollador de Data Lake Analytics**.
5. Seleccione las listas de control de acceso (ACL) para las bases de datos de U-SQL. Cuando esté satisfecho con las opciones seleccionadas, haga clic en **Seleccionar**.
6. Seleccione las ACL para los archivos. Para el almacén predeterminado, no cambie las ACL de la carpeta raíz "/" ni de la carpeta /system. Haga clic en **Seleccionar**.
7. Revise todos los cambios seleccionados y luego haga clic en **Ejecutar**.
8. Al finalizar el asistente, haga clic en **Listo**.

## <a name="manage-role-based-access-control"></a>Administrar el control de acceso basado en roles

Al igual que otros servicios de Azure, puede usar el control de acceso basado en roles (RBAC) para controlar cómo interactúan los usuarios con el servicio.

Los roles estándar de RBAC tienen las siguientes capacidades:
* **Propietario**: puede enviar, supervisar y cancelar trabajos de cualquier usuario y configurar la cuenta.
* **Colaborador**: puede enviar, supervisar y cancelar trabajos de cualquier usuario y configurar la cuenta.
* **Lector**: puede supervisar trabajos.

Emplee el rol de desarrollador de Data Lake Analytics para permitir que los desarrolladores de U-SQL usen el servicio Data Lake Analytics. Puede usar el rol de desarrollador de Data Lake Analytics para:
* Enviar trabajos.
* Supervisar el estado y el progreso de los trabajos enviados por cualquier usuario.
* Ver los scripts de U-SQL de los trabajos enviados por cualquier usuario.
* Cancelar solo sus propios trabajos.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Agregar usuarios o grupos de seguridad a una cuenta de Data Lake Analytics

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Control de acceso (IAM)** > **Agregar**.
3. Seleccione un rol.
4. Agregue un usuario.
5. Haga clic en **Aceptar**.

>[!NOTE]
>Si un usuario o un grupo de seguridad necesita enviar trabajos, también necesita permiso en la cuenta de almacenamiento. Para más información, vea [Secure data stored in Data Lake Store (Protección de datos almacenados en Data Lake Store)](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Trabajos de administración

### <a name="submit-a-job"></a>Enviar un trabajo

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.

2. Haga clic en **Nuevo trabajo**. Para cada trabajo, configure:

    1. **Nombre del trabajo**: nombre del trabajo.
    2. **Prioridad**: los números más bajos tienen mayor prioridad. Si hay dos trabajos en cola, se ejecuta primero el que tenga un valor de prioridad más bajo.
    3. **Paralelismo**: número máximo de procesos de cálculo que se van a reservar para este trabajo.

3. Haga clic en **Enviar trabajo**.

### <a name="monitor-jobs"></a>Supervisión de trabajos

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Ver todos los trabajos**. Se muestra una lista de todos los trabajos activos y finalizados recientemente en la cuenta.
3. De forma opcional, haga clic en **Filtrar** para encontrar los trabajos por los valores **Intervalo de tiempo**, **Nombre del trabajo** y **Autor**. 

### <a name="monitoring-pipeline-jobs"></a>Supervisión de trabajos de canalización
Los trabajos que forman parte de un trabajo de canalización actúan juntos, normalmente de forma secuencial, para lograr un escenario concreto. Por ejemplo, puede tener una canalización que limpia, extrae, transforma y agrega el uso para información del cliente. Los trabajos de canalización se identifican mediante la propiedad "Pipeline" al enviar el trabajo. En los trabajos programados con ADF V2 esta propiedad se rellena de forma automática. 

Para ver una lista de trabajos de U-SQL que forman parte de canalizaciones: 

1. En Azure Portal, vaya a las cuentas de Data Lake Analytics.
2. Haga clic en **Información de trabajos**. La pestaña "Todos los trabajos" aparece de forma predeterminada y muestra una lista de trabajos en ejecución, en cola y finalizados.
3. Haga clic en la pestaña **Trabajos de canalización**. Se muestra una lista de trabajos de canalización junto con estadísticas agregadas de cada canalización.

### <a name="monitoring-recurring-jobs"></a>Supervisión de trabajos periódicos
Un trabajo periódico es aquel que tiene la misma lógica de negocios pero usa distintos datos de entrada cada vez que se ejecuta. Lo ideal sería que los trabajos periódicos siempre fueran correctos y tuvieran un tiempo de ejecución relativamente estable; la supervisión de estos comportamientos ayuda a garantizar que el trabajo sea correcto. Los trabajos periódicos se identifican mediante la propiedad "Recurrence". En los trabajos programados con ADF V2 esta propiedad se rellena de forma automática.

Para ver una lista de trabajos de U-SQL que son periódicos: 

1. En Azure Portal, vaya a las cuentas de Data Lake Analytics.
2. Haga clic en **Información de trabajos**. La pestaña "Todos los trabajos" aparece de forma predeterminada y muestra una lista de trabajos en ejecución, en cola y finalizados.
3. Haga clic en la pestaña **Trabajos periódicos**. Se muestra una lista de trabajos periódicos junto con estadísticas agregadas de cada uno de ellos.

## <a name="manage-policies"></a>Administración de directivas

### <a name="account-level-policies"></a>Directivas de nivel de cuenta

Estas directivas se aplican a todos los trabajos de una cuenta de Data Lake Analytics.

#### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Número máximo de unidades de análisis en una cuenta de Data Lake Analytics
Una directiva controla el número total de unidades de análisis (AU) que puede usar la cuenta de Data Lake Analytics. De forma predeterminada, el valor se establece en 250. Por ejemplo, si este valor está establecido en 250 unidades, puede tener un trabajo en ejecución con 250 unidades asignadas o 10 trabajos en ejecución con 25 unidades cada uno. Los trabajos adicionales que se envían se ponen en cola hasta que finalizan los trabajos en ejecución. Cuando terminan los trabajos en ejecución, se liberan unidades de análisis para la ejecución de los trabajos en cola.

Para cambiar el número de unidades de análisis de la cuenta de Data Lake Analytics:

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Propiedades**.
3. En **Número de AU máximo**, mueva el control deslizante para seleccionar un valor o escríbalo en el cuadro de texto. 
4. Haga clic en **Guardar**.

> [!NOTE]
> Si necesita más unidades de análisis que el valor predeterminado (250), haga clic en **Ayuda y soporte técnico** en el portal para enviar una solicitud de soporte técnico. Es posible aumentar el número de unidades de análisis disponibles en la cuenta de Data Lake Analytics.
>

#### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Número máximo de trabajos que se pueden ejecutar simultáneamente
Una directiva controla cuántos trabajos pueden ejecutarse al mismo tiempo. De forma predeterminada, este valor se establece en 20. Si Data Lake Analytics tiene unidades de análisis disponibles, los trabajos nuevos se programan para ejecutarse de inmediato hasta que el número total de trabajos en ejecución alcanza el valor de esta directiva. Cuando se alcanza el número máximo de trabajos que se pueden ejecutar al mismo tiempo, los trabajos posteriores se ponen en cola en orden de prioridad hasta que finalizan uno o varios de los trabajos que están en ejecución (según la disponibilidad de unidades de análisis).

Para cambiar el número de trabajos que se pueden ejecutar simultáneamente:

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Propiedades**.
3. En **Número máximo de trabajos en ejecución**, mueva el control deslizante para seleccionar un valor o escríbalo en el cuadro de texto. 
4. Haga clic en **Guardar**.

> [!NOTE]
> Si necesita ejecutar más que el número predeterminado de trabajos (20), en el portal, haga clic en **Ayuda y soporte técnico** para enviar una solicitud de soporte técnico. Es posible aumentar el número de trabajos que se pueden ejecutar al mismo tiempo en la cuenta de Data Lake Analytics.
>

#### <a name="how-long-to-keep-job-metadata-and-resources"></a>Periodo de conservación de metadatos de trabajo y recursos 
Cuando los usuarios ejecutan trabajos de U-SQL, el servicio Data Lake Analytics conserva todos los archivos relacionados. Los archivos relacionados incluyen el script de U-SQL, los archivos DLL a los que se hace referencia en el script de U-SQL, los recursos compilados y las estadísticas. Los archivos están en la carpeta /system/ de la cuenta predeterminada de Azure Data Lake Storage. Esta directiva controla durante cuánto tiempo se almacenan estos recursos antes de su eliminación automática (el valor predeterminado es 30 días). Puede usar estos archivos para la depuración y para la optimización de rendimiento de los trabajos que se vayan a volver a ejecutar en el futuro.

Para cambiar el periodo de conservación de metadatos de trabajo y recursos:

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Propiedades**.
3. En **Días durante los que se conservarán las consultas de trabajo**, mueva el control deslizante para seleccionar un valor o escríbalo en el cuadro de texto.  
4. Haga clic en **Guardar**.

### <a name="job-level-policies"></a>Directivas de nivel de trabajo
Con las directivas de nivel de trabajo, puede controlar las unidades de análisis máximas y la prioridad máxima que los usuarios individuales (o los miembros de grupos de seguridad concretos) pueden establecer en los trabajos que envían. Esto permite controlar los costos en que incurren los usuarios. También permite controlar el efecto que los trabajos programados podrían tener en los trabajos de producción de alta prioridad que se ejecutan en la misma cuenta de Data Lake Analytics.

Data Lake Analytics tiene dos directivas que se pueden establecer en el nivel de trabajo:

* **Límite de AU por trabajo**: los usuarios solo pueden enviar trabajos que tengan como máximo este número de unidades de análisis. De forma predeterminada, este límite es el mismo que el límite máximo de unidades de análisis de la cuenta.
* **Prioridad**: los usuarios solo pueden enviar trabajos con una prioridad inferior o igual a este valor. Tenga en cuenta que un número mayor significa una prioridad más baja. De forma predeterminada, se establece en 1, que es la prioridad más alta posible.

Hay una directiva predeterminada establecida en cada cuenta. La directiva predeterminada se aplica a todos los usuarios de la cuenta. Puede establecer directivas adicionales para usuarios y grupos concretos. 

> [!NOTE]
> Las directivas de nivel de cuenta y las directivas de nivel de trabajo se aplican de forma simultánea.
>

#### <a name="add-a-policy-for-a-specific-user-or-group"></a>Agregar una directiva para un usuario o grupo concreto

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Propiedades**.
3. En **Límites del envío de trabajos**, haga clic en el botón **Agregar directiva**. Luego, seleccione o escriba los siguientes valores:
    1. **Nombre de la directiva de cálculo**: escriba un nombre de directiva que le recuerde su propósito.
    2. **Seleccionar usuario o grupo**: seleccione el usuario o grupo al que se aplica esta directiva.
    3. **Establezca el límite de AU del trabajo**: establezca el límite de unidades de análisis que se aplica al usuario o grupo seleccionado.
    4. **Establezca el límite de prioridad**: establezca el límite de prioridad que se aplica al usuario o grupo seleccionado.

4. Haga clic en **Aceptar**.

5. La nueva directiva aparece en la tabla de directivas **Predeterminado** de **Límites del envío de trabajos**. 

#### <a name="delete-or-edit-an-existing-policy"></a>Eliminar o editar una directiva existente

1. En Azure Portal, vaya a la cuenta de Data Lake Analytics.
2. Haga clic en **Propiedades**.
3. En **Límites del envío de trabajos**, busque la directiva que quiere editar.
4.  Para ver las opciones **Eliminar** y **Editar**, en la columna situada más a la derecha de la tabla, haga clic en **...** .

### <a name="additional-resources-for-job-policies"></a>Recursos adicionales para directivas de trabajo
* [Entrada de blog de información general sobre directivas](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Entrada de blog sobre directivas de nivel de cuenta](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Entrada de blog sobre directivas de nivel de trabajo](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Pasos siguientes

* [Información general de Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introducción al uso de Azure Portal por parte de Azure Data Lake Analytics](data-lake-analytics-get-started-portal.md)
* [Manage Azure Data Lake Analytics by using Azure PowerShell](data-lake-analytics-manage-use-powershell.md) (Administración de Azure Data Lake Analytics mediante Azure PowerShell)

