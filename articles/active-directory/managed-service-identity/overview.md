---
title: ¿Qué es Managed Service Identity (MSI) para recursos de Azure?
description: Información general de Managed Service Identity para recursos de Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 3493c726b600c1fd70e0c6041ec57c8f0ba01c38
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
#  <a name="what-is-managed-service-identity-msi-for-azure-resources"></a>¿Qué es Managed Service Identity (MSI) para recursos de Azure?

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Un desafío común al crear aplicaciones en la nube consiste en el modo de administrar las credenciales que tienen que estar en el código para autenticar los servicios en la nube. Proteger estas credenciales es una tarea esencial. Lo ideal sería que nunca aparecieran en las estaciones de trabajo de los desarrolladores o que se controlaran en el código fuente. Azure Key Vault proporciona una manera de almacenar de forma segura las credenciales y otras claves y secretos, pero el código tiene que autenticarse en Key Vault para recuperarlos. Para solucionar este problema, Managed Service Identity (MSI) proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta identidad para autenticar cualquier servicio que admita la autenticación de Azure AD, incluido Key Vault, sin necesidad de tener credenciales en el código.

## <a name="how-does-it-work"></a>¿Cómo funciona?

Existen dos tipos de identidades de servicio administradas: **asignada por el sistema** y **asignada por el usuario**.

- Una **identidad asignada por el sistema** se habilita directamente en una instancia de servicio de Azure. Cuando se habilita, Azure crea una identidad para la instancia del servicio en el inquilino de Azure AD de confianza para la suscripción de la instancia de servicio. Una vez creada la identidad, sus credenciales se aprovisionan en la instancia de servicio. El ciclo de vida de una identidad de servicio asignada por el sistema está vinculado directamente a la instancia de servicio de Azure en que está habilitada. Si se elimina la instancia del servicio, Azure limpia automáticamente las credenciales y la identidad en Azure AD.
- Se crea una **identidad asignada por el usuario** (versión preliminar pública) como recurso de Azure independiente. Mediante un proceso de creación, Azure crea una identidad en el inquilino de Azure AD de confianza para la suscripción que se utiliza. Una vez creada la identidad, puede asignarse a una o varias instancias de servicio de Azure. El ciclo de vida de una identidad asignada por el usuario se administra por separado del ciclo de vida de las instancias de servicio de Azure a las que se asigna.

Por consiguiente, el código puede usar una identidad asignada por el sistema o una identidad asignada por el usuario para solicitar tokens de acceso para los servicios que admiten la autenticación de Azure AD. Mientras tanto, Azure se encarga de rotar las credenciales utilizadas por la instancia de servicio.

Este es un ejemplo de cómo funcionan las identidades asignadas por el sistema con Azure Virtual Machines:

![Ejemplo de MSI en una máquina virtual](overview/msi-vm-vmextension-imds-example.png)

1. Azure Resource Manager recibe una solicitud para habilitar la identidad asignada por el sistema en una máquina virtual.
2. Entonces crea una entidad de servicio en Azure AD para representar la identidad de la máquina virtual. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para esta suscripción.
3. Azure Resource Manager configura la identidad en la máquina virtual:
    - Actualiza el punto de conexión de la identidad de Azure Instance Metadata Service con el identificador de cliente de la entidad de servicio y el certificado.
    - Aprovisiona la extensión de máquina virtual de MSI y agrega el identificador de cliente de la entidad de servicio y el certificado. (va a quedar en desuso)
