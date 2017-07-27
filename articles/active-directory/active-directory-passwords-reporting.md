---
title: "Creación de informes: SSPR de Azure AD | Microsoft Docs"
description: "Creación de informes sobre eventos de autoservicio de restablecimiento de contraseña de Azure AD"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: joflore
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 4d7f05d626e6f718ca3597dff00715af4055f046
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017

---
# <a name="reporting-options-for-azure-ad-password-management"></a>Opciones de creación de informes para la administración de contraseñas de Azure AD

Tras la implementación, muchas organizaciones desean saber cómo se usa realmente SSPR. Azure AD proporciona características de creación de informes que ayudan a responder a preguntas mediante informes predefinidos y, si dispone de la licencia adecuada, también permite crear consultas personalizadas.

Las siguientes preguntas se pueden responder mediante informes existentes en [Azure Portal] (https://portal.azure.com/).

> [!NOTE]
> Debe ser [un administrador global](active-directory-assign-admin-roles.md#assign-or-remove-administrator-roles) y debe seleccionar la opción para que estos datos se recopilen en nombre de la organización; para ello, vaya a la pestaña de creación de informes o consulte los registros de auditoría al menos una vez. Los datos de su organización no se recopilarán hasta que lo haga.

* ¿Cuántas personas se han registrado para el restablecimiento de contraseña?
* ¿Quién se ha registrado para el restablecimiento de contraseña?
* ¿Qué datos está registrando la gente?
* ¿Cuántas personas han restablecido sus contraseñas en los últimos siete días?
* ¿Cuáles son los métodos más comunes que utilizan los usuarios o administradores para restablecer sus contraseñas?
* ¿Cuáles son los problemas comunes que encuentran los usuarios o administradores al intentar utilizar el restablecimiento de contraseña?
* ¿Qué administradores restablecen sus propias contraseñas con frecuencia?
* ¿Hay alguna actividad sospechosa en relación con el restablecimiento de contraseña?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Visualización de los informes de administración de contraseñas en Azure Portal

En la experiencia de Azure Portal, se ha mejorado la visualización del restablecimiento de contraseña y de la actividad de registro de dicho restablecimiento.  Realice los pasos siguientes para encontrar los eventos de registro de restablecimiento de contraseña y de restablecimiento de contraseña:

1. Vaya a [**portal.azure.com**](https://portal.azure.com).
2. Haga clic en el menú **Más servicios** que aparece en la barra de navegación principal de la izquierda de Azure Portal.
3. Busque y seleccione **Azure Active Directory** en la lista de servicios.
4. Haga clic en **Usuarios y grupos** en el menú de navegación de Azure Active Directory.
5. Haga clic en el elemento de navegación **Registros de auditoría** en el menú de navegación Usuarios y grupos. Se muestran todos los eventos de auditoría que se producen en todos los usuarios del directorio. Puede filtrar esta vista para ver también todos los eventos relacionados con contraseñas.
6. Para filtrar esta vista y ver únicamente los eventos relacionados con el restablecimiento de contraseñas, haga clic en el botón **Filtrar** que se encuentra en la parte superior de la hoja.
7. En el menú **Filtrar**, seleccione la lista desplegable **Categoría** o cámbiela al tipo de categoría **Administración de contraseñas de autoservicio**.
8. Si lo desea, también puede filtrar la lista si elige la **actividad** específica que le interesa.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Recuperación de eventos de administración de contraseñas desde la API de eventos e informes de Azure AD

La API de eventos e informes de Azure AD admite la recuperación de toda la información incluida en los informes de restablecimiento de contraseña y del registro de restablecimiento de contraseña. Mediante esta API, puede descargar eventos de registro de restablecimiento de contraseña individuales o eventos de registro de restablecimiento de contraseña para su integración con la tecnología de informes de su preferencia.

### <a name="how-to-get-started-with-the-reporting-api"></a>Introducción a la API de informes

Para obtener acceso a estos datos, debe escribir una pequeña aplicación o un script para recuperarlos de los servidores. [Obtenga información sobre cómo empezar con la API de informes de Azure AD](active-directory-reporting-api-getting-started.md).

Cuando tenga un script de trabajo, querrá examinar los eventos de registro y el restablecimiento de contraseña que puede recuperar para cumplir con sus escenarios.

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): muestra las columnas disponibles para los eventos de restablecimiento de contraseña.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): muestra las columnas disponibles para los eventos de registro de restablecimiento de contraseña.

### <a name="reporting-api-data-retrieval-limitations"></a>Informes de las limitaciones de recuperación de datos de la API

Actualmente, la API de eventos e informes de Azure AD recupera hasta **75 000 eventos individuales** de tipo [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) y [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent), con lo que abarca los **últimos 30 días**.

Si necesita recuperar o almacenar datos más allá de este período, se sugiere mantenerlos en una base de datos externa y usar la API para consultar las diferencias que se generen. Se recomienda comenzar a recuperar estos datos cuando empiece a usar SSPR en la organización, mantenerlos en una base de datos externa y, luego, hacer seguimiento de las diferencias a partir de este momento.

## <a name="how-to-download-password-reset-registration-events-quickly-with-powershell"></a>Descarga rápida de los eventos de registro de restablecimiento de contraseña con PowerShell

Además de usar directamente la API de eventos e informes de Azure AD, también puede usar el siguiente script de PowerShell para consultar los eventos de registro recientes en el directorio. Esto resulta útil si desea ver quién se registró recientemente o si quisiera asegurarse de que la implementación del restablecimiento de contraseña se ejecuta según lo esperado.

* [Script de PowerShell para la actividad de registro de SSPR de Azure AD](https://gallery.technet.microsoft.com/scriptcenter/azure-ad-self-service-e31b8aee)

### <a name="description-of-report-columns-in-azure-portal"></a>Descripción de las columnas del informe en Azure Portal

La siguiente lista explica en detalle cada una de las columnas del informe:

* **Usuario** : usuario que intentó una operación de registro de restablecimiento de contraseña.
* **Rol** : rol del usuario en el directorio.
* **Fecha y hora** : fecha y hora del intento.
* **Datos registrados** : datos de autenticación que el usuario proporcionó durante el registro de restablecimiento de contraseña.

### <a name="description-of-report-values-in-azure-portal"></a>Descripción de los valores del informe en Azure Portal

En la tabla siguiente se describen los distintos valores permitidos para cada columna:

| Columna | Valores permitidos y su significado |
| --- | --- |
| Datos registrados |**Correo electrónico alternativo**: el usuario ha usado un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse.<p><p>**Teléfono de la oficina**: el usuario ha usado el teléfono de la oficina para autenticarse.<p>**Teléfono móvil**: el usuario ha usado un teléfono móvil o un teléfono de autenticación para autenticarse.<p>**Preguntas de seguridad**: el usuario ha usado preguntas de seguridad para autenticarse.<p>**Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)**: tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña. |

## <a name="view-password-reset-activity-in-the-classic-portal"></a>Visualización de la actividad de restablecimiento de contraseña en el portal clásico

Este informe muestra todos los intentos de restablecimiento de contraseña que se han producido en su organización.

* **Intervalo de tiempo máximo**: 30 días
* **Número máximo de filas**: 75 000
* **Se puede descargar**: Sí, en un archivo CSV

### <a name="description-of-report-columns-in-azure-classic-portal"></a>Descripción de las columnas del informe en el Portal de Azure clásico

La siguiente lista explica en detalle cada una de las columnas del informe:

1. **Usuario**: usuario que intentó una operación de restablecimiento de contraseña (basado en el campo Id. de usuario especificado cuando el usuario intenta restablecer una contraseña).
2. **Rol** : rol del usuario en el directorio.
3. **Fecha y hora** : fecha y hora del intento.
4. **Métodos usados**: métodos de autenticación que utiliza el usuario para esta operación de restablecimiento.
5. **Resultado**: resultado de la operación de restablecimiento de contraseña.
6. **Detalles** : detalles de por qué el restablecimiento de contraseña dio ese resultado.  También incluye los pasos de mitigación que podría seguir para resolver un error inesperado.

### <a name="description-of-report-values-in-azure-classic-portal"></a>Descripción de los valores del informe en el Portal de Azure clásico

En la tabla siguiente se describen los distintos valores permitidos para cada columna:

| Columna | Valores permitidos y su significado |
| --- | --- |
| Métodos usados |**Correo electrónico alternativo**: el usuario ha usado un correo electrónico alternativo o un correo electrónico de autenticación para autenticarse.<p>**Teléfono de la oficina**: el usuario ha usado el teléfono de la oficina para autenticarse.<p>**Teléfono móvil**: el usuario ha usado un teléfono móvil o un teléfono de autenticación para autenticarse.<p>**Preguntas de seguridad**: el usuario ha usado preguntas de seguridad para autenticarse.<p>**Cualquier combinación de los valores anteriores (por ejemplo, correo electrónico alternativo + teléfono móvil)**: tiene lugar cuando se especifica una directiva de 2 puertas y muestra los dos métodos que el usuario utilizó para autenticar su solicitud de restablecimiento de contraseña. |
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
| El usuario lo canceló antes de pasar a los métodos de autenticación requeridos. |Canceled |
| El usuario lo canceló antes de enviar una contraseña nueva. |Canceled |
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

## <a name="self-service-password-management-activity-types"></a>Tipos de actividad de administración de contraseñas de autoservicio

Los siguientes tipos de actividad aparecen en la categoría de evento de auditoría **Administración de contraseñas de autoservicio**.  Una descripción de cada uno de ellos.

* [**Bloqueado para el restablecimiento de contraseña de autoservicio**](#activity-type-blocked-from-self-service-password-reset): indica que un usuario intentó restablecer una contraseña, usar una puerta específica o validar un número de teléfono más de 5 veces en total en 24 horas.
* [**Cambio de contraseña (autoservicio)**](#activity-type-change-password-self-service): indica que un usuario realizó un cambio de contraseña voluntario o forzado (por expiración).
* [**Restablecimiento de contraseña (de parte del administrador)**](#activity-type-reset-password-by-admin): indica que un administrador realizó un restablecimiento de contraseña en nombre de un usuario en Azure Portal.
* [**Restablecimiento de contraseña (autoservicio)**](#activity-type-reset-password-self-service): indica que un usuario restableció correctamente su contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com).
* [**Progreso de la actividad del flujo de restablecimiento de contraseña de autoservicio**](#activity-type-self-serve-password-reset-flow-activity-progress): indica cada paso específico que un usuario sigue como parte del proceso de restablecimiento de contraseña (como atravesar una puerta de autenticación específica para el restablecimiento de contraseña).
* [**Desbloqueo de la cuenta de usuario**](#activity-type-unlock-user-account-self-service): indica que un usuario desbloqueó correctamente su cuenta de Active Directory sin restablecer la contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com) mediante la característica para desbloquear cuentas de AD sin restablecimiento.
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
  * _FuzzyPolicyViolationInvalidPassword_: el usuario seleccionó una contraseña que se prohibió automáticamente debido a que las funcionalidades de detección de contraseñas prohibidas de Microsoft consideraron que era demasiado común o muy poco segura.

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
  * _FuzzyPolicyViolationInvalidPassword_: el administrador seleccionó una contraseña que se prohibió automáticamente debido a que las funcionalidades de detección de contraseñas prohibidas de Microsoft consideraron que era demasiado común o muy poco segura.

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

* **Descripción de la actividad**: indica que un usuario desbloqueó correctamente su cuenta de Active Directory sin restablecer la contraseña en el [portal de restablecimiento de contraseña de Azure AD](https://passwordreset.microsoftonline.com) mediante la característica para desbloquear cuentas de AD sin restablecimiento.
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

## <a name="next-steps"></a>Pasos siguientes

Los vínculos siguientes proporcionan información adicional sobre el restablecimiento de contraseñas con Azure AD:

* [Acceso directo a los registros de auditoría de administración de usuario](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Audit): se le remite directamente a los registros de auditoría de administración de usuario del inquilino
* [**Inicio rápido**](active-directory-passwords-getting-started.md): preparativos para el autoservicio de administración de contraseñas de Azure AD 
* [**Licencias**](active-directory-passwords-licensing.md): configuración de licencias de Azure AD
* [**Datos**](active-directory-passwords-data.md): información sobre los datos necesarios y cómo se usan para administrar contraseñas
* [**Implementación**](active-directory-passwords-best-practices.md): planificación e implementación de SSPR para los usuarios con las directrices que aquí se proporcionan
* [**Personalización**](active-directory-passwords-customize.md): personalización de la experiencia de SSPR para la empresa
* [**Artículo técnico de profundización**](active-directory-passwords-how-it-works.md): más información para comprender el funcionamiento de la administración de contraseñas
* [**Preguntas más frecuentes**](active-directory-passwords-faq.md): ¿Cómo? ¿Por qué? ¿Qué? ¿Dónde? ¿Quién? ¿Cuándo? : respuestas a las preguntas que siempre se ha hecho.
* [**Solución de problemas**](active-directory-passwords-troubleshoot.md): información para resolver problemas habituales de SSPR
* [**Directiva**](active-directory-passwords-policy.md): información sobre las directivas de contraseñas de Azure AD y cómo establecerlas

