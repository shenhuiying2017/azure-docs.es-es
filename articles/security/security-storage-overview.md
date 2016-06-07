<properties
   pageTitle="Información general sobre seguridad de Almacenamiento de Azure | Microsoft Azure"
   description="Almacenamiento de Azure es la solución de almacenamiento en la nube para las aplicaciones modernas que dependen de la durabilidad, la disponibilidad y la escalabilidad para satisfacer las necesidades de sus clientes. Este artículo ofrece una visión general de las principales características de seguridad de Azure que se pueden usar con Almacenamiento de Azure."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="terrylan"/>

# Información general sobre seguridad de Almacenamiento de Azure

Almacenamiento de Azure es la solución de almacenamiento en la nube para las aplicaciones modernas que dependen de la durabilidad, la disponibilidad y la escalabilidad para satisfacer las necesidades de sus clientes. Almacenamiento de Azure proporciona un conjunto completo de funcionalidades de seguridad:

- La cuenta de almacenamiento se puede proteger mediante el control de acceso basado en rol y Azure Active Directory.
- Los datos se pueden proteger en tránsito entre una aplicación y Azure usando cifrado de cliente, HTTPS o SMB 3.0.
- Se puede establecer una configuración para que los datos se cifren automáticamente cuando se escriben en Almacenamiento de Azure mediante el Cifrado del servicio de Almacenamiento.
- Se puede establecer el cifrado de los discos de datos y del sistema operativo utilizados por máquinas virtuales mediante el Cifrado de discos de Azure.
- Se puede conceder acceso delegado a los objetos de datos de Almacenamiento de Azure mediante las Firmas de acceso compartido.

Este artículo ofrece una visión general de cada una de estas características de seguridad principales que se pueden usar con Almacenamiento de Azure. Además, se incluyen vínculos a artículos que ofrecen detalles de cada característica para que pueda tener más información al respecto.

