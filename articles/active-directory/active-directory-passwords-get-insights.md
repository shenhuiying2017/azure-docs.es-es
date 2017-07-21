---
title: "Visión operativa con los informes de administración de contraseñas de Azure AD | Microsoft Docs"
description: "En este artículo se describe cómo usar los informes para obtener información sobre las operaciones de administración de contraseñas en su organización."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 1472b51d-53f4-4b0f-b1be-57f6fa88fa65
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 7375d2d3c237c3b1c2dcdab44b2fcb0000ff961c
ms.contentlocale: es-es
ms.lasthandoff: 05/04/2017

---
# <a name="how-to-get-operational-insights-with-password-management-reports"></a>Visión operativa con los informes de administración de contraseñas
> [!IMPORTANT]
> **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account).
>
>

En esta sección se describe cómo puede usar informes de administración de contraseñas de Azure Active Directory para ver cómo los usuarios utilizan el restablecimiento y el cambio de contraseña en su organización.

* [**Información general de los informes de administración de contraseñas**](#overview-of-password-management-reports)
* [**Visualización de los informes de administración de contraseñas en el nuevo Azure Portal**](#how-to-view-password-management-reports)
 * [Roles de directorio que pueden leer los informes](#directory-roles-allowed-to-read-reports)
* [**Tipos de actividad de administración de contraseñas de autoservicio en el nuevo Azure Portal**](#self-service-password-management-activity-types)
 * [Bloqueado para el restablecimiento de contraseña de autoservicio](#activity-type-blocked-from-self-service-password-reset)
 * [Cambio de contraseña (autoservicio)](#activity-type-change-password-self-service)
 * [Restablecimiento de contraseña (de parte del administrador)](#activity-type-reset-password-by-admin)
 * [Restablecimiento de contraseña (autoservicio)](#activity-type-reset-password-self-service)
 * [Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio](#activity-type-self-serve-password-reset-flow-activity-progress)
 * [Desbloqueo de la cuenta de usuario (autoservicio)](#activity-type-unlock-user-account-self-service)
 * [Usuario registrado para el restablecimiento de contraseña de autoservicio](#activity-type-user-registered-for-self-service-password-reset)
* [**Recuperación de eventos de administración de contraseñas desde la API de eventos e informes de Azure AD**](#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
 * [Informes de las limitaciones de recuperación de datos de la API](#reporting-api-data-retrieval-limitations)
* [**Descarga rápida de los eventos de registro de restablecimiento de contraseña con PowerShell**](#how-to-download-password-reset-registration-events-quickly-with-powershell)
* [**Visualización de los informes de administración de contraseñas en el portal clásico**](#how-to-view-password-management-reports-in-the-classic-portal)
* [**Visualización de la actividad de registro de restablecimiento de contraseña de la organización en el portal clásico**](#view-password-reset-registration-activity-in-the-classic-portal)
* [**Visualización de la actividad de restablecimiento de contraseña de la organización en el portal clásico**](#view-password-reset-activity-in-the-classic-portal)


## <a name="overview-of-password-management-reports"></a>Información general de los informes de administración de contraseñas
Después de implementar el restablecimiento de contraseña, uno de los siguientes pasos más comunes es ver cómo se usa en su organización.  Por ejemplo, puede que desee saber cómo se registran los usuarios para el restablecimiento de contraseña o cuántos restablecimientos de contraseña se han realizado en los últimos días.  Estas son algunas de las preguntas comunes que podrá responder con los informes de administración de contraseñas que existen actualmente en el [Portal de administración de Azure](https://manage.windowsazure.com):

* ¿Cuántas personas se han registrado para el restablecimiento de contraseña?
* ¿Quién se ha registrado para el restablecimiento de contraseña?
* ¿Qué datos está registrando la gente?
* ¿Cuántas personas restablecieron sus contraseñas en los últimos 7 días?
* ¿Cuáles son los métodos más comunes que utilizan los usuarios o administradores para restablecer sus contraseñas?
* ¿Cuáles son los problemas comunes que encuentran los usuarios o administradores al intentar utilizar el restablecimiento de contraseña?
* ¿Qué administradores restablecen sus propias contraseñas con frecuencia?
* ¿Hay alguna actividad sospechosa en relación con el restablecimiento de contraseña?

## <a name="how-to-view-password-management-reports"></a>Visualización de los informes de administración de contraseñas
En la nueva experiencia de [Azure Portal](https://portal.azure.com), contamos con una mejor forma de ver la actividad de registro de restablecimiento de contraseña y de restablecimiento de contraseña.  Siga los pasos siguientes para encontrar los eventos de registro de restablecimiento de contraseña y de restablecimiento de contraseña en el nuevo [Azure Portal](https://portal.azure.com):

1. Vaya a [**portal.azure.com**](https://portal.azure.com).
2. Haga clic en el menú **Más servicios** que aparece en la barra de navegación principal de la izquierda de Azure Portal.
3. Busque y seleccione **Azure Active Directory** en la lista de servicios.
4. Haga clic en **Usuarios y grupos** en el menú de navegación de Azure Active Directory.
5. Haga clic en el elemento de navegación **Registros de auditoría** en el menú de navegación Usuarios y grupos. De este modo verá todos los eventos de auditoría que se producen en todos los usuarios del directorio. Puede filtrar esta vista para ver también todos los eventos relacionados con contraseñas.
6. Para filtrar esta vista y ver únicamente los eventos relacionados con la administración de contraseñas, haga clic en el botón **Filtrar** que se encuentra en la parte superior de la hoja.
7. En el menú **Filtrar**, seleccione la lista desplegable **Categoría** o cámbiela al tipo de categoría **Administración de contraseñas de autoservicio**.
8. Si lo desea, también puede filtrar la lista si elige la **actividad** específica que le interesa.
### <a name="direct-link-to-user-audit-blade"></a>Vínculo directo a la hoja Auditoría de usuario
Si inició sesión en el portal, este es un vínculo directo a la hoja de auditoría de usuario en la que puede ver estos eventos:

* [Vaya directamente a la vista de auditoría de administración de usuario](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit)

### <a name="directory-roles-allowed-to-read-reports"></a>Roles de directorio que pueden leer los informes
Actualmente, los siguientes roles de directorio pueden leer los informes de administración de contraseñas de Azure AD en el Portal de Azure clásico:

* Administrador global

A fin de poder leer estos informes, un administrador global de la empresa debe haber optado porque estos datos se recuperen en nombre de la organización; para ello, debe haber ido a la pestaña de informes o a los registros de auditoría al menos una vez. Los datos de su organización no se recopilarán hasta que lo haga.

Para leer más sobre los roles de directorio y lo que pueden hacer, consulte [Asignación de roles de administrador en Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-assign-admin-roles).

## <a name="self-service-password-management-activity-types"></a>Tipos de actividad de administración de contraseñas de autoservicio
Los siguientes tipos de actividad aparecen en la categoría de evento de auditoría **Administración de contraseñas de autoservicio**.  A continuación aparece una descripción de cada uno de ellos.

* [**Bloqueado para el restablecimiento de contraseña de autoservicio**](#activity-type-blocked-from-self-service-password-reset): indica que un usuario intentó restablecer una contraseña, usar una puerta específica o validar un número de teléfono más de 5 veces en total en 24 horas.
* [**Cambio de contraseña (autoservicio)**](#activity-type-change-password-self-service): indica que un usuario realizó un cambio de contraseña voluntario o forzado (por expiración).
* [**Restablecimiento de contraseña (de parte del administrador)**](#activity-type-reset-password-by-admin): indica que un administrador realizó un restablecimiento de contraseña en nombre de un usuario en Azure Portal.
* [**Restablecimiento de contraseña (autoservicio)**](#activity-type-reset-password-self-service): indica que un usuario restableció correctamente su contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com).
* [**Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio**](#activity-type-self-serve-password-reset-flow-activity-progress): indica cada paso específico que un usuario sigue como parte del proceso de restablecimiento de contraseña (como atravesar una puerta de autenticación específica para el restablecimiento de contraseña).
* [**Desbloqueo de la cuenta de usuario**](#activity-type-unlock-user-account-self-service): indica que un usuario desbloqueó correctamente su cuenta de Active Directory sin restablecer la contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com) mediante la característica para [desbloquear cuentas de AD sin restablecimiento](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password).
* [**Usuario registrado para el restablecimiento de contraseña de autoservicio**](#activity-type-user-registered-for-self-service-password-reset): indica que un usuario registró toda la información requerida para restablecer la contraseña de acuerdo con la directiva de restablecimiento de contraseña del inquilino especificado actualmente.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Tipo de actividad: Bloqueado para el restablecimiento de contraseña de autoservicio
En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario intentó restablecer una contraseña, usar una puerta específica o validar un número de teléfono más de 5 veces en total en 24 horas.
* **Actor de la actividad**: el usuario que no pudo realizar operaciones de restablecimiento adicionales. Puede tratarse de un usuario final o un administrador.
* **Destino de la actividad**: el usuario que no pudo realizar operaciones de restablecimiento adicionales. Puede tratarse de un usuario final o un administrador.
* **Estados permitidos de la actividad**
 * _Correcto_: indica que un usuario no pudo realizar restablecimientos adicionales, intentar ningún otro método de autenticación ni validar ningún otro número de teléfono durante las próximas 24 horas.
* **Motivo del error del estado de la actividad**: no aplicable

### <a name="activity-type-change-password-self-service"></a>Tipo de actividad: Cambio de contraseña (autoservicio)
En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario realizó un cambio de contraseña voluntario o forzado (por expiración).
* **Actor de la actividad**: el usuario que cambió la contraseña. Puede tratarse de un usuario final o un administrador.
* **Destino de la actividad**: el usuario que cambió la contraseña. Puede tratarse de un usuario final o un administrador.
* **Estados permitidos de la actividad**
 * _Correcto_: indica que un usuario cambió correctamente la contraseña.
 * _Error_: indica que un usuario no pudo cambiar la contraseña. Si hace clic en la fila podrá ver la categoría **Motivo del estado de la actividad** para más información sobre por qué se produjo el error.
* **Motivo del error del estado de la actividad** -
 * _FuzzyPolicyViolationInvalidPassword_: el usuario seleccionó una contraseña que se prohibió automáticamente debido a que las funcionalidades de detección de contraseñas prohibidas de Microsoft consideraron que era demasiado común o especialmente poco segura.

### <a name="activity-type-reset-password-by-admin"></a>Tipo de actividad: Restablecimiento de contraseña (de parte del administrador)
En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un administrador realizó un restablecimiento de contraseña en nombre de un usuario en Azure Portal.
* **Actor de la actividad**: el administrador que realizó el restablecimiento de contraseña en nombre de otro usuario final o administrador. Debe ser un administrador global, administrador de contraseñas, administrador de usuarios o administrador del departamento de soporte técnico.
* **Destino de la actividad**: el usuario cuya contraseña se restableció. Puede tratarse de un usuario final u otro administrador.
* **Estados permitidos de la actividad**
 * _Correcto_: indica que un administrador restableció correctamente la contraseña de un usuario.
 * _Error_: indica que un administrador no pudo cambiar la contraseña de un usuario. Si hace clic en la fila podrá ver la categoría **Motivo del estado de la actividad** para más información sobre por qué se produjo el error.

### <a name="activity-type-reset-password-self-service"></a>Tipo de actividad: Restablecimiento de contraseña (autoservicio)
En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario restableció correctamente su contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com).
* **Actor de la actividad**: el usuario que restableció la contraseña. Puede tratarse de un usuario final o un administrador.
* **Destino de la actividad**: el usuario que restableció la contraseña. Puede tratarse de un usuario final o un administrador.
* **Estados permitidos de la actividad**
 * _Correcto_: indica que un usuario restableció correctamente su propia contraseña.
 * _Error_: indica que un usuario no pudo restablecer su propia contraseña. Si hace clic en la fila podrá ver la categoría **Motivo del estado de la actividad** para más información sobre por qué se produjo el error.
* **Motivo del error del estado de la actividad** -
 * _FuzzyPolicyViolationInvalidPassword_: el administrador seleccionó una contraseña que se prohibió automáticamente debido a que las funcionalidades de detección de contraseñas prohibidas de Microsoft consideraron que era demasiado común o especialmente poco segura.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Tipo de actividad: Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio
En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica cada paso específico que un usuario sigue como parte del proceso de restablecimiento de contraseña (como atravesar una puerta de autenticación específica para el restablecimiento de contraseña).
* **Actor de la actividad**: el usuario que realizó parte del flujo de restablecimiento de contraseña. Puede tratarse de un usuario final o un administrador.
* **Destino de la actividad**: el usuario que realizó parte del flujo de restablecimiento de contraseña. Puede tratarse de un usuario final o un administrador.
* **Estados permitidos de la actividad**
 * _Correcto_: indica que un usuario completó correctamente un paso específico del flujo de restablecimiento de contraseña.
 * _Error_: indica que hubo un error en un paso específico del flujo de restablecimiento de contraseña. Si hace clic en la fila podrá ver la categoría **Motivo del estado de la actividad** para más información sobre por qué se produjo el error.
* **Motivos del estado permitido de la actividad**
 * Consulte la tabla siguiente para ver [todos los motivos del estado permitido de la actividad de restablecimiento](#allowed-values-for-details-column)

### <a name="activity-type-unlock-user-account-self-service"></a>Tipo de actividad: Desbloqueo de la cuenta de usuario (autoservicio)
En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario desbloqueó correctamente su cuenta de Active Directory sin restablecer la contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com) mediante la característica para [desbloquear cuentas de AD sin restablecimiento](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-customize#allow-users-to-unlock-accounts-without-resetting-their-password).
* **Actor de la actividad**: el usuario que desbloqueó su cuenta sin restablecer la contraseña. Puede tratarse de un usuario final o un administrador.
* **Destino de la actividad**: el usuario que desbloqueó su cuenta sin restablecer la contraseña. Puede tratarse de un usuario final o un administrador.
* **Estados permitidos de la actividad**
 * _Correcto_: indica que un usuario desbloqueó correctamente su propia cuenta.
 * _Error_: indica que un usuario no pudo desbloquear su cuenta. Si hace clic en la fila podrá ver la categoría **Motivo del estado de la actividad** para más información sobre por qué se produjo el error.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Tipo de actividad: Usuario registrado para el restablecimiento de contraseña de autoservicio
En la lista siguiente se explica en detalle esta actividad:

* **Descripción de la actividad**: indica que un usuario registró toda la información requerida para restablecer la contraseña de acuerdo con la directiva de restablecimiento de contraseña del inquilino especificado actualmente.
* **Actor de la actividad**: el usuario que se registró para el restablecimiento de contraseña. Puede tratarse de un usuario final o un administrador.
* **Destino de la actividad**: el usuario que se registró para el restablecimiento de contraseña. Puede tratarse de un usuario final o un administrador.
* **Estados permitidos de la actividad**
 * _Correcto_: indica que un usuario se registró correctamente para el restablecimiento de contraseña de acuerdo con la directiva actual.
 * _Error_: indica que un usuario no pudo registrarse para el restablecimiento de contraseña. Si hace clic en la fila podrá ver la categoría **Motivo del estado de la actividad** para más información sobre por qué se produjo el error. Nota: Esto no significa que un usuario no puede restablecer su propia contraseña, sino simplemente que no completó el proceso de registro. Si en la cuenta hay datos no comprobados que son correctos (como un número de teléfono no validado), de todos modos pueden usarlos para restablecer la contraseña, incluso si no están comprobados. Para más información, consulte [¿Qué ocurre cuando se registra un usuario?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Recuperación de eventos de administración de contraseñas desde la API de eventos e informes de Azure AD
A partir de agosto de 2015, la API de eventos e informes de Azure AD admite ahora la recuperación de toda la información incluida en los informes de restablecimiento de contraseña y del registro de restablecimiento de contraseña. Mediante esta API, puede descargar eventos de registro de restablecimiento de contraseña individuales o eventos de registro de restablecimiento de contraseña para su integración con la tecnología de informes de su preferencia.

### <a name="how-to-get-started-with-the-reporting-api"></a>Introducción a la API de informes
Para obtener acceso a estos datos, deberá escribir una pequeña aplicación o un script para recuperarlos de nuestros servidores. [Obtenga información sobre cómo empezar con la API de informes de Azure AD](active-directory-reporting-api-getting-started.md).

Cuando tenga un script de trabajo, querrá examinar los eventos de registro y el restablecimiento de contraseña que puede recuperar para cumplir con sus escenarios.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): muestra las columnas disponibles para los eventos de restablecimiento de contraseña.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): muestra las columnas disponibles para los eventos de registro de restablecimiento de contraseña.

### <a name="reporting-api-data-retrieval-limitations"></a>Informes de las limitaciones de recuperación de datos de la API
Actualmente, la API de eventos e informes de Azure AD recupera hasta **75 000 eventos individuales** de tipo [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) y [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent), con lo que abarca los **últimos 30 días**.

Si necesita recuperar o almacenar datos más allá de este período, se sugiere mantenerlos en una base de datos externa y usar la API para consultar las diferencias que se generen. Un procedimiento recomendado es comenzar a recuperar estos datos cuando inicie el proceso de registro de restablecimiento de contraseña en la organización, mantenerlos en una base de datos externa y, luego, hacer seguimiento de las diferencias a partir de este momento.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Descarga rápida de los eventos de registro de restablecimiento de contraseña con PowerShell
Además de usar directamente la API de eventos e informes de Azure AD, también puede usar el siguiente script de PowerShell para consultar los eventos de registro recientes en el directorio. Esto resulta útil si desea ver quién se registró recientemente o si quisiera asegurarse de que la implementación del restablecimiento de contraseña se ejecuta según lo esperado.

* [Script de PowerShell para la actividad de registro de SSPR de Azure AD](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

## <a name="how-to-view-password-management-reports-in-the-classic-portal"></a>Visualización de los informes de administración de contraseñas en el portal clásico
Para buscar los informes de administración de contraseñas, siga estos pasos:

1. Haga clic en la extensión de **Active Directory** en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Seleccione el directorio de la lista que aparece en el portal.
3. Haga clic en la pestaña **Informes** .
4. Busque en la sección **Registros de actividad** .
5. Seleccione el informe **Actividad de restablecimiento de contraseña** o el informe **Actividad de registro de restablecimiento de contraseña**.

## <a name="view-password-reset-registration-activity-in-the-classic-portal"></a>Visualización de la actividad de registro de restablecimiento de contraseña en el portal clásico
El informe de actividad de registro de restablecimiento de contraseña muestra todos los registros de restablecimiento de contraseña que se han producido en su organización.  En este informe, se muestra un registro de restablecimiento de contraseña para cualquier usuario que haya registrado correctamente la información de autenticación en el portal de registro de restablecimiento de contraseña ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

* **Intervalo de tiempo máximo**: 30 días
* **Número máximo de filas**: 75 000
* **Se puede descargar**: Sí, en un archivo CSV

### <a name="description-of-report-columns"></a>Descripción de las columnas del informe
La siguiente lista explica en detalle cada una de las columnas del informe:

* **Usuario** : usuario que intentó una operación de registro de restablecimiento de contraseña.
* **Rol** : rol del usuario en el directorio.
* **Fecha y hora** : fecha y hora del intento.
* **Datos registrados** : datos de autenticación que el usuario proporcionó durante el registro de restablecimiento de contraseña.

### <a name="description-of-report-values"></a>Descripción de los valores del informe
En la tabla siguiente se describen los distintos valores permitidos para cada columna:

| Columna | Valores permitidos y su significado |
| --- | --- |
| Datos registrados |**Correo electrónico alternativo**: el usuario ha usado un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse.<p><p>**Teléfono de la oficina**: el usuario ha usado el teléfono de la oficina para autenticarse.<p>**Teléfono móvil**: el usuario ha usado un teléfono móvil o un teléfono de autenticación para autenticarse.<p>**Preguntas de seguridad**: el usuario ha usado preguntas de seguridad para autenticarse.<p>**Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)** : tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña. |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>Visualización de la actividad de restablecimiento de contraseña en el portal clásico
Este informe muestra todos los intentos de restablecimiento de contraseña que se han producido en su organización.

* **Intervalo de tiempo máximo**: 30 días
* **Número máximo de filas**: 75 000
* **Se puede descargar**: Sí, en un archivo CSV

### <a name="description-of-report-columns"></a>Descripción de las columnas del informe
La siguiente lista explica en detalle cada una de las columnas del informe:

1. **Usuario**: usuario que intentó una operación de restablecimiento de contraseña (basado en el campo Id. de usuario especificado cuando el usuario intenta restablecer una contraseña).
2. **Rol** : rol del usuario en el directorio.
3. **Fecha y hora** : fecha y hora del intento.
4. **Métodos usados** : métodos de autenticación que utiliza el usuario para esta operación de restablecimiento.
5. **Resultado** : resultado final de la operación de restablecimiento de contraseña.
6. **Detalles** : detalles de por qué el restablecimiento de contraseña dio ese resultado.  También incluye los pasos de mitigación que podría seguir para resolver un error inesperado.

### <a name="description-of-report-values"></a>Descripción de los valores del informe
En la tabla siguiente se describen los distintos valores permitidos para cada columna:

| Columna | Valores permitidos y su significado |
| --- | --- |
| Métodos usados |**Correo electrónico alternativo**: el usuario ha usado un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse.<p>**Teléfono de la oficina**: el usuario ha usado el teléfono de la oficina para autenticarse.<p>**Teléfono móvil**: el usuario ha usado un teléfono móvil o un teléfono de autenticación para autenticarse.<p>**Preguntas de seguridad**: el usuario ha usado preguntas de seguridad para autenticarse.<p>**Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)** : tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña. |
| Resultado |**Abandonado**: el usuario ha iniciado un restablecimiento de contraseña, pero lo ha interrumpido antes de que finalizara.<p>**Bloqueado**: se ha impedido que la cuenta de usuario usara el restablecimiento de contraseña debido a un intento de usar la página de restablecimiento de contraseña o una sola puerta de restablecimiento de contraseña demasiadas veces en un período de 24 horas.<p>**Cancelado**: el usuario ha iniciado un restablecimiento de contraseña, pero después hizo clic en el botón Cancelar para cancelar la sesión en mitad del proceso. <p>**Administrador notificado**: el usuario ha tenido un problema durante su sesión que no pudo resolver, por lo que hizo clic en el vínculo “Póngase en contacto con el administrador”, en lugar de finalizar el flujo de restablecimiento de contraseña.<p>**Con error**: el usuario no ha podido restablecer una contraseña, probablemente porque el usuario no estaba configurado para usar esta característica (por ejemplo, no tiene licencia, falta información de autenticación, o bien la contraseña se administra en el entorno local, pero la escritura diferida está desactivada).<p>**Correcto** : la contraseña se restableció correctamente. |
| Detalles |Consulte la tabla siguiente: |

### <a name="allowed-values-for-details-column"></a>Valores permitidos para la columna Detalles
A continuación se muestra la lista de los tipos de resultados que puede esperar cuando usa el informe de actividad de restablecimiento de contraseña:

| Detalles | Tipo de resultado |
| --- | --- |
| El usuario abandonó tras completar la opción de comprobación de correo electrónico. |Abandonado |
| El usuario abandonó tras completar la opción de comprobación por SMS en el teléfono móvil. |Abandonado |
| El usuario abandonó tras completar la opción de comprobación por llamada de voz al teléfono móvil. |Abandonado |
| El usuario abandonó tras completar la opción de comprobación por llamada de voz a la oficina. |Abandonado |
| El usuario abandonó tras completar la opción de preguntas de seguridad. |Abandonado |
| El usuario abandonó después de escribir su identificador de usuario. |Abandonado |
| El usuario abandonó tras iniciar la opción de comprobación de correo electrónico. |Abandonado |
| El usuario abandonó tras iniciar la opción de comprobación por SMS en el teléfono móvil. |Abandonado |
| El usuario abandonó tras iniciar la opción de comprobación por llamada de voz al teléfono móvil. |Abandonado |
| El usuario abandonó tras iniciar la opción de comprobación por llamada de voz a la oficina. |Abandonado |
| El usuario abandonó tras iniciar la opción de preguntas de seguridad. |Abandonado |
| El usuario abandonó antes de seleccionar una nueva contraseña. |Abandonado |
| El usuario abandonó mientras seleccionaba una nueva contraseña. |Abandonado |
| El usuario escribió demasiados códigos de comprobación por SMS no válidos y se ha bloqueado durante 24 horas. |Bloqueado |
| El usuario intentó la comprobación por llamada de voz al teléfono móvil demasiadas veces y se ha bloqueado durante 24 horas. |Bloqueado |
| El usuario intentó la comprobación por llamada de voz al teléfono de la oficina demasiadas veces y se ha bloqueado durante 24 horas. |Bloqueado |
| El usuario intentó responder las preguntas de seguridad demasiadas veces y se ha bloqueado durante 24 horas. |Bloqueado |
| El usuario intentó comprobar un número de teléfono demasiadas veces y se ha bloqueado durante 24 horas. |Bloqueado |
| El usuario lo canceló antes de pasar a los métodos de autenticación requeridos. |Cancelado |
| El usuario lo canceló antes de enviar una contraseña nueva. |Cancelado |
| El usuario se puso en contacto con un administrador después de intentar la opción de comprobación de correo electrónico. |Administrador contactado |
| El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por SMS en el teléfono móvil. |Administrador contactado |
| El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por llamada de voz al teléfono móvil. |Administrador contactado |
| El usuario se puso en contacto con un administrador después de intentar la opción de comprobación por llamada de voz a la oficina. |Administrador contactado |
| El usuario se puso en contacto con un administrador después de intentar la opción de comprobación con preguntas de seguridad. |Administrador contactado |
| El restablecimiento de contraseña no está habilitado para este usuario. Habilite el restablecimiento de contraseña en la pestaña Configurar para resolver este problema. |Con error |
| El usuario no tiene una licencia. Puede agregar una licencia al usuario para resolver este problema. |Con error |
| El usuario intentó el restablecimiento desde un dispositivo sin las cookies habilitadas. |Con error |
| La cuenta del usuario no tiene definidos suficientes métodos de autenticación. Agregue información de autenticación para resolver este problema. |Con error |
| La contraseña del usuario se administra en el entorno local. Puede habilitar la escritura diferida de contraseña para resolver este problema. |Con error |
| No pudimos obtener acceso a su servicio de restablecimiento de contraseña local. Compruebe el registro de eventos de su máquina de sincronización. |Con error |
| Se encontró un problema durante el restablecimiento de la contraseña local del usuario. Compruebe el registro de eventos de su máquina de sincronización. |Con error |
| Este usuario no es miembro del grupo de usuarios de restablecimiento de contraseña. Agregue este usuario a ese grupo para resolver este problema. |Con error |
| El restablecimiento de contraseña se ha deshabilitado por completo para este inquilino. Consulte [aquí](http://aka.ms/ssprtroubleshoot) para resolver este problema. |Con error |
| El usuario restableció la contraseña correctamente. |Correcto |

## <a name="next-steps"></a>Pasos siguientes
A continuación se muestran vínculos a todas las páginas de documentación de restablecimiento de contraseña de Azure AD:

* **¿Está aquí porque tiene problemas para iniciar sesión?** Si es así, [aquí aprenderá a cambiar y restablecer la contraseña](active-directory-passwords-update-your-own-password.md#reset-or-unlock-my-password-for-a-work-or-school-account).
* [**Funcionamiento**](active-directory-passwords-how-it-works.md): obtenga información acerca de los seis componentes diferentes del servicio y lo que hace cada uno.
* [**Introducción**](active-directory-passwords-getting-started.md): obtenga información sobre cómo permitir a los usuarios restablecer y cambiar sus contraseñas en la nube o locales.
* [**Personalizar**](active-directory-passwords-customize.md): obtenga información acerca de cómo personalizar la apariencia y el comportamiento del servicio para ajustarse a las necesidades de su organización
* [**Procedimientos recomendados**](active-directory-passwords-best-practices.md): obtenga información acerca de cómo implementar rápidamente y administrar eficazmente las contraseñas de la organización
* [**P+F**](active-directory-passwords-faq.md) : obtenga respuestas a las preguntas más frecuentes.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): obtenga información sobre cómo solucionar rápidamente los problemas del servicio.
* [**Más información**](active-directory-passwords-learn-more.md): profundice en los detalles técnicos del funcionamiento del servicio.

