---
title: "Informes del autoservicio de restablecimiento de contraseña: Azure Active Directory"
description: "Creación de informes sobre eventos de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: ae9475218141f4fdf08ef6b39fb85f3b18711e61
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/17/2018
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opciones de creación de informes para la administración de contraseñas de Azure AD

Tras la implementación, muchas organizaciones quieren saber cómo se usa (y si se usa) realmente el autoservicio de restablecimiento de contraseña (SSPR). La característica de informes que proporciona Azure Active Directory (Azure AD) le ayuda a responder preguntas mediante informes creados previamente. Si está debidamente protegido por licencia, también puede crear consultas personalizadas.

![Informes][Reporting]

Las siguientes preguntas se pueden responder mediante informes existentes en [Azure Portal] (https://portal.azure.com/):

> [!NOTE]
> Debe ser [administrador global](active-directory-assign-admin-roles-azure-portal.md) y habilitar la opción para que estos datos se recopilen en nombre de la organización. Para habilitarla, debe visitar la pestaña **Informes** o los registros de auditoría como mínimo una vez. Hasta entonces, no se recopilan datos para la organización.
>

* ¿Cuántas personas se han registrado para el restablecimiento de contraseña?
* ¿Quién se ha registrado para el restablecimiento de contraseña?
* ¿Qué datos está registrando la gente?
* ¿Cuántas personas han restablecido sus contraseñas en los últimos siete días?
* ¿Cuáles son los métodos más comunes que usan los usuarios o administradores para restablecer sus contraseñas?
* ¿Cuáles son los problemas comunes que encuentran los usuarios o administradores al intentar usar el restablecimiento de contraseña?
* ¿Qué administradores restablecen sus propias contraseñas con frecuencia?
* ¿Hay alguna actividad sospechosa en relación con el restablecimiento de contraseña?

## <a name="power-bi-content-pack"></a>Paquete de contenido de Power BI

Si es usuario de Power BI, hay un paquete de contenido para Azure AD que incluye informes fáciles de usar con SSPR. Para obtener más información sobre cómo usar e implementar el paquete de contenido, vea [Uso del paquete de contenido de Power BI de Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md). Con el paquete de contenido, puede crear sus propios paneles y compartirlos con otras personas de la organización.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Visualización de los informes de administración de contraseñas en Azure Portal

En la experiencia de Azure Portal, se ha mejorado la visualización de la actividad de restablecimiento de contraseña y de registro de restablecimiento de contraseña. Realice los pasos siguientes para encontrar los eventos de restablecimiento de contraseña y de registro de restablecimiento de contraseña:

1. Vaya a [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** en el panel izquierdo.
3. Busque **Azure Active Directory** en la lista de servicios y selecciónelo.
4. Seleccione **Usuarios y grupos**.
5. Seleccione **Registros de auditoría** en el menú **Usuarios y grupos**. Se muestran todos los eventos de auditoría que se producen en todos los usuarios del directorio. Puede filtrar esta vista para ver también todos los eventos relacionados con contraseñas.
6. Para filtrar esta vista y ver únicamente los eventos relacionados con el restablecimiento de contraseña, seleccione el botón **Filtrar** que se encuentra en la parte superior del panel.
7. En el menú **Filtrar**, seleccione la lista desplegable **Categoría** y cámbiela al tipo de categoría **Self-service Password Management** (Administración de contraseñas de autoservicio).
8. También puede filtrar la lista si elige la **actividad** específica que le interesa.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Recuperación de eventos de administración de contraseñas desde la API de eventos e informes de Azure AD

La API de eventos e informes de Azure AD admite la recuperación de toda la información incluida en los informes de restablecimiento de contraseña y de registro de restablecimiento de contraseña. Mediante esta API, puede descargar eventos individuales de restablecimiento de contraseña y de registro de restablecimiento de contraseña e integrarlos con la tecnología de informes que prefiera.

> [!IMPORTANT]
> Actualmente, la API de eventos e informes de Azure AD recupera hasta *75 000 eventos individuales* de tipo [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) y [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent). La API abarca los últimos 30 días.
> 
> Si necesita recuperar o almacenar datos más allá de este período, se sugiere mantenerlos en una base de datos externa que use la API para consultar las diferencias que se generen. Se recomienda comenzar a recuperar estos datos cuando se empiece a usar SSPR en la organización. Manténgalos externamente y luego siga realizando el seguimiento de las diferencias a partir de ese momento.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>Introducción a la API de informes

Para obtener acceso a estos datos, debe escribir una pequeña aplicación o un script para recuperarlos de los servidores. Para obtener más información, vea [Introducción a la API de generación de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md).

Cuando tenga un script que funcione, querrá examinar los eventos de registro y de restablecimiento de contraseña que puede recuperar para cumplir con sus escenarios:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): muestra las columnas disponibles para los eventos de restablecimiento de contraseña.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): muestra las columnas disponibles para eventos de registro de restablecimiento de contraseña.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Descripción de las columnas de informe en Azure Portal

