<properties
   pageTitle="Características de Azure AD Connect en vista previa | Microsoft Azure"
   description="En este tema se describen más detenidamente las características que se encuentran en vista previa en Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/13/2015"
   ms.author="andkjell;billmath"/>

# Más detalles sobre las características de vista previa
En este tema se describe cómo usar las características que existen actualmente en la vista previa.

## Reescritura de usuarios
> [AZURE.IMPORTANT]La característica de vista previa de escritura diferida de usuarios se quitó temporalmente en la actualización de agosto para AAD Connect. Si la ha habilitado, debería deshabilitarla.

La escritura diferida de usuarios se encuentra en la primera etapa de la vista previa. Solo se puede usar cuando Azure AD es el origen de todos los objetos de usuario y Active Directory local está vacío antes de habilitar la característica.

> [AZURE.IMPORTANT]Esta característica solo se debe probar en un entorno de prueba y no debe usarse en un directorio de Azure AD empleado para el uso de producción.

## Escritura diferida de grupos
La opción para la escritura diferida de grupos en las características opcionales le permitirá escribir en diferido "Grupos de Office 365" en un bosque con Exchange instalado. Se trata de un nuevo tipo de grupo que siempre se controla en la nube. Puede encontrarlo en outlook.office365.com o en myapps.microsoft.com, tal como se muestra aquí:


![Filtrado de sincronización](./media/active-directory-aadconnect-feature-preview/office365.png)

![Filtrado de sincronización](./media/active-directory-aadconnect-feature-preview/myapps.png)

Este grupo se representará como un grupo de distribución en AD DS local. El servidor de Exchange local debe tener la actualización acumulativa 8 de Exchange 2013 (publicado en marzo de 2015) para reconocer este nuevo tipo de grupo.

**Nota:**

- En la vista previa no se rellena actualmente el atributo de libreta de direcciones. La manera más fácil de hacerlo es usar el cmdlet de Exchange PowerShell update-recipient.
- Solo los bosques con el esquema de Exchange son destinos válidos para los grupos. Si no se ha detectado ningún Exchange, no se podrá habilitar la reescritura de grupos.
- La característica Reescritura de grupos no controla actualmente los grupos de seguridad ni los grupos de distribución.

Puede encontrar más información sobre los grupos de Office 365 [aquí](http://aka.ms/O365g).

## Extensiones de directorio
Las extensiones de directorio le permiten ampliar el esquema de Azure AD con sus propios atributos desde Active Directory local.

Solo se admiten los atributos de un solo valor y los valores de los atributos no pueden tener más de 250 caracteres. Con los atributos seleccionados, se ampliará el metaverso y el esquema de AD de Azure. En Azure AD se agrega una nueva aplicación con los atributos.

![Filtrado de sincronización](./media/active-directory-aadconnect-feature-preview/extension3.png)

Estos atributos ahora estarán disponibles en Graph:

![Filtrado de sincronización](./media/active-directory-aadconnect-feature-preview/extension4.png)

## Pasos siguientes
Continúe su [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=Oct15_HO3-->