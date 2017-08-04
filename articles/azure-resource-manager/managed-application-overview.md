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
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 51732474e7983827988f950d344d36745564dfd2
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---

# <a name="azure-managed-applications-overview"></a>Introducción a las aplicaciones administradas de Azure

Azure permite que los proveedores ofrezcan soluciones a clientes de todo el mundo. Microsoft Azure Marketplace es una galería que consta de cientos de plantillas complejas de múltiples recursos de proveedores propios y de terceros. Los clientes pueden implementarlas e iniciarlas mediante aplicaciones PaaS y SaaS en cuestión de minutos. Aunque proporciona una manera excelente de implementar rápidamente cualquier oferta, el cliente sigue siendo responsable de mantener y actualizar la solución. En cuanto a los proveedores, no hay forma alguna de cobrar a los clientes el uso de una aplicación, más allá de la facturación de las imágenes de máquinas virtuales. Además, los proveedores no pueden evitar que los clientes modifiquen recursos de aplicaciones críticas ni pueden bloquear el acceso a la propiedad intelectual que conforma una aplicación. Las aplicaciones administradas de Azure proporcionan una solución para estos problemas. 

Una aplicación administrada es similar a una plantilla de solución de Marketplace, aunque hay una diferencia importante. En una aplicación administrada, los recursos se aprovisionan en un grupo de recursos que está administrado por el proveedor. El grupo de recursos está presente en la suscripción del cliente, pero una identidad en el inquilino de proveedores tiene acceso al grupo de recursos.

## <a name="advantages-of-managed-applications"></a>Ventajas de las aplicaciones administradas

Las aplicaciones administradas permiten que los proveedores de servicios administrados (MSP), los fabricantes de software independiente (ISV) y los equipos centrales de TI de la empresa ofrezcan soluciones mediante Marketplace o el catálogo de servicios. A pesar de que los clientes implementan estas aplicaciones administradas en sus suscripciones, no es necesario que las mantengan, actualicen ni atiendan. Los proveedores administran estas aplicaciones y brindan soporte técnico para ellas. Por lo tanto, no es necesario que los clientes desarrollen un conocimiento ni dominio específico para administrar estas aplicaciones. Permite que los clientes adquieran de inmediato las actualizaciones de aplicación sin tener que preocuparse de solucionar ni diagnosticar problemas con la administración.

En el caso de los proveedores, las aplicaciones administradas crear un canal no solo para vender infraestructura y software mediante Marketplace, sino también una forma de combinar compatibilidad operacional y de servicios para los clientes de Azure. Permite que los proveedores facturen a los clientes mediante el sistema de facturación de Azure y usen plantillas para administrar el ciclo de vida de las aplicaciones implementadas. Estas soluciones son autónomas y se entregan selladas al cliente, para que los proveedores puedan brindar un servicio de alta calidad. Este enfoque no solo beneficia a los proveedores de PaaS y SaaS, sino también a los equipos de las plataformas centrales corporativas y a los integradores de sistemas que desean empaquetar y revender sus soluciones.

## <a name="managed-application-types"></a>Tipos de aplicaciones administradas
Las aplicaciones administradas de Windows tienen dos versiones: catálogo de servicios y Marketplace.
 
### <a name="service-catalog"></a>Catálogo de servicios  

El catálogo de servicios permite que las organizaciones creen un catálogo de las soluciones aprobadas de Azure para su uso por parte de los usuarios de esa organización. Mantener un catálogo de soluciones de ese tipo resulta útil para los equipos centrales de TI en las empresas. Permite que estos equipos garanticen el cumplimiento con ciertos estándares organizacionales mientras ofrecen soluciones excelentes para la organización. Pueden controlar, actualizar y mantener estas aplicaciones. El catálogo de servicios permite que los empleados de la organización detecten fácilmente el amplio conjunto de aplicaciones que el departamento de TI recomienda y aprueba. Los clientes solo ven las aplicaciones administradas del catálogo de servicios que han creado o las aplicaciones administradas que otros usuarios de la organización compartieron con ellos.
 
Para información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
 
Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).
 
### <a name="marketplace"></a>Marketplace

Las aplicaciones administradas están disponibles mediante Marketplace en Azure Portal. Una vez que el proveedor las publica, estas aplicaciones quedan a disposición para que la use cada persona, ya sea dentro o fuera de la organización. Este enfoque permite que los MSP, ISV e integradores de sistemas (SI) ofrezcan sus soluciones a todos los clientes de Azure. Los clientes obtienen el beneficio de aprovechar esas soluciones complejas sin tener que invertir para comprender y mantener las soluciones. Actualmente, el publicador puede hacer que su oferta esté disponible como una aplicación administrada o como una plantilla de solución no administrada. El componente principal de la publicación de una aplicación administrada incluye los archivos de plantilla, que describen los recursos que se aprovisionan, y el archivo de definición de la UI, que describe cómo se muestran en el portal las entradas requeridas para aprovisionar estos recursos. Los archivos requeridos se empaquetan en un archivo .ZIP y se cargan mediante el portal de publicación.
 
Para información sobre cómo publicar aplicaciones administradas en Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).

Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).

## <a name="key-concepts"></a>Conceptos clave

### <a name="managed-resource-group"></a>Grupo de recursos administrado
El grupo de recursos en el que se crean todos los recursos de Azure que se aprovisionan en la plantilla. Por ejemplo, si el dispositivo crea una cuenta de almacenamiento, este grupo de recursos contiene el recurso de la misma. No contiene el recurso del dispositivo.

### <a name="appliance-package"></a>Paquete de aplicación
El publicador crea un paquete que contiene los archivos de plantilla y el archivo createUIDefinition. En concreto, contiene los siguientes archivos:

- **applianceMainTemplate.json**: archivo de plantilla que define todos los recursos que aprovisiona el dispositivo. Este archivo es un archivo de plantilla normal que se usa para crear recursos.

- **MainTemplate.json**: archivo de plantilla solo contiene el recurso del dispositivo (Microsoft.Solutions/appliances). Una propiedad clave definida en este recurso es ManagedResourceGroupId. Esta propiedad indica qué grupo de recursos se usa para hospedar los recursos reales definidos en applianceMainTemplate.json.

- **applianceCreateUIDefinition.json**: este archivo describe cómo se representa la interfaz de usuario necesaria para los parámetros definidos en la plantilla.

### <a name="authorization"></a>Autorización
El publicador necesita especificar los permisos que requiere el proveedor para administrar los recursos en nombre del cliente. Este permiso se aplica al grupo de recursos administrado. Establezca los valores siguientes:

- **PrincipalID**: el identificador de Azure AD del usuario, grupo o aplicación que se usa para conceder acceso al grupo de recursos administrado. Este identificador pertenece al inquilino del publicador.

- **RoleDefinitionID**: el identificador de Azure AD del rol asignado al identificador de la entidad de seguridad anterior. Puede ser cualquiera de los roles RBAC integrados del inquilinos del publicador. Para más información, consulte [Roles integrados para el control de acceso basado en rol de Azure](../active-directory/role-based-access-built-in-roles.md).

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre cómo publicar aplicaciones administradas en Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
* Para información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
* Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).
* Para crear un archivo de definición de la interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