En la siguiente lista se explica en detalle cada una de las columnas de informe en Azure Portal:

* **Usuario**: usuario que ha intentado realizar una operación de registro de restablecimiento de contraseña.
* **Rol**: rol del usuario en el directorio.
* **Fecha y hora**: fecha y hora del intento.
* **Datos registrados**: datos de autenticación que el usuario ha proporcionado durante el registro de restablecimiento de contraseña.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Descripción de los valores de informe en Azure Portal

En la tabla siguiente se describen los distintos valores que se que puede establecer para cada columna en Azure Portal:

| Columna | Valores permitidos y su significado |
| --- | --- |
| Datos registrados |**Correo electrónico alternativo**: el usuario ha utilizado un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse.<p><p>**Teléfono de la oficina**: el usuario ha utilizado un teléfono de la oficina para autenticarse.<p>**Teléfono móvil**: el usuario ha utilizado un teléfono móvil o un teléfono de autenticación para autenticarse.<p>**Preguntas de seguridad**: el usuario ha utilizado preguntas de seguridad para autenticarse.<p>**Cualquier combinación de los métodos anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)**: se produce cuando se especifica una directiva de dos puertas y se muestran los dos métodos que el usuario ha utilizado para autenticar su solicitud de restablecimiento de contraseña. |

## <a name="self-service-password-management-activity-types"></a>Tipos de actividad de Self-service Password Management (Administración de contraseñas de autoservicio)

Los siguientes tipos de actividad aparecen en la categoría de evento de auditoría **Self-service Password Management** (Administración de contraseñas de autoservicio):

