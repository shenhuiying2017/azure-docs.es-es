---
title: ¿Qué es Azure Active Directory B2C? | Microsoft Docs
description: Aprenda a crear y administrar su experiencia de inicio de sesión de aplicación con Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/05/2018
ms.author: davidmu
ms.openlocfilehash: ca9e45a214639da86cf8e0c4a39b3e3d6b6d6491
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2018
---
# <a name="what-is-azure-active-directory-b2c"></a>¿Qué es Azure Active Directory B2C?

Azure Active Directory (Azure AD) B2C es un servicio de administración de identidades que le permite personalizar y controlar la manera en que los clientes se registran, inician sesión y administran sus perfiles al usar las aplicaciones. Esto incluye las aplicaciones desarrolladas para iOS, Android y. NET, entre otros. Azure AD B2C permite estas acciones y, al mismo tiempo, protege las identidades de los clientes.

Puede configurar una aplicación registrada con Azure AD B2C para realizar una variedad de acciones de administración de identidad. A continuación, se indican algunos ejemplos:

- Permitir que un cliente se registre para usar la aplicación registrada
- Permitir que un cliente registrado inicie sesión y empiece a usar la aplicación
- Permitir que un cliente registrado edite su perfil
- Permitir la autenticación multifactor en la aplicación
- Permitir que el cliente se registre e inicie sesión con proveedores de identidades específicos
- Conceder acceso a la aplicación a las API que cree
- Personalizar la apariencia de la experiencia de registro y de inicio de sesión
- Administrar sesiones de inicio de sesión único para la aplicación

## <a name="what-do-i-need-to-think-about-before-using-azure-ad-b2c"></a>¿Qué se debe tener en cuenta antes de utilizar Azure AD B2C?

- ¿Cómo desea el cliente interactuar con la aplicación?
- ¿Cuál es la experiencia de la interfaz de usuario que quiero ofrecer a los clientes?
- ¿Qué proveedores de identidades quiero que elijan los clientes en mi aplicación?
- ¿El proceso de inicio de sesión requiere API adicionales para ejecutarse?

### <a name="customer-interaction"></a>Interacción del cliente

