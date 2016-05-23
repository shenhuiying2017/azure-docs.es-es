<properties
   pageTitle="Administración de bases de datos en Almacenamiento de datos SQL de Azure | Microsoft Azure"
   description="Información general de administración de bases de datos de Almacenamiento de datos SQL Incluye herramientas de administración, DWU y rendimiento de escalado horizontal, solución de problemas de rendimiento de las consultas, el establecimiento de directivas de seguridad y la restauración una base de datos de daños en los datos o de un apagón regional."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/04/2016"
   ms.author="barbkess;sonyama;"/>

# Administración de base datos en Almacenamiento de datos SQL de Azure

Almacenamiento de datos SQL automatiza muchos aspectos de la administración de las bases de datos. Por ejemplo, para escalar el rendimiento solo necesita ajustar y pagar por el nivel adecuado de recursos de proceso y, a continuación, dejar que Almacenamiento de datos SQL haga todo el trabajo de escalado horizontal y escalado de nuevo.

Sin duda deseará supervisar la carga de trabajo para identificar sus necesidades de rendimiento, así como solucionar problemas de consultas de larga ejecución. También debe realizar algunas tareas de seguridad para administrar los permisos de los usuarios e inicios de sesión.

Esta información general describe estos aspectos de Almacenamiento de datos SQL.

- Herramientas de administración
- Escalado de proceso
- Pausa y reanudación
- Prácticas recomendadas de rendimiento
- Supervisión de consulta
- Seguridad
- Copia de seguridad y restauración

## Herramientas de administración

Puede usar una variedad de herramientas para administrar bases de datos en Almacenamiento de datos SQL. A medida que administra bases de datos, desarrollará las preferencias de la herramienta para cada tipo de tarea que necesite realizar.

### Portal de Azure
El [Portal de Azure][] es un portal basado en web donde se pueden crear, actualizar y eliminar bases de datos, así como supervisar recursos de bases de datos. Es una herramienta muy útil si se está empezando a trabajar con Azure, se administran pocas bases de datos de almacenamiento de datos o hay que hacer algo rápidamente.

Para empezar a usar el Portal de Azure, consulte [Creación de Almacenamiento de datos SQL (Portal de Azure)][].

### SQL Server Data Tools en Visual Studio
[SQL Server Data Tools][] (SSDT) de Visual Studio le permite conectar, administrar y desarrollar las bases de datos. Si es un programador familiarizado con Visual Studio o con otros entornos de desarrollo integrado (IDE), pruebe a usar SSDT en Visual Studio.

SSDT incluye el Explorador de objetos de SQL Server, que permite visualizar, conectar y ejecutar scripts en bases de datos de Almacenamiento de datos SQL. Para conectarse rápidamente a Almacenamiento de datos SQL, simplemente haga clic en el botón **Abrir en Visual Studio** de la barra de comandos al visualizar los detalles de la base de datos en el Portal de Azure clásico.

Para obtener instrucciones sobre SSDT en Visual Studio, consulte [Conexión a Almacenamiento de datos SQL con Visual Studio][].

### Herramientas de línea de comandos
Las herramientas de línea de comandos son ideales para automatizar las cargas de trabajo. PowerShell y sqlcmd son dos excelentes formas de automatizar los procesos. Se recomienda el uso de estas herramientas para administrar un gran número de servidores lógicos e implementar cambios en los recursos en un entorno de producción, ya que las tareas necesarias se pueden realizar mediante scripts y automatizar.

### Vistas de administración dinámica 

Las DMV son las herramientas esenciales de la administración de Almacenamiento de datos SQL. Casi toda la información que se presenta en el portal se basa en DMV. Para ver una lista de las DMV de Almacenamiento de datos SQL, consulte [Vistas del sistema de Almacenamiento de datos SQL][].

Para comenzar, consulte [Conexión y consultas con SQLCMD][] y [Creación de una base de datos (PowerShell)][].

## Escalado de proceso

En Almacenamiento de datos SQL, puede escalar horizontalmente o de nuevo de forma rápida el rendimiento mediante el aumento o disminución de los recursos de proceso de la CPU, la memoria y el ancho de banda de E/S. Para escalar el rendimiento, todo lo que necesita hacer es ajustar el número de unidades de almacenamiento de datos (DWU) que asigna Almacenamiento de datos SQL. Almacenamiento de datos SQL realiza el cambio y controla todos los cambios subyacentes al hardware o software rápidamente.

Para obtener más información sobre el escalado de DWU, consulte [Scale performance][] (Escalado del rendimiento).

##  Pausa y reanudación

Para ahorrar costos, puede pausar y reanudar recursos de proceso a petición. Por ejemplo, si no va a usar la base de datos durante la noche y los fines de semana, puede pausarla durante esas horas y reanudarla durante el día. No se le cobrará por DWU mientras la base de datos se encuentre en pausa.

Para obtener más información, consulte [Pausa del proceso][] y [Reanudación del proceso][].

## Prácticas recomendadas de rendimiento

Cuando comience a trabajar con una nueva tecnología, descubrir las sugerencias y trucos que funcionan mejor directamente desde el inicio puede ahorrarle mucho tiempo. Encontrará prácticas recomendadas a lo largo de muchos de nuestros temas.

Para ver un resumen de las consideraciones más importantes al desarrollar la carga de trabajo, consulte [Prácticas recomendadas de Almacenamiento de datos SQL][].

## Supervisión de consulta

