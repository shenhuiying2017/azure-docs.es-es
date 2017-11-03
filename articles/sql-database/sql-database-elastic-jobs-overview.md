---
title: "Administración de bases de datos escaladas horizontalmente en la nube | Microsoft Docs"
description: "Use el servicio Trabajo de Elastic Database para ejecutar un script a través de un grupo de bases de datos."
metakeywords: azure sql database elastic databases
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 6fa47cf2-1162-4534-a206-6e2d95b78580
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: f709cd38a690ba666ca290cc029caa2ce4f9dff0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2017
---
# <a name="managing-scaled-out-cloud-databases"></a>Administración de bases de datos escaladas horizontalmente en la nube
Para administrar bases de datos con particiones y escaladas horizontalmente en la nube, la característica **Trabajos de base de datos elástica** (versión preliminar) permite ejecutar un script de Transact-SQL (T-SQL) de forma confiable en un grupo de bases de datos, lo que incluye:

* una colección personalizada de bases de datos (se explica más adelante)
* todas las bases de datos de un [grupo elástico](sql-database-elastic-pool.md)
* un conjunto de particiones (creadas con la [biblioteca de cliente de bases de datos elásticas](sql-database-elastic-database-client-library.md)) 

## <a name="documentation"></a>Documentación
* [Información general sobre la instalación de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md). 
* [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md)
* [Creación y administración de un grupo de bases de datos SQL elásticas mediante PowerShell](sql-database-elastic-jobs-powershell.md).
* [Creación y administración de Bases de datos SQL de Azure escaladas horizontalmente](sql-database-elastic-jobs-getting-started.md)

**Trabajos de base de datos elástica** es actualmente un servicio en la nube de Azure hospedado en el cliente que permite la ejecución de tareas administrativas ad hoc y programadas, que se denominan **trabajos**. Con los trabajos, puede administrar de forma fácil y confiable grandes grupos de bases de datos SQL de Azure mediante la ejecución de scripts de Transact-SQL para realizar operaciones administrativas. 

![Servicio del trabajo de bases de datos elásticas][1]

## <a name="why-use-jobs"></a>¿Por qué usar trabajos?
**Manage**

Realice fácilmente cambios de esquema, administración de credenciales, actualizaciones de datos de referencia, recopilación de datos de rendimiento o recopilación de telemetría de inquilinos (cliente).

**Informes**

Agregue datos de una colección de bases de datos SQL de Azure en una tabla de destino única.

**Reducción de la sobrecarga**

Normalmente, debe conectarse a cada base de datos de forma independiente para ejecutar instrucciones T-SQL o realizar otras tareas administrativas. Un trabajo controla la tarea de inicio de sesión en cada base de datos en el grupo de destino. También se definen, mantienen y conservan los scripts Transact-SQL que se van a ejecutar transversalmente en un grupo de bases de datos SQL de Azure.

**Control**

Los trabajos ejecutan el script y registran el estado de ejecución de cada base de datos. Obtenga también el reintento automático en caso de errores.

**Flexibilidad**

Defina grupos personalizados de bases de datos SQL de Azure, así como programaciones para ejecutar un trabajo.

> [!NOTE]
> En el Portal de Azure, solo hay disponible un conjunto reducido de funciones limitadas a grupos elásticos de SQL Azure. Use las API de PowerShell para tener acceso al conjunto completo de funcionalidad actual.
> 
> 

## <a name="applications"></a>Aplicaciones
* Realice tareas administrativas como, por ejemplo, la implementación de un nuevo esquema
* Actualice información de producto con datos de referencia comunes en todas las bases de datos. O bien, programe actualizaciones automáticas todos los días laborables, fuera del horario de trabajo.
* Regeneración de índices para mejorar el rendimiento de consultas La regeneración puede configurarse para que se ejecute periódicamente en una colección de bases de datos como, por ejemplo, fuera de horas pico.
* Recopile los resultados de consulta de un conjunto de bases de datos en una tabla central de forma continua. Las consultas de rendimiento pueden ejecutarse continuamente y configurarse para que desencadenen la ejecución de otras tareas.
* Ejecute consultas de procesamiento de datos de ejecución más larga en un conjunto grande de bases de datos; por ejemplo, la recopilación de telemetría de cliente. Los resultados se recopilan en una sola tabla de destino para su posterior análisis.

