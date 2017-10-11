---
title: "Cumplimentación de campos específicos en una aplicación personalizada | Microsoft Docs"
description: "Guía acerca de cómo deben rellenarse determinados campos cuando se registra una aplicación personalizada desarrollada con Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 04fd35f238e4dd05486f85b0b16c2ab0c5ae9f30
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-fill-out-specific-fields-for-a-custom-developed-application"></a>Cumplimentación de campos específicos en una aplicación personalizada

Este artículo contiene una breve descripción de todos los campos disponibles en el formulario de registro de aplicaciones de [Azure Portal](https://portal.azure.com).

## <a name="register-a-new-application"></a>Registro de una nueva aplicación

-   Para registrar una nueva aplicación, vaya a [Azure Portal](https://portal.azure.com).

-   En el panel de navegación izquierdo, haga clic en **Azure Active Directory**.

-   Haga clic en **Registros de aplicaciones** y en **Agregar**.

-   Se abrirá el formulario de registro de aplicaciones.

## <a name="fields-in-the-application-registration-form"></a>Campos del formulario de registro de aplicaciones


| Campo            | Description                                                                              |
|------------------|------------------------------------------------------------------------------------------|
| Nombre             | Nombre de la aplicación. Debe tener un mínimo de cuatro caracteres.                |
| Tipo de aplicación | **Aplicación web / API web**: aplicación que representa una aplicación web, una API web o ambas cosas 
| |**Nativo**: aplicación que puede instalarse en el dispositivo o equipo de un usuario           |
| URL de inicio de sesión      | Dirección URL donde los usuarios pueden iniciar sesión para utilizar la aplicación                                  |

Cuando haya rellenado los campos anteriores, la aplicación se registrará en Azure Portal y la página de la aplicación se abrirá automáticamente. El botón **Configuración** del panel de la aplicación abre la página Configuración, que contiene otros campos que le permiten personalizar la aplicación. En la tabla siguiente se describen todos los campos de la página Configuración. Tenga en cuenta que, en función de si ha creado una aplicación web o una aplicación nativa, es posible que solo aparezca un subconjunto de estos campos.

| Campo           | Descripción                                                                                                                                                                                                                                                                                                     |
|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identificador de aplicación  | Cuando registra una aplicación, Azure AD le asigna un identificador de aplicación. Este identificador puede usarse para identificar la aplicación en las solicitudes de autenticación que se envían a Azure AD, así como para acceder a ciertos recursos, como la API Graph.                                                          |
| URI del identificador de la aplicación      | Debe ser un URI único. Normalmente, tendrá el formato **https://&lt;inquilino\_nombre&gt;/&lt;aplicación\_nombre&gt;.** Se utiliza durante el flujo de concesión de autorizaciones como identificador único a fin de especificar el recurso para el que se debe emitir el token. También se convierte en la notificación "aud" del token de acceso emitido. |
| Cargar nuevo logotipo | Puede usar este campo para cargar un logotipo en la aplicación. El logotipo debe tener el formato .bmp, .jpg o .png y el tamaño del archivo debe ser inferior a 100 KB. Las dimensiones de la imagen deben ser 215 x 215 píxeles y las de la imagen central, 94 x 94 píxeles.                                                       |
| URL de página principal   | Se trata de la dirección URL de inicio de sesión especificada durante el registro de la aplicación.                                                                                                                                                                                                                                              |
| URL de cierre de sesión      | Es la dirección URL de cierre de sesión único. Azure AD envía una solicitud de cierre de sesión a esta dirección URL cuando el usuario termina la sesión en Azure AD con cualquier otra aplicación registrada.                                                                                                                                       |
| Multiinquilino  | Este parámetro especifica si la aplicación pueden utilizarla varios inquilinos. Normalmente, esto significa que las organizaciones externas pueden usar la aplicación si la registran en su inquilino y conceden acceso a los datos de la organización.                                                                   |
| URL de respuesta      | Las direcciones URL de respuesta son puntos de conexión en los que Azure AD devuelve los tokens que solicita la aplicación.                                                                                                                                                                                                          |
| URI de redirección   | En el caso de las aplicaciones nativas, es donde se le enviará al usuario la siguiente autorización correcta. Azure AD comprueba que el URI de redirección que proporciona la aplicación en la solicitud de OAuth 2.0 coincide con uno de los valores registrados en el portal.                                                            |
| simétricas            | Puede crear claves para acceder mediante programación a las API web protegidas por Azure AD sin interacción del usuario. En la página \*\*Claves\*\*, escriba la descripción de la clave y la fecha de vencimiento. A continuación, haga clic en Guardar para generar la clave. No olvide guardar la clave en un lugar seguro, ya que no podrá acceder a ella más adelante.             |

## <a name="next-steps"></a>Pasos siguientes
[Administración de aplicaciones con Azure Active Directory](active-directory-enable-sso-scenario.md)
