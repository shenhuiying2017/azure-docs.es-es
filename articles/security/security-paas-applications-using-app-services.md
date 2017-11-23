---
title: "Protección de aplicaciones web y móviles PaaS con Azure App Service| Microsoft Docs"
description: " Aprenda sobre los procedimientos recomendados de seguridad de Azure App Service para proteger las aplicaciones web y móviles PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: dec45d91ad1a73306b3e2656dd9bf7fdbe456720
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Protección de aplicaciones web y móviles PaaS con Azure App Service

En este artículo se expone una colección de procedimientos recomendados de seguridad de [Azure App Service](https://azure.microsoft.com/services/app-service/) para proteger las aplicaciones web y móviles PaaS. Estos procedimientos recomendados proceden de nuestra experiencia con Azure y las experiencias de clientes como usted.

## <a name="azure-app-service"></a>Azure App Service
[Azure App Service](../app-service/app-service-web-overview.md) es una oferta PaaS que permite crear aplicaciones web y móviles para cualquier plataforma o dispositivo y conectarse a datos en cualquier lugar, en la nube o en un entorno local. App Service incluye las funcionalidades web y móviles que anteriormente se ofrecían por separado como Azure Websites y Azure Mobile Services. También incluye nuevas funcionalidades para automatizar procesos empresariales y hospedar las API en la nube. Como único servicio integrado, App Service ofrece un amplio conjunto de funcionalidades para escenarios web, móviles y de integración.

## <a name="best-practices"></a>Prácticas recomendadas

Al usar App Service, siga estos procedimientos recomendados:

- [Autenticación mediante Azure Active Directory (AD)](../app-service/app-service-authentication-overview.md). App Service proporciona un servicio OAuth 2.0 para el proveedor de identidades. OAuth 2.0 se centra en la sencillez del desarrollador del cliente y ofrece flujos de autorización específicos de aplicaciones web, aplicaciones de escritorio y teléfonos móviles. Azure AD usa OAuth 2.0 para permitir la autorización del acceso a los dispositivos móviles y a las aplicaciones web.
- Restricción del acceso siguiendo los principios de seguridad de limitar el acceso a lo que se necesita saber y a los principios de seguridad con privilegios mínimos. La restricción del acceso es fundamental para las organizaciones que deseen aplicar directivas de seguridad para el acceso a los datos. El control de acceso basado en rol (RBAC) sirve para asignar permisos a usuarios, grupos y aplicaciones en un ámbito determinado. Consulte [Introducción a la administración de acceso](../active-directory/role-based-access-control-what-is.md) para aprender más sobre cómo conceder acceso a los usuarios a las aplicaciones.
- Protección de las claves. No importa la calidad de la seguridad si pierde las claves de suscripción. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Mediante el uso de Key Vault, puede cifrar claves y secretos (por ejemplo claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .PFX y contraseñas) a través del uso de claves que están protegidas por módulos de seguridad de hardware (HSM). Para tener mayor seguridad, puede importar o generar las claves en HSM. Vea [Azure Key Vault](../key-vault/key-vault-whatis.md) para más información. También puede utilizar Key Vault para administrar los certificados TLS con renovación automática.
- Restricción de las direcciones IP de origen entrantes. [App Service Environment](../app-service/environment/intro.md) tiene una característica de integración de la red virtual que ayuda a restringir las direcciones IP de origen entrantes mediante grupos de seguridad de red (NSG). Si no conoce Azure Virtual Network, se trata de una funcionalidad que permite colocar muchos de los recursos de Azure en una red no enrutable sin conexión a Internet cuyo acceso controla. Consulte [Integración de su aplicación con una instancia de Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md) para más información.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se presenta una colección de procedimientos recomendados de seguridad de App Service para proteger las aplicaciones web y móviles PaaS. Para obtener más información sobre cómo proteger las implementaciones de PaaS, vea:

- [Protección de implementaciones de PaaS](security-paas-deployments.md)
- [Protección de aplicaciones web y móviles PaaS con Azure SQL Database y SQL Data Warehouse](security-paas-applications-using-sql.md)