* [Bloqueado para el restablecimiento de contraseña de autoservicio](#activity-type-blocked-from-self-service-password-reset): indica que un usuario ha intentado restablecer una contraseña, usar una puerta específica o validar un número de teléfono más de cinco veces en total en 24 horas.
* [Cambio de contraseña (autoservicio)](#activity-type-change-password-self-service): indica que un usuario ha realizado un cambio de contraseña voluntario o forzado (por expiración).
* [Restablecimiento de contraseña (de parte del administrador)](#activity-type-reset-password-by-admin): indica que un administrador ha realizado un restablecimiento de contraseña en nombre de un usuario en Azure Portal.
* [Restablecimiento de contraseña (autoservicio)](#activity-type-reset-password-self-service): indica que un usuario ha restablecido correctamente su contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com).
* [Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio](#activity-type-self-serve-password-reset-flow-activity-progress): indica cada paso específico que un usuario sigue como parte del proceso de restablecimiento de contraseña (como atravesar una puerta de autenticación específica para el restablecimiento de contraseña).
* [Desbloqueo de la cuenta de usuario (autoservicio)](#activity-type-unlock-user-account-self-service): indica que un usuario ha desbloqueado correctamente su cuenta de Active Directory sin restablecer la contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com) mediante la característica de desbloqueo de cuentas sin restablecimiento de Active Directory.
* [Usuario registrado para el restablecimiento de contraseña de autoservicio](#activity-type-user-registered-for-self-service-password-reset): indica que un usuario ha registrado toda la información necesaria para poder restablecer la contraseña de acuerdo con la directiva de restablecimiento de contraseña de inquilino especificada actualmente.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de actividad: Bloqueado para el restablecimiento de contraseña de autoservicio

En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario ha intentado restablecer una contraseña, usar una puerta específica o validar un número de teléfono más de cinco veces en total en 24 horas.
* **Actor de la actividad**: el usuario que no ha podido realizar operaciones de restablecimiento adicionales. Puede tratarse de un usuario final o de un administrador.
* **Destino de la actividad**: el usuario que no ha podido realizar operaciones de restablecimiento adicionales. Puede tratarse de un usuario final o de un administrador.
* **Estado de la actividad**:
  * _Correcto_: indica que un usuario no ha podido realizar restablecimientos adicionales, intentar ningún otro método de autenticación ni validar ningún otro número de teléfono durante las 24 horas siguientes.
* **Motivo del error del estado de la actividad**: no aplicable.

### <a name="activity-type-change-password-self-service"></a>Tipo de actividad: Cambio de contraseña (autoservicio)

En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario ha realizado un cambio de contraseña voluntario o forzado (por expiración).
* **Actor de la actividad**: usuario que ha cambiado la contraseña. Puede tratarse de un usuario final o de un administrador.
* **Destino de la actividad**: usuario que ha cambiado la contraseña. Puede tratarse de un usuario final o de un administrador.
* **Estados de la actividad**:
  * _Correcto_: indica que un usuario ha cambiado correctamente la contraseña.
  * _Error_: indica que un usuario no ha podido cambiar la contraseña. Puede seleccionar la fila para ver la categoría **Motivo del estado de la actividad** y obtener más información sobre por qué se ha producido el error.
* **Motivo del error de estado de la actividad**: 
  * _FuzzyPolicyViolationInvalidPassword_: el usuario ha seleccionado una contraseña que se prohibió automáticamente debido a que las funcionalidades de detección de contraseñas prohibidas de Microsoft consideraron que era demasiado común o muy poco segura.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de actividad: Restablecimiento de contraseña (de parte del administrador)

En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un administrador ha realizado un restablecimiento de contraseña en nombre de un usuario en Azure Portal.
* **Actor de la actividad**: administrador que ha realizado el restablecimiento de contraseña en nombre de otro administrador o usuario final. Debe ser un administrador global, administrador de contraseñas, administrador de usuarios o administrador del departamento de soporte técnico.
* **Destino de la actividad**: usuario cuya contraseña se ha restablecido. Puede tratarse de un usuario final o de otro administrador.
* **Estados de la actividad**:
  * _Correcto_: indica que un administrador ha restablecido correctamente la contraseña de un usuario.
  * _Error_: indica que un administrador no ha podido cambiar la contraseña de un usuario. Puede seleccionar la fila para ver la categoría **Motivo del estado de la actividad** y obtener más información sobre por qué se ha producido el error.

### <a name="activity-type-reset-password-self-service"></a>Tipo de actividad: Restablecimiento de contraseña (autoservicio)

En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario ha restablecido correctamente su contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com).
* **Actor de la actividad**: usuario que ha restablecido la contraseña. Puede tratarse de un usuario final o de un administrador.
* **Destino de la actividad**: usuario que ha restablecido la contraseña. Puede tratarse de un usuario final o de un administrador.
* **Estados de la actividad**:
  * _Correcto_: indica que un usuario ha restablecido correctamente su propia contraseña.
  * _Error_: indica que un usuario no ha podido restablecer su propia contraseña. Puede seleccionar la fila para ver la categoría **Motivo del estado de la actividad** y obtener más información sobre por qué se ha producido el error.
* **Motivo del error de estado de la actividad**: 
  * _FuzzyPolicyViolationInvalidPassword_: el administrador ha seleccionado una contraseña que se prohibió automáticamente debido a que las funcionalidades de detección de contraseñas prohibidas de Microsoft consideraron que era demasiado común o muy poco segura.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de actividad: Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio

En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica cada paso específico que un usuario sigue como parte del proceso de restablecimiento de contraseña (como atravesar una puerta de autenticación específica para el restablecimiento de contraseña).
* **Actor de la actividad**: usuario que ha realizado parte del flujo de restablecimiento de contraseña. Puede tratarse de un usuario final o de un administrador.
* **Destino de la actividad**: usuario que realizó parte del flujo de restablecimiento de contraseña. Puede tratarse de un usuario final o de un administrador.
* **Estados de la actividad**:
  * _Correcto_: indica que un usuario ha completado correctamente un paso específico del flujo de restablecimiento de contraseña.
  * _Error_: indica que no se ha podido realizar un paso específico del flujo de restablecimiento de contraseña. Puede seleccionar la fila para ver la categoría **Motivo del estado de la actividad** y obtener más información sobre por qué se ha producido el error.
* **Razones para el estado actividad**: vea en la tabla siguiente [todos los motivos del estado de la actividad de restablecimiento permitidos](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Tipo de actividad: desbloqueo de la cuenta de usuario (autoservicio)

En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario ha desbloqueado correctamente su cuenta de Active Directory sin restablecer la contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com) mediante la característica de desbloqueo de cuentas sin restablecimiento de Active Directory.
* **Actor de la actividad**: usuario que ha desbloqueado su cuenta sin restablecer la contraseña. Puede tratarse de un usuario final o de un administrador.
* **Destino de la actividad**: usuario que ha desbloqueado su cuenta sin restablecer la contraseña. Puede tratarse de un usuario final o de un administrador.
* **Estados permitidos de la actividad**:
  * _Correcto_: indica que un usuario ha desbloqueado correctamente su propia cuenta.
  * _Error_: indica que un usuario no ha podido desbloquear su cuenta. Puede seleccionar la fila para ver la categoría **Motivo del estado de la actividad** y obtener más información sobre por qué se ha producido el error.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de actividad: Usuario registrado para el restablecimiento de contraseña de autoservicio

En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario ha registrado toda la información necesaria para restablecer la contraseña de acuerdo con la directiva de restablecimiento de contraseña de inquilino especificada actualmente. 
* **Actor de la actividad**: usuario que se ha registrado para el restablecimiento de contraseña. Puede tratarse de un usuario final o de un administrador.
* **Destino de la actividad**: usuario que se ha registrado para el restablecimiento de contraseña. Puede tratarse de un usuario final o de un administrador.
* **Estados permitidos de la actividad**:
  * _Correcto_: indica que un usuario se ha registrado correctamente para el restablecimiento de contraseña de acuerdo con la directiva actual. 
  * _Error_: indica que un usuario no ha podido registrarse para el restablecimiento de contraseña. Puede seleccionar la fila para ver la categoría **Motivo del estado de la actividad** y obtener más información sobre por qué se ha producido el error. 

     >[!NOTE]
     >Un error no implica que un usuario no pueda restablecer su propia contraseña. Sino que no finaliza el proceso de registro. Si en la cuenta hay datos no comprobados que son correctos (como un número de teléfono no validado), todavía pueden usarse para restablecer la contraseña aunque no estén comprobados. Para obtener más información, vea [¿Qué ocurre cuando se registra un usuario?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>pasos siguientes

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md).
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md).
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Creo que algo se ha roto. ¿Cómo se solucionan problemas en SSPR?](active-directory-passwords-troubleshoot.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Ejemplo de registros de auditoría de actividad de SSPR en Azure AD"
