---
title: Managed Service Identity (MSI) para Azure Active Directory
description: "Información general de Managed Service Identity para recursos de Azure."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 53577c8da5f82235284d1cb9e48f2d47254aa6bd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Managed Service Identity (MSI) para recursos de Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Un desafío común al crear aplicaciones en la nube consiste en el modo de administrar las credenciales que tienen que estar en el código para autenticar los servicios en la nube. Proteger estas credenciales es una tarea esencial. Lo ideal sería que nunca aparecieran en las estaciones de trabajo de los desarrolladores o que se controlaran en el código fuente. Azure Key Vault proporciona una manera de almacenar de forma segura las credenciales y otras claves y secretos, pero el código tiene que autenticarse en Key Vault para recuperarlos. Para solucionar este problema, Managed Service Identity (MSI) proporciona a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory (Azure AD). Puede usar esta identidad para autenticar cualquier servicio que admita la autenticación de Azure AD, incluido Key Vault, sin necesidad de tener credenciales en el código.

## <a name="how-does-it-work"></a>¿Cómo funciona?

Al usar la identidad de servicio administrada en una instancia de servicio de Azure, Azure crea una identidad en el inquilino de Azure AD que la suscripción de Azure usa. Además, Azure proporciona las credenciales para la identidad en la instancia de servicio. Como resultado, el código puede hacer una solicitud local para obtener los tokens de acceso para los servicios que admiten la autenticación de Azure AD. Mientras tanto, Azure se encarga de rotar las credenciales utilizadas por la instancia de servicio.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>¿Cómo habilito mis recursos para usar una identidad de servicio administrada?

Existen dos tipos de identidades de servicio administradas: *asignada por el sistema* y *asignada por el usuario*.

- Una identidad de servicio administrada **asignada por el sistema** se habilita directamente en una instancia de servicio de Azure. Mediante el proceso de habilitación, Azure crea una identidad para la instancia de servicio en el inquilino de Azure AD y proporciona las credenciales para la identidad en la instancia de servicio. El ciclo de vida de una identidad de servicio administrada asignada por el sistema está vinculado directamente a la instancia de servicio de Azure en que está habilitada. Si se elimina la instancia del servicio, Azure limpia automáticamente las credenciales y la identidad en Azure AD.

- Una identidad de servicio administrada **asignada por el usuario** *(versión preliminar privada)* se crea como un recurso de Azure independiente. Con el proceso de creación, Azure crea una identidad en el inquilino de Azure AD. Una vez creada la identidad, puede asignarse a una o varias instancias de servicio de Azure. Debido a que varias instancias de servicio de Azure pueden usar una misma identidad de servicio administrada asignada por el usuario, su ciclo de vida se administra por separado.

A continuación, se expone un ejemplo de cómo funciona una identidad de servicio administrada asignada por el sistema con Azure Virtual Machines.

