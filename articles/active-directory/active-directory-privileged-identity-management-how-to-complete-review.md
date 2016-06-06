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


Los administradores de roles con privilegios pueden revisar el acceso con privilegios cuando se [ha iniciado una revisión de seguridad](active-directory-privileged-identity-management-how-to-start-security-review.md). Privileged Identity Management (PIM) de Azure AD enviará automáticamente un correo electrónico para pedir a los usuarios que revisen su acceso. Si un usuario no recibió un correo electrónico, puede enviarle las instrucciones que se indican en [Privileged Identity Management de Azure AD: Realización de una revisión de seguridad](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Después de que acabe el período de revisión de seguridad o de que todos los usuarios hayan finalizado su autorrevisión, siga los pasos de este artículo para administrar la revisión y ver los resultados.

## Administración de las revisiones de seguridad

1. Vaya al [Portal de Azure](https://portal.azure.com/) y seleccione la aplicación **Azure AD Privileged Identity Management** en el panel.
2. Seleccione la sección **Revisiones de seguridad** del panel. Aparecerá la hoja **Revisiones de seguridad**.
3. Seleccione la revisión de seguridad que quiere administrar. Aparecerá la hoja de detalles de la revisión de seguridad.

Una vez en la hoja de detalles de la revisión de seguridad, dispone de tres opciones para administrar dicha revisión. Puede completar la revisión, exportar los detalles o eliminarla.

### Completar o detener una revisión

Si los usuarios tienen el acceso denegado, puede completar la revisión para quitar la asignación de rol a esos usuarios en el directorio. El botón **Detener revisión** archivará la revisión.

### Exportar una revisión

Si quiere aplicar los resultados de la revisión de seguridad manualmente, puede exportar la revisión. El botón **Exportar** iniciará la descarga de un archivo CSV. Puede administrar los resultados en Excel u otros programas que abran archivos CSV.

### Eliminar una revisión

> [AZURE.IMPORTANT] No recibirá ninguna advertencia antes de la eliminación, así que asegúrese de que realmente quiere eliminar esa revisión.

Si ya no le interesa más la revisión, elimínela. El botón **Eliminar** quita la revisión de la aplicación PIM.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->