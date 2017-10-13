---
title: "Protección de datos personales en tránsito con cifrado en Azure | Microsoft Docs"
description: Uso de cifrado en Azure para proteger datos personales
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Tecnologías de cifrado de Azure: protección de datos personales en tránsito con cifrado

Este artículo le ayudará a entender y usar las tecnologías de cifrado de Azure para proteger datos en tránsito. 

La protección de la privacidad de los datos personales mientras circulan por la red constituye una parte fundamental de una estrategia de seguridad de defensa en profundidad de varias capas. El cifrado en tránsito se ha diseñado para impedir que un atacante que intercepte transmisiones pueda ver o usar los datos.

## <a name="scenario"></a>Escenario

Una gran empresa de cruceros, con sede en Estados Unidos, se encuentra en proceso de expansión de sus operaciones para ofrecer itinerarios en los mares Mediterráneo, Adriático y Báltico, así como en las Islas Británicas. Para apoyar esos esfuerzos, ha adquirido varias líneas de cruceros más pequeñas establecidas en Italia, Alemania, Dinamarca y Reino Unido. 

La empresa usa Microsoft Azure para almacenar datos corporativos en la nube. Esto incluye información de identificación personal como nombres, direcciones, números de teléfono e información de la tarjeta de crédito de su clientela global. También incluye información tradicional de recursos humanos como direcciones, números de teléfono, números de identificación fiscal y otra información sobre los empleados de la empresa de todas las ubicaciones. La línea de cruceros también conserva una gran base de datos de miembros del programa de recompensa y lealtad que incluye información personal para hacer un seguimiento de las relaciones con clientes actuales y antiguos.

Los datos personales de los clientes se incluyen en la base de datos de las oficinas remotas de la empresa y de agentes de viajes de todo el mundo. Los documentos que contienen información de los clientes se transfieren por la red hasta Azure Storage.

## <a name="problem-statement"></a>Declaración del problema

La empresa debe proteger la privacidad de los datos personales de los clientes y los empleados mientras se encuentran en tránsito hacia o desde los servicios de Azure.

## <a name="company-goal"></a>Objetivo de la empresa

Objetivo de la empresa para garantizar que los datos personales estén cifrados fuera del disco. Si personas no autorizadas interceptan los datos personales fuera del disco, estos deben resultar ilegibles. La aplicación del cifrado debería ser fácil o totalmente transparente tanto para los usuarios como para los administradores.

## <a name="solutions"></a>Soluciones

Los servicios de Azure proporcionan varias herramientas y tecnologías para ayudarle a proteger datos personales en tránsito.

### <a name="azure-storage"></a>Azure Storage

Los datos almacenados en la nube deben circular desde el cliente, que puede encontrarse físicamente en cualquier lugar del mundo, hasta el centro de datos de Azure. Cuando los usuarios recuperan dichos datos, estos circulan de nuevo, en la dirección opuesta. Siempre existe el riesgo de que los atacantes intercepten los datos que se encuentran en tránsito por Internet público. Es importante proteger la privacidad de los datos personales mediante cifrado de nivel de transporte para salvaguardarlos mientras circulan entre las distintas ubicaciones.

El protocolo HTTPS proporciona un canal de comunicaciones seguro y cifrado a través de Internet. HTTPS debe usarse para tener acceso a objetos de Azure Storage y al llamar a API de REST. Exige el uso del protocolo HTTPS al utilizar [firmas de acceso compartido](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) (SAS) para administrar el acceso a los objetos de Azure Storage. Hay dos tipos de SAS: SAS de servicio y SAS de cuenta.

#### <a name="how-do-i-construct-a-service-sas"></a>¿Cómo se puede crear una SAS de servicio?

Una SAS de servicio administra el acceso a un recurso en solo uno de los servicios de almacenamiento (Blob service, Queue service, Table service o File service). Para crear una SAS de servicio, haga lo siguiente:

1. Especifique el campo de versión firmada

