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
ms.date: 09/19/2017
ms.author: gauravbh
ms.openlocfilehash: 96b07bb3b923a5120e3d43c3fa60b3b1308010b3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-managed-applications-overview"></a>Introducción a las aplicaciones administradas de Azure

Los proveedores que usan Azure pueden ofrecer soluciones a clientes de todo el mundo. Azure Marketplace es una galería que consta de cientos de plantillas complejas de múltiples recursos de proveedores propios y de terceros. En minutos, los clientes pueden implementar y empezar a usar aplicaciones de plataforma como servicio (PaaS) y de software como servicio (SaaS). 

Aunque Marketplace proporciona una manera excelente de que los clientes implementen rápidamente cualquier oferta, siguen siendo responsables de mantener y actualizar la solución. Más allá de la facturación de las imágenes de máquinas virtuales, los proveedores no pueden cobrar a los clientes por el uso de una aplicación. Además, los proveedores no pueden evitar que los clientes modifiquen los recursos de aplicaciones esenciales. Los proveedores tampoco pueden impedir el acceso a la propiedad intelectual que constituye una aplicación. Las aplicaciones administradas de Azure proporcionan soluciones para estos problemas. 

Una aplicación administrada es similar a una plantilla de solución de Marketplace, aunque hay una diferencia importante. En una aplicación administrada, los recursos se aprovisionan en un grupo de recursos que está administrado por el proveedor. El grupo de recursos está presente en la suscripción del cliente, pero una identidad en el inquilino del proveedor tiene acceso al grupo de recursos.

## <a name="advantages-of-managed-applications"></a>Ventajas de las aplicaciones administradas

Los proveedores de servicios administrados (MSP), los fabricantes de software independiente (ISV) y los equipos centrales de TI de la empresa pueden usar las aplicaciones administradas para ofrecer soluciones mediante Marketplace o el catálogo de servicios. A pesar de que los clientes implementan estas aplicaciones administradas en sus suscripciones, no es necesario que las mantengan, actualicen ni atiendan. Dado que los proveedores administran las aplicaciones y ofrecen soporte técnico, no es necesario que los clientes desarrollen un conocimiento ni dominio específico para administrar estas aplicaciones. Los clientes pueden adquirir de inmediato las actualizaciones de las aplicaciones sin tener que preocuparse de solucionar ni diagnosticar problemas de estas.

Para los fabricantes y proveedores, las aplicaciones administradas crean un canal para vender la infraestructura y el software a través de Marketplace. Las aplicaciones administradas también proporcionan una manera de asociar compatibilidad operativa y servicios a los clientes de Azure. Los proveedores pueden facturar a los clientes con los sistemas de facturación de Azure. Puede utilizar plantillas para administrar el ciclo de vida de las aplicaciones implementadas. Estas soluciones son autónomas y se entregan selladas al cliente, para que los proveedores puedan brindar un servicio de alta calidad. Este enfoque beneficia a los proveedores de PaaS y SaaS. También ayuda a los equipos corporativos de la plataforma central y a los integradores de sistemas (SI) que desean empaquetar y vender sus soluciones.

## <a name="managed-application-types"></a>Tipos de aplicaciones administradas
Las aplicaciones administradas de Azure son de dos tipos: del catálogo de servicios y de Marketplace.
 
### <a name="service-catalog"></a>Catálogo de servicios  

Con el catálogo de servicios, los clientes pueden crear un catálogo de las soluciones aprobadas para Azure a fin de que los usuarios de esa organización las usen. Mantener un catálogo de soluciones de ese tipo resulta útil para los equipos centrales de TI en las empresas. Pueden usar el catálogo para garantizar el cumplimiento de ciertos estándares organizativos al tiempo que ofrecen soluciones excelentes para la organización. Pueden controlar, actualizar y mantener estas aplicaciones. Los empleados pueden usar el catálogo para detectar fácilmente el amplio conjunto de aplicaciones que el departamento de TI recomienda y aprueba. Los clientes ven las aplicaciones administradas del catálogo de servicios administrados que han creado. También pueden ver las aplicaciones administradas que otras personas de su organización comparten con ellos.
 
