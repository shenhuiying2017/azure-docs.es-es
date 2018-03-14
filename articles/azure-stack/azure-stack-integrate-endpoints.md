---
title: "Integración del centro de datos de Azure Stack: publicar puntos de conexión"
description: "Obtenga información sobre cómo publicar puntos de conexión de Azure Stack en su centro de datos."
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: 
ms.openlocfilehash: 8dec686b9ccf87009a23cedf6023f15b84a0f155
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Integración del centro de datos de Azure Stack: publicar puntos de conexión
Azure Stack configura direcciones IP virtuales (VIP) para sus roles de infraestructura. Estas VIP se asignan desde el grupo de direcciones IP públicas. Cada VIP está protegida con una lista de control de acceso (ACL) en el nivel de red definido por software. Las ACL también se usan en los conmutadores físicos (Tor y BMC) para proteger aún más la solución. Se crea una entrada DNS para cada punto de conexión de la zona DNS externa que se haya especificado durante la implementación.


En el siguiente diagrama de arquitectura se muestran los diferentes niveles de red y ACL:

![Imagen estructural](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Puertos y protocolos (de entrada)

Se requiere un conjunto de direcciones IP virtuales de infraestructura para publicar los puntos de conexión de Azure Stack en redes externas. La tabla *Punto de conexión (VIP)* se muestra cada punto de conexión, el puerto requerido y el protocolo. Consulte la documentación de implementación de proveedor de recursos específica para los puntos de conexión que requieren proveedores de recursos adicionales, como el proveedor de recursos de SQL.

No se indican las VIP de infraestructura interna porque no son necesarias para la publicación de Azure Stack.

> [!NOTE]
> Las VIP de usuario son dinámicas y están definidas por los propios usuarios, sin ningún control por parte del operador de Azure Stack.


|Punto de conexión (VIP)|Registro de host DNS A|Protocolo|Puertos|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portal (administrador)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (administrador)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Portal (usuario)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (usuario)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|Grafo|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Lista de revocación de certificados|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP y UDP|53|
|Key Vault (usuario)|&#42;.vault.*&lt;región>.&lt;fqdn>*|HTTPS|443|
|Key Vault (administrador)|&#42;.adminvault.*&lt;región>.&lt;fqdn>*|HTTPS|443|
|Cola de almacenamiento|&#42;.queue.*&lt;región>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabla de almacenamiento|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Proveedor de recursos SQL|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Proveedor de recursos MySQL|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice.*&lt;región>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;región>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;región>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;región>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>Puertos y direcciones URL (de salida)

Azure Stack solo admite servidores proxy transparentes. En una implementación en la que un proxy transparente establece un vínculo superior a un servidor proxy tradicional, debe permitir los siguientes puertos y direcciones URL para la comunicación saliente:


|Propósito|URL|Protocolo|Puertos|
|---------|---------|---------|---------|
|Identidad|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Redifusión de Marketplace|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|Revisión y actualización|https://&#42;.azureedge.net|HTTPS|443|
|Registro|https://management.azure.com|HTTPS|443|
|Uso|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>Pasos siguientes

[Requisitos de PKI de Azure Stack](azure-stack-pki-certs.md)