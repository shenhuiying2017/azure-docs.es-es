<properties
   pageTitle="Realización de una revisión de seguridad | Microsoft Azure"
   description="Aprenda a realizar una revisión con la aplicación Privileged Identity Management de Azure."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Realización de una revisión de seguridad en Privileged Identity Management de Azure AD

Privileged Identity Management de Azure Active Directory (AD) simplifica el modo en que las empresas administran las identidades con privilegios y el acceso a los recursos en Azure AD y en otros servicios en línea de Microsoft como Office 365 o Microsoft Intune.

Si se le ha asignado a un rol administrativo, el administrador de roles con privilegios de su organización le puede pedir que revise y confirme periódicamente que aún necesita ese rol para su trabajo. Puede que reciba un correo electrónico con un vínculo o que vaya directamente al [Portal de Azure](https://portal.azure.com). Siga los pasos de este artículo para realizar una autorrevisión de los roles asignados.

Si es un administrador de roles con privilegios interesado en las revisiones de seguridad, puede obtener más información en [Inicio de una revisión de seguridad](active-directory-privileged-identity-management-how-to-start-security-review.md).

## Incorporación de la aplicación Privileged Identity Management

Puede usar la aplicación Privileged Identity Management (PIM) de Azure AD en el [Portal de Azure](https://portal.azure.com/) para realizar la revisión. Si no tiene la aplicación en el portal, siga estos pasos para comenzar:

1. Si aún no lo ha hecho, inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Si su organización tiene más de un directorio, haga clic en su nombre de usuario en la esquina superior derecha del Portal de Azure y seleccione el directorio donde va a trabajar.
3. Haga clic en el icono **Nuevo** en el panel de navegación de la izquierda.
4. Seleccione **Seguridad e identidad** en el menú.
5. Seleccione **Azure AD Privileged Identity Management**.
6. Deje activada la casilla **Anclar al panel** y luego haga clic en el botón **Crear**. Se abrirá la aplicación Privileged Identity Management.


## Aprobación o denegación de acceso

El acceso no cambiarán hasta que [la revisión esté completa](active-directory-privileged-identity-management-how-to-complete-review.md). Este proceso consiste simplemente en crear una lista de comprobación de aquellos cuyo acceso del rol se cambiaría. Después de seleccionar al menos un usuario, se habilitarán los botones **Aprobar acceso** y **Denegar acceso**.

1. En la aplicación PIM, haga clic en **Revisar acceso administrativo** en el menú principal. Aparecerá una lista de revisiones de seguridad.
2. Seleccione en la lista los **usuarios** cuyo acceso quiere cambiar.
3. Haga clic en **Aprobar acceso** o **Denegar el acceso** para los usuarios que seleccionó. Aparecerá una notificación en el menú principal del Portal de Azure y los nombres seleccionados en la lista de revisiones desaparecerán (puede recuperarlos si vuelve a cambiar la opción de filtro). Cierre la hoja **Revisar roles de Azure AD**.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->