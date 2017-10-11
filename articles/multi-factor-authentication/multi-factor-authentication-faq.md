---
title: P+F sobre Azure Multi-Factor Authentication | Microsoft Docs
description: "Preguntas y respuestas más frecuentes relacionadas con Azure Multi-Factor Authentication. Multi-Factor Authentication es un método para comprobar la identidad de un usuario que requiere usar más de un nombre de usuario y contraseña. Proporciona una capa adicional de seguridad a los inicios de sesión y transacciones de los usuarios."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023dee623ca6ec35ab77578c97e5bf197b4bfe75
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Preguntas más frecuentes relacionadas con Azure Multi-Factor Authentication
Este artículo de P+F da respuesta a las preguntas más frecuentes sobre Azure Multi-Factor Authentication y el uso del servicio Multi-Factor Authentication. Se divide en preguntas sobre el servicio en general, los modelos de facturación, las experiencias del usuario y la solución de problemas.

## <a name="general"></a>General
**P.: ¿Cómo controla Servidor Azure Multi-Factor Authentication los datos de usuario?**

Con Servidor Multi-Factor Authentication, los datos del usuario se almacenan solo en los servidores locales. Los datos de usuario persistentes no se almacenan en la nube. Cuando el usuario realiza la comprobación en dos pasos, Servidor Multi-Factor Authentication envía datos al servicio en la nube Azure Multi-Factor Authentication para llevar a cabo la autenticación. Para establecer la comunicación entre Servidor Multi-Factor Authentication y el servicio en la nube Multi-Factor Authentication se utilizan los protocolos Capa de sockets seguros (SSL) o Seguridad de la capa de transporte (TLS) en el puerto 443 de salida.

Cuando se envían solicitudes de autenticación al servicio en la nube, se recopilan datos para los informes de uso y autenticación. Los campos de datos que se incluyen en los registros de comprobación en dos pasos son los siguientes:

* **Id. único** (cualquier nombre de usuario o id. de Servidor Multi-Factor Authentication local)
* **Nombre y apellidos** (opcional)
* **Dirección de correo electrónico** (opcional)
* **Número de teléfono** (al realizar la autenticación de una llamada de voz o SMS)
* **Token de dispositivo** (al realizar la autenticación de una aplicación móvil)
* **Modo de autenticación**
* **Resultado de la autenticación**
* **Nombre de Servidor Multi-Factor Authentication**
* **IP de Servidor Multi-Factor Authentication**
* **IP de cliente** (si está disponible)

Los campos opcionales pueden configurarse en Servidor Multi-Factor Authentication.

El resultado de la comprobación (aceptación o denegación) y el motivo de la denegación se almacenan con los datos de autenticación. Los datos están disponibles en los informes de autenticación y uso.