Para información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
 
Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).
 
### <a name="marketplace"></a>Marketplace

Las aplicaciones administradas están disponibles mediante Marketplace en Azure Portal. Una vez que el proveedor las publica, quedan a disposición para que la use cada persona, ya sea dentro o fuera de la organización. Con este enfoque, los MSP, ISV e integradores de sistemas (SI) pueden ofrecer sus soluciones a todos los clientes de Azure. Los clientes obtienen el beneficio de aprovechar esas soluciones complejas sin tener que invertir para comprender y mantener las soluciones. 

Actualmente, los publicadores pueden hacer que sus ofertas estén disponibles como una aplicación administrada o como una plantilla de solución no administrada. Los componentes principales de la publicación de una aplicación administrada incluyen el archivo de plantilla y el archivo de definición de la interfaz de usuario. El archivo de plantilla describe los recursos que se han aprovisionado. El archivo de definición de interfaz de usuario describe cómo se muestran en el portal las entradas necesarias para el aprovisionamiento de estos recursos. Los archivos requeridos se empaquetan en un archivo .ZIP y se cargan mediante el portal de publicación.
 
Para información sobre cómo publicar una aplicación administrada en Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).

Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).

## <a name="key-concepts"></a>Conceptos clave

### <a name="managed-resource-group"></a>Grupo de recursos administrado
El grupo de recursos administrado es donde se crean todos los recursos de Azure que se aprovisionan en la plantilla. Por ejemplo, si la aplicación se usa para crear una cuenta de almacenamiento, este grupo de recursos contiene el recurso de la misma. No contiene el recurso de ka aokucacuón.

### <a name="application-package"></a>Paquete de aplicación
El editor crea un paquete que contiene el archivo de plantilla y el archivo createUIDefinition. En concreto, contiene los siguientes archivos:

- **mainTemplate.json**: archivo de plantilla que define todos los recursos que aprovisiona la aplicación. Este archivo es un archivo de plantilla normal que se usa para crear recursos.

- **createUIDefinition.json**: este archivo describe cómo se representa la interfaz de usuario necesaria para los parámetros definidos en la plantilla.

### <a name="authorization"></a>Autorización
El publicador debe especificar los permisos que requiere el proveedor para administrar los recursos en nombre del cliente. Este permiso se aplica al grupo de recursos administrado. Establezca los valores siguientes:

- **PrincipalID**: el identificador de Azure Active Directory (AD) del usuario, grupo o aplicación que se usa para conceder acceso al grupo de recursos administrado. Este identificador pertenece al inquilino del publicador.

- **RoleDefinitionID**: el identificador de Azure AD del rol asignado al identificador de la entidad de seguridad anterior. Puede ser cualquiera de los roles integrados de control de acceso basado en roles en el inquilino del publicador. Para más información, consulte [Roles integrados para el control de acceso basado en rol de Azure](../active-directory/role-based-access-built-in-roles.md).

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre cómo publicar aplicaciones administradas en Marketplace, consulte [Aplicaciones administradas de Azure en Marketplace](managed-application-author-marketplace.md).
* Para información sobre cómo usar una aplicación administrada de Marketplace, consulte [Uso de aplicaciones administradas de Azure en Marketplace](managed-application-consume-marketplace.md).
* Para información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación y publicación de una aplicación administrada del catálogo de servicios](managed-application-publishing.md).
* Para información sobre cómo usar una aplicación administrada del catálogo de servicios, consulte [Uso de una aplicación administrada del catálogo de servicios](managed-application-consumption.md).
* Para crear un archivo de definición de la interfaz de usuario, consulte [Introducción a CreateUiDefinition](managed-application-createuidefinition-overview.md).