Azure AD B2C es compatible con [OpenID Connect ](https://openid.net/connect/) para todas las experiencias del cliente. En la implementación de OpenID Connect de Azure AD B2C, su aplicación inicia este recorrido del usuario mediante la emisión de solicitudes de autenticación a Azure AD B2C. El resultado de la solicitud es un elemento `id_token`. Este token de seguridad representa la identidad del cliente.

Toda aplicación que utilice Azure AD B2C debe estar registrada en un inquilino de Azure AD B2C que use Azure Portal. El proceso de registro recopila y asigna valores a la aplicación. Estos valores incluyen un identificador de aplicación que identifica unívocamente la aplicación y una URI de redirección que se puede utilizar para dirigir las respuestas hacia ella.

La interacción de cada aplicación sigue un patrón de nivel alto similar:

1. La aplicación dirige el cliente para que ejecute una directiva.
2. El cliente completa la directiva según la definición de la misma.
3. La aplicación recibe un token de seguridad.
4. La aplicación utiliza el token de seguridad para intentar acceder a un recurso protegido.
5. El servidor de recursos valida el token de seguridad para asegurarse de que se puede conceder acceso.
6. La aplicación actualiza periódicamente el token de seguridad.

Estos pasos pueden diferir ligeramente según el tipo de aplicación que se está creando.

Azure AD B2C interactúa con proveedores de identidades, clientes, otros sistemas y con el directorio local en secuencia para completar una tarea de identidad. Por ejemplo, iniciar sesión en un cliente, registrar un nuevo cliente o restablecer una contraseña. La plataforma subyacente que establece la confianza de varias partes y completa estos pasos se denomina marco de experiencia de identidad. Esta plataforma y una directiva (también denominados recorrido del usuario o directiva del marco de confianza) definen explícitamente los actores, las acciones, los protocolos y la secuencia de pasos para completar.

Azure AD B2C protege contra ataques de denegación de servicio y de contraseña contra sus aplicaciones de varias maneras. Azure AD B2C usa técnicas de detección y mitigación, como las cookies SYN y los límites de velocidad y conexión, para proteger los recursos frente a los ataques por denegación de servicio. La mitigación incluye también ataques a contraseñas por fuerza bruta y ataques de diccionario.

#### <a name="built-in-policies"></a>Directivas integradas

En cada solicitud que se envía a Azure AD B2C se especifica una directiva. Una directiva controla el comportamiento de cómo interactúa la aplicación con Azure AD B2C. Las directivas integradas están predefinidas para las tareas de identidad más comunes, como el registro, el inicio de sesión y la edición de perfiles.  Por ejemplo, una directiva de registro le permite controlar comportamientos configurando los siguientes valores:

- Cuentas de redes sociales que el cliente puede utilizar para registrarse a la aplicación
- Datos recopilados del cliente como el nombre o código postal
- Multi-Factor Authentication
- Apariencia de todas las páginas de registro
- Información que se devuelve a la aplicación

#### <a name="custom-policies"></a>Directivas personalizadas 

Las [directivas personalizadas](active-directory-b2c-overview-custom.md) son archivos de configuración que definen el comportamiento del marco de experiencia de identidad en el inquilino de Azure AD B2C. Las directivas personalizadas se pueden editar totalmente para realizar muchas tareas. Una directiva personalizada se representa como uno o varios archivos con formato XML que se hacen referencia entre sí en una cadena jerárquica. 

Se pueden usar varias directivas personalizadas de diferentes tipos en el inquilino de Azure AD B2C según sea necesario y se pueden reutilizar en las aplicaciones. Esta flexibilidad le permite definir y modificar las experiencias de identidad de cliente con cambios mínimos o ningún cambio en el código. Se pueden utilizar las directivas si se agrega un parámetro de consulta especial en solicitudes de autenticación de HTTP.

Se pueden utilizar directivas personalizadas para controlar los recorridos del usuario de las siguientes maneras:

- Para definir la interacción con las API para capturar información adicional, comprobar las notificaciones proporcionadas por el cliente o desencadenar procesos externos.
- Para cambiar el comportamiento basado en notificaciones de API o de notificaciones del directorio como *migrationStatus*.
- Cualquier flujo de trabajo no cubierto por directivas integradas. Por ejemplo, para recopilar más información de un cliente durante una experiencia de inicio de sesión y realizar una comprobación de autorización para acceder a un recurso.

### <a name="identity-providers"></a>Proveedores de identidades

Un proveedor de identidades es una autoridad que autentica las identidades de los usuarios y emite los tokens de seguridad. En Azure AD B2C, puede configurar varios proveedores de identidades en su inquilino, por ejemplo, una cuenta de Microsoft, Facebook o Amazon entre otros. 

Para configurar un proveedor de identidades en su inquilino de Azure AD B2C, debe registrar el identificador de aplicación o identificador de cliente, así como la contraseña o secreto de cliente desde la aplicación de proveedor de identidades que cree. Este identificador y la contraseña se utilizan entonces para configurar la aplicación.

### <a name="user-interface-experience"></a>Experiencia de interfaz de usuario

Se puede controlar la mayor parte del contenido HTML y CSS que se presenta a los clientes. La característica de personalización de la interfaz de usuario de la página sirve para personalizar la apariencia de cualquier directiva. También puede mantener la coherencia visual y de la marca entre la aplicación y Azure AD B2C.

Azure AD B2C ejecuta código en el explorador del consumidor y usa un enfoque moderno denominado Uso compartido de recursos entre orígenes (CORS). En primer lugar, especifique una dirección URL en una directiva con contenido HTML personalizado. Azure AD B2C combina elementos de la interfaz de usuario con el contenido HTML cargado desde la dirección URL y muestra la página al consumidor.

Puede enviar parámetros a Azure AD B2C en una cadena de consulta. Al pasar dicho parámetro al punto de conexión HTML, puede cambiar de forma dinámica el contenido de la página. Por ejemplo, puede cambiar la imagen de fondo en la página de inicio de sesión o de registro de Azure AD B2C en función de un parámetro que se pasa desde la aplicación web o dispositivo móvil.

## <a name="how-do-i-get-started-with-azure-ad-b2c"></a>¿Cómo puedo empezar a usar Azure AD B2C?

En Azure AD B2C, un inquilino representa a su organización y es un directorio de usuarios. Cada inquilino de Azure AD B2C es distinto e independiente del resto de inquilinos de Azure AD B2C. Un inquilino contiene información acerca de los clientes que se han registrado para utilizar la aplicación. Por ejemplo, contraseñas, datos de perfil y permisos.

Debe vincular su inquilino de Azure AD B2C a su suscripción a Azure para habilitar todas las funcionalidades y pagar los cargos por uso. Para permitir que los clientes de Azure AD B2C inicien sesión en su aplicación, debe registrar la aplicación en un inquilino de Azure AD B2C.

Antes de configurar su aplicación para usar Azure AD B2C, primero debe crear un inquilino de Azure AD B2C y registrar la aplicación. Para ello, complete los pasos que se indican en [Tutorial: Register an application to enable sign-up and sign-in using Azure AD B2C](tutorial-register-applications.md) (Tutorial: Registro de una aplicación para habilitar el registro y el inicio de sesión con Azure AD B2C).
  
Si es un desarrollador de aplicaciones web de ASP.NET, configure la aplicación para autenticar cuentas siguiendo los pasos indicados en [Tutorial: Autenticación de usuarios con Azure Active Directory B2C en una aplicación web para ASP.NET](active-directory-b2c-tutorials-web-app.md).

Si es un desarrollador de aplicaciones de escritorio, configure la aplicación para autenticar cuentas siguiendo los pasos indicados en [Tutorial: Enable a desktop application to authenticate with accounts using Azure AD B2C](active-directory-b2c-tutorials-desktop-app.md) (Tutorial: Autenticación de usuarios con Azure Active Directory B2C en una aplicación de escritorio).

Si es un desarrollador de aplicaciones de página única mediante Node.js, configure la aplicación para autenticar cuentas siguiendo los pasos indicados en [Tutorial: Enable a single-page application to authenticate with accounts using Azure AD B2C](active-directory-b2c-tutorials-spa.md) (Tutorial: Autenticación de usuarios con Azure Active Directory B2C en una aplicación de página única).

## <a name="next-steps"></a>Pasos siguientes

Comience a configurar su aplicación para la experiencia de registro e inicio de sesión al continuar el tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Register an application to enable sign-up and sign-in using Azure AD B2C](tutorial-register-applications.md) (Tutorial: Registro de una aplicación para habilitar el registro y el inicio de sesión con Azure AD B2C).
