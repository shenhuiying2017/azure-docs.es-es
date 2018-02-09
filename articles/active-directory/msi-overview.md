---
title: Managed Service Identity (MSI) para Azure Active Directory
description: "Información general de Managed Service Identity para recursos de Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: b28f5c7c90783c5abd933b50ddb9631739b8f421
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Managed Service Identity (MSI) para recursos de Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Un desafío común al crear aplicaciones en la nube consiste en el modo de administrar las credenciales que tienen que estar en el código para autenticar los servicios en la nube. Proteger estas credenciales es una tarea esencial. Lo ideal sería que nunca aparecieran en las estaciones de trabajo de los desarrolladores o que se controlaran en el código fuente. Azure Key Vault proporciona una manera de almacenar de forma segura las credenciales y otras claves y secretos, pero el código tiene que autenticarse en Key Vault para recuperarlos. Para solucionar este problema, Managed Service Identity (MSI) proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta identidad para autenticar cualquier servicio que admita la autenticación de Azure AD, incluido Key Vault, sin necesidad de tener credenciales en el código.

## <a name="how-does-it-work"></a>¿Cómo funciona?

Cuando se habilita Managed Service Identity en un servicio de Azure, Azure crea automáticamente una identidad para la instancia del servicio en el inquilino de Azure AD que la suscripción de Azure usa.  En segundo plano, Azure proporciona las credenciales para la identidad en la instancia del servicio.  El código puede hacer una solicitud local para obtener los tokens de acceso para los servicios que admiten la autenticación de Azure AD.  Azure se encarga de rotar las credenciales utilizadas por la instancia del servicio.  Si se elimina la instancia del servicio, Azure limpia automáticamente las credenciales y la identidad en Azure AD.

Este es un ejemplo de cómo funciona Managed Service Identity con Azure Virtual Machines.

![Ejemplo de MSI en una máquina virtual](./media/msi-vm-example.png)

1. Azure Resource Manager recibe un mensaje para habilitar MSI en una máquina virtual.
2. Entonces crea una entidad de servicio en Azure AD para representar la identidad de la máquina virtual. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para esta suscripción.
3. Azure Resource Manager configura los detalles de la entidad de servicio en la extensión MSI de máquina virtual.  Este paso incluye la configuración del identificador de cliente y del certificado utilizados por la extensión para obtener tokens de acceso de Azure AD.
4. Ahora que se conoce la identidad de la entidad de servicio de la máquina virtual, se puede conceder acceso a los recursos de Azure.  Por ejemplo, si el código necesita llamar a Azure Resource Manager, asignaría a la entidad de servicio de la máquina virtual el rol adecuado con Control de acceso basado en roles (RBAC) en Azure AD.  Si el código necesita llamar a Key Vault, concedería a su código acceso al secreto o a la clave específicos en Key Vault.
5. El código que se ejecuta en la máquina virtual solicita un token de un punto de conexión local que está hospedado por la extensión MSI de máquina virtual: http://localhost:50342/oauth2/token.  El parámetro del recurso especifica el servicio al que se va a enviar el token. Por ejemplo, si desea que su código se autentique en Azure Resource Manager, usaría resource=https://management.azure.com/.
6. La extensión MSI de máquina virtual usa el identificador de cliente configurado y el certificado para solicitar un token de acceso de Azure AD.  Azure AD devuelve un token de acceso JSON Web Token (JWT).
7. El código envía el token de acceso en una llamada a un servicio que admite la autenticación de Azure AD.

Todos los servicios de Azure que son compatibles con Managed Service Identity tienen su propio método para que el código pueda obtener un token de acceso. Revise los tutoriales de cada servicio a fin de averiguar el método específico para obtener un token.

## <a name="try-managed-service-identity"></a>Evaluación de Managed Service Identity