![Ejemplo de MSI en una máquina virtual](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager recibe un mensaje para habilitar la identidad de servicio administrada asignada por el sistema en una máquina virtual.
2. Entonces crea una entidad de servicio en Azure AD para representar la identidad de la máquina virtual. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para esta suscripción.
3. Azure Resource Manager configura los detalles de la entidad de servicio en la extensión MSI de máquina virtual. Este paso incluye la configuración del identificador de cliente y del certificado utilizados por la extensión para obtener tokens de acceso de Azure AD.
4. Ahora que se conoce la identidad de la entidad de servicio de la máquina virtual, se puede conceder acceso a los recursos de Azure. Por ejemplo, si el código necesita llamar a Azure Resource Manager, asignaría a la entidad de servicio de la máquina virtual el rol adecuado con Control de acceso basado en roles (RBAC) en Azure AD.  Si el código necesita llamar a Key Vault, concedería a su código acceso al secreto o a la clave específicos en Key Vault.
5. El código que se ejecuta en la máquina virtual solicita un token de un punto de conexión local que está hospedado en la extensión MSI de máquina virtual: http://localhost:50342/oauth2/token. El parámetro del recurso especifica el servicio al que se va a enviar el token. Por ejemplo, si desea que su código se autentique en Azure Resource Manager, usaría resource=https://management.azure.com/.
6. La extensión MSI de máquina virtual usa el identificador de cliente configurado y el certificado para solicitar un token de acceso de Azure AD.  Azure AD devuelve un token de acceso JSON Web Token (JWT).
7. El código envía el token de acceso en una llamada a un servicio que admite la autenticación de Azure AD.

Según el mismo diagrama, se expone un ejemplo de cómo funciona una identidad de servicio administrada asignada por el usuario con Azure Virtual Machines.

![Ejemplo de MSI en una máquina virtual](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager recibe un mensaje para crear una identidad de servicio administrada asignada por el usuario.
2. Entonces crea una entidad de servicio en Azure AD para representar la identidad de la identidad de servicio administrada. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para esta suscripción.
3. Azure Resource Manager recibe un mensaje para configurar los detalles de la entidad de servicio en la extensión MSI de una máquina virtual. Este paso incluye la configuración del identificador de cliente y del certificado utilizados por la extensión para obtener tokens de acceso de Azure AD.
4. Ahora que se conoce la identidad de la entidad de servicio de la identidad de servicio administrada, se puede conceder acceso a los recursos de Azure. Por ejemplo, si el código necesita llamar a Azure Resource Manager, asignaría a la entidad de servicio de la identidad de servicio administrada el rol adecuado con el control de acceso basado en rol (RBAC) en Azure AD. Si el código necesita llamar a Key Vault, concedería a su código acceso al secreto o a la clave específicos en Key Vault. Nota: El paso 3 no es necesario para realizar el paso 4. Cuando ya exista la identidad de servicio administrada, se le puede conceder acceso a los recursos, con independencia de que esté configurada en una máquina virtual o no.
5. El código que se ejecuta en la máquina virtual solicita un token de un punto de conexión local que está hospedado por la extensión MSI de máquina virtual: http://localhost:50342/oauth2/token. El parámetro del identificador de cliente especifica el nombre de la identidad de servicio administrada que se va a usar. Además, el parámetro del recurso especifica el servicio al que se va a enviar el token. Por ejemplo, si desea que su código se autentique en Azure Resource Manager, usaría resource=https://management.azure.com/.
6. La extensión MSI de máquina virtual comprueba si el certificado para el identificador de cliente solicitado está configurado y solicita un token de acceso de Azure AD. Azure AD devuelve un token de acceso JSON Web Token (JWT).
7. El código envía el token de acceso en una llamada a un servicio que admite la autenticación de Azure AD.

Todos los servicios de Azure que son compatibles con Managed Service Identity tienen su propio método para que el código pueda obtener un token de acceso. Revise los tutoriales de cada servicio a fin de averiguar el método específico para obtener un token.

## <a name="try-managed-service-identity"></a>Evaluación de Managed Service Identity

Pruebe un tutorial de Managed Service Identity para obtener información sobre escenarios de extremo a extremo para tener acceso a diferentes recursos de Azure:
<br><br>
| Desde un recurso habilitado para MSI | Obtenga información sobre cómo |
| ------- | -------- |
| Máquina virtual de Azure (Linux)   | [Acceso a Azure Resource Manager con Managed Service Identity en una máquina virtual Linux](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Acceder a Azure Storage mediante una clave de acceso con Managed Service Identity en una máquina virtual Linux](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>¿Qué servicios de Azure son compatibles con Managed Service Identity?

Los servicios de Azure que son compatibles con Managed Service Identity (MSI) pueden usarlo para autenticarse en los servicios que admiten la autenticación de Azure AD.  Ya estamos integrando la autenticación de MSI y Azure AD en Azure.  Compruebe con frecuencia si existen actualizaciones.

### <a name="azure-services-that-support-managed-service-identity"></a>Servicios de Azure que admiten Managed Service Identity

Los siguientes servicios de Azure admiten Managed Service Identity.

| Servicio | Status | Date | Configuración | Obtención de un token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Vista previa | Septiembre de 2017 | [CLI de Azure](msi-qs-configure-cli-windows-vm.md)<br>[Plantillas del Administrador de recursos de Azure](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Servicios de Azure que admiten la autenticación de Azure AD

Los siguientes servicios admiten la autenticación de Azure AD y se han probado con los servicios de cliente que utilizan Managed Service Identity.

| Servicio | Id. de recurso | Status | Date | Asignación de acceso |
| ------- | ----------- | ------ | ---- | ------------- |
| Administrador de recursos de Azure | https://management.azure.com/ | Disponible | Septiembre de 2017 | [CLI de Azure](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Disponible | Septiembre de 2017 | |
| Azure Data Lake | https://datalake.azure.net/ | Disponible | Septiembre de 2017 | |
| Azure SQL | https://database.windows.net/ | Disponible | Octubre de 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>¿Cuánto cuesta Managed Service Identity?

Managed Service Identity viene con Azure Active Directory Free, que es la opción predeterminada para las suscripciones de Azure.  Su uso no supone ningún costo adicional.

## <a name="support-and-feedback"></a>Soporte y comentarios

Estaríamos encantados de ayudarle.

* Formule sus preguntas sobre procedimientos acerca de Stack Overflow con la etiqueta [azure-msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Haga solicitudes de características o envíe comentarios en el [foro de comentarios de Azure AD para desarrolladores](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