2. Especifique el recurso firmado (solo Blob service y File service)

3. Especifique los parámetros de consulta para invalidar los encabezados de respuesta (solo Blob service y File service)

4. Especifique el nombre de la tabla (solo Table service)

5. Especifique la directiva de acceso

6. Especifique el intervalo de validez de firma

8. Especifique los permisos

9. Especifique la dirección IP o el intervalo IP

10. Especifique el protocolo HTTP

11. Especifique los intervalos de acceso a la tabla

12. Especifique el identificador firmado

13. Especifique la firma

Para obtener instrucciones más detalladas, consulte [Creación de una SAS de servicio](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN).

#### <a name="how-do-i-construct-an-account-sas"></a>¿Cómo se puede crear una SAS de cuenta?

Una SAS de cuenta administra el acceso a los recursos en uno o varios de los servicios de almacenamiento. También puede delegar el acceso para leer, escribir y eliminar operaciones en contenedores de blobs, tablas, colas y recursos compartidos de archivos que no están permitidos con SAS de servicio. La creación de una SAS de cuenta es similar a la de una SAS de servicio. Para obtener instrucciones detalladas, consulte [Creación de una SAS de cuenta.](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN)

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>¿Cómo se puede aplicar HTTPS al llamar a API de REST?

Para exigir el uso de HTTPS al llamar a las API de REST para tener acceso a objetos de cuentas de almacenamiento, puede habilitar la opción Se requiere transferencia segura para la cuenta de almacenamiento. 

1. En Azure Portal, seleccione **Crear cuenta de almacenamiento** o, para una cuenta de almacenamiento existente, seleccione **Opciones** y, a continuación, **Configuración**.

2. En **Se requiere transferencia segura**, seleccione **Habilitado**.

