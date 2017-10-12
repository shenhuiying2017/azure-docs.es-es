---
title: Recursos, roles y control de acceso en Azure Application Insights | Microsoft Docs
description: "Propietarios, colaboradores y lectores de las perspectivas de su organización."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: bwren
ms.openlocfilehash: c979a8bfbeecacc7c0bbc112e02a4b68e874c219
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Recursos, roles y control de acceso en Application Insights
Puede controlar quién tiene acceso de lectura y actualización a sus datos en Azure [Application Insights][start], mediante el uso del [control de acceso basado en rol de Microsoft Azure](../active-directory/role-based-access-control-configure.md).

> [!IMPORTANT]
> Asigne acceso a los usuarios para el **grupo de recursos o la suscripción** a los que pertenece el recurso de aplicación, no para el propio recurso. Asigne el rol de **colaborador de componentes de Application Insights** . De esta forma, se garantiza el control de acceso uniforme a las alertas y las pruebas web junto con su recurso de aplicación. [Más información](#access).
> 
> 

## <a name="resources-groups-and-subscriptions"></a>Recursos, grupos y suscripciones
En primer lugar, vamos a ver algunas definiciones:

* **Recurso** : una instancia de un servicio de Microsoft Azure. El recurso de Application Insights recopila, analiza y muestra los datos de telemetría enviados desde su aplicación.  Otros tipos de recursos de Azure son aplicaciones web, bases de datos y máquinas virtuales.
  
    Para ver todos los recursos, vaya a [Azure Portal][portal], inicie sesión y haga clic en Todos los recursos. Para buscar un recurso, escriba parte del nombre en el campo de filtro.
  
    ![Enumeración de los recursos de Azure](./media/app-insights-resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Grupo de recursos**][group]: cada recurso pertenece a un grupo. Un grupo es una forma cómoda de administrar los recursos relacionados, especialmente de cara al control de acceso. Por ejemplo, en un grupo de recursos podría colocar una aplicación web, un recurso de Application Insights para supervisar la aplicación y un recurso de almacenamiento para mantener los datos exportados.

    ![Elija Examinar, Grupos de recursos, y luego elija un grupo.](./media/app-insights-resources-roles-access-control/11-group.png)

* [**Suscripción**](https://manage.windowsazure.com): para usar Application Insights u otros recursos de Azure, inicie sesión en una suscripción de Azure. Cada grupo de recursos pertenece a una suscripción de Azure, donde elije su paquete de precios y, si se trata de una suscripción de la organización, selecciona los miembros y sus permisos de acceso.
* [**Cuenta de Microsoft**][account]: el nombre de usuario y la contraseña que usa para iniciar sesión en suscripciones de Microsoft Azure, XBox Live, Outlook.com y otros servicios de Microsoft.

## <a name="access"></a> Control de acceso para el grupo de recursos
Es importante comprender que, además del recurso que ha creado para su aplicación, también hay recursos ocultos independientes para las alertas y las pruebas web. Estos están conectados al mismo [grupo de recursos](#resource-group) que la aplicación. También podría haber colocado ahí otros servicios de Azure, como sitios web o almacenamiento.

![Recursos en Application Insights](./media/app-insights-resources-roles-access-control/00-resources.png)

Para controlar el acceso a estos recursos, se recomienda por lo tanto lo siguiente:

* Controlar el acceso en el nivel de **grupo de recursos o suscripción** .
* Asignar el rol de **colaborador de componentes de Application Insights** a los usuarios. Esto les permite editar pruebas web, alertas y recursos de Application Insights, sin proporcionar acceso a otros servicios en el grupo.

## <a name="to-provide-access-to-another-user"></a>Para proporcionar acceso a otro usuario, siga estos pasos:
Debe tener derechos de propietario a la suscripción o al grupo de recursos.

El usuario debe tener una [cuenta de Microsoft][account] o tener acceso a su [cuenta de Microsoft de organización](../active-directory/sign-up-organization.md). Puede proporcionar acceso individual y también a grupos de usuarios definidos en Active Directory de Azure.

#### <a name="navigate-to-the-resource-group"></a>Desplácese al grupo de recursos.
Agregue ahí el usuario.

![En la hoja de recursos de la aplicación, abra Essentials, abra el grupo de recursos y seleccione Configuración/Usuarios. Haga clic en Agregar.](./media/app-insights-resources-roles-access-control/01-add-user.png)

O bien, puede ascender otro nivel y agregar el usuario a la suscripción.

#### <a name="select-a-role"></a>Seleccione un rol.
![Seleccione un rol para el nuevo usuario.](./media/app-insights-resources-roles-access-control/03-role.png)

| Rol | En el grupo de recursos: |
| --- | --- |
| Propietario |Puede cambiar cualquier cosa, incluido el acceso de usuario. |
| Colaborador |Puede editar cualquier cosa, incluidos todos los recursos. |
| colaborador de componentes de Application Insights |Puede editar alertas, pruebas web y recursos de Application Insights. |
| Lector |Puede ver, pero no puede cambiar nada. |

La "edición" incluye la creación, la eliminación y la actualización:

* Recursos
* Pruebas web
* Alertas
* Exportación continua

#### <a name="select-the-user"></a>Seleccione el usuario.

Si el usuario de su elección no está en el directorio, puede invitar a cualquier persona con una cuenta Microsoft.
(Si se usan servicios como Outlook.com, OneDrive, Windows Phone o XBox Live, se tiene una cuenta Microsoft).

## <a name="related-content"></a>Contenido relacionado

* [Control de acceso basado en rol de Azure](../active-directory/role-based-access-control-configure.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: app-insights-overview.md