## <a name="billing"></a>Facturación
La mayoría de las preguntas sobre facturación se pueden responder si se consulta la [página sobre precios de Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) o la documentación sobre [cómo conseguir Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**P: ¿Se le aplican cargos a mi organización por el envío de llamadas telefónicas y mensajes de texto que se usan para la autenticación?**

No, no se le cobrará nada por las llamadas de teléfono individuales que se realicen o los mensajes de texto que se envíen a los usuarios cuando se use Azure Multi-Factor Authentication. Si usa un proveedor de MFA por autenticación, se le facturará por cada autenticación pero no por el método que se use.

Podrían cobrarse a los usuarios las llamadas de teléfono o los mensajes de texto que reciban, dependiendo de su servicio telefónico personal.

**P: En el modelo de facturación por usuario, ¿los cargos se aplican por todos los usuarios habilitados o solo por los que realizaron la verificación en dos pasos?**

La facturación se basa en la cantidad de usuarios que están configurados para usar Multi-Factor Authentication, sin tener en cuenta si realizaron o no una verificación en dos pasos el mes en cuestión.

**P.: ¿Cómo funciona la facturación de Multi-Factor Authentication?**

Cuando se crea un proveedor de MFA por usuario o por autenticación, se factura mensualmente a la suscripción de Azure de su organización según el uso. Este modelo de facturación es similar a la forma en que Azure factura el uso de máquinas virtuales y sitios web.

Al adquirir una suscripción para Azure Multi-Factor Authentication, su organización solo paga la cuota de licencia anual para cada usuario. Las licencias MFA así como los conjuntos de productos de Office 365, Azure AD Premium o Enterprise Mobility + Security se facturan de esta manera. 

Obtenga más información sobre las opciones en [Cómo conseguir Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**P: ¿Existe una versión gratuita de Azure Multi-Factor Authentication?**

En algunos casos, sí.

Multi-Factor Authentication para administradores de Azure ofrece un subconjunto de características de Azure MFA sin costo para acceder a Microsoft Online Services, incluidos los portales para administradores de Azure y Office 365. Esta oferta se aplica solamente a los administradores globales en instancias de Azure Active Directory que no tienen la versión completa de Azure MFA a través de una licencia de MFA, un conjunto de productos o un proveedor independiente basado en el consumo. Si sus administradores usan la versión gratuita y usted adquiere una versión completa de Azure Multi-Factor Authentication, todos los administradores globales se elevan a la versión de pago automáticamente.

Multi-Factor Authentication para usuarios de Office 365 ofrece un subconjunto de características de Azure MFA sin costo para acceder a servicios de Office 365, incluidos Exchange Online y SharePoint Online. Esta oferta se aplica a los usuarios que tienen asignada una licencia de Office 365 cuando la instancia correspondiente de Azure Active Directory no tiene la versión completa de Azure MFA a través de una licencia de MFA, un paquete o un proveedor independiente basado en el consumo.

**P.: ¿Puede mi organización cambiar entre los modelos de facturación de consumo por usuario y por autenticación en cualquier momento?**

Si la organización adquiere MFA como servicio independiente con facturación basada en el consumo, se elige un modelo de facturación cuando se crea un proveedor de MFA. No se puede cambiar el modelo de facturación después de que se crea un proveedor de MFA. Sin embargo, puede eliminar el proveedor de MFA y crear otro con un modelo de facturación distinto.

Cuando se crea un proveedor de MFA, se puede vincular a una instancia de Azure Active Directory (también conocido como "inquilino de Azure AD"). Si el proveedor de MFA actual está vinculado a un inquilino de Azure AD, puede eliminar el proveedor de MFA de manera segura y crear otro que esté vinculado al mismo inquilino de Azure AD. Como alternativa, si compra suficientes licencias de MFA, Azure AD Premium o Enterprise Mobility + Security (EMS) para abarcar a todos los usuarios habilitados para MFA, puede eliminar por completo el proveedor de MFA.

Si el proveedor de MFA *no* está vinculado a un inquilino de Azure AD o si vincula el nuevo proveedor de MFA a un inquilino de Azure AD diferente, las opciones de configuración y ajustes de usuario no se transferirán. Además, los servidores Azure MFA se tendrán que reactivar mediante las credenciales de activación generadas a través del nuevo proveedor de MFA. Reactivar los servidores MFA para vincularlos al nuevo proveedor de MFA no afecta a la autenticación por llamada telefónica y mensaje de texto, sino que las notificaciones de aplicación móvil dejarán de funcionar para todos los usuarios hasta que se reactive la aplicación móvil.

Encuentre más información sobre los proveedores de MFA en [Introducción al Proveedor de Azure Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md).

**P: ¿Mi organización puede cambiar entre la facturación basada en el consumo y las suscripciones (un modelo basado en licencias) en cualquier momento?**

En algunos casos, sí.

Si el directorio tiene un proveedor de Azure Multi-Factor Authentication *por usuario*, puede agregar licencias de MFA. Los usuarios con licencia no se cuentan en la facturación basada en consumo por usuario. Los usuarios sin licencias podrán seguir habilitados para MFA a través del proveedor de MFA. Si compra y asigna licencias para todos los usuarios que están configurados para usar Multi-Factor Authentication, puede eliminar el proveedor de Azure Multi-Factor Authentication. Siempre puede crear otro proveedor de MFA por usuario si en el futuro tiene más usuarios que licencias.

Si el directorio tiene un proveedor de Azure Multi-Factor Authentication *por autenticación*, siempre se le facturará por cada autenticación mientras que el proveedor de MFA esté vinculado a la suscripción. Puede asignar licencias de MFA a los usuarios, pero se le seguirá facturando por cada solicitud de verificación en dos pasos, independientemente de si viene de alguien que tiene asignada una licencia de MFA o no.

**P.: ¿Mi organización tiene que usar y sincronizar las identidades para utilizar Azure Multi-Factor Authentication?**

Si la organización usa un modelo de facturación basado en el consumo, Azure Active Directory es opcional, no obligatorio. Si el proveedor de MFA no está vinculado a un inquilino de Azure AD, solo puede implementar Servidor Azure Multi-Factor Authentication o el SDK de Azure Multi-Factor Authentication de manera local.

El modelo de licencia requiere Azure Active Directory porque las licencias se agregan al inquilino de Azure AD cuando las compra y asigna a los usuarios en el directorio.

## <a name="manage-and-support-user-accounts"></a>Administración y soporte técnico de las cuentas de usuario

**P: ¿Qué debo decirles que hagan a los usuarios si no reciben respuesta en el teléfono o no están con su teléfono?**

Es de esperar que todos los usuarios hayan configurado más de un método de comprobación. Indíqueles que intenten volver a iniciar sesión, pero que seleccionen un método de comprobación distinto en la página de inicio de sesión.

Puede indicarles a los usuarios que consulte la [guía de solución de problemas del usuario final](./end-user/multi-factor-authentication-end-user-troubleshoot.md).


**P: ¿Qué debo hacer si uno de los usuarios no puede ingresar a su cuenta?**

Puede restablecer la cuenta del usuario; para ello, pídale que vuelva a realizar el proceso de registro. Puede obtener más información en [Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P: ¿Qué debo hacer si a uno de los usuarios se le pierde el teléfono en el que usa las contraseñas de aplicación?**

Para evitar el acceso no autorizado, elimine todas las contraseñas de aplicación del usuario. Una vez que el usuario tenga un dispositivo de reemplazo, pueden volver a crearlas. Puede obtener más información en [Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P: ¿Qué puede hacer un usuario si no puede iniciar sesión en aplicaciones sin explorador?**

Si la organización todavía usa clientes heredados y se [permite el uso de contraseñas de aplicación](multi-factor-authentication-whats-next.md#app-passwords), los usuarios no podrán iniciar sesión en estos clientes heredados con su nombre de usuario y contraseña. En lugar de eso, deberán [configurar contraseñas de aplicación](./end-user/multi-factor-authentication-end-user-app-passwords.md). Los usuarios deben borrar (eliminar) su información de inicio de sesión, reiniciar la aplicación y, luego, iniciar sesión con su nombre de usuario y la *contraseña de aplicación* en lugar de la contraseña habitual.

Si la organización no tiene clientes heredados, no debe permitir que los usuarios creen contraseñas de aplicación.

> [!NOTE]
> Autenticación moderna para clientes de Office 2013
>
> Las contraseñas de aplicación solo son necesarias para las aplicaciones que no admiten la autenticación moderna. Los clientes de Office 2013 admiten protocolos de autenticación moderna, pero se deben configurar. Los clientes de Office más recientes admiten automáticamente protocolos de autenticación moderna. Para obtener más información, consulte [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)(Anuncio de la versión preliminar pública de la autenticación moderna de Office 2013).

**P: Mis usuarios dicen que hay ocasiones en que no reciben el mensaje de texto o que responder a mensajes de texto bidireccionales, pero se agota el tiempo de espera de la comprobación.**

Tanto el envío de mensajes de texto como la recepción de respuestas en SMS bidireccionales no están garantizados, pues hay factores que no se pueden controlar y que podrían afectar a la confiabilidad del servicio. Entre estos factores se incluyen el país de destino, el operador del teléfono móvil y la intensidad de la señal.

Si frecuentemente sus usuarios tienen problemas para recibir mensajes de texto de manera confiable, indíqueles que usen en su lugar el método de llamada de teléfono o aplicación móvil. La aplicación móvil puede recibir notificaciones con conexiones de telefonía móvil y Wi-Fi. Además, la aplicación móvil puede generar códigos de comprobación aunque el dispositivo no tenga señal. La aplicación Microsoft Authenticator está disponible para [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) y [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

En la medida de lo posible, recomendamos usar SMS unidireccionales en lugar de bidireccionales si tiene que usar mensajes de texto. Esto se debe a que los SMS unidireccionales son más seguros y evitan que los usuarios incurran en cargos SMS globales como consecuencia de responder a un mensaje de texto enviado desde otro país.

**P: ¿Puedo cambiar la cantidad de tiempo que los usuarios tienen para escribir el código de comprobación de un mensaje de texto antes de que se agote el tiempo de espera del sistema?**

En algunos casos, sí es posible. 

Para SMS unidireccionales con el servidor Azure MFA v7.0 o superior, puede configurar el ajuste de tiempo de espera estableciendo una clave del registro. Una vez que el servicio de nube MFA envía el mensaje de texto, se devuelve el código de comprobación (o código de acceso de un solo uso) al servidor MFA. El servidor MFA almacena el código en la memoria durante 300 segundos de forma predeterminada. Si el usuario no escribe el código antes de que transcurran los 300 segundos, se denegará la autenticación. Siga estos pasos para cambiar la configuración de tiempo de espera predeterminada:

1. Vaya a HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Cree una clave del Registro DWORD llamada **pfsvc_pendingSmsTimeoutSeconds** y establezca el tiempo (en segundos) durante el cual desea que el Servidor Azure MFA almacene los códigos de acceso de un solo uso.

>[!TIP] 
>Si tiene varios servidores MFA, solo el que procesó la solicitud de autenticación original conoce el código de comprobación que se envió al usuario. Cuando el usuario escribe el código, la solicitud de autenticación para validarlo tiene que enviarse al mismo servidor. Si la validación del código se envía a un servidor diferente, se deniega la autenticación. 

Para SMS bidireccional con el servidor Azure MFA, puede configurar el ajuste de tiempo de espera en el Portal de administración de MFA. Si los usuarios no responden al SMS dentro del período de tiempo de espera definido, se deniega la autenticación. 

Para SMS unidireccionales con Azure MFA en la nube (incluido el adaptador de AD FS o la extensión del Servidor de directivas de redes), no se puede configurar el ajuste de tiempo de espera. Azure AD almacena el código de comprobación durante 180 segundos. 

**P.: ¿Puedo utilizar tokens de hardware con Servidor Microsoft Azure Multi-Factor Authentication?**

Si usa Servidor Azure Multi-Factor Authentication, puede importar los tokens de contraseña de un solo uso de duración definida (TOTP) y los de autenticación abierta (OATH) de terceros y, después, utilizarlos para realizar la comprobación en dos pasos.

Puede usar tokens de ActiveIdentity del tipo OATH TOTP si coloca la clave secreta en un archivo CSV y lo importa a Servidor Azure Multi-Factor Authentication. Puede usar tokens de OATH con Active Directory Federation Services (ADFS), autenticación basada en formularios de Internet Information Services (IIS) y Servicio de autenticación remota telefónica de usuario (RADIUS) siempre que el sistema cliente pueda aceptar las entradas de usuario.

Puede importar tokens de OATH TOTP de terceros con los siguientes formatos:  

- Contenedor de claves simétricas portátil (PSKC)  
- CSV si el archivo contiene un número de serie, una clave secreta en formato Base 32 y un intervalo de tiempo  

**P.: ¿Puedo utilizar Servidor Microsoft Azure Multi-Factor Authentication para proteger Terminal Services?**

Sí, pero si usa Windows Server 2012 R2 o versiones posteriores, solo puede proteger Terminal Services con Puerta de enlace de Escritorio remoto (Puerta de enlace de RD).

Gracias a los cambios de seguridad realizados en Windows Server 2012 R2, se ha modificado la forma en que Servidor Microsoft Azure Multi-Factor Authentication se conecta al paquete de seguridad de autoridad de seguridad local (LSA) en Windows Server 2012 y versiones anteriores. Para las versiones de Terminal Services en Windows 2012 o versiones anteriores, puede [proteger una aplicación con la autenticación de Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Sin embargo, si va a utilizar Windows Server 2012 R2, necesita el servicio Puerta de enlace de Escritorio remoto.

**P: Configuré el identificador de llamada en Servidor MFA, pero mis usuarios siguen recibiendo llamadas de Multi-Factor Authentication provenientes de un autor de llamada anónimo.**

A veces, cuando las llamadas de Multi-Factor Authentication se realizan a través de la red telefónica pública, se enrutan a través de un operador que no admite la característica de identificación de llamadas. Por este motivo, los identificadores de llamada no están garantizados, aunque el sistema de Multi-Factor Authentication los envíe siempre.

**P: ¿Por qué se les pide a mis usuarios que registren su información de seguridad?**
Hay varios motivos por los cuales se les pueden pedir a los usuarios que registren su información de seguridad:

- El administrador habilitó al usuario para MFA en Azure AD, pero todavía no tiene registrada la información de seguridad de su cuenta.
- Se habilitó al usuario para el restablecimiento de contraseña de autoservicio en Azure AD. La información de seguridad le ayudará a restablecer su contraseña en el futuro si llegara a olvidarla.
- El usuario tuvo acceso a una aplicación con una directiva de acceso condicional para requerir MFA y no se registró anteriormente para MFA.
- El usuario registra un dispositivo con Azure AD (incluido Azure AD Join) y la organización requiere MFA para el registro de dispositivos, pero el usuario no se registró anteriormente para MFA.
- El usuario genera Windows Hello para empresas en Windows 10 (que requiere MFA) y no se registró anteriormente para MFA.
- La organización creó y habilitó una directiva de registro de MFA que se aplicó al usuario.
- El usuario se registró anteriormente para MFA, pero eligió un método de comprobación que un administrador deshabilitó desde entonces. Por lo tanto, el usuario debe volver a registrarse para MFA para poder seleccionar un método de comprobación predeterminado nuevo.


## <a name="errors"></a>Errors
**P.: ¿Qué deben hacer los usuarios si ven un mensaje de error que indica que la solicitud de autenticación no es para una cuenta activada al usar notificaciones de aplicación móvil?**

Indíqueles que sigan este procedimiento a fin de quitar su cuenta de la aplicación móvil para después agregarla de nuevo:

1. Vaya a [su perfil del Portal de Azure](https://account.activedirectory.windowsazure.com/profile/) e inicie sesión con la cuenta de la organización.
2. Seleccione **Comprobación de seguridad adicional**.
3. Quite la cuenta existente de la aplicación móvil.
4. Haga clic en **Configurar**y siga las instrucciones para volver a configurar la aplicación móvil.

**P.: ¿Qué deben hacer los usuarios si ven un mensaje de error 0x800434D4L al iniciar sesión en una aplicación que no es de explorador?**

El error 0x800434D4L se genera cuando intenta iniciar sesión en una aplicación sin explorador, instalada en un equipo local, que no funciona con las cuentas que requieren la verificación en dos pasos.

Una forma de solucionar este error es tener una cuenta de usuario independiente para las operaciones relacionadas con la administración y otra para las no administrativas. Más adelante, puede vincular los buzones entre la cuenta de administrador y una sin derechos administrativos; de este modo, podrá iniciar sesión en Outlook con la cuenta sin derechos administrativos. Para más información, consulte [Dar a un administrador la capacidad de abrir y ver el contenido del buzón de correo de un usuario](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Pasos siguientes
Si su pregunta no se responde aquí, déjela en los comentarios de la parte inferior de la página. O bien, aquí se muestran algunas opciones adicionales para obtener ayuda:

* Busque soluciones a problemas técnicos comunes en [Microsoft Support Knowledge Base](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport).
* Busque y examine cuestiones técnicas y sus respuestas en la comunidad, o bien realice su propia pregunta en los [foros de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Si es un cliente antiguo de PhoneFactor y tiene alguna pregunta o necesita ayuda para restablecer una contraseña, haga clic en el vínculo de [restablecimiento de contraseña](mailto:phonefactorsupport@microsoft.com) para abrir un caso de soporte.
* Póngase en contacto con un profesional de soporte técnico a través de [Soporte técnico de Servidor Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Al ponerse en contacto con nosotros, nos resultará de gran utilidad que incluya tanta información sobre su problema como sea posible. Entre la información que puede aportar se incluyen la página donde vio el error, el código de error específico, el identificador de sesión específico y el identificador del usuario que vio el error.