![Creación de una cuenta de almacenamiento](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Para obtener instrucciones más detalladas, incluido cómo habilitar Se requiere transferencia segura mediante programación, consulte [Exigencia de transferencia segura](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>¿Cómo se pueden cifrar datos en Azure File Storage?

Para cifrar datos en tránsito con [Azure File Storage](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), puede usar SMB 3.x con Windows 8, 8.1 y 10 y con Windows Server 2012 R2 y Windows Server 2016. Si se usa el servicio Azure Files, se produce un error en todas las conexiones sin cifrado cuando la opción "Se requiere transferencia segura" está habilitada. Aquí se incluyen los escenarios que usan SMB 2.1 y SMB 3.0 sin cifrado, y algunas versiones del cliente SMB de Linux.

#### <a name="azure-client-side-encryption"></a>Cifrado de cliente de Azure

Otra opción para proteger datos personales mientras se transfieren entre una aplicación cliente y Azure Storage es [Cifrado de cliente](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). Los datos se cifran antes de transferirse a Azure Storage. Al recuperarse estos de allí, se descifran tras recibirse en el lado cliente.

### <a name="azure-site-to-site-vpn"></a>VPN de sitio a sitio de Azure

Una forma eficaz de proteger datos personales en tránsito entre una red o usuario corporativos y Azure Virtual Network es usar una red privada virtual (VPN) de [sitio a sitio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o de [punto a sitio](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). Una conexión VPN crea un túnel cifrado seguro a través de Internet.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>¿Cómo se puede crear una conexión VPN de sitio a sitio?

Una VPN de sitio a sitio conecta varios usuarios de la red corporativa a Azure. Para crear una conexión de sitio a sitio en Azure Portal, realice lo siguiente:

1. Cree una red virtual.

2. Especifique un servidor DNS

3. Cree la subred de la puerta de enlace.

4. Cree la puerta de enlace de VPN. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. Cree la puerta de enlace de red local.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Configure el dispositivo VPN.

7. Cree la conexión VPN.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. Compruebe la conexión VPN.

Para obtener instrucciones más detalladas sobre cómo crear una conexión de sitio a sitio en Azure Portal, consulte [Creación de una conexión de sitio a sitio mediante Azure Portal.] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal)

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>¿Cómo se puede crear una conexión VPN de punto a sitio?

Una VPN de punto a sitio crea una conexión segura entre un equipo cliente individual y una red virtual. Esta resulta útil cuando desea conectarse a Azure desde una ubicación remota, como desde casa o desde un hotel o centro de conferencia. Para crear una conexión de punto a sitio en Azure Portal:

1. Cree una red virtual.

2. Agregue una subred de puerta de enlace.

3. Especifique un servidor DNS (opcional).

4. Cree una puerta de enlace de red virtual.

5. Genere certificados.

6. Incorpore el grupo de direcciones del cliente.

7. Cargue la información del certificado de datos público del certificado raíz.

8. Genere e instale el paquete de configuración de cliente de VPN.

9. Instale un certificado de cliente exportado.

10. Conéctese a Azure.

11. Compruebe la conexión.

Para obtener instrucciones más detalladas, consulte [Configuración de una conexión de punto a sitio a una red virtual mediante la autenticación de certificado: Azure Portal.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)

### <a name="ssltls"></a>SSL/TLS

Microsoft recomienda que siempre use protocolos SSL/TLS para intercambiar datos entre distintas ubicaciones. Las organizaciones que eligen usar [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) para mover grandes conjuntos de datos sobre un vínculo WAN de alta velocidad dedicado también pueden cifrar los datos en el nivel de aplicación mediante SSL/TLS u otros protocolos para obtener una mayor protección.

### <a name="encryption-by-default"></a>Cifrado de forma predeterminada

Microsoft usa el cifrado para proteger datos en tránsito entre los clientes y Azure Cloud Services. Si interactúa con el Almacenamiento de Azure a través del Portal de Azure, todas las transacciones se realizan a través de HTTPS.

[Seguridad de la capa de transporte](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) es el protocolo que los centros de datos de Microsoft intentarán negociar con aquellos sistemas cliente conectados a Servicios en la nube de Microsoft. TLS proporciona una autenticación sólida, privacidad de mensajes e integridad (lo que permite la detección de la manipulación, interceptación y falsificación de mensajes), interoperabilidad, flexibilidad de algoritmo y facilidad de implementación y uso.

[Confidencialidad directa total](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) también se emplea para que cada conexión entre los sistemas cliente de los clientes y los servicios en la nube de Microsoft use claves únicas. Las conexiones a Servicios en la nube de Microsoft también aprovechan las longitudes de clave de cifrado de 2048 bits basadas en RSA. La combinación de TLS, las longitudes de clave de 2048 bits de RSA y PFS dificultan mucho más la interceptación y el acceso a datos en tránsito entre Servicios en la nube de Microsoft y los clientes.

Los datos en tránsito siempre se cifran en [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). Además de que los datos se cifran antes de almacenarse en un medio persistente, también se protegen cuando están en tránsito mediante HTTPS. HTTPS es el único protocolo admitido para las interfaces de REST de Data Lake Store.

## <a name="summary"></a>Resumen

La empresa puede lograr su objetivo de proteger datos personales y la privacidad de dichos datos aplicando conexiones HTTPS a Azure Storage, mediante firmas de acceso compartido y habilitando Se requiere transferencia segura en las cuentas de almacenamiento. También puede proteger datos personales mediante conexiones SMB 3.0 e implementando cifrado de cliente. Las conexiones VPN de sitio a sitio de la red corporativa a Azure Virtual Network y las conexiones VPN de punto a sitio de usuarios individuales crearán un túnel seguro a través del cual los datos personales pueden circular de forma segura. Las prácticas de cifrado predeterminadas de Microsoft protegerán aún más la privacidad de los datos personales.

## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados de cifrado y seguridad de datos en Azure](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [Planeamiento y diseño de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [Preguntas más frecuentes sobre VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Compra y configuración de un certificado SSL para Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
