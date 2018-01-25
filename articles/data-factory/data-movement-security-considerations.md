---
title: Consideraciones de seguridad de Azure Data Factory | Microsoft Docs
description: "En este artículo se describe la infraestructura de seguridad básica que utilizan los servicios de movimiento de datos de Azure Data Factory para proteger los datos."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 8bd5ae2aac23b18aeb3ef44692f448b50b7e3d44
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>Azure Data Factory: consideraciones de seguridad para el movimiento de datos
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-data-movement-security-considerations.md)
> * [Versión 2: versión preliminar](data-movement-security-considerations.md)

En este artículo se describe la infraestructura de seguridad básica que utilizan los servicios de movimiento de datos en Azure Data Factory para proteger los datos. Los recursos de administración de Azure Data Factory están integrados en la infraestructura de seguridad de Azure y aplican todas las medidas de seguridad que ofrece Azure.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Azure Data Factory: consideraciones de seguridad para el movimiento de datos](v1/data-factory-data-movement-security-considerations.md).

En una solución de Data Factory, se crean una o varias [canalizaciones](concepts-pipelines-activities.md)de datos. Una canalización es una agrupación lógica de actividades que realizan una tarea. Estas canalizaciones residen en la región donde se creó la factoría de datos. 

Aunque Data Factory solo está disponible en las regiones del **Este de EE. UU.**, del **Este de EE. UU. 2** y de **Europa Occidental** (versión preliminar de la versión 2), el servicio de movimiento de datos está disponible [de forma global en varias regiones](concepts-integration-runtime.md#azure-ir). Si el servicio de movimiento de datos no se ha implementado todavía en esa región, el servicio de Data Factory garantiza que los datos no abandonan un área geográfica o región, a menos que se indique explícitamente al servicio que utilice una región alternativa. 

Azure Data Factory en sí no almacena datos, excepto las credenciales de servicio vinculadas de los almacenes de datos en la nube, que se cifran mediante certificados. Permite crear flujos de trabajo controlados por datos para orquestar el movimiento de los datos entre los [almacenes de datos admitidos](copy-activity-overview.md#supported-data-stores-and-formats) y organizar el procesamiento de los datos mediante [servicios de proceso](compute-linked-services.md) en otras regiones o en entornos locales. También le permite supervisar y administrar flujos de trabajo mediante SDK y Azure Monitor.

El movimiento de datos mediante Azure Data Factory tiene la **certificación**:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [CSA STAR](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Si está interesado en el cumplimiento de Azure y en cómo Azure protege su propia infraestructura, visite el [Centro de confianza de Microsoft](https://www.microsoft.com/TrustCenter/default.aspx). 

En este artículo, revisamos las consideraciones de seguridad en los dos escenarios de movimiento de datos siguientes: 

- **Escenario de nube**: en este escenario, el origen y el destino son públicamente accesibles a través de Internet. Por ejemplo, los servicios de almacenamiento administrado en la nube, como Azure Storage, Azure SQL Data Warehouse, Azure SQL Database, Azure Data Lake Store, Amazon S3, Amazon Redshift, los servicios de SaaS como Salesforce y los protocolos de web, como FTP y OData. [Aquí](copy-activity-overview.md#supported-data-stores-and-formats) encontrará una lista integral de los orígenes de datos admitidos.
- **Escenario híbrido**: en este escenario, el origen o el destino está detrás de un firewall o dentro de una red corporativa local, o el almacén de datos se encuentra en una red privada o virtual (normalmente, el origen) y no es accesible públicamente. Los servidores de base de datos hospedados en máquinas virtuales también se incluyen en este escenario.

## <a name="cloud-scenarios"></a>Escenarios de nube
###<a name="securing-data-store-credentials"></a>Protección de las credenciales del almacén de datos
- Almacenamiento de credenciales cifradas en el almacén administrado de Azure Data Factory.

   Azure Data Factory protege las credenciales del almacén de datos al **cifrarlos** con **certificados administrados por Microsoft**. Estos certificados se alternan cada **dos años** (lo cual incluye la renovación del certificado y la migración de las credenciales). Estas credenciales cifradas se almacenan de forma segura en una instancia de **Azure Storage administrada por los servicios de administración de Azure Data Factory**. Para más información acerca de la seguridad de Azure Storage, consulte [Introducción a la seguridad de Azure Storage](../security/security-storage-overview.md).
- Almacenamiento de credenciales en Azure Key Vault 

   Ahora puede elegir almacenar credenciales del almacén de datos en [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) y, a continuación, permitir que Azure Data Factory las recupere durante la ejecución de una actividad. Para obtener más información, consulte el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md).

   > [!NOTE]
   > Actualmente, solo [Dynamics Connector](connector-dynamics-crm-office-365.md) admite esta característica. 

### <a name="data-encryption-in-transit"></a>Cifrado de datos en tránsito
Si el almacén de datos en la nube es compatible con HTTPS o TLS, todas las transferencias de datos entre los servicios de movimiento de datos de Data Factory y un almacén de datos en la nube se realizan a través del canal seguro HTTPS o TLS.

> [!NOTE]
> Todas las conexiones a **Azure SQL Database** y **Azure SQL Data Warehouse** requieren cifrado (SSL/TLS) siempre que haya datos en tránsito hacia y desde la base de datos. Al crear una canalización con JSON, agregue la propiedad **encryption** y establézcala en **true** en la **cadena de conexión**. Para **Azure Storage**, puede usar **HTTPS** en la cadena de conexión.

### <a name="data-encryption-at-rest"></a>Cifrado de datos en reposo
Algunos almacenes de datos admiten el cifrado de datos en reposo. Se recomienda habilitar el mecanismo de cifrado de datos para estos almacenes. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
El Cifrado de datos transparente (TDE) de Azure SQL Data Warehouse ayuda a proteger frente a la amenaza de actividad malintencionada al realizar el cifrado y descifrado en tiempo real de los datos en reposo. Este comportamiento es transparente para el cliente. Para más información, consulte [Proteger una base de datos en SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database admite también el Cifrado de datos transparente (TDE), que ayuda a proteger frente a la amenaza de actividad malintencionada al realizar el cifrado y descifrado en tiempo real de los datos sin que haya que efectuar cambios en la aplicación. Este comportamiento es transparente para el cliente. Para más información, consulte [Transparent Data Encryption with Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) (Cifrado de datos transparente con Azure SQL Database). 

#### <a name="azure-data-lake-store"></a>Almacén de Azure Data Lake
Azure Data Lake Store también ofrece el cifrado de los datos que se almacenan en la cuenta. Cuando se habilita, Data Lake Store cifrará automáticamente los datos antes de la persistencia y los descifrará antes de la recuperación, por lo que resulta un proceso completamente transparente para el cliente que accede a los datos. Para más información, consulte [Seguridad en Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Azure Blob Storage y Azure Table Storage
Azure Blob Storage y Azure Table Storage admiten el cifrado del servicio Storage (SSE), que cifra automáticamente los datos antes de su persistencia en el almacenamiento y los descifra antes de su recuperación. Para más información, consulte [Cifrado del servicio Azure Storage para datos en reposo](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 admite el cifrado de cliente y servidor para datos en reposo. Para más información, consulte [Protección de datos del cliente mediante cifrado](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Actualmente, Data Factory no admite Amazon S3 en Virtual Private Cloud (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift admite cifrado de clúster para datos en reposo. Para más información, consulte [Amazon Redshift Database Encryption](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html) (Cifrado de base de datos de Amazon Redshift). Actualmente, Data Factory no admite Amazon Redshift en VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce admite Shield Platform Encryption, que permite el cifrado de todos los archivos, datos adjuntos y campos personalizados. Para más información, consulte [Understanding the Web Server OAuth Authentication Flow](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm) (Descripción del flujo de autenticación OAuth de servidor web).  

## <a name="hybrid-scenarios-using-self-hosted-integration-runtime"></a>Escenarios híbridos (uso de Integration Runtime autohospedado)
Los escenarios híbridos necesitan que Integration Runtime autohospedado se instale en una red local o en una virtual (Azure), o bien en una nube privada virtual (Amazon). Integration Runtime autohospedado debe poder tener acceso a los almacenes de datos locales. Para obtener más información acerca de Integration Runtime autohospedado, consulte el artículo sobre el [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md). 

![canales de Integration Runtime autohospedado](media/data-movement-security-considerations/data-management-gateway-channels.png)

El **canal del comandos** permite la comunicación entre los servicios de movimiento de datos de Data Factory e Integration Runtime autohospedado. La comunicación contiene información relacionada con la actividad. El canal de datos se usa para transferir datos entre los almacenes de datos locales y los almacenes de datos en la nube.    

### <a name="on-premises-data-store-credentials"></a>Credenciales de los almacenes de datos locales
Las credenciales de los almacenes de datos locales se almacenan y se cifran siempre. Pueden almacenarse localmente en el equipo de Integration Runtime autohospedado o en el almacenamiento administrado de Azure Data Factory (al igual que las credenciales del almacén en la nube). 

1. Puede elegir **almacenar credenciales localmente**. Si desea cifrar y almacenar las credenciales localmente en Integration Runtime autohospedado, siga los pasos descritos en [cifrado de credenciales en Integration Runtime autohospedado](encrypt-credentials-self-hosted-integration-runtime.md). Todos los conectores admiten esta opción. Integration Runtime autohospedado utiliza Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) para cifrar los datos confidenciales o la información de credenciales. 

   Use el cmdlet **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** para cifrar las credenciales del servicio vinculado o para cifrar información confidencial en el servicio vinculado. A continuación, puede usar el JSON devuelto (con el elemento **EncryptedCredential** en **connectionString**) para crear un servicio vinculado por el cmdlet **Set-AzureRmDataFactoryV2LinkedSevrice**.  

2. Si no usa el cdmlet **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** como se describe en el paso anterior y, en su lugar, utiliza directamente el cmdlet **Set-AzureRmDataFactoryV2LinkedSevrice** con las cadenas de conexión o credenciales insertada en JSON, a continuación, el servicio vinculado se **cifrará y almacenará en el almacenamiento administrado Azure Data Factory**. La información confidencial todavía se cifrará por certificado y Microsoft administra dichos certificados.



#### <a name="ports-used-during-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Puertos utilizados durante el cifrado del servicio vinculado en Integration Runtime autohospedado
De forma predeterminada, PowerShell usa el puerto **8050** en la máquina con Integration Runtime autohospedado para una comunicación segura. Si es necesario, se puede cambiar este puerto.  

![Puerto HTTPS para la puerta de enlace](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Cifrado en tránsito
Todas las transferencias de datos se realizan a través del canal seguro **HTTPS** y **TLS sobre TCP** para evitar ataques tipo "Man in the middle" durante la comunicación con los servicios de Azure.

También puede usar una [conexión VPN de IPSec](../vpn-gateway/vpn-gateway-about-vpn-devices.md) o [ExpressRoute](../expressroute/expressroute-introduction.md) para proteger aún más el canal de comunicación entre la red local y Azure.

Una red virtual es una representación lógica de la red en la nube. Puede conectar una red local a la red virtual de Azure (VNet) mediante la configuración de una conexión VPN de IPSec (de sitio a sitio) o ExpressRoute (Emparejamiento privado)     

En la tabla siguiente, se resumen las recomendaciones de configuración de red e Integration Runtime autohospedado en función de diferentes combinaciones de ubicaciones de origen y de destino para el movimiento de datos híbridos.

| Origen      | Destino                              | Network configuration (Configuración de red)                    | Configuración de Integration Runtime                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Local | Máquinas virtuales y servicios en la nube implementados en redes virtuales | VPN de IPSec (de punto a sitio o de sitio a sitio) | Integration Runtime autohospedado se puede instalar de forma local o en una máquina virtual (VM) de Azure en VNet |
| Local | Máquinas virtuales y servicios en la nube implementados en redes virtuales | ExpressRoute (Emparejamiento privado)           | Integration Runtime autohospedado se puede instalar de forma local o en una VM de Azure en VNet |
| Local | Servicios basados en Azure que tienen un punto de conexión público | ExpressRoute (Emparejamiento público)            | Integration Runtime autohospedado debe instalarse de forma local |

Las siguientes imágenes muestran el uso de Integration Runtime autohospedado para mover datos entre una base de datos local y servicios de Azure mediante ExpressRoute e IPSec VPN (con Virtual Network):

**ExpressRoute:**

![Uso de ExpressRoute con la puerta de enlace](media/data-movement-security-considerations/express-route-for-gateway.png) 

**Conexión VPN de IPSec:**

![Conexión VPN de IPSec con la puerta de enlace](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-self-hosted-integration-runtime"></a>Opciones de configuración de firewall y lista blanca de direcciones IP (Integration Runtime autohospedado)

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos de firewall para redes locales o privadas  
En una empresa, se ejecuta un **firewall corporativo** en el enrutador central de la organización y el **firewall de Windows** se ejecuta como demonio en la máquina local con Integration Runtime autohospedado instalado. 

En la tabla siguiente se proporcionan el **puerto de salida** y los requisitos de dominio para el **firewall corporativo**.

| Nombres de dominio                  | Puertos de salida | DESCRIPCIÓN                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443, 80        | Lo necesita Integration Runtime autohospedado para conectarse a los servicios de movimiento de datos de Data Factory |
| `*.core.windows.net`          | 443            | Lo usa Integration Runtime autohospedado para conectarse a la cuenta de Azure Storage cuando se utiliza la característica [Copia almacenada provisionalmente](copy-activity-performance.md#staged-copy). |
| `*.frontend.clouddatahub.net` | 443            | Lo necesita Integration Runtime autohospedado para conectarse a los servicios de movimiento de Azure Data Factory. |
| `*.database.windows.net`      | 1433           | (OPCIONAL) Necesario cuando el destino es Azure SQL Database o Azure SQL Data Warehouse. Usa la característica de copia almacenada provisionalmente para copiar datos en Azure SQL Database o Azure SQL Data Warehouse sin abrir el puerto 1433. |
| `*.azuredatalakestore.net`    | 443            | (OPCIONAL) Necesario cuando el destino es Azure Data Lake Store. |

> [!NOTE] 
> Puede que tenga que administrar puertos dominios de listas de admitidos a nivel del firewall corporativo, en función de cada origen de datos. En esta tabla, Azure SQL Database, Azure SQL Data Warehouse y Azure Data Lake Store solo se usan a modo de ejemplo.   

En la tabla siguiente, se proporcionan los requisitos del **puerto de entrada** para el **firewall de Windows**.

| Puertos de entrada | DESCRIPCIÓN                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Requerido por el cmdlet de cifrado de PowerShell como se describe en el [cifrado de credenciales en Integration Runtime autohospedado](encrypt-credentials-self-hosted-integration-runtime.md) o en la aplicación de administrador de credenciales para establecer de forma segura credenciales para almacenes de datos locales en Integration Runtime autohospedado. |

![Requisitos de puerto de la puerta de enlace](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurationswhitelisting-in-data-store"></a>Configuraciones IP/lista de admitidos en el almacén de datos
Algunos almacenes de datos en la nube también requieren listas de direcciones IP admitidas para que la máquina acceda a ellas. Asegúrese de que la dirección IP de la máquina de Integration Runtime autohospedado aparece en la lista o está configurada en el firewall correctamente.

Los siguientes almacenes de datos en la nube necesitan una lista de direcciones IP admitidas por la máquina de Integration Runtime autohospedado. De forma predeterminada, algunos de estos almacenes de datos no necesitan listas de direcciones IP admitidas. 

- [Azure SQL Database](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [Azure Cosmos DB](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**Pregunta:** ¿Integration Runtime autohospedado puede compartirse entre diferentes factorías de datos?
**Respuesta:** Aún no se admite esta característica. Estamos trabajando en ello.

**Pregunta:** ¿Cuáles son los requisitos de puerto para que Integration Runtime autohospedado funcione?
**Respuesta:** Integration Runtime autohospedado establece conexiones basadas en HTTP para abrir Internet. Los **puertos de salida 80 y 443** deben estar abiertos para que Integration Runtime autohospedado establezca la conexión. Abra el **puerto de entrada 8050** solo en la máquina (no en el nivel del firewall corporativo) para la aplicación de administración de credenciales. Si se utiliza Azure SQL Database o Azure SQL Data Warehouse como origen y destino, tendrá que abrir también el puerto **1433**. Para más información, consulte la sección [Configuraciones del firewall y lista de direcciones IP admitidas](#firewall-configurations-and-whitelisting-ip-address-of gateway). 


## <a name="next-steps"></a>pasos siguientes
Para información sobre el rendimiento de la actividad de copia, consulte la [Guía de optimización y rendimiento de la actividad de copia](copy-activity-performance.md).

 
