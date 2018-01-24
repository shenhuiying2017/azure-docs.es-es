---
title: "Requisitos de certificados de infraestructura de clave pública de Azure Stack para sistemas integrados de Azure Stack | Microsoft Docs"
description: "Describe los requisitos de implementación de certificados PKI de Azure Stack para sus sistemas integrados."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 8f0bb2266cb3a8a869ad50c40a46eb82985d17ed
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Requisitos de certificados de infraestructura de clave pública de Azure Stack
Azure Stack tiene una red de infraestructura pública que usa direcciones IP públicas accesibles externamente y asignadas a un pequeño conjunto de servicios de Azure Stack y, posiblemente, a las máquinas virtuales del inquilino. Se requieren certificados PKI con los nombres DNS apropiados para estos puntos de conexión de la infraestructura pública de Azure Stack durante la implementación de este. En este artículo se proporciona información acerca de lo siguiente:

- Los certificados son necesarios para implementar Azure Stack
- El proceso de obtención de certificados que concuerda con esas especificaciones
- Cómo preparar, validar y utilizar esos certificados durante la implementación

## <a name="certificate-requirements"></a>Requisitos de certificados
En la lista siguiente se describen los requisitos de certificados que son necesarios para implementar Azure Stack: 
- Los certificados deben ser emitidos desde una entidad de certificación interna o pública. Si se usa una entidad de certificación pública, se debe incluir en la imagen del sistema operativo base como parte del programa de entidades de certificación raíz de confianza de Microsoft (Microsoft Trusted Root Certificate Program). Puede encontrar la lista completa aquí: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- El certificado puede ser uno único comodín que abarque todos los espacios de nombres en el campo del nombre alternativo del firmante (SAN). Como alternativa, puede usar certificados individuales con caracteres comodín para los puntos de conexión como el almacenamiento y el almacén de claves donde sean necesarios. 
- El algoritmo de firma de certificados no puede ser SHA1, ya que debe ser más seguro. 
- El formato del certificado debe ser PFX, porque las claves públicas y privadas son necesarias para la instalación de Azure Stack. 
- Los archivos PFX de certificado deben tener un valor "Digital Signature" (firma digital) y "KeyEncipherment" (cifrado de clave) en el campo "Key Usage" (uso de clave).
- Las contraseñas para todos los archivos PFX de certificado deben ser las mismas en el momento de la implementación.
- Asegúrese de que los nombres de asunto y nombres alternativos del firmante de todos los certificados coinciden con las especificaciones descritas en este artículo para evitar errores en las implementaciones.

> [!NOTE]
> Se admite la presencia de entidades emisoras de certificados intermediarias en una cadena de relaciones de confianza del certificado. 

## <a name="mandatory-certificates"></a>Certificados obligatorios
En la tabla de esta sección se describen los certificados PKI públicos de punto de conexión de Azure Stack que son necesarios tanto para las implementaciones de Azure AD como para las de Azure Stack en AD FS. Los requisitos de certificados se agrupan por área, así como los espacios de nombres utilizados y los certificados que son necesarios para cada espacio de nombres. En la tabla también se describe la carpeta en la que el proveedor de soluciones copia los certificados diferentes para cada punto de conexión público. 

Se requieren certificados con los nombres DNS apropiados para cada punto de conexión de la infraestructura pública de Azure Stack. El nombre DNS de cada punto de conexión se expresa en el formato: *&lt;prefijo>.&lt;región>.&lt;fqdn>*. 

En la implementación, los valores de [region] y [externalfqdn] deben coincidir con los nombres de dominio externo y región que eligió para el sistema de Azure Stack. Por ejemplo, si el nombre de la región es *Redmond* y el nombre de dominio externo fuese *contoso.com*, los nombres DNS tendrían el formato  *&lt;prefijo>.redmond.contoso.com* . Los valores de *&lt;prefijo>* son designados de antemano por Microsoft para describir el punto de conexión protegido por el certificado. Además, los valores de  *&lt;prefijo>* de los puntos de conexión de la infraestructura externa dependen del servicio de Azure Stack que use el punto de conexión concreto. 