A veces se ejecuta una consulta durante demasiado tiempo, pero no está seguro de cuál es la culpable. Almacenamiento de datos de SQL tiene vistas de administración dinámica (DMV) que puede utilizar para averiguar qué consulta está tardando demasiado.

Para buscar las consultas de larga ejecución, consulte [Supervisión de la carga de trabajo con DMV][].

## Seguridad

Para mantener un sistema seguro, debe estar en la alerta y protegerse contra cualquier tipo de acceso no autorizado. Un sistema de seguridad debe asegurarse de que las reglas de firewall están en su lugar para que solo puedan conectarse las direcciones IP autorizadas. Necesita la autenticación correcta de las credenciales de usuario. Después de que un usuario se conecte a la base de datos, el usuario solo debe tener permisos para realizar un número mínimo de acciones. Para proteger los datos, puede usar el cifrado. También es importante realizar una auditoría y un seguimiento para poder volver a realizar un seguimiento de los eventos si existe una actividad sospechosa.

Para obtener más información sobre cómo administrar la seguridad, diríjase a [Introducción a la seguridad][].

## Copia de seguridad y restauración

Hay dos maneras de recuperar una base de datos. Si tiene algunos datos dañados en la base de datos o se produjo un error, puede restaurar una instantánea de base de datos. Si hay un apagón regional o un desastre que provoca que una de las regiones no esté disponible, puede volver a crear la base de datos en otra región.

Almacenamiento de datos SQL realiza automáticamente una base de datos en intervalos regulares. Para la programación de la copia de seguridad de datos y la directiva de retención, consulte [Alta confiabilidad][].

### Almacenamiento con redundancia geográfica

Puesto que Almacenamiento de datos SQL separa el proceso y el almacenamiento, todos los datos se escriben directamente en el Almacenamiento de Azure con redundancia geográfica (RA-GRS). El almacenamiento con redundancia geográfica replica sus datos a una región secundaria que se encuentra a cientos de kilómetros de distancia de la región principal. Los datos se replican tres veces en cada región primaria y secundaria, en dominios de error y en dominios de actualización independientes. Esto garantiza que los datos perduran incluso en el caso de un apagón regional completo o un desastre que haga que una de las regiones deje de estar disponible. Para obtener más información sobre almacenamiento con redundancia geográfica con acceso de lectura, lea [Opciones de redundancia de Almacenamiento de Azure][].

### Restauración de base de datos

La restauración de base de datos está diseñada para restablecer la base de datos a un punto anterior en el tiempo. El servicio Almacenamiento de datos SQL protege todas las bases de datos con instantáneas de almacenamiento automáticas cada 8 horas como mínimo y las conserva durante 7 días para ofrecerle un conjunto discreto de puntos de restauración. Estas copias de seguridad se almacenan en el Almacenamiento de Azure RA-GRS y, por tanto, tienen redundancia geográfica de forma predeterminada. Las características de copia de seguridad y restauración automáticas se incluyen sin cargos adicionales y permiten proteger las bases de datos de daños o eliminaciones accidentales sin costos ni esfuerzos de administración.

Para obtener más información sobre la restauración de bases de datos, diríjase a [Recuperación desde instantánea][].

### Restauración geográfica

La restauración geográfica se ha diseñado para recuperar la base de datos en caso de que deje de estar disponible por una interrupción. Póngase en contacto con el soporte técnico para restaurar una base de datos desde una copia de seguridad con redundancia geográfica para crear una nueva base de datos en cualquier región de Azure. Dado que la copia de seguridad tiene redundancia geográfica, puede usarse para recuperar una base de datos aunque la base de datos sea inaccesible debido a una interrupción. La característica de restauración geográfica no incluye ningún cargo adicional.

Para utilizar la restauración geográfica, diríjase a [Restauración geográfica desde instantánea][].

## Pasos siguientes
Unos buenos principios de diseño de base de datos le facilitarán la administración de las bases de datos en Almacenamiento de datos SQL. Para obtener más información, diríjase a la [Información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[Opciones de redundancia de Almacenamiento de Azure]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Creación de Almacenamiento de datos SQL (Portal de Azure)]: sql-data-warehouse-get-started-provision.md
[Creación de una base de datos (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Conexión a Almacenamiento de datos SQL con Visual Studio]: sql-data-warehouse-get-started-connect.md
[Conexión y consultas con SQLCMD]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Información general sobre desarrollo]: sql-data-warehouse-overview-development.md
[Restauración geográfica desde instantánea]: sql-data-warehouse-backup-and-geo-restore-from-snapshot.md
[Alta confiabilidad]: sql-data-warehouse-overview-expectations.md#high-reliability
[Supervisión de la carga de trabajo con DMV]: sql-data-warehouse-manage-monitor.md
[Pausa del proceso]: sql-data-warehouse-overview-scalability.md#pause-compute-bk
[Recuperación desde instantánea]: sql-data-warehouse-backup-and-restore-from-snapshot.md
[Reanudación del proceso]: sql-data-warehouse-overview-scalability.md#resume-compute-performance-bk
[Scale performance]: sql-data-warehouse-overview-scalability.md#scale-performance-bk
[Introducción a la seguridad]: sql-data-warehouse-overview-security.md
[Prácticas recomendadas de Almacenamiento de datos SQL]: sql-data-warehouse-best-practices.md
[Vistas del sistema de Almacenamiento de datos SQL]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portal de Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0511_2016-->