<properties
	pageTitle="Control de acceso basado en roles de Azure Active Directory | Microsoft Azure"
	description="Introducción al control de acceso basado en roles de Azure en el Portal de Azure. Use las asignaciones de roles para asignar permisos en el directorio."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/03/2016"
	ms.author="kgremban"/>

# Introducción a la administración de acceso en el Portal de Azure

El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. Gracias a RBAC puede dividir las tareas entre el equipo de DevOps, y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. En este artículo se presentan los conceptos básicos de la administración de acceso que le ayudarán a utilizar RBAC en el Portal de Azure.

## Aspectos básicos de la administración de acceso en Azure
Cada suscripción de Azure está asociada a un directorio de Azure Active Directory (AD). Los usuarios, grupos y aplicaciones de ese directorio pueden administrar los recursos en la suscripción de Azure. Estos derechos de acceso se conceden con el uso del Portal de Azure, las herramientas de la línea de comandos de Azure o las API de administración de Azure.

El acceso se concede al asignar el rol RBAC adecuado a usuarios, grupos y aplicaciones de un determinado ámbito. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un único recurso. Un rol asignado en un ámbito principal también concede acceso a los elementos secundarios dentro del mismo. Por ejemplo, un usuario con acceso a un grupo de recursos puede administrar todos los recursos que contiene, como sitios web, máquinas virtuales y subredes.

![Relación entre elementos de Azure Active Directory - diagrama](./media/role-based-access-control-what-is/rbac_aad.png)

El rol RBAC que se asigna determina qué recursos puede administrar el usuario, el grupo o la aplicación dentro de ese ámbito.

## Roles integrados
RBAC de Azure cuenta con tres roles básicos que se aplican a todos los tipos de recurso:

- El **propietario** tiene acceso completo a todos los recursos y cuenta con el derecho a delegar este acceso a otros.
- El **colaborador** puede crear y administrar todos los tipos de recursos de Azure pero no puede conceder acceso a otros.
- El **lector** solo puede ver los recursos existentes de Azure.

El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite al usuario crear y administrar máquinas virtuales. No otorga acceso a la red virtual ni a la subred a las que se conecta la máquina virtual.

[Roles RBAC integrados](role-based-access-built-in-roles.md) muestra los roles disponibles en Azure. Especifica las operaciones y el ámbito de cada rol integrado que se concede a los usuarios. Si quiere definir sus propios roles para tener un mayor control, consulte cómo crear [roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md).

## Jerarquía de recursos de Azure y herencia de acceso
- Cada **suscripción** de Azure pertenece a un único directorio.
- Cada **grupo de recursos** pertenece a una única suscripción.
- Cada **recurso** pertenece a un único grupo de recursos.

El acceso que se concede en el nivel principal se hereda en los ámbitos secundarios. Por ejemplo:

- Asigne el rol de lector a un grupo de Azure AD en el ámbito de la suscripción. Los miembros de ese grupo pueden ver todos los grupos de recursos y los recursos de la suscripción.
- Asigne el rol de colaborador a una aplicación en el ámbito del grupo de recursos. Puede administrar todos los tipos de recursos de ese grupo de recursos, pero no otros grupos de la suscripción.

## RBAC de Azure frente a administradores de la suscripción clásica
Los administradores y coadministradores de la suscripción clásica tienen acceso completo a la suscripción de Azure. Pueden administrar recursos mediante el [Portal de Azure](https://portal.azure.com) con las API de Azure Resource Manager, o mediante el [Portal de Azure clásico ](https://manage.windowsazure.com) y las API de administración de servicios de Azure. En el modelo RBAC, a los administradores clásicos se les asigna el rol de propietario en el ámbito de suscripción.

RBAC de Azure solo es compatible con el Portal de Azure y las nuevas API de Azure Resource Manager. Los usuarios y las aplicaciones a los que se asignan roles RBAC no pueden usar el portal de administración clásico ni las API de Administración de servicios de Azure.

## Autorización para administración frente a operaciones de datos
RBAC de Azure solo es compatible con operaciones de administración de los recursos de Azure del Portal de Azure y de las API de Azure Resource Manager. No todas las operaciones de nivel de datos para recursos de Azure se pueden autorizar mediante RBAC. Por ejemplo, las cuentas de almacenamiento se pueden administrar con RBAC, pero no los blobs ni las tablas que pertenecen a una cuenta de almacenamiento. De igual forma, una base de datos SQL se puede administrar, pero no las tablas que contiene.

## Pasos siguientes
- Introducción a [Control de acceso basado en roles en el portal de Azure](role-based-access-control-configure.md).
- Consulte [RBAC: Roles integrados](role-based-access-built-in-roles.md)
- Definir sus propios [roles personalizados en RBAC de Azure](role-based-access-control-custom-roles.md)

<!---HONumber=AcomDC_0504_2016-->