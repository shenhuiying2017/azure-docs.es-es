---
title: "Introducción al cifrado de Azure | Microsoft Docs"
description: "Obtenga información acerca de las distintas opciones de cifrado en Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/18/2017
ms.author: barclayn
ms.openlocfilehash: 752568747ab96bc0a9c7fc5f24ff28c3bce4e09f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-overview"></a>Información general del cifrado de Azure

Este artículo proporciona información general sobre cómo se usa el cifrado en Microsoft Azure. Trata las áreas principales de cifrado, incluidos el cifrado en reposo, el cifrado en tránsito y la administración de claves con Key Vault. Cada sección incluye vínculos para obtener información más detallada.

## <a name="encryption-of-data-at-rest"></a>Cifrado de datos en reposo

Los datos en reposo incluyen información que se encuentra en el almacenamiento persistente en un medio físico, en cualquier formato digital. Esto incluye archivos en medios ópticos o magnéticos, datos archivados y copias de seguridad de datos. Microsoft Azure ofrece una variedad de soluciones de almacenamiento de datos para satisfacer diferentes necesidades, incluidos el almacenamiento de tablas, blobs y archivos, así como el almacenamiento en disco. Microsoft también proporciona cifrado para proteger [Azure SQL Database](../sql-database/sql-database-technical-overview.md), [CosmosDB](../cosmos-db/introduction.md) y Azure Data Lake.

El cifrado de datos en reposo está disponible para los servicios a través de los modelos en la nube de software como servicio (SaaS), plataforma como servicio (PaaS) e infraestructura como servicio (IaaS) de Azure. Este documento resume y proporciona recursos para ayudarle a usar opciones de cifrado de Azure.

Para más información detallada sobre cómo se cifran los datos en reposo en Azure, vea el documento titulado [Cifrado en reposo de datos de Azure](azure-security-encryption-atrest.md).

## <a name="azure-encryption-models"></a>Modelos de cifrado de Azure

Azure admite distintos modelos de cifrado, incluido el cifrado de servidor con claves administradas por el servicio, mediante claves administradas por el cliente en Azure Key Vault o mediante las claves administradas por el cliente en el hardware controlado por el cliente. El cifrado de cliente permite administrar y almacenar claves de forma local o en otra ubicación segura.

### <a name="client-side-encryption"></a>cifrado de cliente

El cifrado de cliente se realiza fuera de Azure. El cifrado de cliente incluye:

- Datos cifrados por una aplicación que se está ejecutando en el centro de datos del cliente o por una aplicación de servicio
- Datos que ya están cifrados cuando Azure los recibe.

Con el cifrado de cliente, el proveedor de servicios en la nube no tiene acceso a las claves de cifrado y no puede descifrar estos datos. Mantenga un control completo de las claves.

### <a name="server-side-encryption"></a>Cifrado del servidor

Los tres modelos de cifrado del servidor ofrecen características de administración de claves diferentes, que se pueden elegir según sus requisitos.

- **Claves administradas del servicio** proporcionan una combinación de control y comodidad con una sobrecarga reducida

- Las **claves administradas por el cliente** le permiten controlar las claves, incluida la capacidad de llevar sus propias claves (BYOK) o de generar nuevas.

