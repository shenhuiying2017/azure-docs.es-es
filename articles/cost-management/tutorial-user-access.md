---
title: "Asignar acceso en Administración de costos de Azure | Microsoft Docs"
description: "Asigne acceso a los datos de administración de costos con cuentas de usuario que definen los niveles de acceso para las entidades."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: a42f3b51bf6d888d0d5602887ed317c6164391ef
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="assign-access-to-cost-management-data"></a>Asignar acceso a datos de Administración de costos

La administración de entidades o usuarios proporciona los datos de administración de costos. Las cuentas de usuario de Cloudyn determinan el acceso a las *entidades* y las funciones administrativas. Existen dos tipos de acceso: administrador y usuario. A menos que lo modifique el usuario, el acceso de administrador permite un uso no restringido de todas las funciones del portal de Cloudyn, incluida la administración de usuarios y listas de destinatarios, y el acceso a la entidad raíz para todos los datos de la entidad. Con el acceso de usuario se pretende que los usuarios finales puedan ver y crear informes mediante el acceso que tienen a los datos de entidad.

Las entidades se usan para reflejar la estructura jerárquica de la organización empresarial. Identifican departamentos, divisiones y equipos de la organización en Cloudyn. La jerarquía de entidad le ayuda a realizar un seguimiento preciso de los gastos de las entidades.

Al registrar su cuenta o contrato de Azure, se creó una cuenta con permisos de administrador en Cloudyn, con la que podrá seguir todos los pasos de este tutorial. Este tutorial trata el acceso a datos de administración de costos, incluida la administración de usuarios y entidades. Aprenderá a:

> [!div class="checklist"]
> * Crear un usuario con acceso de administrador
> * Crear un usuario con acceso de usuario
> * Crear entidades



## <a name="create-a-user-with-admin-access"></a>Crear un usuario con acceso de administrador

Usted ya tiene acceso de administrador, pero puede que los compañeros de su organización también lo necesiten. En el portal de Cloudyn, haga clic en el símbolo de engranaje en la esquina superior derecha y seleccione **User Management** (Administración de usuarios). Haga clic en **Add New User** (Agregar nuevo usuario) para agregar un usuario nuevo.

Especifique la información del usuario obligatoria. Puede dejar vacío el campo de contraseña para que el usuario pueda definir una contraseña nueva cuando inicie sesión por primera vez. Si selecciona **Notify user by email** (Notificar al usuario por correo electrónico), se envía al usuario un correo de Cloudyn que contiene vínculo con información de inicio de sesión. Elija Allow User Management (Permitir administración de usuarios) para que el usuario pueda crear y modificar otros usuarios. La opción Recipient Lists Management (Administración de listas de destinatarios) permite al usuario editar listas de destinatarios.

En **User has admin access** (El usuario tiene acceso de administrador), se selecciona la entidad raíz de la organización. Deje la raíz seleccionada y guarde la información de usuario. Al seleccionar la entidad raíz, se permite al usuario tener permiso de administrador no solo para la entidad raíz del árbol, sino también para todas sus entidades secundarias.  
  ![agregar usuario nuevo con acceso de administrador](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Crear un usuario con acceso de usuario
Los usuarios típicos que necesitan acceder a datos de administración de costos, como paneles e informes, deben tener acceso de usuario para verlos. Cree un usuario nuevo con un acceso de usuario similar al que creó con acceso de administrador, con las siguientes diferencias:

- Desmarque **Allow User Management** (Permitir administración de usuarios), **Allow Recipient lists Management** (Permitir administración de listas de destinatarios) y todas las opciones de la lista **User has admin access** (El usuario tiene acceso de administrador).
- Seleccione las entidades a las que necesita acceso el usuario en la lista **User has user access** (El usuario tiene acceso de usuario).
- También puede permitir que el administrador acceda a entidades concretas, según sea necesario.

![agregar un usuario nuevo con acceso de usuario](.\media\tutorial-user-access\new-user-access.png)

Para ver un vídeo tutorial sobre cómo agregar usuarios, consulte [Adding Users to Azure Cost Management by Cloudyn](https://youtu.be/Nzn7GLahx30) (Adición de usuarios a Azure Cost Management de Cloudyn).

## <a name="create-entities"></a>Crear entidades

Cuando se define la jerarquía de entidades de costo, identificar la estructura de la organización es un procedimiento recomendado.

Al crear el árbol, considere cómo quiere o necesita ver sus costos separados por unidades de negocio, centros de costo, entornos y departamentos de ventas. El árbol de entidades de Cloudyn es flexible debido a la herencia de la entidad. Las suscripciones individuales para las cuentas de nube están vinculadas a entidades concretas. Por lo tanto, las entidades tienen varios inquilinos. Puede asignar acceso a usuarios específicos únicamente a su segmento del negocio mediante entidades. Si lo hace, los datos se mantienen aislados, incluso en grandes partes de un negocio, como subsidiarias. Y el aislamiento de los datos facilita su control.  

Cuando registró su cuenta o contrato de Azure con Cloudyn, se copiaron los datos de recursos de Azure, como el uso, el rendimiento, la facturación y los datos de etiquetas de las suscripciones a su cuenta de Cloudyn. Sin embargo, debe crear manualmente el árbol de entidades. Si omitió el registro de Azure Resource Manager, solo estarán disponibles en el portal de Cloudyn los datos de facturación y algunos informes de recursos.

En el portal de Cloudyn, haga clic en **Settings** (Configuración) en la esquina superior derecha y seleccione **Cloud Accounts** (Cuentas en la nube). Empiece con una sola entidad (raíz) y cree el árbol de entidades a partir de la raíz. A continuación se incluye un ejemplo de una jerarquía de entidad que puede ser similar a muchas organizaciones de TI una vez completado el árbol:

![árbol de entidades](.\media\tutorial-user-access\entity-tree.png)

Junto a **Entities** (Entidades), haga clic en **Add Entity** (Agregar entidad). Especifique información acerca de la persona o departamento que quiere agregar. No es necesario que los campos **Full Name** (Nombre completo) y **Email** (Correo electrónico) coincidan con usuarios existentes. Si quiere ver una lista de niveles de acceso, busque información en la ayuda para *agregar una entidad*.

![agregar entidad](.\media\tutorial-user-access\add-entity.png)

Al acabar, **guarde** la entidad.


Para ver un vídeo tutorial acerca de cómo crear una jerarquía de entidades de costo, consulte [Creating a Cost Entity Hierarchy in Azure Cost Management by Cloudyn](https://youtu.be/dAd9G7u0FmU) (Creación de una jerarquía de entidades de costo en Azure Cost Management de Cloudyn).

Si es un usuario de contrato Enterprise de Azure, vea un vídeo tutorial acerca de cómo asociar cuentas y las suscripciones a las entidades en [Connecting to Azure Resource Manager with Azure Cost Management by Cloudyn](https://youtu.be/oCIwvfBB6kk) (Conexión a Azure Resource Manager con Azure Cost Management de Cloudyn).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Crear un usuario con acceso de administrador
> * Crear un usuario con acceso de usuario
> * Crear entidades

Avance al siguiente tutorial para aprender a pronosticar gastos mediante datos históricos.

> [!div class="nextstepaction"]
> [Previsión de gastos futuros](tutorial-forecast-spending.md)
