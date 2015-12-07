<properties 
	pageTitle="P+F sobre Azure Multi-Factor Authentication " 
	description="Proporciona una lista de las preguntas y respuestas más frecuentes relacionadas con Azure Multi-Factor Authentication (MFA). MFA es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una capa adicional de seguridad a los inicios de sesión y transacciones de los usuarios." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="billmath"/>

# P+F sobre Azure Multi-Factor Authentication


Estas preguntas más frecuentes responden a preguntas acerca de Azure Multi-Factor Authentication. Estas preguntas más frecuentes responden a preguntas acerca de cómo usar el servicio, incluidos el modelo de facturación y la facilidad de uso.

## General

**P: ¿Cómo se puede obtener ayuda con Azure Multi-Factor Authentication?**

[Buscar en Microsoft Knowledge Base (KB)](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Busque en Microsoft Knowledge Base (KB) soluciones técnicas a los problemas de break-fix más comunes sobre soporte de Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor).

[Foros de Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- Puede buscar y examinar preguntas técnicas y sus respuestas en la comunidad o realizar su propia pregunta haciendo clic [aquí](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Restablecimiento de contraseña](mailto:phonefactorsupport@microsoft.com)

- Los clientes de Phonefactor heredado que tienen consultas sobre cómo restablecer la contraseña o desean obtener ayuda para que se la restablezcan deben usar el siguiente vínculo para abrir una incidencia.

[Servicio al cliente de Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor)](https://support.microsoft.com/oas/default.aspx?prid=14947)

- Use este vínculo para ponerse en contacto con un profesional de soporte técnico de Microsoft. Le haremos algunas preguntas para ayudarnos a determinar cuáles son las opciones de soporte. Las opciones de soporte pueden incluir correo electrónico, envío en línea o soporte telefónico. 

[Consultas generales acerca de Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor)](http://azure.microsoft.com/services/multi-factor-authentication)

- Para obtener más información acerca de Servidor Microsoft Azure Multi-Factor Authentication (Phone Factor), o si tiene preguntas acerca de cómo adquirir el producto y las distintas opciones de soporte técnico, envíe un mensaje de correo electrónico a [pfsales@microsoft.com](mailto:pfsales@microsoft.com). 



**P: ¿Cómo controla Servidor Azure Multi-Factor Authentication los datos de usuario?**

Cuando se usa Servidor Azure Multi-Factor Authentication (MFA) local, los datos de un usuario se almacenan en los servidores locales. Los datos de usuario persistentes no se almacenan en la nube. Cuando el usuario realiza una autenticación en dos fases, Servidor MFA envía datos al servicio en la nube de Azure MFA para realizar la autenticación. Cuando estas solicitudes de autenticación se envían al servicio en la nube, en la solicitud y los registros se incluyen los siguientes campos para que estén disponibles en los informes de uso/autenticación del cliente. Algunos de los campos son opcionales y se pueden habilitar o deshabilitar en Servidor Multi-Factor Authentication. La comunicación desde Servidor MFA al servicio en la nube MFA usa SSL/TLS en el puerto 443 de salida. Estos campos son:

- Id. exclusivo: nombre de usuario o Id. interno de Servidor MFA
- Nombre y apellidos: opcional
- Dirección de correo electrónico: opcional
- Número de teléfono: al realizar la autenticación de una llamada de voz o SMS
- Token de dispositivo: al realizar la autenticación de una aplicación móvil
- Modo de autenticación 
- Resultado de la autenticación 
- Nombre de Servidor MFA 
- IP de Servidor MFA 
- IP de cliente: si está disponible



Además de los campos anteriores, el resultado de la autenticación (aceptación o denegación) y el motivo de la denegación también se almacenan con los datos de autenticación y están disponibles mediante los informes de uso y de autenticación.




## Facturación

**P: ¿Se le aplicarán cargos a mi organización por llamadas telefónicas o mensajes de texto que se usen para autenticar a los usuarios?**

Todos los costos se agrupan en el costo por usuario o por autenticación del servicio. No se aplicará cargo alguno a las organizaciones por las llamadas de teléfono individuales que se realicen o los mensajes de texto que se envíen a los usuarios finales cuando se usa Azure Multi-Factor Authentication. Los propietarios de los teléfonos pueden incurrir en costos relacionados con la movilidad u otros que sus compañías de teléfono puedan aplicar a la recepción de llamadas de teléfono o mensajes de texto.

**"P:¿Cómo se factura a la organización por Azure Multi-Factor Authentication?"**

Cuando se crea un proveedor de Multi-Factor Authentication en el Portal de administración de Azure se elige el modelo de facturación y uso "por usuario" o "por autenticación". Es un recurso basado en el consumo que se factura con suscripción a Azure de la organización, al igual que las máquinas virtuales, sitios Web, etc. se facturan con la suscripción.

**P: En el modelo de facturación "por usuario" ¿cómo se efectúan los cargos, según el número de usuarios habilitados para Multi-Factor Authentication o el número de usuarios que realizan comprobaciones?**

La facturación se basa en el número de usuarios habilitados para Multi-Factor Authentication.

## Facilidad de uso

**P: ¿Qué puedo hacer si no recibo una respuesta en mi teléfono o si he olvidado mi teléfono?**

Si configuró previamente un teléfono de reserva, vuelva a intentarlo seleccionando ese teléfono cuando se le pida desde la página de inicio de sesión. Si no dispone de otro método configurado, póngase en contacto con su administrador y pídale que actualice el número asignado a tu teléfono principal (móvil o de oficina).

**P: Quité a un usuario de un rol de administrador pero olvidé deshabilitar Multi-Factor Authentication y ahora no aparece en la lista ¿Cómo puedo quitar esta característica?**

- Depende del portal que esté usando, en el panel izquierdo, haga clic en Usuarios o Usuarios y grupos.
- Active la casilla situada junto al usuario que desea editar y, a continuación, haga clic en Editar o en el icono de edición.
- Haga clic en Configuración, en Asignar rol, seleccione Sí y vuelva a agregar al usuario al rol de administrador que ya tenía.
- Vaya a la página de Multi-Factor Authentication. La cuenta debe aparecer en la lista en la página. 
- Siga los pasos anteriores para deshabilitar Multi-Factor Authentication para una cuenta. 
- Ahora ya puede quitar la cuenta del rol de administrador.


**P: ¿Qué tengo que hacer si un usuario se pone en contacto conmigo, el administrador, con una cuenta que se ha bloqueado?**

Puede restablecer el usuario obligándole a realizar de nuevo el proceso de registro. Para ello consulte [Gestión de la configuración de usuario y dispositivo con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md)

**P: ¿Qué tengo que hacer si un usuario ha perdido o le han robado un dispositivo que usa contraseñas de aplicación?**

Puede eliminar todas las contraseñas de aplicación del usuario para evitar un posible acceso no autorizado Una vez que haya un nuevo dispositivo de reemplazo, el usuario puede volver a crearlas. Para ello consulte [Gestión de la configuración de usuario y dispositivo con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md)

**P: ¿Qué pasa si el usuario no es capaz de iniciar sesión en aplicaciones sin explorador?**

- Los usuarios habilitados para la autenticación multifactor requerirán la contraseña de aplicación para iniciar sesión en algunas aplicaciones sin explorador.
- Los usuarios tendrán que borrar la información de inicio de sesión (eliminar información de inicio de sesión), reiniciar la aplicación e iniciar sesión con su nombre de usuario y contraseña de aplicación. 

Para obtener información acerca de cómo crear contraseñas de aplicación consulte [Ayuda con las contraseñas de aplicación](multi-factor-authentication-end-user-app-passwords.md)


>[AZURE.NOTE]Autenticación moderna para los clientes de Office 2013
>
> Los clientes de Office 2013 (incluye Outlook) ahora admiten nuevos protocolos de autenticación que pueden habilitarse para admitir Multi-Factor Authentication. Esto significa que una vez habilitada, las contraseñas de aplicación no son necesarias para los clientes de Office 2013. Para obtener más información, consulte [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Anuncio de la vista previa pública de la autenticación moderna de Office 2013).

**P: ¿Qué tengo que hacer si no recibo un mensaje de texto, o si respondo a un mensaje de texto bidireccional pero se agota el tiempo de espera de la comprobación?**

El servicio Azure Multi-Factor Authentication envía mensajes de texto mediante agregadores SMS. Muchos factores pueden afectar a la confiabilidad de entrega y recepción de mensajes de texto incluido el agregador que se use, el país de destino, el proveedor de telefonía móvil y la intensidad de la señal. Por lo tanto, tanto la entrega de mensajes de texto como la recepción de respuestas SMS cuando hay una comunicación SMS bidireccional, no están garantizadas. Se recomienda cuando sea posible usar SMS unidireccionales con preferencia sobre los SMS bidireccionales, porque es más seguro y evita que los usuarios incurran en cargos SMS globales causados por responder a un mensaje de texto enviado desde otro país.

Las comprobaciones de mensaje de texto también son más confiables en algunos países como Estados Unidos y Canadá. Se aconseja a los usuarios que experimentan dificultades para recibir mensajes de texto de forma confiable al utilizar Azure Multi-Factor Authentication que seleccionen los métodos de aplicación móvil o llamada de teléfono en su lugar. La aplicación móvil es muy útil porque las notificaciones de aplicación móvil se pueden recibir tanto a través de conexiones Wi-Fi como de red móvil y su código de acceso se muestra incluso cuando el dispositivo no tiene ninguna señal. La aplicación Azure Authenticator está disponible para [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

**P: ¿puedo utilizar tokens de hardware con el servidor MFA de Azure?**

Si está utilizando el servidor MFA de Azure, los tokens de TOTP OATH de terceros se pueden importar y usarse para MFA. Actualmente se admite la importación de tokens de TOTP OATH de terceros en un formato anterior de PSKC que puede producir Gemalto para sus tokens y se admite la importación de tokens en formato CSV. Al importar tokens en formato CSV, el archivo CSV debe contener un número de serie, la clave secreta en formato Base32 y un intervalo de tiempo (normalmente 30 segundos).

Por lo tanto, si los tokens de ActiveIdentity son tokens TOTP OATH y puede obtener el archivo de clave secreta en un archivo CSV que puede importarse en el servidor MFA de Azure, podrá utilizarlos. Los tokens OATH pueden utilizarse con AD FS, con RADIUS cuando el sistema cliente puede procesar las respuestas de desafío de acceso y con autenticación basada en formularios de IIS.


## Errors

**P: ¿Qué tengo que hacer cuando al autenticar con las notificaciones de aplicación móvil aparece un error que me informa de que la solicitud de autenticación no es para una cuenta activada?**

- Vaya a [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) e inicie sesión con su cuenta profesional.
- Si es necesario, haga clic en otras opciones de comprobación y seleccione una opción diferente para completar la comprobación de la cuenta.
- Haga clic en Comprobación de seguridad adicional.
- Quite la cuenta existente de la aplicación móvil.
- Haga clic en Configurar y siga las instrucciones para volver a configurar la aplicación móvil.




**P: ¿Qué hago si veo un error 0x800434D4L al intentar iniciar sesión con una aplicación sin explorador?**

Actualmente, la comprobación de seguridad adicional solo puede utilizarse con aplicaciones o servicios a los que pueda tener acceso a través del explorador. Aplicaciones sin explorador (también denominadas aplicaciones cliente enriquecidas) que están instaladas en el equipo local como Windows Powershell no funcionarán con cuentas que son necesarias para la comprobación de seguridad adicional. En este caso, verá el error 0x800434D4L que genera la aplicación.

Una forma de solucionar este problema es tener una cuenta de usuario independiente para las operaciones relacionadas con la administración, diferenciándolas de las operaciones no administrativas. Más adelante puede vincular los buzones entre la cuenta de administrador y una cuenta sin derechos administrativos, y así que podrá iniciar sesión en Outlook con su cuenta sin derechos administrativos. Para obtener más detalles acerca de esta información, consulte [Dar a un administrador la capacidad de abrir y ver el contenido del buzón de correo de un usuario] (http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1)).

<!---HONumber=AcomDC_1125_2015-->