4. Ahora que la máquina virtual tiene una identidad, se usa la información de su entidad de servicio para conceder a la máquina virtual acceso a recursos de Azure. Por ejemplo, si el código necesita llamar a Azure Resource Manager, asignaría a la entidad de servicio de la máquina virtual el rol adecuado con Control de acceso basado en roles (RBAC) en Azure AD. Si el código necesita llamar a Key Vault, concedería a su código acceso al secreto o a la clave específicos en Key Vault.
5. El código que se ejecuta en la máquina virtual puede solicitar un token en dos puntos de conexión que solo son accesibles desde la máquina virtual:

    - Punto de conexión de la identidad de Azure Instance Metadata Service (IMDS): http://169.254.169.254/metadata/identity/oauth2/token (recomendado)
        - El parámetro de recurso especifica el servicio al que se va a enviar el token. Por ejemplo, si desea que su código se autentique en Azure Resource Manager, usaría resource=https://management.azure.com/.
        - El parámetro de versión de API especifica la versión de IMDS, use api-version=2018-02-01 o posterior.
    - Punto de conexión de la extensión de máquina virtual de MSI: http://localhost:50342/oauth2/token (va a quedar en desuso)
        - El parámetro de recurso especifica el servicio al que se va a enviar el token. Por ejemplo, si desea que su código se autentique en Azure Resource Manager, usaría resource=https://management.azure.com/.

6. Se realiza una llamada a Azure AD que solicita un token de acceso, tal y como se especifica en el paso 5, con el identificador de cliente y el certificado configurado en el paso 3. Azure AD devuelve un token de acceso JSON Web Token (JWT).
7. El código envía el token de acceso en una llamada a un servicio que admite la autenticación de Azure AD.

Según el mismo diagrama, se expone un ejemplo de cómo funciona una identidad de servicio administrada asignada por el usuario con Azure Virtual Machines.

1. Azure Resource Manager recibe una solicitud para crear una identidad asignada por el usuario.
2. Luego crea una entidad de servicio en Azure AD para representar la identidad asignada por el usuario. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para esta suscripción.
3. Azure Resource Manager recibe una solicitud para configurar la identidad asignada por el usuario en una máquina virtual:
    - Actualiza el punto de conexión de la identidad de Azure Instance Metadata Service con el identificador de cliente de la entidad de servicio asignada por el usuario y el certificado.
    - Se aprovisiona la extensión de máquina virtual de MSI y se agrega el identificador de cliente de la entidad de servicio de la identidad asignada por el usuario y de certificado (va a quedar en desuso).
4. Ahora que se ha creado la identidad asignada por el usuario, se usa la información de su entidad de servicio para concederle acceso a recursos de Azure. Por ejemplo, si el código tiene que llamar a Azure Resource Manager, asignará a la entidad de servicio de la identidad asignada por el usuario el rol adecuado con el control de acceso basado en rol (RBAC) en Azure AD. Si el código necesita llamar a Key Vault, concedería a su código acceso al secreto o a la clave específicos en Key Vault. Nota: Este paso también se puede realizar antes del paso 3.
5. El código que se ejecuta en la máquina virtual puede solicitar un token en dos puntos de conexión que solo son accesibles desde la máquina virtual:

    - Punto de conexión de la identidad de Azure Instance Metadata Service (IMDS): http://169.254.169.254/metadata/identity/oauth2/token (recomendado)
        - El parámetro de recurso especifica el servicio al que se va a enviar el token. Por ejemplo, si desea que su código se autentique en Azure Resource Manager, usaría resource=https://management.azure.com/.
        - El parámetro de identificador de cliente especifica la identidad para la que se solicita el token. Es necesario para eliminar la ambigüedad cuando más de una de las identidades asignadas por el usuario están en una sola máquina virtual.
        - El parámetro de versión de API especifica la versión de IMDS, use api-version=2018-02-01 o posterior.

    - Punto de conexión de la extensión de máquina virtual de MSI: http://localhost:50342/oauth2/token (va a quedar en desuso)
        - El parámetro de recurso especifica el servicio al que se va a enviar el token. Por ejemplo, si desea que su código se autentique en Azure Resource Manager, usaría resource=https://management.azure.com/.
        - El parámetro de identificador de cliente especifica la identidad para la que se solicita el token. Es necesario para eliminar la ambigüedad cuando más de una de las identidades asignadas por el usuario están en una sola máquina virtual.
