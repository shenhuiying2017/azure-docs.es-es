---
title: "Administración de bases de datos en Azure SQL Data Warehouse | Microsoft Docs"
description: "Información general de administración de bases de datos de Almacenamiento de datos SQL Incluye herramientas de administración, DWU y rendimiento de escalado horizontal, solución de problemas de rendimiento de las consultas, el establecimiento de directivas de seguridad y la restauración una base de datos de daños en los datos o de un apagón regional."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: jhubbard
editor: 
ms.assetid: 8576fdb3-71fe-4b3b-a4e0-5e8a7f148acf
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: kevin;barbkess
ms.openlocfilehash: b14d0aad5a1f50c225391dbab27ec6240423a65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Administración de base datos en Almacenamiento de datos SQL de Azure
Almacenamiento de datos SQL automatiza muchos aspectos de la administración de las bases de datos. Por ejemplo, para escalar el rendimiento solo necesita ajustar y pagar por el nivel adecuado de recursos de proceso y, a continuación, dejar que Almacenamiento de datos SQL haga todo el trabajo de escalado horizontal y escalado de nuevo.

Sin duda deseará supervisar la carga de trabajo para identificar sus necesidades de rendimiento, así como solucionar problemas de consultas de larga ejecución. También debe realizar algunas tareas de seguridad para administrar los permisos de los usuarios e inicios de sesión.

Esta información general describe estos aspectos de Almacenamiento de datos SQL.

* Herramientas de administración
* Escalado de proceso
* Pausa y reanudación
* Prácticas recomendadas de rendimiento
* Supervisión de consulta
* Seguridad
* Copia de seguridad y restauración

## <a name="management-tools"></a>Herramientas de administración
Puede usar una variedad de herramientas para administrar bases de datos en Almacenamiento de datos SQL. A medida que administra bases de datos, desarrollará las preferencias de la herramienta para cada tipo de tarea que necesite realizar.

### <a name="azure-portal"></a>Portal de Azure
[Azure Portal][Azure portal] es un portal basado en web donde se pueden crear, actualizar y eliminar bases de datos, así como supervisar recursos de bases de datos. Es una herramienta muy útil si se está empezando a trabajar con Azure, se administran pocas bases de datos de almacenamiento de datos o hay que hacer algo rápidamente.

Para empezar a usar Azure Portal, consulte [Creación de una instancia de SQL Data Warehouse (Azure Portal)][Create a SQL Data Warehouse (Azure portal)].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools en Visual Studio
[SQL Server Data Tools][SQL Server Data Tools] (SSDT) de Visual Studio le permite conectar, administrar y desarrollar las bases de datos. Si es un programador familiarizado con Visual Studio o con otros entornos de desarrollo integrado (IDE), pruebe a usar SSDT en Visual Studio.

SSDT incluye el Explorador de objetos de SQL Server, que permite visualizar, conectar y ejecutar scripts en bases de datos de Almacenamiento de datos SQL. Para conectarse rápidamente a Almacenamiento de datos SQL, simplemente haga clic en el botón **Abrir en Visual Studio** de la barra de comandos al visualizar los detalles de la base de datos en el Portal de Azure clásico.  

Para consultar una introducción a SSDT en Visual Studio, vea [Consultas en SQL Data Warehouse de Azure con Visual Studio][Query Azure SQL Data Warehouse with Visual Studio].

### <a name="command-line-tools"></a>Herramientas de línea de comandos
Las herramientas de línea de comandos son ideales para automatizar las cargas de trabajo.  PowerShell y sqlcmd son dos excelentes formas de automatizar los procesos.  Se recomienda el uso de estas herramientas para administrar un gran número de servidores lógicos e implementar cambios en los recursos en un entorno de producción, ya que las tareas necesarias se pueden realizar mediante scripts y automatizar.

### <a name="dynamic-management-views"></a>Vistas de administración dinámica
Las DMV son las herramientas esenciales de la administración de Almacenamiento de datos SQL. Casi toda la información que se presenta en el portal se basa en DMV. Para ver una lista de las DMV de SQL Data Warehouse, consulte [Vistas del sistema de SQL Data Warehouse][SQL Data Warehouse system views].

Para comenzar, consulte [Conexión y consultas con SQLCMD][Connect and query with sqlcmd] y [Creación de una base de datos (PowerShell)][Create a database (PowerShell)].

