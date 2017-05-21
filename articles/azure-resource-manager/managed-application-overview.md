---
title: "Introducción a las aplicaciones administradas de Azure | Microsoft Docs"
description: Describe los conceptos de las aplicaciones administradas de Azure
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/11/2017
ms.author: gauravbh; tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: a4348a4d2348d744c03ad3a89d0548526fa2f9f8
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---
# <a name="azure-managed-applications-overview"></a>Introducción a las aplicaciones administradas de Azure

En la actualidad, Azure proporciona un espacio sólido, Marketplace, en el que tanto los ISV como las empresas emergentes puede ofrecer sus soluciones a clientes de todo el mundo. Azure Marketplace es una galería que consta de cientos de plantillas complejas de múltiples recursos de proveedores propios y de terceros. Los clientes pueden implementarlas e iniciarlas mediante aplicaciones PaaS y SaaS en cuestión de minutos. Aunque proporciona una manera excelente de implementar rápidamente cualquier oferta, el cliente sigue siendo responsable de mantener y actualizar la solución. En cuanto a los proveedores, no hay forma alguna de cobrar a los clientes el uso de una aplicación, más allá de la facturación de las imágenes de máquinas virtuales. Además, los proveedores no pueden evitar que los clientes modifiquen recursos de aplicaciones críticas ni pueden bloquear el acceso a la propiedad intelectual que conforma una aplicación. Las aplicaciones administradas de Azure proporcionan una solución para estos problemas. 

## <a name="advantages-of-managed-applications"></a>Ventajas de las aplicaciones administradas

Las aplicaciones administradas de Azure proporcionan un ecosistema que permite que los proveedores hagan que los servicios de PaaS o SaaS estén disponibles como aplicaciones independientes. Los clientes implementan aplicaciones administradas en sus suscripciones, pero los proveedores pueden administrarlas. Permite a los proveedores que facturan a los clientes mediante el sistema de facturación de Azure utilizar plantillas para administrar el ciclo de vida de las aplicaciones implementadas. Por otra parte, permite a los clientes adquirir automáticamente actualizaciones y pagar tanto el soporte técnico como el mantenimiento. No tienen que mantener o actualizar la aplicación ellos mismos ni diagnosticar y solucionar los problemas cuando se produce un error en la aplicación.

Dicho ecosistema de Azure no solo beneficiaría a los proveedores de PaaS y SaaS, sino también a los equipos de las plataformas centrales corporativas y a los integradores de sistemas que desean empaquetar y revender sus soluciones.

## <a name="how-managed-applications-work"></a>Funcionamiento de las aplicaciones administradas
Hay dos posibilidades cuando se trabaja con aplicaciones administradas:

1. El proveedor o fabricante de software independiente (ISV) que crea una aplicación administrada y hace que esté disponible para un uso mayor. 
2. El cliente o el usuario que desea crear y usar la aplicación publicada. 

En este artículo se tratan ambas posibilidades. En primer lugar, permite comprender el funcionamiento de las aplicaciones administradas. 

Una aplicación administrada es similar a una plantilla de solución de Marketplace, aunque hay una diferencia importante. En una aplicación administrada, los recursos se aprovisionan en un grupo de recursos que está administrado por el ISV o proveedor. El grupo de recursos está presente en la suscripción del cliente, pero un usuario, grupo de usuarios o aplicación del inquilino del ISV tiene acceso al grupo de recursos. Para administrar y mantener la aplicación, la identidad del proveedor se agrega a los roles de propietario, colaborador, lector, o cualquier otro rol integrado en Active Directory. 

## <a name="key-concepts"></a>Conceptos clave

### <a name="managed-resource-group"></a>Grupo de recursos administrado
El grupo de recursos en el que se crean todos los recursos de Azure que se aprovisionan en la plantilla. Por ejemplo, si el dispositivo crea una cuenta de almacenamiento, este grupo de recursos contiene el recurso de la misma. No contiene el recurso del dispositivo.

### <a name="appliance-package"></a>Paquete de aplicación
El publicador crea un paquete que contiene los archivos de plantilla y el archivo createUIDefinition. En concreto, contiene los siguientes archivos:

- **applianceMainTemplate.json**: archivo de plantilla que define todos los recursos que aprovisiona el dispositivo. Este archivo es un archivo de plantilla normal que se usa para crear recursos.

- **MainTemplate.json**: archivo de plantilla solo contiene el recurso del dispositivo (Microsoft.Solutions/appliances). Una propiedad clave definida en este recurso es ManagedResourceGroupId. Esta propiedad indica qué grupo de recursos se usa para hospedar los recursos reales definidos en applianceMainTemplate.json.

- **createUIDefinition.json**: este archivo describe cómo se representa la interfaz de usuario necesaria para los parámetros definidos en la plantilla.

### <a name="authorization"></a>Autorización
El publicador necesita especificar los permisos que requiere el proveedor para administrar los recursos en nombre del cliente. Este permiso se aplica al grupo de recursos administrado. Establezca los valores siguientes:

- **PrincipalID**: el identificador de Azure AD del usuario, grupo o aplicación que se usa para conceder acceso al grupo de recursos administrado. Este identificador pertenece al inquilino del publicador.

- **RoleDefinitionID**: el identificador de Azure AD del rol asignado al identificador de la entidad de seguridad anterior. Puede ser cualquiera de los roles RBAC integrados del inquilinos del publicador.

## <a name="next-steps"></a>Pasos siguientes

* Para comprender la experiencia del proveedor, consulte [Creación y publicación de una aplicación administrada de Azure](managed-application-publishing.md).
* Para comprender la experiencia del consumidor, consulte [Uso de una aplicación administrada de Azure](managed-application-consumption.md).
* Para crear un archivo de definición de la interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