6. Se realiza una llamada a Azure AD que solicita un token de acceso, tal y como se especifica en el paso 5, con el identificador de cliente y el certificado configurado en el paso 3. Azure AD devuelve un token de acceso JSON Web Token (JWT).
7. El código envía el token de acceso en una llamada a un servicio que admite la autenticación de Azure AD.
     
## <a name="try-managed-service-identity"></a>Evaluación de Managed Service Identity

Pruebe un tutorial de Managed Service Identity para obtener información sobre escenarios de extremo a extremo para tener acceso a diferentes recursos de Azure:
<br><br>
| Desde un recurso habilitado para MSI | Obtenga información sobre cómo |
| ------- | -------- |
| Máquina virtual de Azure (Windows) | [Acceder a Azure Data Lake Store con una Identidad de servicio administrada de máquina virtual Windows](tutorial-windows-vm-access-datalake.md) |
|                    | [Acceso a Azure Resource Manager con Managed Service Identity en una máquina virtual Windows](tutorial-windows-vm-access-arm.md) |
|                    | [Acceder a Azure SQL con Managed Service Identity en una máquina virtual Windows](tutorial-windows-vm-access-sql.md) |
|                    | [Acceder a Azure Storage mediante una clave de acceso con Managed Service Identity en una máquina virtual Windows](tutorial-windows-vm-access-storage.md) |
|                    | [Acceder a Azure Storage mediante SAS con Managed Service Identity en una máquina virtual Windows](tutorial-windows-vm-access-storage-sas.md) |
|                    | [Acceso a un recurso ajeno a Azure AD con Managed Service Identity en una máquina virtual Windows y Azure Key Vault](tutorial-windows-vm-access-nonaad.md) |
| Máquina virtual de Azure (Linux)   | [Acceder a Azure Data Lake Store con una Identidad de servicio administrada de máquina virtual Linux](tutorial-linux-vm-access-datalake.md) |
|                    | [Acceso a Azure Resource Manager con Managed Service Identity en una máquina virtual Linux](tutorial-linux-vm-access-arm.md) |
|                    | [Acceder a Azure Storage mediante una clave de acceso con Managed Service Identity en una máquina virtual Linux](tutorial-linux-vm-access-storage.md) |
|                    | [Acceder a Azure Storage mediante SAS con Managed Service Identity en una máquina virtual Linux](tutorial-linux-vm-access-storage-sas.md) |
|                    | [Acceder a un recurso ajeno a Azure AD con Managed Service Identity en una máquina virtual Linux y Azure Key Vault](tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Uso de Managed Service Identity con Azure App Service o Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Functions    | [Uso de Managed Service Identity con Azure App Service o Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Uso de identidades de servicio administradas con Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Uso de la identidad de servicio administrada con Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>¿Qué servicios de Azure son compatibles con Managed Service Identity?

Las identidades administradas se pueden usar para autenticarse en servicios que admiten la autenticación de Azure AD. Para obtener la lista de servicios de Azure que admiten Managed Service Identity, consulte el siguiente artículo:
- [Servicios que admiten Managed Service Identity](services-support-msi.md)

## <a name="how-much-does-managed-service-identity-cost"></a>¿Cuánto cuesta Managed Service Identity?

Managed Service Identity viene con Azure Active Directory Free, que es la opción predeterminada para las suscripciones de Azure. Su uso no supone ningún costo adicional.

## <a name="support-and-feedback"></a>Soporte y comentarios

Estaríamos encantados de ayudarle.

* Formule sus preguntas sobre procedimientos acerca de Stack Overflow con la etiqueta [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Haga solicitudes de características o envíe comentarios en el [foro de comentarios de Azure AD para desarrolladores](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).

## <a name="next-steps"></a>Pasos siguientes

Comience a usar Azure Managed Service Identity con los tutoriales siguientes:

* [Uso de Managed Service Identity (MSI) en una máquina virtual de Windows con acceso a Resource Manager: VM de Windows](tutorial-windows-vm-access-arm.md)
* [Uso de Managed Service Identity (MSI) en una máquina virtual de Linux con acceso a Azure Resource Manager: VM de Linux](tutorial-linux-vm-access-arm.md)
