---
title: P+F sobre Azure Multi-Factor Authentication | Microsoft Docs
description: "Proporciona una lista de las preguntas y respuestas más frecuentes relacionadas con Azure Multi-Factor Authentication. Multi-Factor Authentication es un método para comprobar la identidad de un usuario que requiere usar más de un nombre de usuario y contraseña. Proporciona una capa adicional de seguridad a los inicios de sesión y transacciones de los usuarios."
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
ms.date: 01/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9617cd00ed4a5f8f867542238e5008a9a17663c9
ms.openlocfilehash: 1e01bdc099af865e01eb2784cf367e482300ade8


---
# <a name="azure-multi-factor-authentication-faq"></a>P+F sobre Azure Multi-Factor Authentication 
Este artículo de P+F da respuesta a las preguntas más frecuentes sobre Microsoft Azure Multi-Factor Authentication y el uso del servicio de autenticación multifactor; por ejemplo, cuestiones sobre el modelo de facturación y la facilidad de uso.

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

El resultado de la comprobación (aceptación o denegación) y el motivo de la denegación también se almacenan con los datos de autenticación y están disponibles en los informes de uso y autenticación.

## <a name="billing"></a>Facturación
La mayoría de las preguntas de facturación se pueden responder consultando la [página Precios de Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**P.: ¿Se le aplican cargos a mi organización por llamadas de teléfono o mensajes de texto que se usen para autenticar a los usuarios?**

No se cobrará nada a las organizaciones por las llamadas de teléfono individuales que se realicen o los mensajes de texto que se envíen a los usuarios cuando se use Azure Multi-Factor Authentication. Podrían cobrarse a los propietarios de los teléfonos las llamadas de teléfono o los mensajes de texto que reciban, dependiendo de su servicio telefónico personal.

**P.: En el modelo de facturación por usuario, ¿cómo se efectúan los cargos, según el número de usuarios configurado para utiliza Multi-Factor Authentication o el número de usuarios que realizan comprobaciones?**

La facturación se basa en el número de usuarios configurado para utilizar Multi-Factor Authentication.

**P.: ¿Cómo funciona la facturación de Multi-Factor Authentication?**

Cuando usa el modelo "por usuario" o "por autenticación", Azure MFA es un recurso basado en el consumo. Todos los cargos se facturan con respecto a la suscripción de Azure de la organización, igual que las máquinas virtuales, los sitios web, etc.

Cuando se utiliza el modelo de licencias, se compran las licencias de Azure Multi-Factor Authentication y luego se asignan a los usuarios, lo mismo que en Office 365 y otros productos de suscripción.

Obtenga más información sobre sus opciones en [Cómo funciona Azure Multi-Factor Authentication](multi-factor-authentication-how-it-works.md#how-to-get-azure-multi-factor-authentication).

**P.: ¿Existe una versión gratuita de Azure Multi-Factor Authentication para los administradores?**

En algunos casos, sí. Multi-Factor Authentication para administradores de Azure ofrece un subconjunto de características de Azure MFA sin costo alguno. Esta oferta se aplica a los miembros del grupo de administradores globales de Azure en instancias de Azure Active Directory que no se vinculan a un proveedor de Azure Multi-Factor Authentication basado en el consumo. Mediante un proveedor de Multi-Factor Authentication, se actualizan todos los administradores y usuarios del directorio que están habilitados para utilizar la versión completa de Azure Multi-Factor Authentication.

**P.: ¿Existe una versión gratuita de Azure Multi-Factor Authentication para usuarios de Office 365?**

En algunos casos, sí. Multi-Factor Authentication para Office 365 ofrece un subconjunto de características de Azure MFA sin costo alguno. Esta oferta se aplica a los usuarios que tienen asignada una licencia de Office 365, cuando un proveedor de Azure Multi-Factor Authentication basado en el consumo no se ha vinculado a la instancia correspondiente de Azure Active Directory. Mediante un proveedor de Multi-Factor Authentication, se actualizan todos los administradores y usuarios del directorio que están habilitados para utilizar la versión completa de Azure Multi-Factor Authentication.

**P.: ¿Puede mi organización cambiar entre los modelos de facturación de consumo por usuario y por autenticación en cualquier momento?**

Su organización elige un modelo de facturación cuando crea un recurso. No puede cambiar un modelo de facturación después de aprovisionar el recurso. No obstante, sí es posible crear otro recurso de Multi-Factor Authentication para reemplazar al original. Ni la configuración de usuario ni las opciones de configuración se pueden transferir al nuevo recurso.

**P.: ¿Puede mi organización cambiar entre los modelos de licencia y de facturación por consumo en cualquier momento?**

Cuando se agregan licencias a un directorio que ya tiene un proveedor de Multi-Factor Authentication de Azure por usuario, la facturación basada en el consumo disminuye en función del número de licencias que posee. Si todos los usuarios configurados para usar Multi-Factor Authentication tienen licencias asignadas, el administrador puede eliminar el proveedor de Azure Multi-Factor Authentication.

No puede mezclar la facturación de consumo por autenticación con un modelo de licencia. Cuando un proveedor de Multi-Factor Authentication por autenticación está vinculado a un directorio, a la organización se le facturan todas las solicitudes de comprobación de Multi-Factor Authentication, con independencia de las licencias que se posean.

**P.: ¿Mi organización tiene que usar y sincronizar las identidades para utilizar Azure Multi-Factor Authentication?**

Cuando una organización utiliza un modelo de facturación basada en el consumo, no se requiere Azure Active Directory. La vinculación de un Proveedor de Multi-Factor Authentication a un directorio es opcional. Si su organización no está vinculada a un directorio, puede implementar Servidor Microsoft Azure Multi-Factor Authentication o el SDK local de Microsoft Azure Multi-Factor Authentication.

Active Directory de Azure se necesita para el modelo de licencia, ya que, cuando se compran, las licencias se agregan al directorio y se les asignan a los usuarios de dicho directorio.

## <a name="usability"></a>Facilidad de uso
**P.: ¿Qué puede hacer un usuario si no recibe una respuesta en su teléfono o si no tiene disponible este dispositivo?**

Si el usuario ha configurado un teléfono de reserva, tiene que volver a intentarlo y seleccionar ese teléfono cuando se le pida en la página de inicio de sesión. Si el usuario no ha configurado otro método, el administrador de la organización puede actualizar el número asignado al teléfono principal del usuario.

**P.: ¿Qué puede hacer el administrador si un usuario se pone en contacto con él en relación con una cuenta a la que ya no tiene acceso?**

El administrador puede restablecer la cuenta del usuario pidiéndole que realice de nuevo el proceso de registro. Puede obtener más información en [Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P.: ¿Qué puede hacer un administrador si el teléfono de un usuario que utiliza contraseñas de aplicación está perdido o lo han robado?**

El administrador puede eliminar todas las contraseñas de aplicación del usuario para evitar un posible acceso no autorizado. Una vez que el usuario tenga un nuevo dispositivo de reemplazo, puede volver a crearlas. Puede obtener más información en [Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P.: ¿Qué puede hacer un usuario si no puede iniciar sesión en aplicaciones sin explorador?**

Los usuarios configurados para utilizar Multi-Factor Authentication necesitan una contraseña de aplicación para iniciar sesión en algunas aplicaciones sin explorador. Deben borrar la información de inicio de sesión, reiniciar la aplicación e iniciar sesión con su nombre de usuario y contraseña de aplicación.

Puede obtener más información sobre cómo crear contraseñas de aplicación y ver otros [temas de ayuda relacionados con las contraseñas de aplicación en este artículo](multi-factor-authentication-end-user-app-passwords.md).

> [!NOTE]
> Autenticación moderna para clientes de Office 2013
> 
> Los clientes de Office 2013 (como Outlook) admiten nuevos protocolos de autenticación. Puede configurar Office 2013 para que admita la autenticación multifactor. Cuando configure Office 2013, no necesitará las contraseñas de aplicación para los clientes de Office 2013. Para obtener más información, consulte [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)(Anuncio de la versión preliminar pública de la autenticación moderna de Office 2013).
> 
> 

**P.: ¿Qué puede hacer un usuario si no recibe un mensaje de texto o si responde a uno bidireccional, pero se agota el tiempo de espera de la comprobación?**

Tanto el envío de mensajes de texto como la recepción de respuestas en SMS bidireccionales no están garantizados, pues hay factores que no se pueden controlar y que podrían afectar a la confiabilidad del servicio. Entre estos factores se incluyen el país de destino, el operador del teléfono móvil y la intensidad de la señal.

Los usuarios que experimenten dificultades para recibir mensajes de texto de forma confiable deben seleccionar los métodos de aplicación móvil o llamada de teléfono en su lugar. La aplicación móvil puede recibir notificaciones con conexiones de telefonía móvil y Wi-Fi. Además, la aplicación móvil puede generar códigos de comprobación aunque el dispositivo no tenga señal. La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

En la medida de lo posible, recomendamos usar SMS unidireccionales en lugar de bidireccionales si tiene que usar mensajes de texto. Esto se debe a que los SMS unidireccionales son más seguros y evitan que los usuarios incurran en cargos SMS globales como consecuencia de responder a un mensaje de texto enviado desde otro país.

**P.: ¿Puedo utilizar tokens de hardware con Servidor Microsoft Azure Multi-Factor Authentication?**

Si usa Servidor Azure Multi-Factor Authentication, puede importar los tokens de contraseña de un solo uso de duración definida (TOTP) y los de autenticación abierta (OATH) de terceros y, después, utilizarlos para realizar la comprobación en dos pasos.

Puede utilizar tokens de ActiveIdentity del tipo OATH y TOTP si coloca el archivo de clave secreta en un archivo CSV y lo importa en Servidor Azure Multi-Factor Authentication. Puede utilizar tokens de OATH con Active Directory Federation Services (ADFS), Servicio de autenticación remota telefónica de usuario (RADIUS) cuando el sistema cliente pueda procesar las respuestas de desafío de acceso y con autenticación basada en formularios de Internet Information Server (IIS).

Puede importar tokens de OATH TOTP de terceros con los siguientes formatos:  

* Contenedor de claves simétricas portátil (PSKC)  
* CSV si el archivo contiene un número de serie, una clave secreta en formato Base 32 y un intervalo de tiempo  

**P.: ¿Puedo utilizar Servidor Microsoft Azure Multi-Factor Authentication para proteger Terminal Services?**

Sí, pero solo con el servicio Puerta de enlace de Escritorio remoto si utiliza Windows Server 2012 R2 o posterior.

Gracias a los cambios de seguridad realizados en Windows Server 2012 R2, se ha modificado la forma en que Servidor Microsoft Azure Multi-Factor Authentication se conecta al paquete de seguridad de autoridad de seguridad local (LSA) en Windows Server 2012 y versiones anteriores. Para las versiones de Terminal Services en Windows 2012 o versiones anteriores, puede [proteger una aplicación con la autenticación de Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Sin embargo, si va a utilizar Windows Server 2012 R2, necesita el servicio Puerta de enlace de Escritorio remoto.

**P.: ¿Por qué un usuario recibe una llamada de Multi-Factor Authentication de un llamador anónimo después de configurar la identificación de llamadas?**

A veces, cuando las llamadas de Multi-Factor Authentication se realizan a través de la red telefónica pública, se enrutan a través de un operador que no admite la característica de identificación de llamadas. Por este motivo, los identificadores de llamada no están garantizados, aunque el sistema de Multi-Factor Authentication los envíe siempre.

## <a name="errors"></a>Errors
**P.: ¿Qué deben hacer los usuarios si ven un mensaje de error que indica que la solicitud de autenticación no es para una cuenta activada al usar notificaciones de aplicación móvil?**

Indíqueles que sigan este procedimiento a fin de quitar su cuenta de la aplicación móvil para después agregarla de nuevo:

1. Vaya a [su perfil del Portal de Azure](https://account.activedirectory.windowsazure.com/profile/) e inicie sesión con la cuenta de la organización.
2. Seleccione **Comprobación de seguridad adicional**.
3. Quite la cuenta existente de la aplicación móvil.
4. Haga clic en **Configurar**y siga las instrucciones para volver a configurar la aplicación móvil.

**P.: ¿Qué deben hacer los usuarios si ven un mensaje de error 0x800434D4L al iniciar sesión en una aplicación que no es de explorador?**

En estos momentos, los usuarios solo pueden utilizar la comprobación de seguridad adicional con aplicaciones y servicios a los que puedan tener acceso mediante un explorador. Las aplicaciones que no son de explorador (también denominadas *aplicaciones cliente enriquecidas*) que están instaladas en un equipo local como Windows PowerShell no funcionan con cuentas que son necesarias para la comprobación de seguridad adicional. En este caso, el usuario podría ver que la aplicación genera un error 0x800434D4L.

Una forma de solucionar este problema es tener una cuenta de usuario independiente para las operaciones relacionadas con la administración y otra para las no administrativas. Más adelante, puede vincular los buzones entre la cuenta de administrador y una sin derechos administrativos; de este modo, podrá iniciar sesión en Outlook con la cuenta sin derechos administrativos. Para obtener más información, consulte [Dar a un administrador la capacidad de abrir y ver el contenido del buzón de correo de un usuario](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Pasos siguientes
Si su pregunta no se responde aquí, déjela en los comentarios de la parte inferior de la página. O bien, aquí se muestran algunas opciones adicionales para obtener ayuda:

* Busque soluciones a problemas técnicos comunes en [Microsoft Support Knowledge Base](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport).
* Busque y examine cuestiones técnicas y sus respuestas en la comunidad, o bien realice su propia pregunta en los [foros de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Si es un cliente antiguo de PhoneFactor y tiene alguna pregunta o necesita ayuda para restablecer una contraseña, haga clic en el vínculo de [restablecimiento de contraseña](mailto:phonefactorsupport@microsoft.com) para abrir un caso de soporte.
* Póngase en contacto con un profesional de soporte técnico a través de [Soporte técnico de Servidor Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Al ponerse en contacto con nosotros, nos resultará de gran utilidad que incluya tanta información sobre su problema como sea posible. Entre la información que puede aportar se incluyen la página donde vio el error, el código de error específico, el identificador de sesión específico y el identificador del usuario que vio el error.




<!--HONumber=Jan17_HO1-->