## <a name="elastic-database-jobs-end-to-end"></a>Información detallada sobre los trabajos de base de datos elástica
1. Instale los componentes de **Trabajos de base de datos elástica** . Para obtener más información, vea [Instalación de Trabajos de base de datos elástica](sql-database-elastic-jobs-service-installation.md). En caso de error en la instalación, vea [Desinstalación](sql-database-elastic-jobs-uninstall.md).
2. Use las API de PowerShell para tener acceso a otra funcionalidad, por ejemplo, crear colecciones de bases de datos personalizadas, agregar programaciones o recopilar conjuntos de resultados. Use el portal para instalar, crear y supervisar de manera sencilla los trabajos que se limiten a la ejecución de un **grupo elástico**. 
3. Cree credenciales cifradas para la ejecución de trabajos y [agregue el usuario (o rol) a cada base de datos del grupo](sql-database-security-overview.md).
4. Cree un script de T-SQL idempotente que se pueda ejecutar en cada base de datos del grupo. 
5. Siga estos pasos para crear trabajos mediante el Portal de Azure: [Creación y administración de trabajos de base de datos elástica](sql-database-elastic-jobs-create-and-manage.md). 
6. O bien use scripts de PowerShell: [Creación y administración de trabajos de base de datos elástica de Base de datos SQL (vista previa)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Scripts idempotentes
Los scripts deben ser [idempotentes](https://en.wikipedia.org/wiki/Idempotence). En términos sencillos, "idempotente" significa que si el script se ejecuta correctamente y se vuelve a ejecutar, produce el mismo resultado. Un script puede producir errores debido a problemas de red transitorios. En ese caso, el trabajo volverá a intentar ejecutar automáticamente el script un número de veces predefinido antes de desistir. Un script idempotente produce el mismo resultado aunque se ejecute correctamente dos veces. 

Una táctica sencilla es probar la existencia de un objeto antes de crearlo.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

De forma similar, un script debe ser capaz de comprobar lógicamente y contrarrestar las condiciones que encuentre para ejecutarse correctamente.

## <a name="failures-and-logs"></a>Errores y registros
Si se produce un error en un script después de varios intentos, el trabajo registra el error y continúa. Después de que finalice un trabajo (es decir, una ejecución en todas las bases de datos del grupo), puede comprobar la lista de intentos incorrectos. Los registros proporcionan detalles para depurar scripts defectuosos. 

## <a name="group-types-and-creation"></a>Tipos de grupo y creación
Hay dos tipos de grupos: 

1. Conjuntos de particiones
2. Grupos personalizados

Los grupos de conjuntos de particiones se crean mediante las [Herramientas para bases de datos elásticas](sql-database-elastic-scale-introduction.md). Cuando se crea un grupo de conjuntos de particiones, las bases de datos se agregan o se quitan del grupo automáticamente. Por ejemplo, las nuevas particiones se incluyen automáticamente en el grupo al agregarlas al mapa de particiones. A continuación, se puede ejecutar un trabajo en el grupo.

Por otro lado, los grupos personalizados se definen de forma rígida. Debe agregar o quitar explícitamente las bases de datos de los grupos personalizados. Si se quita una base de datos del grupo, el trabajo intentará ejecutar el script en la base de datos, lo que puede producir un error. Los grupos creados mediante el Portal de Azure son grupos personalizados. 

## <a name="components-and-pricing"></a>Componentes y precios
Los siguientes componentes funcionan conjuntamente para crear un servicio de nube de Azure que permite la ejecución de ad hoc de trabajos administrativos. Los componentes se instalan y configuran automáticamente durante la instalación, en su suscripción. Puede identificar los servicios, ya que todos tienen el mismo nombre generado automáticamente. El nombre es único y se compone del prefijo "edj" seguido de 21 caracteres generados de forma aleatoria.

* **Servicio de nube de Azure**: los trabajos de bases de datos elásticas (vista previa) se entregan como un servicio de nube de Azure hospedado por el cliente para realizar la ejecución de las tareas requeridas. Desde el portal, el servicio se implemente y hospeda en su suscripción de Microsoft Azure. El servicio implementado predeterminado se ejecuta con un mínimo de dos roles de trabajador para ofrecer un elevado nivel de disponibilidad. El tamaño predeterminado de cada función de trabajador (ElasticDatabaseJobWorker) se ejecuta en una instancia de A0. Para obtener información sobre los precios, vea [Precios de servicios de nube](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Azure SQL Database**: el servicio usa una base de datos de Azure SQL conocida como **base de datos de control** para almacenar todos los metadatos del trabajo. El nivel de servicio predeterminado es S0. Para obtener información sobre precios, vea [Precios de bases de datos SQL](https://azure.microsoft.com/pricing/details/sql-database/).
* **Bus de servicio de Azure**: el bus de servicio de Azure permite coordinar el trabajo del servicio de nube de Azure. Vea [Precios del bus de servicio](https://azure.microsoft.com/pricing/details/service-bus/).
* **Almacenamiento de Azure**: se usa una cuenta de almacenamiento de Azure para almacenar los registros de salida de diagnóstico en caso de que un problema requiera una mayor depuración (vea [Habilitación de diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)). Para obtener información sobre precios, vea [Precios de almacenamiento de Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Funcionamiento de Trabajos de base de datos elástica
1. Se designa una Base de datos SQL de Azure como **base de datos de control** que almacena todos los datos de estado y los metadatos.
2. Se obtiene acceso a la base de control mediante el **servicio de trabajos** para iniciar los trabajos que deben ejecutarse y hacerles el seguimiento.
3. Dos roles diferentes se comunican con la base de datos de control: 
   * Controlador: determina los trabajos que requieren tareas para realizar el trabajo solicitado y reintenta los trabajos con errores creando nuevas tareas de trabajo.
   * Ejecución de tareas de trabajo: lleva a cabo las tareas de trabajo.

### <a name="job-task-types"></a>Tipos de tareas de trabajo
Hay varios tipos de tareas de trabajo que efectúan la ejecución de trabajos:

* ShardMapRefresh: consulta el mapa de particiones para determinar todas las bases de datos que se usan como particiones
* ScriptSplit: divide el script en lotes de instrucciones 'GO'
* ExpandJob: crea trabajos secundarios para cada base de datos en un trabajo destinado a un grupo de bases de datos
* ScriptExecution: ejecuta un script en una base de datos concreta con las credenciales que se definan
* Dacpac: aplica un DACPAC a una base de datos concreta con las credenciales que se determinen

## <a name="end-to-end-job-execution-work-flow"></a>Flujo de trabajo de ejecución de trabajos completo
1. Con el Portal o la API de PowerShell, se inserta un trabajo en la **base de datos de control**. El trabajo solicita la ejecución de un script de Transact-SQL en un grupo de bases de datos con las credenciales específicas.
2. El controlador identifica el nuevo trabajo. Se crean tareas de trabajo y se ejecutan para dividir el script y actualizar las bases de datos del grupo. Por último, se crea otro trabajo y se ejecuta para expandir el trabajo y crear nuevos trabajos secundarios donde se especifica que cada trabajo secundario ejecute el script de Transact-SQL en una base de datos individual del grupo.
3. El controlador identifica los trabajos secundarios creados. Para cada trabajo, el controlador crea y desencadena una tarea de trabajo que ejecuta el script en una base de datos. 
4. Tras completar todas las tareas de trabajo, el controlador actualiza los trabajos con el estado completado. 
   En cualquier momento durante la ejecución de trabajos, puede usarse la API de PowerShell para ver el estado actual de la ejecución de trabajos. Todas las horas que devuelven las API de PowerShell se representan en formato UTC. Si lo desea, se puede iniciar una solicitud de cancelación para detener un trabajo. 

## <a name="next-steps"></a>Pasos siguientes
[Instale los componentes](sql-database-elastic-jobs-service-installation.md) y luego [cree y agregue un registro en cada base de datos del grupo](sql-database-manage-logins.md). Para comprender mejor la administración y creación de trabajos, consulte [Creación y administración de trabajos de bases de datos elásticas](sql-database-elastic-jobs-create-and-manage.md). Vea también [Introducción a Trabajos de base de datos elástica](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->


