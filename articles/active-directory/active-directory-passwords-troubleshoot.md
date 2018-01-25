---
title: "Solución de problemas del autoservicio de restablecimiento de contraseña: Azure Active Directory"
description: "Solución de problemas de autoservicio de restablecimiento de contraseña de Azure AD"
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
ms.openlocfilehash: c038a9ec682a5971a5f79b9fe36e667493702cbd
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-self-service-password-reset"></a>Solución de problemas del autoservicio de restablecimiento de contraseñas

¿Tiene un problema con el autoservicio de restablecimiento de contraseñas (SSPR) de Azure Active Directory (Azure AD)? La siguiente información puede ayudarle a conseguir que funcione de nuevo.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Solución de los errores del autoservicio de restablecimiento de contraseñas que el usuario puede encontrarse

| Error | Detalles | Detalles técnicos |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | En este momento no se puede restablecer la contraseña porque el administrador ha deshabilitado la característica en la organización. No hay ninguna otra acción que puede realizar para resolver esta situación. Póngase en contacto con el administrador y pídale que habilite esta característica. Para más información, vea [Ayuda, he olvidado mi contraseña de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: se ha detectado que el administrador no ha habilitado el restablecimiento de contraseñas. Póngase en contacto con el administrador y pídale que lo habilite en la organización. |
| WritebackNotEnabled = 10 |En este momento no se puede restablecer la contraseña porque el administrador no ha habilitado un servicio necesario para la organización. No hay ninguna otra acción que puede realizar para resolver esta situación. Póngase en contacto con el administrador y pídale que compruebe la configuración de su organización. Para más información sobre este servicio necesario, vea [Configuración de la escritura diferida de contraseñas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback). | SSPR_0010: se ha detectado que no se ha habilitado la escritura diferida de contraseñas. Póngase en contacto con el administrador y pídale que la habilite. |
| SsprNotEnabledInUserPolicy = 11 | En este momento no se puede restablecer la contraseña porque el administrador no ha configurado el restablecimiento de contraseñas en la organización. No hay ninguna otra acción que puede realizar para resolver esta situación. Póngase en contacto con él y pídale que lo configure. Para más información sobre la configuración del restablecimiento de contraseñas, vea [Implementación rápida del autoservicio de restablecimiento de contraseña de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Su organización no ha definido una directiva de restablecimiento de contraseña. Póngase en contacto con el administrador y pídale que defina una directiva de restablecimiento de contraseña. |
| UserNotLicensed = 12 | En este momento no se puede restablecer la contraseña porque faltan licencias necesarias en la organización. No hay ninguna otra acción que puede realizar para resolver esta situación. Póngase en contacto con el administrador y pídale que compruebe su asignación de licencias. Para más información sobre licencias, vea [Requisitos de concesión de licencias del autoservicio de restablecimiento de contraseña de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Su organización no tiene las licencias necesarias para realizar el restablecimiento de contraseña. Póngase en contacto con el administrador y pídale que revise las asignaciones de licencia. |
| UserNotMemberOfScopedAccessGroup = 13 | En este momento no se puede restablecer la contraseña porque el administrador no ha configurado la cuenta para que use el restablecimiento de contraseñas. No hay ninguna otra acción que puede realizar para resolver esta situación. Póngase en contacto con el administrador y pídale que configure su cuenta con el restablecimiento de contraseña. Para más información sobre la configuración de cuentas en cuanto al restablecimiento de contraseñas, vea [Cómo implementar correctamente el lanzamiento del restablecimiento de contraseña de autoservicio](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: No es miembro de un grupo habilitado para restablecer contraseñas. Póngase en contacto con el administrador y pídale que lo agregue al grupo. |
| UserNotProperlyConfigured = 14 | En este momento no se puede restablecer la contraseña porque falta información necesaria de su cuenta. No hay ninguna otra acción que puede realizar para resolver esta situación. Póngase en contacto con el administrador y pídale que restablezca la contraseña por usted. Después de tener acceso a su cuenta de nuevo, debe registrar la información requerida. Para ello, siga los pasos que se indican en el artículo [Registro para el autoservicio de restablecimiento de contraseñas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register). | SSPR_0014: Se necesita información de seguridad adicional para restablecer la contraseña. Para continuar, póngase en contacto con el administrador y pídale que restablezca la contraseña. Después de tener acceso a su cuenta, puede registrar información de seguridad adicional en https://aka.ms/ssprsetup. El administrador puede agregar información de seguridad adicional a su cuenta siguiendo los pasos de [Establecimiento y lectura de datos de autenticación mediante PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | En este momento no se puede restablecer la contraseña debido a un problema con la configuración del restablecimiento de contraseñas de la organización. No hay ninguna otra acción que puede realizar para resolver esta situación. Póngase en contacto con el administrador y pídale que lo investigue. Para más información sobre el posible problema, vea [Solución de problemas de escritura diferida de contraseñas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: No se puede restablecer la contraseña debido a un error en la configuración local. Póngase en contacto con el administrador y pídale que lo investigue. |
| OnPremisesConnectivityError = 30 | En este momento no se puede restablecer la contraseña debido a problemas de conectividad con su organización. No hay ninguna acción que pueda realizar ahora mismo, pero puede que el problema se resuelva si lo intenta más tarde. Si el problema continúa, póngase en contacto con el administrador y pídale que lo investigue. Para más información sobre problemas de conectividad, vea [Solución de problemas con la conectividad de la escritura diferida de contraseñas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: no se puede restablecer la contraseña debido a una mala conexión con el entorno local. Póngase en contacto con el administrador y pídale que lo investigue.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Solución de problemas de configuración del restablecimiento de contraseñas en Azure Portal

| Error | Solución |
| --- | --- |
| No veo la sección **Restablecimiento de contraseña** en Azure AD en Azure Portal. | Este problema puede ocurrir si no tiene una licencia de Azure Active Directory Premium o Azure Active Directory Basic asignada al administrador que realiza la operación. <br> <br> Asigne una licencia a la cuenta de administrador en cuestión. Puede seguir los pasos descritos en el artículo [Asignación, comprobación y solución de los problemas de licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).|
| No veo una opción de configuración determinada. | Muchos elementos de la interfaz de usuario están ocultos hasta que se necesitan. Pruebe a habilitar todas las opciones que desee ver. |
| No veo la pestaña **Integración local**. | Esta opción solo está visible si ha descargado Azure AD Connect y configurado la escritura diferida de contraseñas. Para más información, vea [Introducción a Azure AD Connect mediante la configuración rápida](./connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Solución de problemas de informe de restablecimiento de contraseña

| Error | Solución |
| --- | --- |
| No veo ningún tipo de actividad de administración de contraseñas en la categoría de evento de auditoría **Administración de autoservicio de contraseñas**. | Este problema puede ocurrir si no tiene una licencia de Azure Active Directory Premium o Azure Active Directory Basic asignada al administrador que realiza la operación. <br> <br> Puede resolver este problema si asigna una licencia a la cuenta de administrador en cuestión. Siga los pasos descritos en el artículo [Asignación, comprobación y solución de los problemas de licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses). |
| Los registros de usuario se muestran varias veces. | Actualmente, cuando un usuario se registra, anotamos cada dato individual registrado como un evento independiente. <br> <br> Si desea agregar estos datos y tener mayor flexibilidad en el modo de verlos, puede descargar el informe y abrirlos como una tabla dinámica de Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Solución de problemas con el portal de registro de restablecimiento de contraseña

| Error | Solución |
| --- | --- |
| El directorio no está habilitado para el restablecimiento de contraseñas. **El administrador no lo ha habilitado para usar esta característica.** | Cambie la marca **Se habilitó el restablecimiento de contraseña del autoservicio** a **Seleccionado** o **Todos**, y haga clic en **Guardar**. |
| El usuario no tiene asignada una licencia de Azure Active Directory Premium o Azure Active Directory Basic. **El administrador no lo ha habilitado para usar esta característica.** | Este problema puede ocurrir si no tiene una licencia de Azure Active Directory Premium o Azure Active Directory Basic asignada al administrador que realiza la operación. <br> <br> Puede resolver este problema si asigna una licencia a la cuenta de administrador en cuestión. Siga los pasos descritos en el artículo [Asignación, comprobación y solución de los problemas de licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses).|
| Se produjo un error al procesar la solicitud. | Aunque las causas pueden ser varias, en general este error se debe a una interrupción del servicio o a un problema de configuración. Si experimenta este error y afecta a su negocio, póngase en contacto con el servicio de soporte técnico de Microsoft para recibir ayuda adicional. |

## <a name="troubleshoot-the-password-reset-portal"></a>Solución de problemas con el portal de restablecimiento de contraseña

| Error | Solución |
| --- | --- |
| El directorio no está habilitado para el restablecimiento de contraseñas. | Cambie la marca **Se habilitó el restablecimiento de contraseña del autoservicio** a **Seleccionado** o **Todos**, y haga clic en **Guardar**. |
| El usuario no tiene asignada una licencia de Azure Active Directory Premium o Azure Active Directory Basic. | Este problema puede ocurrir si no tiene una licencia de Azure Active Directory Premium o Azure Active Directory Basic asignada al administrador que realiza la operación. <br> <br> Puede resolver este problema si asigna una licencia a la cuenta de administrador en cuestión. Siga los pasos descritos en el artículo [Asignación, comprobación y solución de los problemas de licencias](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses). |
| El directorio está habilitado para restablecer la contraseña, pero falta la información de autenticación del usuario o es incorrecta. | Antes de continuar, asegúrese de que el usuario ha formado correctamente los datos de contacto en el archivo del directorio. Para obtener más información, consulte los [Datos usados en el autoservicio de restablecimiento de contraseña de Azure AD](active-directory-passwords-data.md). |
| El directorio está habilitado para restablecer la contraseña, pero el usuario tiene solo una parte de los datos de contacto en el archivo cuando la directiva está configurada para requerir dos métodos de verificación. | Antes de continuar, asegúrese de que el usuario tenga al menos dos métodos de contacto configurados correctamente. Un ejemplo es tener tanto un número de teléfono móvil *como* un número de teléfono de trabajo. |
| El directorio está habilitado para restablecer la contraseña y el usuario está configurado correctamente, pero no es posible ponerse en contacto con él. | Puede deberse a un error de servicio temporal o a que hay datos de contacto configurados incorrectamente que no hemos podido detectar como es debido. <br> <br> Si el usuario espera 10 segundos, aparecen los vínculos "Volver a intentarlo" y "Póngase en contacto con el administrador". Si el usuario selecciona "Volver a intentarlo", vuelve a intentar la llamada. Si el usuario selecciona "Póngase en contacto con el administrador", se envía a los administradores un correo electrónico de formulario para solicitarles que realicen el restablecimiento de la contraseña para esa cuenta de usuario. |
| El usuario nunca recibe la llamada de teléfono o el SMS de restablecimiento de la contraseña. | Esto puede deberse a que se haya incluido un número de teléfono incorrecto en el directorio. Asegúrese de que el número de teléfono tiene el formato "+ccc xxxyyyzzzzXeeee". <br> <br> El restablecimiento de la contraseña no admite extensiones, aunque especifique una en el directorio. Las extensiones se quitan antes de realizar la llamada. Use un número sin una extensión o integre la extensión en el número de teléfono en su central de conmutación (PBX). |
| El usuario nunca recibe el correo electrónico de restablecimiento de la contraseña. | La causa más común de este problema es que un filtro de correo no deseado rechace el mensaje. Compruebe la carpeta de elementos eliminados o correo no deseado para comprobar si ha recibido el correo. <br> <br> Asegúrese también de comprobar la cuenta de correo electrónico correcta para el mensaje. |
| He configurado una directiva para restablecer la contraseña, pero, cuando una cuenta de administrador usa el restablecimiento de contraseña, esa directiva no se aplica. | Microsoft administra y controla la directiva de restablecimiento de contraseña de administrador para garantizar el nivel de seguridad más alto. |
| Se impide que el usuario trate de restablecer la contraseña demasiadas veces al día. | Implementamos un mecanismo de limitación automático para evitar que los usuarios intenten restablecer sus contraseñas demasiadas veces en un breve período de tiempo. La limitación se produce cuando: <br><ul><li>Un usuario intenta validar un número de teléfono cinco veces en una hora.</li><li>Un usuario intenta utilizar la puerta de preguntas de seguridad cinco veces en una hora.</li><li>Un usuario intenta restablecer la contraseña de la misma cuenta de usuario cinco veces en una hora.</li></ul>Para solucionar este problema, pida al usuario que espere 24 horas después del último intento. El usuario puede entonces restablecer su contraseña. |
| El usuario experimenta un error al validar su número de teléfono. | Este error se produce cuando el número de teléfono especificado no coincide con el número de teléfono archivado. Asegúrese de que el usuario escribe el número de teléfono completo, incluidos el código de área y el país, al intentar utilizar un método basado en teléfono para restablecer la contraseña. |
| Se produjo un error al procesar la solicitud. | Aunque las causas pueden ser varias, en general este error se debe a una interrupción del servicio o a un problema de configuración. Si experimenta este error y afecta a su negocio, póngase en contacto con el servicio de soporte técnico de Microsoft para recibir ayuda adicional. |

## <a name="troubleshoot-password-writeback"></a>Solución de problemas de escritura diferida de contraseñas

| Error | Solución |
| --- | --- |
| El servicio de restablecimiento de la contraseña no se inicia de forma local. Aparece el error 6800 en el registro de eventos de aplicación de la máquina de Azure AD Connect. <br> <br> Después de la incorporación, los usuarios federados o con sincronización mediante hash de la contraseña no pueden restablecer sus contraseñas. | Cuando está habilitada la escritura diferida de contraseñas, el motor de sincronización llama a la biblioteca de escritura diferida para realizar la configuración (incorporación) comunicándose con el servicio de incorporación de la nube. Los errores detectados durante la incorporación o al iniciar el punto de conexión de Windows Communication Foundation (WCF) para la escritura diferida de contraseñas producirán otros errores en el registro de eventos de la máquina de Azure AD Connect. <br> <br> Durante el reinicio del servicio Azure AD Sync (ADSync), si se configuró la escritura diferida, se inicia el punto de conexión de WCF. Sin embargo, si se produce un error en el inicio del punto de conexión, se registra el evento 6800 y se deja que se inicie el servicio de sincronización. La presencia de este evento significa que el punto de conexión de la escritura diferida de contraseñas no se ha iniciado. Los detalles del registro de eventos para este evento (6800) junto con sus entradas generadas por el componente PasswordResetService indican por qué el punto de conexión no se ha podido iniciar. Revise estos errores del registro de eventos e intente volver a iniciar Azure AD Connect si la escritura diferida de contraseñas sigue sin funcionar. Si el problema persiste, intente deshabilitar la escritura diferida de contraseñas y vuelva a habilitarla.
| Cuando un usuario intenta restablecer una contraseña o desbloquear una cuenta con la escritura diferida de contraseñas habilitada, se produce un error en la operación. <br> <br> Además, verá un evento en el registro de eventos de Azure AD Connect que contiene: “Synchronization Engine returned an error hr=800700CE, message=The filename or extension is too long” (El motor de sincronización devolvió un error hr=800700CE, mensaje=El nombre de archivo o la extensión es demasiado largo) después de la operación de desbloqueo. | Busque la cuenta de Active Directory para Azure AD Connect y restablezca la contraseña para que no contenga más de 127 caracteres. Después, abra el **Servicio de sincronización** desde el menú **Inicio**. Vaya a **Conectores** y busque el **Conector Active Directory**. Selecciónelo y, a continuación, seleccione **Propiedades**. Vaya a la página **Credenciales** y escriba la nueva contraseña. Seleccione **Aceptar** para cerrar la página. |
| En el último paso del proceso de instalación de Azure AD Connect, se generará un error que indica que no se ha podido configurar la escritura diferida de contraseñas. <br> <br> El registro de eventos de la aplicación para Azure AD Connect contiene el error 32009 con el texto "Error getting auth token" (Error al obtener el token de autorización). | Este error se produce en los dos casos siguientes: <br><ul><li>Ha especificado una contraseña incorrecta para la cuenta de administrador global especificada al principio del proceso de instalación de Azure AD Connect.</li><li>Ha tratado de usar un usuario federado para la cuenta de administrador global especificada al principio del proceso de instalación de Azure AD Connect.</li></ul> Para corregir este problema, asegúrese de que no esté usando una cuenta federada para el administrador global que especificó al principio del proceso de instalación. Asegúrese también de que la contraseña especificada es correcta. |
| El registro de eventos de la máquina de Azure AD Connect contiene el error 32002 que se genera al ejecutar PasswordResetService. <br> <br> El error reza como sigue: "Error Connecting to ServiceBus. The token provider was unable to provide a security token” (Error al conectar a ServiceBus: el proveedor de tokens no pudo proporcionar un token de seguridad). | El entorno local no es capaz de conectarse al punto de conexión de la instancia de Azure Service Bus en la nube. Este error se debe normalmente a una regla de firewall que bloquea una conexión saliente a una dirección de puerto o web determinada. Vea [Requisitos previos de conectividad](./connect/active-directory-aadconnect-prerequisites.md) para más información. Una vez que haya actualizado estas reglas, reinicie la máquina de Azure AD Connect y la escritura diferida de contraseñas debería empezar a funcionar de nuevo. |
| Después de trabajar durante algún tiempo, los usuarios federados o con sincronización de hash de contraseña no pueden restablecer las contraseñas. | En algunos casos excepcionales, el servicio de escritura diferida de contraseñas puede no volver a iniciarse cuando se reinicia Azure AD Connect. En estos casos, en primer lugar, compruebe si la escritura diferida de contraseñas está habilitada en el entorno local. Puede comprobarlo con el asistente de Azure AD Connect o con PowerShell (vea la sección explicativa anterior). Si la característica aparece como habilitada, intente habilitarla o deshabilitarla de nuevo mediante la interfaz de usuario o PowerShell. Si esto no funciona, pruebe a desinstalar Azure AD Connect por completo y volver a instalarlo. |
| Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseña obtienen un error después de intentar enviar su contraseña. El error indica que ha habido un problema del servicio. <br ><br> Además, durante las operaciones de restablecimiento de contraseña, podría ver un error relacionado con que al agente de administración se le denegó el acceso a los registros de eventos locales. | Si ve estos errores en el registro de eventos, confirme que la cuenta del Agente de administración de Active Directory (ADMA) (que se especificó en el asistente en el momento de la configuración) tiene los permisos necesarios para la escritura diferida de contraseñas. <br> <br> Tenga en cuenta que una vez que se concede este permiso, puede tardar hasta una hora en poder utilizarse a través de la tarea en segundo plano `sdprop`, en el controlador de dominio (DC). <br> <br> Para que el restablecimiento de contraseña funcione, el permiso debe quedar marcado en el descriptor de seguridad del objeto de usuario cuya contraseña se está restableciendo. Hasta que este permiso se muestra en el objeto de usuario, el restablecimiento de la contraseña seguirá generando un mensaje de acceso denegado. |
| Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseña obtienen un error después de que envíen su contraseña. El error indica que ha habido un problema del servicio. <br> <br> Además de este problema, durante las operaciones de restablecimiento de contraseña, puede aparecer un error en los registros de eventos del servicio Azure AD Connect con el mensaje “No se encontró el objeto”. | Este error suele indicar que el motor de sincronización no puede encontrar el objeto de usuario en el espacio del conector de Azure AD o en el objeto del espacio del conector de Azure AD o del metaverso vinculado (MV). <br> <br> Para solucionar este problema, asegúrese de que el usuario realmente se sincroniza desde el entorno local a Azure AD a través de la instancia actual de Azure AD Connect e inspeccione el estado de los objetos de los espacios de conector y MV. Confirme que el objeto de Servicios de certificados de Active Directory (AD CS) está conectado al objeto MV a través de la regla "Microsoft.InfromADUserAccountEnabled.xxx".|
| Los usuarios federados o con sincronización de hash de contraseña que intenten restablecer su contraseña obtienen un error después de intentar enviar su contraseña. El error indica que ha habido un problema del servicio. <br> <br> Además de este problema, durante las operaciones de restablecimiento de contraseña, puede obtener un error en los registros de eventos del servicio Azure AD Connect que indique que "se encontraron varias coincidencias". | Ello indica que el motor de sincronización ha detectado que el objeto de MV está conectado a más de un objeto de AD CS a través de "Microsoft.InfromADUserAccountEnabled.xxx". Esto significa que el usuario tiene una cuenta habilitada en más de un bosque. Este escenario no se admite para la escritura diferida de contraseñas. |
| Error de configuración en las operaciones con contraseñas. El registro de eventos de la aplicación contiene el error 6329 de Azure AD Connect con el texto: 0x8023061f (The operation failed because password synchronization is not enabled on this Management Agent) (Error en la operación porque no está habilitada la sincronización de contraseñas en este agente de administración). | Este error se produce si se cambia la configuración de Azure AD Connect para agregar un nuevo bosque de Active Directory (o para quitar y volver a agregar un bosque existente) después de que ya se haya habilitado la escritura diferida de contraseñas. Se producirá un error en las operaciones de contraseñas para los usuarios de estos bosques recién agregados. Para corregir el problema, deshabilite la característica de escritura diferida de contraseñas después de que se hayan completado los cambios de configuración del bosque y vuelva a habilitarla entonces. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de error del registro de eventos de escritura diferida de contraseñas

Un procedimiento recomendado para solucionar problemas con la escritura diferida de contraseñas consiste en inspeccionar el registro de eventos de la aplicación en la máquina de Azure AD Connect. Este registro de eventos contiene eventos de dos orígenes de interés para la escritura diferida de contraseñas. El origen PasswordResetService describe las operaciones y los problemas relacionados con el funcionamiento de la escritura diferida de contraseñas. El origen ADSync describe las operaciones y los problemas relacionados con la configuración de contraseñas en el entorno de Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Si el origen del evento es ADSync

| Código | Nombre o mensaje | DESCRIPCIÓN |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "A restriction prevents the password from being changed to the current one specified" (Una restricción impide que la contraseña se modifique por la que ha especificado actualmente). | Este evento se produce cuando el servicio de escritura diferida de contraseñas intenta establecer una contraseña en su directorio local que no cumple los requisitos del dominio en cuanto al filtrado, la vigencia de la contraseña, el historial o la complejidad. <br> <br> Si tiene una vigencia mínima de la contraseña y ha cambiado recientemente la contraseña dentro de ese margen de tiempo, no puede volver a cambiarla hasta que alcance la duración especificada en el dominio. Para las pruebas, la vigencia mínima debe establecerse en 0. <br> <br> Si tiene habilitados los requisitos del historial de contraseñas, debe seleccionar una contraseña que no se haya utilizado en las últimas *N* veces, donde *N* es la configuración del historial de contraseñas. Si selecciona una contraseña que se haya usado en las últimas *N* veces, verá un error en este caso. Para las pruebas, el historial mínimo debe establecerse en 0. <br> <br> Si tiene requisitos de complejidad de contraseña, todos ellos se aplican cuando el usuario intenta cambiar o restablecer una contraseña. <br> <br> Si tiene habilitados filtros de contraseña y un usuario selecciona una contraseña que no cumple los criterios de filtrado, se producirá un error en la operación de restablecimiento o modificación. |
| 6329 | MMS(3040): admaexport.cpp(2837): el servidor no contiene el control de directiva de contraseña LDAP. | Este problema se produce si el control LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) no está habilitado en los controladores de dominio. Para usar la característica de escritura diferida de contraseña, debe habilitar el control. Para ello, los controladores de dominio deben estar en Windows Server 2008 (con el SP más reciente) o una versión posterior. Si los controladores de dominio están en 2008 (antes de la versión R2), también debe aplicar la revisión [KB2386717](http://support.microsoft.com/kb/2386717). |
| HR 8023042 | El motor de sincronización devolvió un error hr=80230402, mensaje=Error al intentar obtener un objeto debido a que hay entradas duplicadas con el mismo delimitador. | Este error se produce cuando se habilita el mismo identificador de usuario en varios dominios. Por ejemplo se produciría si se están sincronizando los bosques de cuentas y recursos, y tienen el mismo identificador de usuario que está habilitado en cada bosque. <br> <br> Este error también puede ocurrir si usa un atributo delimitador que no sea único, como un alias o UPN, y dos usuarios comparten el mismo. <br> <br> Para resolver este problema, asegúrese de no tener ningún usuario duplicado dentro de los dominios y de estar utilizando un atributo delimitador único para cada usuario. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>El origen del evento es PasswordResetService

| Código | Nombre o mensaje | DESCRIPCIÓN |
| --- | --- | --- |
| 31001 | PasswordResetStart | Este evento indica que el servicio local detectó una solicitud de restablecimiento de contraseña para un usuario federado o con sincronización de hash de contraseña originada desde la nube. Este evento es el primero en cada operación de escritura diferida de contraseñas. |
| 31002 | PasswordResetSuccess | Este evento indica que un usuario seleccionó una contraseña nueva durante una operación de restablecimiento de contraseña. Determinamos que dicha contraseña cumple los requisitos de las contraseñas corporativas. La escritura diferida de la contraseña se realizó correctamente en el entorno de Active Directory local. |
| 31003 | PasswordResetFail | Este evento indica que un usuario seleccionó una contraseña y esta llegó correctamente al entorno local. Pero, al intentar establecer la contraseña en el entorno de Active Directory local, se produjo un error. Esto puede ocurrir por varios motivos: <br><ul><li>La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Para resolver este problema, cree una contraseña nueva.</li><li>La cuenta de servicio de ADMA no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión.</li><li>La cuenta de usuario está en un grupo protegido, como el de administradores de empresa o dominio, lo que impide la ejecución de operaciones de establecimiento de contraseñas.</li></ul>|
| 31004 | OnboardingEventStart | Este evento se produce si habilita la escritura diferida de contraseñas con Azure AD Connect y hemos iniciado la incorporación en la organización del servicio web de escritura diferida de contraseñas. |
| 31005 | OnboardingEventSuccess | Este evento indica que el proceso de incorporación se realizó correctamente y que la funcionalidad de escritura diferida de contraseñas está lista para usarse. |
| 31006 | ChangePasswordStart | Este evento indica que el servicio local detectó una solicitud de cambio de contraseña para un usuario federado o con sincronización de hash de contraseña originada desde la nube. Este evento es el primero de cada operación de escritura diferida de cambio de contraseña. |
| 31007 | ChangePasswordSuccess | Indica que un usuario seleccionó una nueva contraseña durante una operación de cambio de contraseña, determinamos que esta contraseña cumple los requisitos corporativos de las contraseñas y que esa contraseña se ha escrito en diferido correctamente en el entorno de Active Directory local. |
| 31008 | ChangePasswordFail | Este evento indica que un usuario seleccionó una contraseña y que esta ha llegado correctamente al entorno local, pero, al intentar establecer la contraseña en el entorno de Active Directory local, se produjo un error. Esto puede ocurrir por varios motivos: <br><ul><li>La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Para resolver este problema, cree una contraseña nueva.</li><li>La cuenta de servicio de ADMA no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión.</li><li>La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | El servicio local ha detectado una solicitud de restablecimiento de contraseña para un usuario federado o con sincronización de hash de contraseña originada por el administrador local en nombre de un usuario. Este evento es el primero en una operación de escritura diferida de restablecimiento de contraseña que fue iniciada por el administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | El administrador seleccionó una contraseña nueva durante una operación de restablecimiento de contraseña iniciada por él. Determinamos que dicha contraseña cumple los requisitos de las contraseñas corporativas. La escritura diferida de la contraseña se realizó correctamente en el entorno de Active Directory local. |
| 31011 | ResetUserPasswordByAdminFail | El administrador seleccionó una contraseña en nombre del usuario. La contraseña ha llegado correctamente al entorno local. Pero, al intentar establecer la contraseña en el entorno de Active Directory local, se produjo un error. Esto puede ocurrir por varios motivos: <br><ul><li>La contraseña el usuario no cumple los requisitos de antigüedad, historial, complejidad o filtro del dominio. Para resolver este problema, pruebe con una contraseña nueva.</li><li>La cuenta de servicio de ADMA no tiene los permisos adecuados para establecer la nueva contraseña en la cuenta de usuario en cuestión.</li><li>La cuenta de usuario está en un grupo protegido, como administradores del dominio o de empresa, lo que impide la ejecución de operaciones de conjunto de contraseñas.</li></ul>  |
| 31012 | OffboardingEventStart | Este evento se produce si habilita la escritura diferida de contraseñas con Azure AD Connect e indica que se inició la externalización en su organización del servicio web de escritura diferida de contraseñas. |
| 31013| OffboardingEventSuccess| Este evento indica que el proceso de externalización se realizó correctamente y que la funcionalidad de escritura diferida de contraseñas se ha deshabilitado también correctamente. |
| 31014| OffboardingEventFail| Este evento indica que el proceso de externalización no se realizó correctamente. Puede deberse a un error de permisos en la cuenta de administrador en la nube o local especificada durante la configuración. El error también puede producirse si está intentando utilizar un administrador global en la nube federado al deshabilitar la escritura diferida de contraseñas. Para solucionar este problema, compruebe los permisos administrativos y asegúrese de que no se utilice ninguna cuenta federada al configurar la funcionalidad de escritura diferida de contraseñas.|
| 31015| WriteBackServiceStarted| Este evento indica que el servicio de escritura diferida de contraseñas se ha iniciado correctamente. Está listo para aceptar las solicitudes de administración de contraseñas de la nube.|
| 31016| WriteBackServiceStopped| Este evento indica que el servicio de escritura diferida de contraseñas se ha detenido. Todas las solicitudes de administración de contraseñas de la nube fracasarán.|
| 31017| AuthTokenSuccess| Este evento indica que se ha recuperado correctamente un token de autorización para el administrador global especificado durante la instalación de Azure AD Connect para iniciar el proceso de incorporación o externalización.|
| 31018| KeyPairCreationSuccess| Este evento indica que se creó correctamente la clave de cifrado de contraseña. Esta clave se usa para que las contraseñas cifradas de la nube se envíen al entorno local.|
| 32000| UnknownError| Este evento indica que se produjo un error desconocido durante una operación de administración de contraseñas. Observe el texto de excepción en el evento para obtener más detalles. Si tiene problemas, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas. Si así no se soluciona el problema, incluya una copia del registro de eventos junto con el identificador de seguimiento especificado interno para el ingeniero de soporte técnico.|
| 32001| ServiceError| Este evento indica que se ha producido un error al conectarse a la instancia de restablecimiento de contraseñas de la nube. Este error suele producirse cuando el servicio local no pudo conectarse al servicio web de restablecimiento de contraseña.|
| 32002| ServiceBusError| Este evento indica que se ha producido un error al conectarse a la instancia de Service Bus del inquilino. Esto puede deberse a que está bloqueando las conexiones salientes en el entorno local. Compruebe el firewall para asegurarse de que permite conexiones a través de TCP 443 y a https://ssprsbprodncu-sb.accesscontrol.windows.net/, e inténtelo de nuevo. Si sigue teniendo problemas, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas.|
| 32003| InPutValidationError| Este evento indica que la entrada transferida a la API del servicio web no era válida. Vuelva a intentarlo.|
| 32004| DecryptionError| Este evento indica que se ha producido un error al descifrar la contraseña que ha llegado de la nube. Podría deberse a una falta de coincidencia de la clave de descifrado entre el servicio en la nube y su entorno local. Para resolver este problema, deshabilite y vuelva a habilitar la escritura diferida de contraseñas en su entorno local.|
| 32005| ConfigurationError| Durante la incorporación, guardamos la información específica del inquilino en un archivo de configuración en su entorno local. Este evento indica que se ha producido un error al guardar este archivo o que cuando se inició el servicio hubo un error en la lectura del archivo. Para corregir este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas para forzar la reescritura del archivo de configuración.|
| 32007| OnBoardingConfigUpdateError| Durante la incorporación, enviamos datos de la nube al servicio de restablecimiento de contraseñas local. Esos datos, a continuación, se escriben en un archivo en memoria antes de enviarse al servicio de sincronización para almacenarse de forma segura en el disco. Este evento indica un problema con la escritura o actualización de los datos en la memoria. Para corregir este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas para forzar la reescritura de este archivo de configuración.|
| 32008| ValidationError| Este evento indica que hemos recibido una respuesta no válida desde el servicio web de restablecimiento de contraseña. Para solucionar este problema, pruebe a deshabilitar y volver a habilitar la escritura diferida de contraseñas.|
| 32009| AuthTokenError| Este evento indica que no se ha podido obtener un token de autorización para la cuenta de administrador global especificada durante la instalación de Azure AD Connect. Este error puede deberse a un nombre de usuario o contraseña especificados para la cuenta de administrador global. También puede producirse si la cuenta de administrador global especificada está federada. Para corregir este problema, vuelva a ejecutar la configuración con el nombre de usuario y la contraseña correctos, y asegúrese de que el administrador es una cuenta administrada (solo en la nube o con la sincronización de contraseñas).|
| 32010| CryptoError| Este evento indica que se produjo un error al generar la clave de cifrado de contraseña o al descifrar una contraseña que llega desde el servicio en la nube. Este error probablemente indica un problema con su entorno. Consulte los detalles de su registro de eventos para obtener más información y resolver este problema. También puede intentar deshabilitar y volver a habilitar el servicio de escritura diferida de contraseñas.|
| 32011| OnBoardingServiceError| Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio web de restablecimiento de contraseña para iniciar el proceso de incorporación. Puede ocurrir como resultado de una regla de firewall o si hay un problema al obtener un token de autenticación para el inquilino. Para corregir este problema, asegúrese de no estar bloqueando las conexiones salientes sobre TCP 443 y TCP 9350 a 9354 o a https://ssprsbprodncu-sb.accesscontrol.windows.net/. Asegúrese también de que la cuenta de administrador de Azure AD que usa para la incorporación no está federada.|
| 32013| OffBoardingError| Este evento indica que el servicio local no pudo comunicarse correctamente con el servicio web de restablecimiento de contraseña para iniciar el proceso de externalización. Puede ocurrir como resultado de una regla de firewall o si hay un problema al obtener un token de autorización para el inquilino. Para solucionar este problema, asegúrese de que no esté bloqueando las conexiones salientes sobre TCP 443 o a https://ssprsbprodncu-sb.accesscontrol.windows.net/, y que la cuenta de administrador de Azure Active Directory utilizada para la externalización no esté federada.|
| 32014| ServiceBusWarning| Este evento indica que tenemos que tratar de volver a establecer la conexión a la instancia de Service Bus del inquilino. En condiciones normales, esto no debe ser un problema, pero si este evento se produce muchas veces, considere la posibilidad de comprobar la conexión de red a Service Bus, especialmente si se trata de una conexión de ancho de banda bajo o una latencia alta.|
| 32015| ReportServiceHealthError| Para supervisar el estado de su servicio de escritura diferida de contraseñas, enviamos datos de latido a nuestro servicio web de restablecimiento de contraseñas cada cinco minutos. Este evento indica que se ha producido un error al enviar esta información de latido al servicio web en la nube. Esta información de estado no incluye datos de información de identificación de objetos (OII) o de información de identificación personal (PII) y se trata únicamente de un latido y estadísticas de servicio básicas para que podamos ofrecer información de estado de servicio en la nube.|
| 33001| ADUnKnownError| Este evento indica que se produjo un error desconocido que devuelve Active Directory. Compruebe en el registro de eventos de servidor de Azure AD Connect si hay eventos del origen ADSync para obtener más información.|
| 33002| ADUserNotFoundError| Este evento indica que el usuario que está intentando restablecer o cambiar una contraseña no se encontró en el directorio local. Esto puede ocurrir cuando el usuario se ha eliminado en el entorno local pero no en la nube. Este error también puede producirse si hay un problema con la sincronización. Compruebe los registros de sincronización, así como los últimos detalles de ejecución de la sincronización para más información.|
| 33003| ADMutliMatchError| Si una solicitud de restablecimiento o cambio de contraseña se origina desde la nube, usamos el delimitador de la nube especificado durante el proceso de configuración de Azure AD Connect para determinar cómo vincular tal solicitud de nuevo a un usuario en el entorno local. Este evento indica que hemos encontrado dos usuarios en el directorio local con el mismo atributo delimitador de la nube. Compruebe los registros de sincronización, así como los últimos detalles de ejecución de la sincronización para más información.|
| 33004| ADPermissionsError| Este evento indica que la cuenta de servicio del Agente de administración de Active Directory no tiene los permisos adecuados en la cuenta en cuestión para establecer una nueva contraseña. Asegúrese de que la cuenta de ADMA en el bosque del usuario tiene los permisos de restablecimiento y cambio de contraseña en todos los objetos del bosque. Para más información sobre cómo establecer los permisos, consulte el Paso 4: configurar los permisos adecuados de Active Directory.|
| 33005| ADUserAccountDisabled| Este evento indica que hemos intentado restablecer o cambiar una contraseña de una cuenta deshabilitada en el entorno local. Habilite la cuenta y vuelva a intentarlo.|
| 33006| ADUserAccountLockedOut| Este evento indica que hemos intentado restablecer o cambiar una contraseña de una cuenta bloqueada en el entorno local. Los bloqueos se aplican cuando un usuario intenta modificar o restablecer la contraseña demasiadas veces en poco tiempo. Desbloquee la cuenta y vuelva a intentarlo.|
| 33007| ADUserIncorrectPassword| Este evento indica que el usuario ha especificado una contraseña incorrecta actual al realizar una operación de cambio de contraseña. Especifique la contraseña actual correcta e inténtelo de nuevo.|
| 33008| ADPasswordPolicyError| Este evento se produce cuando el servicio de escritura diferida de contraseñas intenta establecer una contraseña en su directorio local que no cumple los requisitos del dominio en cuanto al filtrado, la vigencia de la contraseña, el historial o la complejidad. <br> <br> Si tiene una vigencia mínima de la contraseña y ha cambiado recientemente la contraseña dentro de ese margen de tiempo, no puede volver a cambiarla hasta que alcance la duración especificada en el dominio. Para las pruebas, la vigencia mínima debe establecerse en 0. <br> <br> Si tiene habilitados los requisitos del historial de contraseñas, debe seleccionar una contraseña que no se haya utilizado en las últimas *N* veces, donde *N* es la configuración del historial de contraseñas. Si selecciona una contraseña que se haya usado en las últimas *N* veces, verá un error en este caso. Para las pruebas, el historial mínimo debe establecerse en 0. <br> <br> Si tiene requisitos de complejidad de contraseña, todos ellos se aplican cuando el usuario intenta cambiar o restablecer una contraseña. <br> <br> Si tiene habilitados filtros de contraseña y un usuario selecciona una contraseña que no cumple los criterios de filtrado, se producirá un error en la operación de restablecimiento o modificación.|
| 33009| ADConfigurationError| Este evento indica que se ha producido un problema al escribir la contraseña en diferido en el directorio local por un problema de configuración con Active Directory. Compruebe el registro de eventos de la aplicación de la máquina de Azure AD Connect para ver los mensajes del servicio ADSync, a fin de obtener más información sobre el error que se ha producido.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Solución de problemas con la conectividad de la escritura diferida de contraseñas

Si se producen interrupciones del servicio con el componente de escritura diferida de contraseñas de Azure AD Connect, aquí tiene algunos pasos que puede seguir para resolver este problema:

* [Confirmación de la conectividad de la red](#confirm-network-connectivity)
* [Reinicio del servicio Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Deshabilitar y volver a habilitar la característica de escritura diferida de contraseña](#disable-and-re-enable-the-password-writeback-feature)
* [Instalación de la última versión de Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Solución de problemas con la escritura diferida de contraseñas](#troubleshoot-password-writeback)

En general, con el fin de recuperar el servicio de la manera más rápida, se recomienda que ejecute estos pasos en el orden anterior.

### <a name="confirm-network-connectivity"></a>Confirmación de la conectividad de la red

El punto de error más común es la configuración incorrecta del firewall o los puertos del proxy y los tiempos de inactividad. 

En el caso de Azure AD Connect, versiones 1.1.443.0 y posteriores, se necesita acceso HTTPS saliente al siguiente:

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

Para mayor granularidad, consulte la lista actualizada de [intervalos IP de centro de datos de Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653), que se actualiza todos los miércoles y que entra en vigor los lunes siguientes.

Para más información, revise los requisitos previos de conectividad en el artículo [Requisitos previos para Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md).



### <a name="restart-the-azure-ad-connect-sync-service"></a>Reinicio del servicio Azure AD Connect Sync

Para resolver los problemas de conectividad y otros transitorios con el servicio, reinicie el servicio Azure AD Connect Sync:

   1. Como administrador, seleccione **Iniciar** en el servidor que ejecuta Azure AD Connect.
   2. Escriba **services.msc** en el campo de búsqueda y seleccione **Entrar**.
   3. Busque la entrada **Microsoft Azure AD Sync**.
   4. Haga clic con el botón derecho en la entrada del servicio, haga clic en **Reiniciar** y espere a que se complete la operación.

   ![Reinicio del servicio Azure AD Sync][Service restart]

Estos pasos restablecen la conexión con el servicio en la nube y resuelven las interrupciones que podría estar experimentando. Si al reiniciar el servicio ADSync no se resuelve el problema, se recomienda que intente deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas

Para resolver problemas de conectividad, deshabilite y vuelva a habilitar la característica de escritura diferida de contraseñas:

   1. Como administrador, abra el Asistente para configuración de Azure AD Connect.
   2. En **Conectarse a Azure AD**, escriba las credenciales de administrador global de Azure AD.
   3. En **Conectarse a AD DS**, escriba las credenciales de administrador de Azure AD Domain Services.
   4. En **Identificación de forma exclusiva de usuarios**, seleccione el botón **Siguiente**.
   5. En **Características opcionales**, desactive la casilla **Escritura diferida de contraseñas**.
   6. Seleccione **Siguiente** en el resto de las páginas del cuadro de diálogo sin cambiar nada hasta llegar a la página **Listo para configurar**.
   7. Asegúrese de que en la página **Listo para configurar** aparece la opción **Escritura diferida de contraseñas** como **deshabilitada** y, a continuación, seleccione el botón verde **Configurar** para confirmar los cambios.
   8. En **Finalizado**, desactive la opción **Sincronizar ahora** y, a continuación, seleccione **Finalizar** para cerrar el asistente.
   9. Vuelva a abrir el Asistente para configuración de Azure AD Connect.
   10. Repita los pasos del 2 al 8, salvo que debe asegurarse de seleccionar la opción **Escritura diferida de contraseñas** en la pantalla **Características opcionales** para volver a habilitar el servicio.

Estos pasos restablecen la conexión con el servicio en la nube y resuelven las interrupciones que pueda experimentar.

Si el problema no se soluciona después de deshabilitar y volver a habilitar la característica de escritura diferida de contraseñas, le recomendamos que vuelva a instalar Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalación de la última versión de Azure AD Connect

Con la reinstalación de Azure AD Connect se pueden resolver los problemas de configuración y de conectividad entre Cloud Services y el entorno local de Active Directory.

Se recomienda realizar este paso solo después de probar los dos primeros pasos descritos anteriormente.

> [!WARNING]
> Si ha personalizado las reglas de sincronización predefinidas, *realice una copia de seguridad de ellas antes de continuar con la actualización y, cuando haya terminado, vuelva a implementarlas manualmente*.

   1. Descargue la versión más reciente de AD Connect del [Centro de descarga de Microsoft](http://go.microsoft.com/fwlink/?LinkId=615771).
   2. Puesto que ya ha instalado Azure AD Connect, solo necesita realizar una actualización in situ para actualizar la instalación de Azure AD Connect a la versión más reciente.
   3. Ejecute el paquete descargado y siga las instrucciones en pantalla para actualizar el equipo de Azure AD Connect.

Los pasos anteriores restablecen la conexión con el servicio en la nube y resuelven las interrupciones que pueda experimentar.

Si con la instalación de la versión más reciente del servidor de Azure AD Connect no se resuelve el problema, le recomendamos que intente deshabilitar y volver a habilitar la escritura diferida de contraseñas como último paso después de instalar la versión más reciente.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Compruebe si Azure AD Connect dispone del permiso necesario para la escritura diferida de contraseñas

Azure AD Connect requiere el permiso de Active Directory **Restablecer contraseña** para realizar la escritura diferida de contraseñas. Para averiguar si Azure AD Connect tiene el permiso requerido para una cuenta de usuario de Active Directory local determinada, puede usar la característica Permiso efectivo de Windows:

   1. Inicie sesión en el servidor Azure AD Connect e inicie **Synchronization Service Manager** seleccionando **Inicio**  > **Synchronization Service**.
   2. En la pestaña **Conectores** seleccione el conector **Active Directory Domain Services** local y, a continuación, seleccione **Propiedades**.  

   ![Permiso efectivo: paso 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
   3. En la ventana emergente, seleccione **Connect to Active Directory Forest** (Conectar con el bosque de Active Directory) y anote el valor de la propiedad **Nombre de usuario**. Esta propiedad es la cuenta de AD DS que Azure AD Connect usa para realizar la sincronización de directorios. Para que Azure AD Connect realice la escritura diferida de contraseñas, la cuenta de AD DS debe tener permiso para restablecer la contraseña.  
   
   ![Permiso efectivo: paso 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
   4. Inicie sesión en un controlador de dominio local e inicie la aplicación **Usuarios y equipos de Active Directory**.
   5. Seleccione **Vista** y asegúrese de que la opción **Características avanzadas** está habilitada.  
   
   ![Permiso efectivo: paso 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
   6. Busque la cuenta de usuario de Active Directory que desee verificar. Haga clic con el botón derecho en el nombre de la cuenta y seleccione **Propiedades**.  
   
   ![Permiso efectivo: paso 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

   7. En la ventana emergente, vaya a la pestaña **Seguridad** y seleccione **Avanzada**.  
   
   ![Permiso efectivo: paso 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
   8. En la ventana emergente **Advanced Security Settings for Administrator** (Configuración de seguridad avanzada para el administrador), vaya a la pestaña **Acceso efectivo**.
   9. Seleccione **Seleccionar un usuario**, seleccione la cuenta de AD DS que usa Azure AD Connect (consulte el paso 3) y, a continuación, seleccione **Ver acceso efectivo**.  
   
   ![Permiso efectivo: paso 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
   10. Desplácese hacia abajo y busque **Restablecer contraseña**. Si la entrada está activada, significa que la cuenta de AD DS tiene permiso para restablecer la contraseña de la cuenta de usuario de Active Directory seleccionada.  
   
   ![Permiso efectivo: paso 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Foros de Azure AD

Si tiene alguna pregunta general sobre Azure AD y el autoservicio de restablecimiento de contraseña, puede pedir ayuda a la comunidad en los [foros de Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). La comunidad está formada por ingenieros, jefes de producto, MVP y profesionales de TI.

## <a name="contact-microsoft-support"></a>Consultar al soporte técnico de Microsoft

Si no encuentra la respuesta a un problema, nuestros equipos de soporte técnico están siempre disponibles para ayudarle.

Para que reciba la ayuda apropiada, le pedimos que proporcione la mayor cantidad de detalles posible al abrir una incidencia. Estos detalles incluyen:

* **Descripción general del error**: ¿cuál es? ¿Qué comportamiento observó? ¿Cómo podemos reproducir el error? Proporcione tantos detalles como sea posible.
* **Página**: ¿en qué página estaba cuando se detectó el error? Incluya la dirección URL, si es posible, y una captura de pantalla de la página.
* **Código de soporte técnico**: ¿qué código de soporte técnico se generó cuando el usuario vio el error?
    * Para encontrarlo, reproduzca el error, seleccione el vínculo **Código de soporte técnico** en la parte inferior de la pantalla y envíe al ingeniero de soporte técnico el GUID resultante.

    ![Busque el código de soporte técnico en la parte inferior de la pantalla][Support code]

    * Si se encuentra en una página sin código de soporte en la parte inferior, seleccione F12, busque el SID y el CID, y envíe estos dos resultados al ingeniero de soporte.
* **Fecha, hora y zona horaria**: incluya la fecha y la hora precisas (incluida la *zona horaria*) en que se produjo el error.
* **Id. de usuario**: ¿quién fue el usuario que vio el error? Un ejemplo es *user@contoso.com*.
    * ¿Es un usuario federado?
    * ¿Es un usuario con sincronización de hash de contraseña?
    * ¿Es un usuario solo de nube?
* **Licencia**: ¿tiene el usuario asignada una licencia de Azure Active Directory Premium o Azure Active Directory Basic?
* **Registro de eventos de aplicación**: si usa la escritura diferida de contraseñas y el error se produce en la infraestructura local, incluya una copia comprimida del registro de eventos de la aplicación desde el servidor de Azure AD Connect.



[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reinicio del servicio Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "El código de soporte técnico se encuentra en la parte inferior derecha de la ventana"

## <a name="next-steps"></a>pasos siguientes

En los siguientes artículos se proporciona información adicional sobre el restablecimiento de contraseña con Azure AD:

* [¿Cómo se realiza un lanzamiento correcto de SSPR?](active-directory-passwords-best-practices.md)
* [Restablecimiento o modificación de la contraseña](active-directory-passwords-update-your-own-password.md)
* [Registro para el autoservicio de restablecimiento de contraseñas](active-directory-passwords-reset-register.md)
* [¿Tiene alguna pregunta acerca de las licencias?](active-directory-passwords-licensing.md)
* [¿Qué datos usa SSPR y cuáles se deben rellenar en lugar de los usuarios?](active-directory-passwords-data.md)
* [¿Qué métodos de autenticación están disponibles para los usuarios?](active-directory-passwords-how-it-works.md#authentication-methods)
* [¿Cuáles son las opciones de directiva con SSPR?](active-directory-passwords-policy.md)
* [¿Qué es la escritura diferida de contraseñas y por qué nos interesa?](active-directory-passwords-writeback.md)
* [¿Cómo se informa sobre la actividad de SSPR?](active-directory-passwords-reporting.md)
* [¿Cuáles son todas las opciones en SSPR y qué significan?](active-directory-passwords-how-it-works.md)
* [Tengo una pregunta que no se ha comentado en ningún otro sitio](active-directory-passwords-faq.md)
