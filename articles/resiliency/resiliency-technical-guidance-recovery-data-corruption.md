<properties
   pageTitle="Guía técnica sobre resistencia para la recuperación ante datos dañados o eliminación accidental | Microsoft Azure"
   description="Artículo para entender cómo recuperarse ante datos dañados o eliminación accidental de datos y para diseñar aplicaciones resistentes, con alta disponibilidad y con tolerancia a errores, así como para planear la recuperación ante desastres"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Guía técnica sobre resistencia en Azure: recuperación ante datos dañados o eliminación accidental

Una parte importante de un buen plan de continuidad empresarial consiste en tener un plan para recuperar los datos si estos se dañan o eliminan accidentalmente. A continuación se muestra información acerca de la recuperación ante datos dañados o eliminados accidentalmente debido a errores de la aplicación o a errores del operador.

##Máquinas virtuales
Para proteger las Máquinas virtuales de Azure, a veces denominadas máquinas virtuales de infraestructura como servicio (IaaS), contra errores de la aplicación o la eliminación accidental use [Copia de seguridad de Azure](https://azure.microsoft.com/services/backup/). Copia de seguridad de Azure permite la creación de copias de seguridad coherentes en varios discos de máquinas virtuales. Además, el almacén de copia de seguridad se puede replicar entre varias regiones para proporcionar recuperación en caso de pérdida de la región.

##Almacenamiento
Tenga en cuenta que aunque Almacenamiento de Azure ofrece resistencia de datos mediante réplicas automatizadas, esto no evita que se puedan dañar los datos del código de la aplicación (ni el de los desarrolladores y usuarios) a causa de una eliminación o actualización accidental o no intencionada. Mantener la fidelidad de los datos en caso de error de la aplicación o del usuario requiere técnicas más avanzadas, como copiar los datos en una ubicación de almacenamiento secundaria con un registro de auditoría. Los desarrolladores pueden aprovechar la [funcionalidad de instantánea](https://msdn.microsoft.com/library/azure/ee691971.aspx) de blobs que permite crear instantáneas de solo lectura de un estado anterior del contenido del blob. Esta funcionalidad puede utilizarse como base de una solución de fidelidad de datos para los blobs de Almacenamiento de Azure.

###Copia de seguridad de blobs y de Almacenamiento de tablas
Aunque el contenido de los blobs y las tablas es muy duradero, siempre representan el estado actual de los datos. La recuperación ante una modificación o eliminación no deseada puede necesitar la restauración de datos a un estado anterior. Esto se puede conseguir aprovechando las funcionalidades proporcionadas por Azure para almacenar y mantener copias de un estado anterior.

Para Blobs de Azure, puede realizar copias de seguridad de un estado anterior mediante la [característica de instantáneas de blob](https://msdn.microsoft.com/library/ee691971.aspx). Por cada instantánea, solo se le cobrará por el almacenamiento que necesite para almacenar las diferencias producidas en el blob desde el último estado de instantánea. Las instantáneas dependen de la existencia del blob original en el que se basan, por lo que aconsejable una operación de copia a otro blob o incluso a otra cuenta de almacenamiento, para asegurarse de que los datos de la copia de seguridad estén protegidos correctamente contra la eliminación accidental. Para las tablas de Azure, puede realizar copias de un estado anterior en una tabla diferente o en los Blobs de Azure. Puede consultar una guía más detallada y ejemplos de cómo realizar copias de seguridad de tablas y blobs en el nivel de aplicación, aquí:

  * [Protecting Your Tables Against Application Errors](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/05/03/protecting-your-tables-against-application-errors/) (Protección de las tablas frente a errores de la aplicación)
  * [Protecting Your Blobs Against Application Errors](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/04/29/protecting-your-blobs-against-application-errors/) (Protección de los blobs frente a errores de la aplicación)

##Base de datos
Hay varias opciones de [continuidad empresarial](../sql-database/sql-database-business-continuity.md) (copia de seguridad, restauración) disponibles para la Base de datos SQL de Azure. Se pueden copiar bases de datos con la funcionalidad de [copia de base de datos](../sql-database/sql-database-copy.md) o mediante la [exportación](../sql-database/sql-database-export.md) e [importación](https://msdn.microsoft.com/library/hh710052.aspx) de un archivo bacpac de SQL Server. Esta funcionalidad proporciona resultados transaccionales coherentes, cosa que no sucede en el caso de un archivo bacpac (a través del servicio de importación y exportación). Ambas opciones se ejecutan como servicios basados en una cola dentro del centro de datos y no proporcionan actualmente un Acuerdo de Nivel de Servicio con tiempo de finalización.

>[AZURE.NOTE]Las opciones de copia de base de datos y las de importación y exportación suponen un nivel significativo de carga en la base de datos de origen que puede desencadenar eventos de contención o limitación de recursos.

###Copia de seguridad de Base de datos SQL
Las copias de seguridad de un estado anterior de la Base de datos SQL de Microsoft Azure se consiguen mediante [la copia de la Base de datos SQL de Azure](../sql-database/sql-database-copy.md). Puede utilizar este comando para crear una copia transaccionalmente coherente de una base de datos en el mismo servidor de bases de datos lógicas o en uno diferente. En cualquier caso, la copia de la base de datos es totalmente funcional y completamente independiente de la base de datos de origen. Cada copia que cree representa una opción de recuperación de un estado anterior. Puede recuperar completamente el estado de la base de datos cambiando el nombre de la nueva base de datos con el nombre de la base de datos de origen. De forma alternativa, puede recuperar un subconjunto específico de datos de la nueva base de datos mediante consultas de Transact-SQL. Para más información acerca de Base de datos SQL, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](../sql-database/sql-database-business-continuity.md).

###SQL Server en la copia de seguridad de Máquinas virtuales
En los casos en que SQL Server se utiliza con máquinas virtuales de infraestructura como servicio de Azure (a menudo denominadas IaaS o máquinas virtuales de IaaS), hay dos opciones: las copias de seguridad tradicionales y el trasvase de registros. El uso de copias de seguridad tradicionales le permite restaurar a un estado anterior específico, pero el proceso de recuperación es lento. La restauración de copias de seguridad tradicionales requiere empezar con una copia de seguridad completa inicial y, a continuación, aplicar las copias de seguridad realizadas después de ella. La segunda opción es configurar una sesión de trasvase de registros para retrasar la restauración de las copias de seguridad del registro (por ejemplo, en dos horas). Esto proporciona un tiempo para recuperarse de los errores que se produzcan en el servidor principal.

##Otros servicios de la plataforma de Azure
Algunos servicios de la plataforma de Azure almacenan información en una cuenta de almacenamiento controlada por el usuario o en la Base de datos SQL de Azure. Si el recurso de almacenamiento o la cuenta se eliminan o se dañan, esto podría provocar errores graves con el servicio. En estos casos, es importante mantener copias de seguridad que le permitirán volver a crear los recursos si estos se han eliminado o dañado.

Para los Sitios web y los Servicios móviles de Azure, debe realizar la copia de seguridad y mantener las bases de datos asociadas. Para los servicios multimedia y las Máquinas virtuales de Azure, debe mantener la cuenta de Almacenamiento de Azure asociada y todos los recursos de esa cuenta. Por ejemplo, para Máquinas virtuales debe realizar la copia de seguridad y administrar los discos de las máquinas virtuales en el Almacenamiento de blobs de Azure.

##Listas de comprobación para datos dañados o eliminación accidental

##Lista de comprobación de máquinas virtuales
  1. Revise la sección [Máquinas virtuales](#virtual-machines) de este documento.
  2. Realice una copia de seguridad y mantenga los discos de las máquinas virtuales mediante Copia de seguridad de Azure (o a través de su propio sistema de copias de seguridad mediante el Almacenamiento de blobs de Azure y las instantáneas del disco duro virtual)

##Lista de comprobación de almacenamiento
  1. Revise la sección [Almacenamiento](#storage) de este documento.
  2. Realice copias de seguridad de los recursos de almacenamiento más importantes de forma regular.
  3. Considere el uso de la característica de instantáneas de blobs

##Lista de comprobación de la base de datos
  1. Revise la sección [Base de datos](#database) de este documento.
  2. Cree copias de seguridad de un estado anterior mediante el comando Database Copy (copia de base de datos).

##Lista de comprobación de SQL Server en la copia de seguridad de Máquinas virtuales
  1. Revise la sección [SQL Server en la copia de seguridad de Máquinas virtuales](#sql-server-on-virtual-machines-backup) de este documento.
  2. Use las técnicas tradicionales de copia de seguridad y restauración
  3. Cree una sesión de trasvase de registros con retraso

##Lista de comprobación de aplicaciones web
  1. Realice una copia de seguridad y mantenga la base de datos asociada, si hay alguna

##Lista de comprobación de los Servicios multimedia
  1. Realice una copia de seguridad y mantenga los recursos de almacenamiento asociados

##Más información
Consulte [Escenarios de almacenamiento, copia de seguridad y recuperación](https://azure.microsoft.com/documentation/scenarios/storage-backup-recovery/) para más información acerca de las características de Copia de seguridad y restauración de Azure.

##Pasos siguientes
Este artículo forma parte de una serie que se centra en la [Guía técnica sobre resistencia en Azure](./resiliency-technical-guidance.md). Si está buscando más recursos de resistencia, recuperación ante desastres y alta disponibilidad, consulte la guía técnica sobre resistencia en Azure en la sección de [recursos adicionales](./resiliency-technical-guidance.md#additional-resources).

<!---HONumber=AcomDC_0525_2016-->