- Las **claves administradas del servicio en el hardware controlado por el cliente** le permiten administrar las claves en el repositorio propietario que se encuentra fuera del control de Microsoft. Esto se denomina Hospedar su propia clave (HYOK). Sin embargo, la configuración es compleja y la mayoría de los servicios de Azure no son compatibles con este modelo.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Las máquinas virtuales Windows y Linux pueden protegerse con [Azure Disk Encryption](azure-security-disk-encryption.md), que usa la tecnología de [BitLocker de Windows](https://technet.microsoft.com/library/cc766295(v=ws.10).aspx) y [DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux para proteger los discos del sistema operativo y los discos de datos con el cifrado de volumen completo.

Las claves de cifrado y secretos se protegen en la suscripción de [Azure Key Vault](../key-vault/key-vault-whatis.md). Puede realizar una copia de seguridad y una restauración de las VM cifradas que están cifradas con la configuración KEK con el servicio Azure Backup.

### <a name="azure-storage-service-encryption"></a>Cifrado del servicio Azure Storage

Los datos en reposo en Azure Storage (tanto de blobs como de archivos) pueden cifrarse en escenarios de cliente y servidor.

[Cifrado del servicio Azure Storage](../storage/storage-service-encryption.md) (SSE) puede cifrar automáticamente los datos antes de que se almacenen y los descifra automáticamente cuando los recupera, lo que hace que el proceso sea completamente transparente para los usuarios. Cifrado del servicio Storage usa el [cifrado AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, que es uno de los cifrados de bloques más sólidos disponibles, y controla el cifrado, el descifrado y la administración de claves de forma transparente.

### <a name="client-side-encryption-of-azure-blobs"></a>Cifrado de cliente de blobs de Azure

El cifrado de cliente de blobs de Azure puede realizarse de maneras diferentes.

Puede usar la biblioteca cliente de Azure Storage para el paquete NuGet de .NET para cifrar los datos dentro de las aplicaciones cliente antes de cargarlos en Azure Storage.

Para obtener más información acerca de la biblioteca cliente de Azure Storage para el paquete NuGet. de NET y descargarla, vea el documento titulado [Microsoft Azure Storage 8.3.0](https://www.nuget.org/packages/WindowsAzure.Storage).

Cuando se usa el cifrado de cliente con Azure Key Vault, los datos se cifran con una clave de cifrado de contenido (CEK) simétrica única generada por el SDK de cliente de Azure Storage. La CEK se cifra mediante una clave de cifrado de claves (KEK), que puede ser una clave simétrica o un par de claves asimétricas. Puede administrarla de forma local o almacenarla en Azure Key Vault. A continuación, se cargan los datos cifrados en el servicio Azure Storage.

Para obtener más información acerca del cifrado de cliente con Azure Key Vault e iniciar las instrucciones sobre procedimientos, vea el documento titulado [Tutorial: Cifrado y descifrado de blobs en Microsoft Azure Storage con Azure Key Vault](../storage/storage-encrypt-decrypt-blobs-key-vault.md).

Por último, también puede usar la biblioteca cliente de Azure Storage para Java para realizar el cifrado de cliente antes de cargar datos en Azure Storage y descifrar los datos cuando se descargan en el cliente. Esta biblioteca también admite la integración con [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de las claves de la cuenta de almacenamiento.

### <a name="encryption-of-data-at-rest-with-azure-sql-database"></a>Cifrado de datos en reposo con Azure SQL Database

[Azure SQL Database](../sql-database/sql-database-technical-overview.md) es un servicio de base de datos relacional de uso general de Microsoft Azure que admite estructuras como datos relacionales, JSON, espacial y XML. Azure SQL admite el cifrado de servidor a través de la característica Cifrado de datos transparente (TDE) y el cifrado de cliente a través de la característica Always Encrypted.

#### <a name="transparent-data-encryption"></a>Cifrado de datos transparente

[Cifrado de datos transparente TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) se utiliza para cifrar archivos de datos de [SQL Server](https://www.microsoft.com/sql-server/sql-server-2016), [Azure SQL Database](../sql-database/sql-database-technical-overview.md) y [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) en tiempo real, con una clave de cifrado de base de datos (DEK), que se almacena en el registro de arranque de base de datos para la disponibilidad durante la recuperación.

TDE protege los archivos de registro y datos con los algoritmos de cifrado AES y 3DES. El cifrado del archivo de base de datos se realiza en el nivel de página; las páginas en una base de datos cifrada se cifran antes de que se escriban en disco y se descifran cuando se leen en la memoria. TDE ahora está habilitado de forma predeterminada en las bases de datos SQL de Azure recién creadas.

#### <a name="always-encrypted"></a>Always Encrypted

La característica [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) en Azure SQL le permite cifrar los datos dentro de las aplicaciones cliente antes de almacenarlos en Azure SQL Database y le permite habilitar la delegación de la administración de la base de datos local a terceros y mantener la separación entre los que poseen y pueden ver los datos y aquellos que los administran, pero no deben tener acceso a ellos.

#### <a name="cellcolumn-level-encryption"></a>Cifrado de nivel de columna/celda

Azure SQL Database le permite aplicar el cifrado simétrico a una columna de datos mediante Transact-SQL. Esto se denomina [cifrado de nivel de columna o de cifrado de nivel de celda](https://docs.microsoft.com/sql/relational-databases/security/encryption/encrypt-a-column-of-data) (CLE), ya que se puede utilizar para cifrar las columnas concretas o incluso determinadas celdas de datos con distintas claves de cifrado. Esto proporciona una capacidad de cifrado más granular que TDE, que cifra los datos en páginas.

CLE tiene funciones integradas que puede usar para cifrar datos con claves simétricas o asimétricas, con la clave pública de un certificado o con una frase de contraseña con 3DES.

### <a name="cosmos-db-database-encryption"></a>Cifrado de base de datos Cosmos DB

[Azure Cosmos DB](../cosmos-db/database-encryption-at-rest.md) es la base de datos multimodelo de distribución global de Microsoft. Los datos de usuario almacenados en Cosmos DB en un almacenamiento no volátil (unidades de estado sólido) se cifran de forma predeterminada; no hay ningún control de activación o desactivación. El cifrado en reposo se implementa mediante una serie de tecnologías de seguridad, como sistemas seguros de almacenamiento de claves, redes cifradas y API criptográficas. Microsoft administra las claves de cifrado y se alternan según las directivas internas de Microsoft.

### <a name="at-rest-encryption-in-azure-data-lake"></a>Cifrado en reposo en Azure Data Lake Store

[Azure Data Lake](../data-lake-store/data-lake-store-encryption.md) es un repositorio para toda la empresa de todos los tipos de datos recopilados en un único lugar antes de cualquier definición formal de requisitos o esquema. Azure Data Lake Store admite el cifrado transparente "de forma predeterminada" de los datos en reposo, que se configura durante la creación de la cuenta. De forma predeterminada, Data Lake Store administra las claves en su nombre, pero tiene la opción de administrarlas usted mismo.

Se utilizan tres tipos de claves en el cifrado y descifrado de datos: la clave de cifrado maestra (MEK), la clave de cifrado de datos (DEK) y la clave de cifrado de bloque (BEK). La MEK se utiliza para cifrar la DEK, que se almacena en medios persistentes, y la BEK se deriva de la DEK y el bloque de datos. Si está administrando sus propias claves, puede alternar la MEK.

## <a name="encryption-of-data-in-transit"></a>Cifrado de datos en tránsito

Azure ofrece varios mecanismos para mantener la privacidad de los datos cuando se mueven de una ubicación a otra.

### <a name="tlsssl-encryption-in-azure"></a>Cifrado TLS/SSL en Azure

Microsoft usa el protocolo [Seguridad de la capa de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) para proteger los datos cuando se transmiten entre los servicios en la nube y los clientes. Los centros de datos de Microsoft negocian una conexión TLS con sistemas cliente que se conectan a servicios de Azure. TLS proporciona una autenticación sólida, privacidad de mensajes e integridad (lo que permite la detección de la manipulación, interceptación y falsificación de mensajes), interoperabilidad, flexibilidad de algoritmo y facilidad de implementación y uso.

[Confidencialidad directa total](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) protege las conexiones entre los sistemas cliente de los clientes y los servicios en la nube de Microsoft mediante claves únicas. Las conexiones también usan longitudes de clave de cifrado RSA de 2048 bits. Esta combinación hace difícil para un usuario interceptar y acceder a datos que están en tránsito.

### <a name="azure-storage-transactions"></a>Transacciones de Azure Storage

Si interactúa con Azure Storage a través de Azure Portal, todas las transacciones se realizan a través de HTTPS. También se puede usar la API de REST de Storage a través de HTTPS para interactuar con Azure Storage. Puede exigir el uso de HTTPS al llamar a las API de REST para acceder a objetos de cuentas de almacenamiento mediante la habilitación de la opción Se requiere transferencia segura para la cuenta de almacenamiento.

Las firmas de acceso compartido ([SAS](../storage/storage-dotnet-shared-access-signature-part-1.md)), que pueden utilizarse para delegar el acceso a objetos de Azure Storage, incluyen una opción para especificar que se pueda utilizar solo el protocolo HTTPS cuando se usen las firmas de acceso compartido. Esto garantiza que cualquier usuario que envíe vínculos con tokens SAS use el protocolo adecuado.

El [SMB 3.0](https://technet.microsoft.com/library/dn551363(v=ws.11).aspx#BKMK_SMBEncryption) utilizado para acceder a recursos compartidos de archivos de Azure admite cifrado y está disponible en Windows Server 2012 R2, Windows 8, Windows 8.1 y Windows 10, lo que permite el acceso entre regiones e incluso el acceso en el escritorio.

El cifrado de cliente cifra los datos antes de enviarlos a Azure Storage, por lo que se cifra a medida que pasa por la red.

### <a name="smb-encryption-over-azure-virtual-networks"></a>Cifrado de SMB a través de redes virtuales de Azure 

[SMB 3.0](https://support.microsoft.com/help/2709568/new-smb-3-0-features-in-the-windows-server-2012-file-server) en VM de Azure con Windows Server 2012 y versiones posteriores ofrece la posibilidad de hacer que las transferencias de datos sean seguras mediante el cifrado de datos en tránsito a través de redes virtuales de Azure, para protegerse contra la manipulación y los ataques de interceptación. Los administradores pueden habilitar el cifrado SMB para todo el servidor, o simplemente algunos recursos compartidos.

De forma predeterminada, una vez que se activa cifrado SMB para un recurso compartido o el servidor, se permite que solo los clientes SMB 3 tengan acceso a los recursos compartidos cifrados.

## <a name="in-transit-encryption-in-azure-virtual-machines"></a>Cifrado en tránsito en Azure Virtual Machines

Los datos en tránsito de destino, de origen y entre VM de Azure que ejecutan Windows se cifran de diversas formas, según la naturaleza de la conexión.

### <a name="rdp-sessions"></a>Sesiones RDP

Puede conectarse e iniciar sesión en una VM de Azure mediante el [Protocolo de escritorio remoto](https://msdn.microsoft.com/library/aa383015(v=vs.85).aspx) (RDP) desde un equipo cliente de Windows o desde un equipo Mac con un cliente RDP instalado. Los datos en tránsito a través de la red en las sesiones RDP se pueden proteger mediante TLS.

Puede usar el escritorio remoto para conectarse a una VM Linux en Azure.

### <a name="secure-access-to-linux-vms-with-ssh"></a>Acceso seguro a las VM de Linux con SSH

Puede usar [Secure Shell](../virtual-machines/linux/ssh-from-windows.md) (SSH) para conectarse a VM de Linux que se ejecutan en Azure para la administración remota. SSH es un protocolo de conexión cifrada que permite inicios de sesión seguros sobre conexiones no seguras. Es el protocolo de conexión predeterminado de las máquinas virtuales Linux hospedadas en Azure. Mediante el uso de claves de SSH para la autenticación, se elimina la necesidad de contraseñas para iniciar sesión. SSH utiliza un par de claves públicas/privadas (cifrado simétrico) para la autenticación.

## <a name="azure-vpn-encryption"></a>Cifrado de VPN de Azure

Puede conectarse a Azure a través de una red privada virtual que crea un túnel seguro para proteger la privacidad de los datos enviados a través de la red.

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

[Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md) puede usarse para enviar tráfico cifrado entre la red virtual y la ubicación local a través de una conexión pública o para enviar tráfico entre redes virtuales.

La VPN de sitio a sitio usa [IPsec](https://en.wikipedia.org/wiki/IPsec) para el cifrado de transporte. Azure VPN Gateway utiliza un conjunto de propuestas predeterminadas. Puede configurar Azure VPN Gateway para usar una directiva IPsec/IKE personalizada con determinados algoritmos criptográficos y ventajas claves, en lugar de conjuntos de directivas predeterminadas de Azure.

### <a name="point-to-site-vpn"></a>VPN de punto a sitio

Las VPN de punto a sitio permiten a los equipos cliente individuales acceder a una red virtual de Azure. El [Protocolo de túnel de sockets seguros](https://technet.microsoft.com/library/2007.06.cableguy.aspx) (SSTP) se utiliza para crear el túnel VPN y puede atravesar firewalls (el túnel aparece como una conexión HTTPS). Puede usar su propio CA raíz de PKI interna para la conectividad de punto a sitio.

Puede configurar una conexión de VPN de punto a sitio a una red virtual con Azure Portal con autenticación de certificados o PowerShell.

Para más información acerca de las conexiones VPN de punto a sitio para redes virtuales de Azure, vea: [Configuración de una conexión de punto a sitio a una red virtual mediante la autenticación de certificado: Azure Portal](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) y

[Configuración de una conexión de punto a sitio a una red virtual mediante la autenticación de certificado: PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="site-to-site-vpn"></a>VPN de sitio a sitio 

Se utiliza una conexión de puerta de enlace VPN de sitio a sitio para conectar su red local a una red virtual de Azure a través de un túnel VPN de IPsec/IKE (IKEv1 o IKEv2). Este tipo de conexión requiere un dispositivo VPN local que tenga una dirección IP pública asignada.

Puede configurar una conexión de VPN de sitio a sitio en una red virtual con Azure Portal, PowerShell o la Interfaz de la línea de comandos de Azure (CLI).

Lea esto para obtener más información:

[Creación de una conexión de sitio a sitio mediante Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

[Creación de una conexión de sitio a sitio](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

[Creación de una red virtual con una conexión VPN de sitio a sitio mediante la CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="in-transit-encryption-in-azure-data-lake"></a>Cifrado en tránsito en Azure Data Lake

Los datos en tránsito (también conocidos como datos en movimiento) también se cifran siempre en Data Lake Store. Además de que los datos se cifran antes de almacenarse en un medio persistente, también se protegen cuando están en tránsito mediante HTTPS. HTTPS es el único protocolo admitido para las interfaces de REST de Data Lake Store.

Para más información acerca del cifrado de datos en tránsito en Azure Data Lake, vea el documento titulado [Cifrado de datos en Azure Data Lake Store](../data-lake-store/data-lake-store-encryption.md).

## <a name="key-management-with-azure-key-vault"></a>Administración de claves con Azure Key Vault

Sin la protección y administración adecuadas de las claves, el cifrado queda inutilizable. Azure Key Vault es la solución recomendada de Microsoft para administrar y controlar el acceso a las claves de cifrado utilizadas por los servicios en la nube. Los permisos para acceder a las claves se pueden asignar a servicios o a usuarios a través de cuentas de Azure Active Directory.

Azure Key Vault libera a las empresas de la necesidad de configurar, aplicar revisiones y mantener los módulos de seguridad de hardware (HSM) y el software de administración de claves. Con Azure Key Vault, Microsoft nunca ve las claves y las aplicaciones no tienen acceso directo a ellas; usted mantiene el control. También puede importar o generar claves en HSM.

## <a name="next-steps"></a>Pasos siguientes

- [Información general de seguridad de Azure](security-get-started-overview.md)
- [Azure Network Security Overview (Información general sobre Azure Network Security)](security-network-overview.md)
- [Introducción a la seguridad de base de datos de Azure](azure-database-security-overview.md)
- [Información general de seguridad de Azure Virtual Machines](security-virtual-machines-overview.md)
- [Cifrado de datos en reposo](azure-security-encryption-atrest.md)
- [Procedimientos recomendados de seguridad de datos y cifrado](azure-security-data-encryption-best-practices.md)
