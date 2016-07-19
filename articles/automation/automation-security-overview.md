<properties
   pageTitle="Seguridad de Automatización de Azure"
   description="Este artículo proporciona información general sobre seguridad de automatización y los diferentes métodos de autenticación disponibles para las cuentas de Automatización en Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="seguridad de automatización, automatización segura" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="magoedte" />

# Seguridad de Automatización de Azure
Automatización de Azure le permite automatizar tareas en recursos de Azure, locales y de otros proveedores de servicios en la nube como Amazon Web Services (AWS). Para que un runbook realice las acciones necesarias, debe tener permiso de acceso seguro a los recursos con los derechos mínimos necesarios en la suscripción. En este artículo se tratarán los distintos escenarios de autenticación admitidos por Automatización de Azure y se le mostrará cómo empezar a trabajar según los entornos que necesite administrar.

## Información general sobre las cuentas de Automatización
Cuando inicia Automatización de Azure por primera vez, debe crear al menos una cuenta de Automatización. Las cuentas de Automatización le permiten aislar los recursos de Automatización (Runbooks, recursos, configuraciones) desde los recursos contenidos en otras cuentas de Automatización. Puede usar cuentas de Automatización para separar los recursos en entornos lógicos independientes. Por ejemplo, puede usar una cuenta para desarrollo, otra para producción y otra para su entorno local. Una cuenta de Automatización de Azure es diferente de su cuenta Microsoft o de las cuentas creadas en su suscripción de Azure.

Los recursos de Automatización de cada cuenta de Automatización están asociados con una sola región de Azure, pero las cuentas de Automatización pueden administrar recursos en cualquier región. El principal motivo para crear cuentas de Automatización en distintas regiones sería si tiene directivas que requieren que los datos y recursos se aíslen en una región específica.

>[AZURE.NOTE]A las cuentas de automatización y los recursos que contienen, que se crean en el Portal de Azure, no se puede acceder desde el Portal de Azure clásico. Si desea administrar estas cuentas o sus recursos con Windows PowerShell, debe usar los módulos del Administrador de recursos de Azure.

Todas las tareas que realice con recursos mediante Azure Resource Manager (ARM) y los cmdlets de Azure en Automatización de Azure deben autenticarse en Azure con una autenticación basada en credenciales de identidad organizativa de Azure Active Directory. La autenticación basada en certificados era el método de autenticación original para el modo de Administración de servicios de Azure (ASM), pero era complicada de configurar. En 2014, se incorporó la autenticación en Azure con un usuario de Azure AD, no solo para simplificar el proceso de configuración de una cuenta de autenticación, sino también para admitir la posibilidad de autenticar de forma no interactiva en Azure con una sola cuenta de usuario que funcione con los modos ASM y ARM.

Hemos lanzado recientemente otra actualización por la que, ahora, creamos automáticamente un objeto de entidad de servicio de Azure AD cuando se crea la cuenta de Automatización. Esto se conoce como una cuenta de ejecución de Azure y es el método de autenticación predeterminado para la automatización de Runbooks con Azure Resource Manager.

En el modo ARM dispone de control de acceso basado en roles para conceder las acciones permitidas a una cuenta de usuario de Azure AD y una entidad de servicio, y autenticar esa entidad de servicio. Lea el artículo [Control de acceso basado en rol en Automatización de Azure](../automation/automation-role-based-access-control.md) para más información de ayuda para desarrollar su modelo de administración de permisos de Automatización.

Los Runbooks que se ejecutan en un trabajo Hybrid Runbook Worker en su centro de datos o en servicios de proceso en AWS no pueden usar el mismo método que se usa normalmente para los Runbooks que se autentican en recursos de Azure. Esto se debe a que esos recursos se ejecutan fuera de Azure y, por lo tanto, requieren sus propias credenciales de seguridad definidas en Automatización para la autenticación en los recursos a los que tendrán acceso localmente.

## Métodos de autenticación

La tabla siguiente resume los diferentes métodos de autenticación para cada entorno admitido por Automatización de Azure y el artículo que describe cómo configurar la autenticación para sus Runbooks.

Método | Environment | Artículo
----------|----------|----------
Cuenta de usuario de Azure AD | Administración de recursos de Azure y Administrador de servicios de Azure | [Autenticación de Runbooks con una cuenta de usuario de Azure AD](../automation/automation-sec-configure-aduser-account.md)
Objeto de entidad de servicio de Azure AD | Administrador de recursos de Azure | [Autenticación de Runbooks con una cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md)
Autenticación de Windows | Centro de datos local | [Autenticación de Runbooks para Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md)
Credenciales de AWS | Amazon Web Services | [Autenticación de Runbooks con Amazon Web Services (AWS)](../automation/automation-sec-configure-aws-account.md)

<!---HONumber=AcomDC_0713_2016-->