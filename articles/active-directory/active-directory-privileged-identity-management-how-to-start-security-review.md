<properties
   pageTitle="Inicio de una revisión de seguridad | Microsoft Azure"
   description="Aprenda a crear una revisión de seguridad para identidades con privilegios con la aplicación Privileged Identity Management de Azure."
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Inicio de una revisión de seguridad en Privileged Identity Management de Azure AD

Las asignaciones de roles se convierten en "obsoletas" cuando los usuarios tienen acceso con privilegios que ya no necesitan. Con el fin de reducir el riesgo asociado a estas asignaciones de roles obsoletos, los administradores de seguridad deben revisar regularmente los roles que se han concedido a los usuarios. En este documento se describen los pasos para iniciar una revisión de seguridad en Privileged Identity Management (PIM) de Azure AD.

## Inicio de una revisión de seguridad
> [AZURE.NOTE] Si no ha agregado la aplicación PIM al panel del Portal de Azure, consulte los pasos que se indican en [Introducción a Azure Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).

En la página principal de la aplicación PIM, hay tres formas de comenzar una revisión de seguridad:

- **Revisiones de seguridad** > **Revisión** > botón **Revisar**
- **Roles** > botón **Revisar**
- Seleccionar el rol que se va a revisar en la lista de roles > botón **Revisar**

Al hacer clic en el botón **Revisar**, aparecen las hojas **Comenzar a revisar un rol** y **Seleccionar un rol para su revisión**. El elemento **Seleccionar un rol para su revisión** se selecciona de forma predeterminada.

### Selección del rol para revisar

1. Seleccione el rol en la lista de roles de la hoja **Seleccionar un rol para su revisión**. Sólo puede elegir un rol a la vez. La hoja **Seleccionar un rol para revisar** se reemplazará por la hoja **Seleccionar revisores**. Al seleccionar revisores tiene dos opciones:
  - Yo: use esta característica si quiere obtener una vista previa de cómo funcionan las revisiones de seguridad sin que intervengan otros administradores.
  - Autorrevisión por miembros del rol: utilice esta opción para que los usuarios revisen ellos mismos sus propias asignaciones de roles.
2. Seleccione cualquiera de estas opciones para comenzar a trabajar con los detalles de revisión. Aparecerá la hoja **Cambiar valores predeterminados**.

### Revisión por mí mismo

Si seleccionó la opción "Yo" como revisor, continúe con la revisión de seguridad. Para obtener más información sobre cómo completar la revisión, vea [Privileged Identity Management de Azure: Realización de una revisión de seguridad](active-directory-privileged-identity-management-how-to-perform-security-review.md).

### Autorrevisión por miembros del rol

Si decide que los usuarios revisen sus propias asignaciones de roles, siga estos pasos para configurar las notificaciones de revisión y envío.

1. Asigne un nombre a la revisión escribiéndolo en el campo **Nombre**. Asigne a la revisión un nombre único que la describa y que permita realizar un seguimiento fácil de ella.
2. Escriba una fecha de inicio para la revisión en el campo **Fecha de inicio**.
3. Escriba una fecha final para la revisión en el campo **Fecha de finalización**. Algunas cosas que debe tener en cuenta al establecer la fecha de finalización para la revisión son:
  - ¿A cuántas personas se revisa?
  - ¿Con qué rapidez los usuarios podrán agregar la aplicación PIM en el Portal de Azure y realizar la revisión?
4. Haga clic en el botón **Aceptar** de la hoja **Cambiar valores predeterminados**. Se cerrará.
5. Haga clic en el botón **Aceptar** de la hoja **Iniciar una revisión de un rol**. Se cerrará. Aparecerá una notificación en el menú principal del Portal de Azure. Actualice el panel haciendo clic en el botón **Actualizar** y la revisión de seguridad aparecerá en la sección **Revisiones de seguridad**.
6. Notifique a los usuarios del rol que tendrán que agregar la aplicación PIM y luego [revise su propio acceso administrativo](active-directory-privileged-identity-management-how-to-perform-security-review.md).  

## Administración de la revisión de seguridad

Puede seguir el progreso de las revisiones a medida que los revisores las realizan en el panel de PIM de Azure AD, en la sección de revisiones de seguridad. Los derechos de acceso no se cambian en el directorio hasta que [la revisión finaliza](active-directory-privileged-identity-management-how-to-complete-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Tabla de contenido de PIM
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->