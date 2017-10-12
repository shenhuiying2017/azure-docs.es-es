---
title: "Obtención de un inquilino de Azure AD | Microsoft Docs"
description: "Obtenga un inquilino de Azure Active Directory para el registro y la creación de aplicaciones."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/19/2017
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: fe33d490b754e2f793f5c7a13dc55ca038b1b71c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Obtención de un inquilino de Azure Active Directory
En Azure Active Directory (Azure AD), un [inquilino](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) es un representante de una organización.  Se trata de una instancia dedicada del servicio de Azure AD que recibe una organización y que posee cuando registra un servicio en la nube de Microsoft, como Azure, Microsoft InTune u Office 365.  Cada inquilino de Azure AD es distinto e independiente de los demás inquilinos de Azure AD.  

Un inquilino aloja los usuarios de una empresa y la información sobre ellos: sus contraseñas, datos de perfil de usuario, permisos, etc.  También contiene grupos, aplicaciones y otra información relativa a una organización y su seguridad.

Para permitir que los usuarios de Azure AD inicien sesión en su aplicación, debe registrar la aplicación en un inquilino que elija.  La publicación de una aplicación en un inquilino de Azure AD es **totalmente gratis**.  De hecho, la mayoría de programadores creará varios inquilinos y aplicaciones para fines de experimentación, desarrollo, almacenamiento provisional y prueba.  Las organizaciones que se registren y consuman la aplicación pueden elegir opcionalmente adquirir licencias si desean aprovechar las características avanzadas de directorio.

Por lo tanto, ¿cómo obtiene un inquilino de Azure AD?  El proceso puede ser poco diferente si:

* [Dispone de una suscripción de Office 365 existente](#use-an-existing-office-365-subscription)
* [Tiene una suscripción de Azure asociada a una cuenta Microsoft](#use-an-msa-azure-subscription)
* [Dispone de una suscripción de Azure existente asociada a una cuenta organizativa](#use-an-organizational-azure-subscription)
* [No dispone de ninguna de las opciones anteriores y desea comenzar desde cero](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Uso de una suscripción de Office 365 existente
Si dispone de una suscripción a Office 365, ya tiene un inquilino de Azure AD. Puede iniciar sesión en [Azure Portal](https://portal.azure.com) con su cuenta de Office 365 y empezar a usar Azure AD.

## <a name="use-an-msa-azure-subscription"></a>Uso de una suscripción de MSA Azure
Si se ha registrado anteriormente en una suscripción de Azure con una cuenta Microsoft individual, ya dispone de un inquilino.  Cuando inicie sesión en [Azure Portal](https://portal.azure.com), se registrará automáticamente en su inquilino predeterminado. Pueden usa este inquilino como considere oportuno, pero es posible que desee crear una cuenta de administrador organizativo.

Para hacerlo, siga estos pasos:  También es posible que quiera crear a un nuevo inquilino y crear un administrador en ese inquilino siguiendo un proceso similar.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta individual.
2. Navegue a la sección "Azure Active Directory" del portal (que se encuentra en la barra de navegación izquierda, en **Más servicios**)
3. Debería iniciar sesión automáticamente en el "directorio predeterminado". Si no, puede cambiar de directorio haciendo clic en el nombre de cuenta en la esquina superior derecha.
4. En la sección **Tareas rápidas**, elija **Agregar un usuario**.
5. En el formulario para agregar usuario, proporcione la siguiente información:

   * Nombre: (elija un valor apropiado)
   * Nombre de usuario: (elija un nombre de usuario para este administrador)
   * Perfil: (rellene los valores apropiados en Nombre, Apellidos, Puesto y Departamento)
   * Rol: administrador global
6. Cuando haya completado el formulario de adición de usuario y reciba la contraseña temporal para el nuevo usuario administrativo, asegúrese de registrar esta contraseña, puesto que tendrá que iniciar sesión con este nuevo usuario para cambiar la contraseña. También puede enviar la contraseña directamente al usuario mediante un correo electrónico alternativo.
7. Haga clic en **Crear** para crear el nuevo usuario.
8. Para cambiar la contraseña temporal, inicie sesión en [https://login.microsoftonline.com](https://login.microsoftonline.com) con esta nueva cuenta de usuario y cambie la contraseña cuando se le solicite.

## <a name="use-an-organizational-azure-subscription"></a>Uso de una suscripción de Azure organizativa
Si se ha registrado anteriormente en una suscripción de Azure con la cuenta organizativa, ya dispone de un inquilino.  En [Azure Portal](https://portal.azure.com) debe buscar un inquilino cuando navegue a "Más servicios" y "Azure Active Directory".  Pueden usar a este inquilino como considere oportuno.

## <a name="start-from-scratch"></a>Comienzo desde cero
Si todo lo anterior es un galimatías para usted, no se preocupe.  Simplemente visite [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) para registrarse en Azure con una nueva organización.  Cuando haya completado el proceso, tendrá su propio inquilino de Azure AD con el nombre de dominio que elija durante el registro.  En [Azure Portal](https://portal.azure.com) puede buscar el inquilino navegando a "Azure Active Directory" en panel de la izquierda.

Como parte del proceso de registro en Azure, se le solicitará que proporcione la información de tarjeta de crédito.  Puede continuar con confianza: no se le cobrará por publicar aplicaciones en Azure AD o crear nuevos inquilinos.