Pruebe un tutorial de Managed Service Identity para obtener información sobre escenarios de extremo a extremo para tener acceso a diferentes recursos de Azure:
<br><br>
| Desde un recurso habilitado para MSI | Obtenga información sobre cómo |
| ------- | -------- |
| Máquina virtual de Azure (Windows) | [Acceder a Azure Data Lake Store con una Identidad de servicio administrada de máquina virtual Windows](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Acceso a Azure Resource Manager con Managed Service Identity en una máquina virtual Windows](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Acceder a Azure SQL con Managed Service Identity en una máquina virtual Windows](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Acceder a Azure Storage mediante una clave de acceso con Managed Service Identity en una máquina virtual Windows](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Acceder a Azure Storage mediante SAS con Managed Service Identity en una máquina virtual Windows](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [Acceso a un recurso ajeno a Azure AD con Managed Service Identity en una máquina virtual Windows y Azure Key Vault](msi-tutorial-windows-vm-access-nonaad.md) |
| Máquina virtual de Azure (Linux)   | [Acceder a Azure Data Lake Store con una Identidad de servicio administrada de máquina virtual Linux](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Acceso a Azure Resource Manager con Managed Service Identity en una máquina virtual Linux](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Acceder a Azure Storage mediante una clave de acceso con Managed Service Identity en una máquina virtual Linux](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Acceder a Azure Storage mediante SAS con Managed Service Identity en una máquina virtual Linux](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [Acceder a un recurso ajeno a Azure AD con Managed Service Identity en una máquina virtual Linux y Azure Key Vault](msi-tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Uso de Managed Service Identity con Azure App Service o Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Función de Azure     | [Uso de Managed Service Identity con Azure App Service o Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Uso de identidades de servicio administradas con Azure Service Bus](../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Uso de la identidad de servicio administrada con Azure Event Hubs](../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>¿Qué servicios de Azure son compatibles con Managed Service Identity?

Los servicios de Azure que son compatibles con Managed Service Identity (MSI) pueden usarlo para autenticarse en los servicios que admiten la autenticación de Azure AD.  Ya estamos integrando la autenticación de MSI y Azure AD en Azure.  Compruebe con frecuencia si existen actualizaciones.

### <a name="azure-services-that-support-managed-service-identity"></a>Servicios de Azure que admiten Managed Service Identity

Los siguientes servicios de Azure admiten Managed Service Identity.

| Servicio | Status | Date | Configuración | Obtención de un token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Vista previa | Septiembre de 2017 | [portal de Azure](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[CLI de Azure](msi-qs-configure-cli-windows-vm.md)<br>[Plantillas del Administrador de recursos de Azure](msi-qs-configure-template-windows-vm.md) | [REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http)<br>[.NET](msi-how-to-use-vm-msi-token.md#get-a-token-using-c)<br>[Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[Go](msi-how-to-use-vm-msi-token.md#get-a-token-using-go)<br>[PowerShell](msi-how-to-use-vm-msi-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Vista previa | Septiembre de 2017 | [portal de Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Plantilla de Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Vista previa | Septiembre de 2017 | [portal de Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Plantilla de Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Vista previa | Noviembre de 2017 | [portal de Azure](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Servicios de Azure que admiten la autenticación de Azure AD

Los siguientes servicios admiten la autenticación de Azure AD y se han probado con los servicios de cliente que utilizan Managed Service Identity.

| Servicio | Id. de recurso | Status | Date | Asignación de acceso |
| ------- | ----------- | ------ | ---- | ------------- |
| Administrador de recursos de Azure | https://management.azure.com/ | Disponible | Septiembre de 2017 | [portal de Azure](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[CLI de Azure](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Disponible | Septiembre de 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponible | Septiembre de 2017 | |
| Azure SQL | https://database.windows.net/ | Disponible | Octubre de 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net/ | Disponible | Diciembre de 2017 | |
| Azure Service Bus | https://servicebus.azure.net/ | Disponible | Diciembre de 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>¿Cuánto cuesta Managed Service Identity?

Managed Service Identity viene con Azure Active Directory Free, que es la opción predeterminada para las suscripciones de Azure.  Su uso no supone ningún costo adicional.

## <a name="support-and-feedback"></a>Soporte y comentarios

Estaríamos encantados de ayudarle.

* Formule sus preguntas sobre procedimientos acerca de Stack Overflow con la etiqueta [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Haga solicitudes de características o envíe comentarios en el [foro de comentarios de Azure AD para desarrolladores](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