|Carpeta de implementación|Nombres alternativos del firmante (SAN) y firmante del certificado requeridos|Ámbito (por región)|Nombre del subdominio|
|-----|-----|-----|-----|
|Public Portal|portal.*&lt;región>.&lt;fqdn>*|Portales|*&lt;región>.&lt;fqdn>*|
|Admin Portal|adminportal.*&lt;región>.&lt;fqdn>*|Portales|*&lt;región>.&lt;fqdn>*|
|Azure Resource Manager Public|management.*&lt;región>.&lt;fqdn>*|Administrador de recursos de Azure|*&lt;región>.&lt;fqdn>*|
|Azure Resource Manager Admin|adminmanagement.*&lt;región>.&lt;fqdn>*|Administrador de recursos de Azure|*&lt;región>.&lt;fqdn>*|
|ACS<sup>1</sup>|Un certificado comodín de subdominio múltiple con nombres alternativos del firmante para:<br>&#42;.blob.*&lt;región>.&lt;fqdn>*<br>&#42;.queue.*&lt;región>.&lt;fqdn>*<br>&#42;.table.*&lt;región>.&lt;fqdn>*|Storage|blob.*&lt;región>.&lt;fqdn>*<br>table.*&lt;región>.&lt;fqdn>*<br>queue.*&lt;región>.&lt;fqdn>*|
|KeyVault|&#42;.vault.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL comodín)|Key Vault|vault.*&lt;región>.&lt;fqdn>*|
|KeyVaultInternal|&#42;.adminvault.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL comodín)|Almacén de claves interno|adminvault.*&lt;región>.&lt;fqdn>*|
|
<sup>1</sup> El certificado ACS requiere tres del nombres alternativos del firmante comodín en un solo certificado. Puede que no todas las entidades de certificación públicas admitan varios nombres alternativos del firmante comodín en un solo certificado. 

Si implementa Azure Stack con el modo de implementación de Azure AD, solo tiene que solicitar los certificados que se muestran en la tabla anterior. Sin embargo, si implementa Azure Stack utilizando el modo de implementación de AD FS, también debe solicitar los certificados descritos en la tabla siguiente:

|Carpeta de implementación|Nombres alternativos del firmante (SAN) y firmante del certificado requeridos|Ámbito (por región)|Nombre del subdominio|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL)|ADFS|*&lt;región>.&lt;fqdn>*|
|Grafo|graph.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL)|Grafo|*&lt;región>.&lt;fqdn>*|
|

> [!IMPORTANT]
> Todos los certificados que se indican en esta sección deben tener la misma contraseña. 

## <a name="optional-paas-certificates"></a>Certificados de PaaS opcionales
Si va a implementar los servicios adicionales PaaS de Azure Stack (SQL, MySQL y App Service) después de implementar y configurar Azure Stack, debe solicitar certificados adicionales para cubrir los puntos de conexión de los servicios PaaS. 

> [!IMPORTANT]
> Los certificados que se usan para los proveedores de recursos de App Service, SQL y MySQL deben tener la misma entidad de certificación raíz que los de los puntos de conexión globales de Azure Stack. 

En la tabla siguiente se describen los puntos de conexión y los certificados necesarios para los adaptadores de SQL y MySQL y para App Service. No es necesario copiar estos certificados en la carpeta de implementación de Azure Stack. En su lugar, estos certificados se proporcionan al instalar los proveedores de recursos adicionales. 

|Ámbito (por región)|Certificate|Nombres alternativos del firmante (SAN) y firmante del certificado requeridos|Nombre del subdominio|
|-----|-----|-----|-----|
|SQL, MySQL|SQL y MySQL|&#42;.dbadapter.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL comodín)|dbadapter.*&lt;región>.&lt;fqdn>*|
|App Service|Certificado SSL predeterminado de tráfico web|&#42;.appservice.*&lt;región>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL comodín de varios dominios<sup>1</sup>)|appservice.*&lt;región>.&lt;fqdn>*<br>scm.appservice.*&lt;región>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;región>.&lt;fqdn>*<br>scm.appservice.*&lt;región>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;región>.&lt;fqdn>*<br>scm.appservice.*&lt;región>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;región>.&lt;fqdn>*<br>(Certificado SSL<sup>2</sup>)|appservice.*&lt;región>.&lt;fqdn>*<br>scm.appservice.*&lt;región>.&lt;fqdn>*|

<sup>1</sup> Requiere un certificado con varios nombres alternativos de firmante comodín. Puede que no todas las entidades de certificación públicas admitan varios nombres alternativos del firmante comodín en un solo certificado. 

<sup>2</sup> A &#42;.appservice.*&lt;región>.&lt;fqdn>* el certificado comodín no se puede usar en lugar de estos tres certificados (api.appservice.*&lt;región>.&lt;fqdn>*, ftp.appservice.*&lt;región>.&lt;fqdn>* y sso.appservice.*&lt;región>.&lt;fqdn>*. Appservice requiere explícitamente el uso de certificados independientes para estos puntos de conexión. 


## <a name="next-steps"></a>pasos siguientes
[Generación de certificados de infraestructura de clave pública para la implementación de Azure Stack](azure-stack-get-pki-certs.md) 