## <a name="scale-compute"></a>Escalado de proceso
En Almacenamiento de datos SQL, puede escalar horizontalmente o de nuevo de forma rápida el rendimiento mediante el aumento o disminución de los recursos de proceso de la CPU, la memoria y el ancho de banda de E/S. Para escalar el rendimiento, todo lo que necesita hacer es ajustar el número de unidades de almacenamiento de datos (DWU) que asigna Almacenamiento de datos SQL. Almacenamiento de datos SQL realiza el cambio y controla todos los cambios subyacentes al hardware o software rápidamente.

Para obtener más información sobre el escalado de DWU, consulte [Scale performance] (Escalado del rendimiento).

## <a name="pause-and-resume"></a>Pausa y reanudación
Para ahorrar costos, puede pausar y reanudar recursos de proceso a petición. Por ejemplo, si no va a usar la base de datos durante la noche y los fines de semana, puede pausarla durante esas horas y reanudarla durante el día. No se le cobrará por DWU mientras la base de datos se encuentre en pausa.

Para obtener más información, consulte [Pausa del proceso][Pause compute] y [Reanudación del proceso][Resume compute].

## <a name="performance-best-practices"></a>Prácticas recomendadas de rendimiento
Cuando comience a trabajar con una nueva tecnología, descubrir las sugerencias y trucos que funcionan mejor directamente desde el inicio puede ahorrarle mucho tiempo.  Encontrará procedimientos recomendados a lo largo de muchos de nuestros temas.

Para ver un resumen de las consideraciones más importantes al desarrollar la carga de trabajo, consulte [Prácticas recomendadas de SQL Data Warehouse][SQL Data Warehouse Best Practices].

## <a name="query-monitoring"></a>Supervisión de consulta
A veces se ejecuta una consulta durante demasiado tiempo, pero no está seguro de cuál es la culpable. Almacenamiento de datos de SQL tiene vistas de administración dinámica (DMV) que puede utilizar para averiguar qué consulta está tardando demasiado.

Para buscar las consultas de larga ejecución, consulte [Supervisión de la carga de trabajo con DMV][Monitor your workload using DMVs].

## <a name="security"></a>Seguridad
Para mantener un sistema seguro, debe estar en la alerta y protegerse contra cualquier tipo de acceso no autorizado. Un sistema de seguridad debe asegurarse de que las reglas de firewall están en su lugar para que solo puedan conectarse las direcciones IP autorizadas. Necesita la autenticación correcta de las credenciales de usuario. Después de que un usuario se conecte a la base de datos, el usuario solo debe tener permisos para realizar un número mínimo de acciones. Para proteger los datos, puede usar el cifrado. También es importante realizar una auditoría y un seguimiento para poder volver a realizar un seguimiento de los eventos si existe una actividad sospechosa.

Para obtener más información sobre cómo administrar la seguridad, consulte [Introducción a la seguridad][Security overview].

## <a name="backup-and-restore"></a>Copia de seguridad y restauración
Una parte esencial de cualquier base de datos de producción es contar con copias de seguridad confiables de los datos. Almacenamiento de datos SQL mantiene los datos seguros al realizar automáticamente una copia de seguridad de las bases de datos activas a intervalos regulares. Estas copias de seguridad le permiten recuperarse de situaciones en las que los datos han quedado dañados o los datos o la base de datos se han eliminado por accidente.  Para obtener información sobre la programación de copias de seguridad de datos, la directiva de retención y la restauración de bases de datos, consulte [Restauración desde una instantánea][Restore from snapshot].

## <a name="next-steps"></a>Pasos siguientes
Unos buenos principios de diseño de base de datos le facilitarán la administración de las bases de datos en Almacenamiento de datos SQL. Para obtener más información, consulte [Información general sobre desarrollo][Development overview].

<!--Image references-->

<!--Article references-->
[Create a SQL Data Warehouse (Azure Portal)]: sql-data-warehouse-get-started-provision.md
[Create a database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell.md
[connection]: sql-data-warehouse-develop-connections.md
[Query Azure SQL Data Warehouse with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connect and query with sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Development overview]: sql-data-warehouse-overview-develop.md
[Monitor your workload using DMVs]: sql-data-warehouse-manage-monitor.md
[Pause compute]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Restore from snapshot]: sql-data-warehouse-restore-database-overview.md
[Resume compute]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale performance]: sql-data-warehouse-manage-compute-overview.md#scale-compute
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SQL Data Warehouse Best Practices]: sql-data-warehouse-best-practices.md
[SQL Data Warehouse system views]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Azure portal]: http://portal.azure.com/