Si desea obtener más información sobre la seguridad en Almacenamiento de Azure, consulte la [Guía de seguridad de Almacenamiento de Azure](https://azure.microsoft.com/documentation/articles../storage/storage-security-guide.md).

Estas son las características principales que se tratan en este artículo:

- Control de acceso basado en roles
- Administración de las claves de la cuenta de almacenamiento
- Acceso delegado a objetos de almacenamiento
- Cifrado en tránsito
- Cifrado en reposo/Cifrado del servicio de Almacenamiento
- Análisis de almacenamiento

## Control de acceso basado en rol (RBAC)

Puede proteger su cuenta de almacenamiento con el control de acceso basado en rol (RBAC). Cada suscripción de Azure está asociada a un inquilino de Azure Active Directory (AD). Los usuarios, grupos y aplicaciones de ese directorio pueden administrar los recursos en la suscripción de Azure. Estos derechos de acceso se conceden al asignar el rol RBAC adecuado a usuarios, grupos y aplicaciones de un determinado ámbito.

Puede usar el control de acceso basado en rol para conceder acceso a las operaciones de administración de su cuenta de Almacenamiento de Azure. Con el control de acceso basado en rol, controla quién puede:

- Administrar esa cuenta de almacenamiento.
- Leer las claves de la cuenta de almacenamiento y usarlas para acceder a blobs, colas, tablas y archivos.
- Regenerar las claves de almacenamiento.

Más información:

- [Control de acceso basado en roles de Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## Administración de las claves de la cuenta de almacenamiento

Al crear una cuenta de almacenamiento, Azure genera dos claves de acceso de almacenamiento de 512 bits que se usan para autenticación cuando se obtiene acceso a la cuenta de almacenamiento. Al brindar dos claves de acceso de almacenamiento, Azure le permite volver a generar las claves sin interrupción en su servicio de almacenamiento, o bien, tener acceso a ese servicio. Puede mantener conexiones con la cuenta de almacenamiento usando una clave de acceso mientras genera de nuevo la otra clave de acceso.

Las claves de la cuenta de almacenamiento, junto con el nombre de la cuenta de almacenamiento, pueden utilizarse para tener acceso a los objetos de datos almacenados en la cuenta de almacenamiento; por ejemplo, blobs, entidades dentro de una tabla, mensajes de una cola y archivos en un recurso compartido de archivos de Azure. Con el control de acceso basado en rol, controla el acceso a las claves de la cuenta de almacenamiento que controlan el acceso a los propios objetos de datos.

Más información:

- [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md)

## Acceso delegado a objetos de almacenamiento

Una firma de acceso compartido (SAS) es una cadena que contiene un token de seguridad que puede asociarse a un identificador URI que le permite delegar el acceso a objetos de almacenamiento y especificar restricciones como permisos o un intervalo de fecha y hora para el acceso.

Si comparte la clave de su cuenta de almacenamiento es como si prestara a alguien las llaves de su reino de almacenamiento. Con ella, es posible acceder a todo el contenido. Con una firma de acceso compartido, es posible conceder a un cliente únicamente los permisos que necesite durante un período de tiempo limitado. Puede:

- Conceder acceso a blobs, contenedores, mensajes de colas, archivos y tablas. En el caso de las tablas, realmente puede conceder permiso para tener acceso a un intervalo de entidades en la tabla especificando los intervalos de clave de fila y partición a los que desea que el usuario tenga acceso.
- Especificar que las solicitudes realizadas mediante una firma de acceso compartido se limiten a una cierta dirección IP o un intervalo de direcciones IP externas a Azure.
- Requerir que las solicitudes se realicen mediante un protocolo específico (HTTPS o HTTP/HTTPS). Esto significa que si solo desea permitir el tráfico HTTPS, puede establecer que el protocolo requerido sea solo HTTPS y se bloqueará el tráfico HTTP.

Más información:

- [Firmas de acceso compartido, Parte 1: Descripción del modelo SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)

## Cifrado en tránsito
Cifrado en tránsito es un mecanismo para proteger datos cuando se transmiten a través de redes. Con Almacenamiento de Azure, puede proteger los datos mediante:

- Cifrado de nivel de transporte, como HTTPS para transferir datos a Almacenamiento de Azure o desde este servicio.
- Cifrado de conexión, como el cifrado SMB 3.0 para recursos compartidos de archivos de Azure.
- Cifrado de cliente, para cifrar los datos antes de transferirlos al almacenamiento y descifrarlos una vez transferidos desde este servicio.

### Cifrado de nivel de transporte

Para garantizar la seguridad de los datos de Almacenamiento de Azure, puede cifrar los datos entre el cliente y Almacenamiento de Azure. Puede [habilitar HTTPS](../app-service-web/web-sites-configure-ssl-certificate.md) al llamar a las API de REST o al acceder a objetos del almacenamiento. Además, puede usar las [firmas de acceso compartido](../storage/storage-dotnet-shared-access-signature-part-1.md) (SAS), que se pueden emplear para delegar el acceso a objetos de Almacenamiento de Azure. Estas incluyen un parámetro opcional ("protocolo") que puede usar para especificar que solo pueda emplearse el protocolo HTTPS al utilizar firmas de acceso compartido. De este modo, se garantiza cualquier persona que envíe vínculos con tokens de SAS use el protocolo adecuado.

### Cifrado de conexión para el acceso a recursos compartidos de archivos

SMB 3.0 admite el cifrado, y se puede utilizar con Windows Server 2012 R2, Windows 8, Windows 8.1 y Windows 10, lo que permite el acceso entre regiones e incluso el acceso en el escritorio.

Los recursos compartidos de archivos de Azure se pueden usar con máquinas virtuales de Linux. El cliente SMB de Linux aún no admite el cifrado, por lo que solo se permite el acceso dentro de una misma región de Azure. La compatibilidad de cifrado para Linux está en nuestra hoja de ruta. Cuando se agregue el cifrado, tendrá el mismo cifrado de nivel de red para el acceso a un recurso compartido de archivos de Azure tanto en Linux como en Windows.

Más información:

- [Uso del almacenamiento de archivos de Azure con Linux](../storage/storage-how-to-use-files-linux.md)
- [Introducción a Almacenamiento de archivos de Azure en Windows](../storage/storage-dotnet-how-to-use-files.md)
- [Almacenamiento de archivos dentro de Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)

### Cifrado de cliente

El cifrado de cliente le ayuda a garantizar que sus datos están protegidos mientras se transfieren entre una aplicación cliente y Almacenamiento de Azure. Los datos se cifran antes de ser transferidos a Almacenamiento de Azure. En el proceso de recuperación de los datos de Almacenamiento de Azure, estos se descifran una vez recibidos en el cliente. Aunque los datos se cifran al pasar por el cable, también puede usar HTTPS para ayudar a mitigar los errores de red que pueden afectar a la integridad de los datos.

Más información:

- [Cifrado del lado cliente para el Almacenamiento de Microsoft Azure (vista previa)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Cloud security controls series: Encrypting Data in Transit](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/) (Serie de controles de seguridad en la nube: cifrado de datos en tránsito)

## Cifrado en reposo

Hay tres características de Azure que proporcionan cifrado de datos "en reposo":

- Cifrado del servicio de almacenamiento
- Cifrado de cliente
- Cifrado de disco de Azure

### Cifrado del servicio de almacenamiento

El cifrado del servicio de almacenamiento permite solicitar que el servicio de almacenamiento cifre automáticamente los datos al escribirlos en Almacenamiento de Azure. Cuando se lean los datos desde el Almacenamiento de Azure, el servicio de almacenamiento los descifrará antes de devolverlos. De este modo, protege los datos sin tener que modificar el código o agregar código a las aplicaciones.

El [Cifrado del servicio Almacenamiento de Azure](https://azure.microsoft.com/services/storage/) está disponible para el [Almacenamiento de blobs de Azure](https://azure.microsoft.com/services/storage/blobs/). Para más información sobre otros tipos de almacenamiento de Azure, consulte [Archivos](https://azure.microsoft.com/services/storage/files/), [Almacenamiento Premium](https://azure.microsoft.com/services/storage/premium-storage/), [Tablas](https://azure.microsoft.com/services/storage/tables/) y [Colas](https://azure.microsoft.com/services/storage/queues/).

Más información:

- [Cifrado del servicio Almacenamiento de Azure para datos en reposo (versión preliminar)](../storage/storage-service-encryption.md)

### Cifrado de cliente

Hemos mencionado el Cifrado de cliente al hablar sobre el cifrado en tránsito. Esta característica le permite cifrar mediante programación los datos en una aplicación cliente antes de enviarlos a través del cable para que se escriban en el Almacenamiento de Azure, y descifrar mediante programación los datos después de recuperarlos de Almacenamiento de Azure.

Proporciona cifrado en tránsito, pero además incluye la característica de cifrado en reposo. Tenga en cuenta que aunque los datos se cifran en tránsito, puede usar HTTPS para aprovechar las comprobaciones incorporadas de integridad de los datos que ayudan a mitigar los errores de red que pueden incidir en esta.

Más información:

- [Cifrado del lado de cliente y Almacén de claves de Azure para el Almacenamiento de Microsoft Azure](../storage/storage-client-side-encryption.md)

### Cifrado de disco de Azure

Cifrado de discos de Azure para máquinas virtuales (VM) le ayuda a solucionar los aspectos de seguridad y cumplimiento normativo de su organización, ya que cifra los discos de las máquinas virtuales (incluidos los discos de arranque y de datos) con claves y directivas que usted controla en el [Almacén de claves de Azure](https://azure.microsoft.com/services/key-vault/).

Cifrado de discos para máquinas virtuales funciona en sistemas operativos Linux y Windows. También utiliza el Almacén de claves para ayudarle a proteger, administrar y auditar el uso de las claves de cifrado del disco. Todos los datos de los discos de máquinas virtuales se cifran en reposo con tecnología de cifrado estándar del sector en sus cuentas de Almacenamiento de Azure. La solución Cifrado de discos para Windows se basa en la tecnología de [Cifrado de unidad BitLocker de Microsoft](https://technet.microsoft.com/library/cc732774.aspx), y la solución de Linux se basa en [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Más información:

- [Azure Disk Encryption for Windows and Linux Azure Virtual Machines (Cifrado de discos de Azure para máquinas virtuales IaaS de Linux y Windows)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## Análisis de almacenamiento

Para cada cuenta de almacenamiento, puede habilitar el Análisis de almacenamiento de Azure para realizar el registro y almacenar datos de métricas. Esta es una herramienta excelente para consultar métricas de rendimiento o solucionar problemas de rendimiento de una cuenta de almacenamiento.

Los registros de análisis de almacenamiento contienen también el método de autenticación utilizado por un usuario al acceder al almacenamiento. Por ejemplo, con el Almacenamiento de blobs, puede ver si los usuarios utilizan una Firma de acceso compartido o las claves de la cuenta de almacenamiento, o si el blob es de acceso público.

Más información:

- [Análisis de almacenamiento](../storage/storage-analytics.md)
- [Formato del registro del análisis de almacenamiento](https://msdn.microsoft.com/library/azure/hh343259.aspx)
- [Supervisión de una cuenta de almacenamiento en el Portal de Azure](../storage/storage-monitor-storage-account.md)
- [Solución integral de problemas con los registros y métricas de Almacenamiento de Azure, AzCopy y el analizador de mensajes](../storage/storage-e2e-troubleshooting.md)

<!---HONumber=AcomDC_0525_2016-->