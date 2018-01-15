---
title: "Introducción a las aplicaciones administradas de Azure | Microsoft Docs"
description: Describe los conceptos de las aplicaciones administradas de Azure
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: a26cfc632dacb41435b3755409d3e91630f5f3eb
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="azure-managed-applications-overview"></a>Introducción a las aplicaciones administradas de Azure

Las aplicaciones administradas de Azure le permiten ofrecer soluciones en la nube que los consumidores pueden implementar y utilizar fácilmente. Puede implementar la infraestructura y proporcionar soporte continuo. Para poner a disposición de todos los clientes una aplicación administrada, publíquela en Azure Marketplace. Si quiere que esté disponible solo para los usuarios de su organización, publíquela en un catálogo interno. 

Una aplicación administrada es similar a una plantilla de solución de Marketplace, aunque hay una diferencia importante. En una aplicación administrada, los recursos se aprovisionan en un grupo de recursos que está administrado por el editor de la aplicación. El grupo de recursos está presente en la suscripción del consumidor, pero una identidad en el inquilino del editor tiene acceso al grupo de recursos. Como editor, puede especificar el costo del soporte técnico continuado de la solución.

## <a name="advantages-of-managed-applications"></a>Ventajas de las aplicaciones administradas

Las aplicaciones administradas reducen las barreras que existen para los consumidores que utilizan sus soluciones. No es necesario que tengan conocimientos de la infraestructura de nube para usar la solución. Los consumidores tienen acceso limitado a los recursos críticos. No necesitan preocuparse de cometer un error al administrarla. 

Las aplicaciones administradas le permiten establecer una relación continuada con los consumidores. Defina los términos de administración de la aplicación y todos los cargos se controlarán a través de la facturación de Azure.

A pesar de que los clientes implementan estas aplicaciones administradas en sus suscripciones, no es necesario que las mantengan, actualicen ni atiendan. Puede asegurarse de que todos los clientes usan versiones aprobadas. No es necesario que los clientes desarrollen un conocimiento ni dominio específico para administrar estas aplicaciones. Los clientes adquieren de inmediato las actualizaciones de las aplicaciones sin tener que preocuparse de solucionar ni diagnosticar problemas de estas. 

En el caso de los equipos de TI, las aplicaciones administradas le permiten ofrecer soluciones aprobadas previamente a los usuarios de la organización. Asegúrese de que estas soluciones son compatibles con los estándares de la organización.

## <a name="types-of-managed-applications"></a>Tipos de aplicaciones administradas

Puede publicar la aplicación administrada externa o internamente.

![Publicación interna o externa](./media/overview/manage_app_options.png)

### <a name="service-catalog"></a>Catálogo de servicios

El catálogo de servicios es un catálogo interno de soluciones aprobadas para los usuarios de una organización. Ellos pueden usar el catálogo para garantizar el cumplimiento de ciertos estándares de la organización al tiempo que ofrecen soluciones excelentes para la organización. Los empleados pueden usar el catálogo para detectar fácilmente el amplio conjunto de aplicaciones que el departamento de TI recomienda y aprueba. También pueden ver las aplicaciones administradas que otras personas de su organización comparten con ellos.

Para más información sobre cómo publicar una aplicación administrada del catálogo de servicios, consulte [Creación de la aplicación de catálogo de servicios](publish-service-catalog-app.md).

### <a name="marketplace"></a>Marketplace

Los proveedores que deseen facturar sus servicios pueden disponer de una aplicación administrada a través de Azure Marketplace. Después de que el proveedor publica una aplicación, esta está disponible para los usuarios de fuera de la organización. Con este método, los proveedores de servicios administrados (MSP), los fabricantes de software independientes (ISV) y los integradores del sistema (SIs) pueden ofrecer sus soluciones a todos los clientes de Azure.

Para más información sobre cómo publicar una aplicación administrada en Azure Marketplace, consulte [Creación de la aplicación de Marketplace](publish-marketplace-app.md).

## <a name="resource-groups-for-managed-applications"></a>Grupos de recursos para aplicaciones administradas

Normalmente, los recursos de una aplicación administrada residen en dos grupos de recursos. El consumidor administra un grupo de recursos y el editor administra el otro. Al definir la aplicación administrada, el editor especifica los niveles de acceso. La siguiente imagen muestra un escenario en el que el editor solicita el rol de propietario del grupo de recursos administrados. El editor puso un bloqueo de solo lectura en este grupo de recursos para el consumidor.

![Acceso al grupo de recursos](./media/overview/access.png)

### <a name="application-resource-group"></a>Grupo de recursos de la aplicación

Este grupo de recursos mantiene la instancia de la aplicación administrada. Este grupo de recursos solo puede contener un recurso. El tipo de recurso de la aplicación administrada es **Microsoft.Solutions/applications**.

El consumidor tiene acceso total al grupo de recursos y lo utiliza para administrar el ciclo de vida de la aplicación administrada.

### <a name="managed-resource-group"></a>Grupo de recursos administrado

Este grupo de recursos contiene todos los recursos que requiere la aplicación administrada. Por ejemplo, este grupo de recursos contiene las máquinas virtuales, cuentas de almacenamiento y redes virtuales para la solución. El consumidor tiene acceso limitado a este grupo de recursos ya que no puede administrar los recursos individuales de la aplicación administrada. El acceso del editor a este grupo de recursos se corresponde con el rol especificado en la definición de la aplicación administrada. Por ejemplo, el editor puede solicitar el rol de propietario o colaborador para este grupo de recursos.

Cuando el consumidor elimina la aplicación administrada, también se elimina el grupo de recursos administrados.

## <a name="next-steps"></a>pasos siguientes

* Para obtener una introducción sobre cómo definir e implementar una aplicación administrada, consulte [Creación e implementación de una aplicación administrada de Azure con la CLI de Azure](managed-apps-quickstart-cli.md)
* Para más información sobre cómo publicar una aplicación interna, consulte [Creación de la aplicación de catálogo de servicios](publish-service-catalog-app.md).
* Para información sobre cómo publicar aplicaciones administradas en Marketplace, consulte [Creación de la aplicación de Marketplace](publish-marketplace-app.md).
