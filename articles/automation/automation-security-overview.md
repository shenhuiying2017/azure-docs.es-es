---
title: "Introducción a la autenticación en Azure Automation | Microsoft Docs"
description: "Este artículo proporciona información general sobre la seguridad de Automation y los diferentes métodos de autenticación disponibles para las cuentas de automatización de Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "seguridad de automatización, automatización segura; autenticación de automatización"
ms.assetid: 4a6bc2f5-c5a2-4dfb-b10d-7950d750dee8
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: magoedte
ROBOTS: NOINDEX
ms.openlocfilehash: 99882c1ff7517beec2ca827c63620f773d7d07c3
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/10/2018
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Introducción a la autenticación en Azure Automation  
Azure Automation le permite automatizar tareas en recursos de Azure, locales y de otros proveedores de servicios en la nube como Amazon Web Services (AWS).  Para que un runbook realice las acciones necesarias, debe tener permiso de acceso seguro a los recursos con los derechos mínimos necesarios en la suscripción.

En este artículo se tratarán los distintos escenarios de autenticación admitidos por Azure Automation y se le mostrará cómo empezar a trabajar según los entornos que necesite administrar.  

## <a name="automation-account-overview"></a>Información general sobre las cuentas de Automation
Cuando inicia Azure Automation por primera vez, debe crear al menos una cuenta de Automation. Las cuentas de Automation le permiten aislar los recursos de Automation (Runbooks, recursos, configuraciones) desde los recursos contenidos en otras cuentas de Automation. Puede usar cuentas de Automation para separar los recursos en entornos lógicos independientes. Por ejemplo, puede usar una cuenta para desarrollo, otra para producción y otra para su entorno local.  Una cuenta de Azure Automation es diferente de su cuenta Microsoft o de las cuentas creadas en su suscripción de Azure.

Los recursos de Automation de cada cuenta de Automation están asociados con una sola región de Azure, pero las cuentas de Automation pueden administrar recursos en su suscripción. El principal motivo para crear cuentas de Automation en distintas regiones sería si tiene directivas que requieren que los datos y recursos se aíslen en una región específica.

Todas las tareas que realice con recursos mediante Azure Resource Manager y los cmdlets de Azure en Azure Automation deben autenticarse en Azure con una autenticación basada en credenciales de identidad organizativa de Azure Active Directory.  La autenticación basada en certificados era el método de autenticación original en Azure clásico, pero era complicada de configurar.  En 2014, se incorporó la autenticación en Azure con un usuario de Azure AD, no solo para simplificar el proceso de configuración de una cuenta de autenticación, sino también para admitir la posibilidad de autenticar de forma no interactiva en Azure con una sola cuenta de usuario que funcione con Azure Resource Manager y los recursos clásicos.   

Actualmente, al crear una nueva cuenta de Automation en el portal de Azure, se crea automáticamente:

* Una cuenta de ejecución que crea a una nueva entidad de servicio en Azure Active Directory y un certificado, y asigna el control de acceso basado en rol (RBAC) Colaborador, que se utilizará para administrar los recursos de Resource Manager mediante runbooks.
* Una cuenta de ejecución clásica mediante la carga de un certificado de administración, que se usa para administrar recursos clásicos mediante runbooks.  

El control de acceso basado en rol está disponible en Azure Resource Manager para conceder las acciones permitidas a una cuenta de usuario de Azure AD y a una cuenta de ejecución, y para autenticar dicha entidad de servicio.  Para más información que le ayude a desarrollar su modelo de administración de permisos de Automatización, consulte el [artículo Control de acceso basado en rol en Azure Automation](automation-role-based-access-control.md) .  

Los Runbooks que se ejecutan en un trabajo Hybrid Runbook Worker en su centro de datos o en servicios de proceso en AWS no pueden usar el mismo método que se usa normalmente para los Runbooks que se autentican en recursos de Azure.  Esto se debe a que esos recursos se ejecutan fuera de Azure y, por lo tanto, requieren sus propias credenciales de seguridad definidas en Automatización para la autenticación en los recursos a los que tienen acceso localmente.  

## <a name="authentication-methods"></a>Métodos de autenticación
La tabla siguiente resume los diferentes métodos de autenticación para cada entorno admitido por Azure Automation y el artículo que describe cómo configurar la autenticación para sus Runbooks.

| Método | Environment | Artículo |
| --- | --- | --- |
| Cuenta de usuario de Azure AD |Azure Resource Manager y Azure clásico |[Autenticación de Runbooks con una cuenta de usuario de Azure AD](automation-create-aduser-account.md) |
| Cuenta de ejecución de Azure |Administrador de recursos de Azure |[Autenticación de Runbooks con una cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md) |
| Cuenta de ejecución de Azure clásico |Azure clásico |[Autenticación de Runbooks con una cuenta de ejecución de Azure](automation-sec-configure-azure-runas-account.md) |
| Autenticación de Windows |Centro de datos local |[Autenticación de Runbooks para Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) |
| Credenciales de AWS |Amazon Web Services |[Autenticación de Runbooks con Amazon Web Services (AWS)](automation-config-aws-account.md) |
