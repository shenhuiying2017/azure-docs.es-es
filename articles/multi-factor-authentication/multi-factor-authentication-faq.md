---
title: 'P+F sobre Azure Multi-Factor Authentication '
description: Proporciona una lista de las preguntas y respuestas más frecuentes relacionadas con Azure Multi-Factor Authentication. Multi-Factor Authentication es un método para comprobar la identidad de un usuario que requiere usar más de un nombre de usuario y contraseña. Proporciona una capa adicional de seguridad a los inicios de sesión y transacciones de los usuarios.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: kgremban

---
# P+F sobre Azure Multi-Factor Authentication
Este artículo de P+F da respuesta a las preguntas más frecuentes sobre Microsoft Azure Multi-Factor Authentication y el uso del servicio de autenticación multifactor; por ejemplo, cuestiones sobre el modelo de facturación y la facilidad de uso.

## General
**P: ¿Cómo se puede obtener ayuda con Azure Multi-Factor Authentication?**

* [Búsqueda en Microsoft Knowledge Base](https://www.microsoft.com/es-ES/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)
  
  Busque soluciones a problemas técnicos comunes en Knowledge Base.
* [Foros de Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
  
  Busque y examine cuestiones técnicas y sus respuestas en la comunidad, o bien realice su propia pregunta en los [foros de Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* [Restablecimiento de contraseña](mailto:phonefactorsupport@microsoft.com)
  
  Si es un cliente antiguo de PhoneFactor y tiene alguna pregunta o necesita ayuda para restablecer una contraseña, haga clic en el vínculo de [restablecimiento de contraseña](mailto:phonefactorsupport@microsoft.com) para abrir un caso de soporte.
* [Soporte técnico de Servidor Microsoft Azure Multi-Factor Authentication (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947)
  
  Use este vínculo para ponerse en contacto con un profesional de soporte técnico de Microsoft. Se le realizarán algunas preguntas para ayudarlo a determinar las opciones de soporte técnico con las que cuenta, entre las que podrían estar el correo electrónico, el envío en línea o el soporte telefónico.

**P.: ¿Cómo controla Servidor Azure Multi-Factor Authentication los datos de usuario?**

Cuando se usa Servidor Microsoft Azure Multi-Factor Authentication, los datos de un usuario se almacenan en los servidores locales. Los datos de usuario persistentes no se almacenan en la nube. Cuando el usuario realiza la autenticación en dos fases, Servidor Microsoft Azure Multi-Factor Authentication envía datos al servicio en la nube Microsoft Azure Multi-Factor Authentication para llevar a cabo la autenticación. Cuando se envíen las solicitudes de autenticación al servicio en la nube, en la solicitud y los registros se incluyen los datos de los campos de la siguiente lista para que estén disponibles en los informes de uso y autenticación del cliente. Algunos de los campos son opcionales, y puede configurarlos en Servidor Microsoft Azure Multi-Factor Authentication. Para establecer la comunicación desde Servidor Microsoft Azure Multi-Factor Authentication al servicio en la nube Multi-Factor Authentication se utilizan los protocolos Capa de sockets seguros (SSL) o Seguridad de la capa de transporte (TLS) en el puerto 443 de salida. Los campos de datos que se incluyen en los registros de autenticación en dos fases son los siguientes:

* **Id. único** (cualquier nombre de usuario o id. de Servidor Microsoft Azure Multi-Factor Authentication local)
* **Nombre y apellidos** (opcional)
* **Dirección de correo electrónico** (opcional)
* **Número de teléfono** (al realizar la autenticación de una llamada de voz o SMS)
* **Token de dispositivo** (al realizar la autenticación de una aplicación móvil)
* **Modo de autenticación**
* **Resultado de la autenticación**
* **Nombre de Servidor Multi-Factor Authentication**
* **IP de Servidor Multi-Factor Authentication**
* **IP de cliente** (si está disponible)

Además de estos campos, el resultado de la autenticación (aceptación o denegación) y el motivo de la denegación también se almacenan con los datos de autenticación y están disponibles en los informes de uso y autenticación.

## Facturación
**P: ¿Se le aplicarán cargos a mi organización por llamadas telefónicas o mensajes de texto que se usen para autenticar a los usuarios?**

Todos los costos se agrupan en el costo por usuario o por autenticación del servicio. No se cobrará nada a las organizaciones por las llamadas de teléfono individuales que se realicen o los mensajes de texto que se envíen a los usuarios cuando se usa Azure Multi-Factor Authentication. Los propietarios de los teléfonos podrían asumir costos relacionados con la itinerancia u otros que sus compañías de teléfono puedan aplicar a la recepción de llamadas de teléfono o mensajes de texto.

**P.: ¿Cómo se factura a una organización por Azure Multi-Factor Authentication?**

Azure Multi-Factor Authentication está disponible como servicio independiente con opciones de facturación por usuario y por autenticación, o bien incluido dentro del paquete de Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite. El servicio independiente está disponible según una base de consumo que se factura mensualmente con respecto al compromiso monetario de Azure de una organización, o bien como una licencia anual por usuario a través de un Contrato Enterprise de Microsoft, el programa Open License de Microsoft, el programa de proveedores de soluciones en la nube de Microsoft (CSP) o de forma directa.

> [!IMPORTANT]
> Las regiones de Australia están disponibles para clientes con presencia empresarial en Australia o Nueva Zelanda.
> 
> 

| Modelo de facturación | Precio |
| --- | --- |
| Consumo por usuario (compromiso monetario de Azure) |1,40 $ al mes (autenticaciones ilimitadas) |
| Consumo por autenticación (compromiso monetario de Azure) |1,40 $ por 10 autenticaciones |
| Licencia anual por usuario (directa) |1,40 $ al mes (autenticaciones ilimitadas) |
| Licencia anual por usuario (licencias por volumen) |Póngase en contacto con su [representante del Contrato Enterprise](https://www.microsoft.com/es-ES/licensing/licensing-programs/enterprise.aspx). |

**P.: En el modelo de facturación por usuario, ¿cómo se efectúan los cargos, según el número de usuarios configurado para utiliza Multi-Factor Authentication o el número de usuarios que realizan comprobaciones?**

La facturación se basa en el número de usuarios configurado para utilizar Multi-Factor Authentication.

**P.: ¿Cómo funciona la facturación de Multi-Factor Authentication?**

Cuando una organización utiliza el modelo de uso y de facturación de consumo por usuarios o por autenticación, el administrador de la organización elige el tipo de uso al crear un proveedor de Multi-Factor Authentication en el Portal de Azure clásico. Se trata de un recurso que se factura con respecto a la suscripción de Azure de la organización, igual que las máquinas virtuales, los sitios web, etc. Cuando se utiliza el modelo de licencias, se compran las licencias de Azure Multi-Factor Authentication y luego se asignan a los usuarios, lo mismo que en Office 365 y otros productos de suscripción.

**P.: ¿Existe una versión gratuita de Azure Multi-Factor Authentication para los administradores?**

Se ofrece un subconjunto de funcionalidades de Azure Multi-Factor Authentication conocidas como "Multi-Factor Authentication para administradores de Azure", sin costo para los miembros del grupo de administradores globales de Azure, cuando un proveedor de Multi-Factor Authentication basado en consumo no se ha vinculado a la instancia correspondiente de Azure Active Directory. Mediante un proveedor de Multi-Factor Authentication, se actualizan todos los administradores y usuarios del directorio que están habilitados para utilizar la versión completa de Azure Multi-Factor Authentication.

**P.: ¿Existe una versión gratuita de Azure Multi-Factor Authentication para usuarios de Office 365?**

Se ofrece un subconjunto de funcionalidades de Azure Multi-Factor Authentication conocidas como "Multi-Factor Authentication para Office 365", sin costo para los usuarios que tienen asignada una licencia de Office 365, cuando un proveedor de Multi-Factor Authentication basado en consumo no se ha vinculado a la instancia correspondiente de Azure Active Directory. Mediante un proveedor de Multi-Factor Authentication, se actualizan todos los administradores y usuarios del directorio que están habilitados para utilizar la versión completa de Azure Multi-Factor Authentication.

**P.: ¿Puede mi organización cambiar entre los modelos de facturación de consumo por usuario y por autenticación en cualquier momento?**

Su organización elige un modelo de facturación cuando crea un recurso. No puede cambiar un modelo de facturación después de aprovisionar el recurso. No obstante, sí es posible crear un nuevo recurso de Multi-Factor Authentication para reemplazar al original. Tenga en cuenta que la configuración de usuario y las opciones de configuración no se pueden transferir al nuevo recurso.

**P.: ¿Puede mi organización cambiar entre los modelos de licencia y de facturación por consumo en cualquier momento?**

Su organización puede comprar licencias de Azure Multi-Factor Authentication, Azure Active Directory Premium, Enterprise Mobility Suite y Enterprise Cloud Suite en cualquier momento. Cuando se agregan licencias a un directorio que ya tiene un proveedor de Multi-Factor Authentication de Azure por usuario, la facturación basada en el consumo disminuye en función del número de licencias que posee. Si todos los usuarios configurados para usar Multi-Factor Authentication tienen licencias asignadas, el administrador puede eliminar el proveedor de Azure Multi-Factor Authentication. Las organizaciones no pueden mezclar la facturación de consumo por autenticación con un modelo de licencia. Cuando un proveedor de Multi-Factor Authentication por autenticación está vinculado a un directorio, a la organización se le facturan todas las solicitudes de comprobación de Multi-Factor Authentication, con independencia de las licencias que se posean.

**P.: ¿Mi organización tiene que usar y sincronizar las identidades para utilizar Azure Multi-Factor Authentication?**

Cuando una organización utiliza un modelo de facturación basada en el consumo, no se requiere Azure Active Directory. La vinculación de un Proveedor de Multi-Factor Authentication a un directorio es opcional. Si su organización no está vinculada a un directorio, puede implementar Servidor Microsoft Azure Multi-Factor Authentication o el SDK local de Microsoft Azure Multi-Factor Authentication. Active Directory de Azure se necesita para el modelo de licencia, ya que, cuando se compran, las licencias se agregan al directorio y se les asignan a los usuarios de dicho directorio.

## Facilidad de uso
**P.: ¿Qué puede hacer un usuario si no recibe una respuesta en su teléfono o si no tiene disponible este dispositivo?**

Si el usuario configuró previamente un teléfono de reserva, tiene que volver a intentarlo y seleccionar ese teléfono cuando se le pida en la página de inicio de sesión. Si el usuario no ha configurado otro método, debe ponerse en contacto con el administrador de la organización y pedirle que actualice el número asignado al teléfono principal del usuario, el móvil o el del trabajo.

**P.: ¿Qué puede hacer el administrador si un usuario se pone en contacto con él en relación con una cuenta a la que ya no tiene acceso?**

El administrador puede restablecer la cuenta del usuario pidiéndole que realice de nuevo el proceso de registro. Puede obtener más información en [Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P.: ¿Qué puede hacer un administrador si el teléfono de un usuario que utiliza contraseñas de aplicación está perdido o lo han robado?**

El administrador puede eliminar todas las contraseñas de aplicación del usuario para evitar un posible acceso no autorizado. Una vez que el usuario tenga un nuevo dispositivo de reemplazo, puede volver a crearlas. Puede obtener más información en [Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube](multi-factor-authentication-manage-users-and-devices.md).

**P.: ¿Qué puede hacer un usuario si no puede iniciar sesión en aplicaciones sin explorador?**

* Los usuarios configurados para utilizar Multi-Factor Authentication necesitan una contraseña de aplicación para iniciar sesión en algunas aplicaciones sin explorador.
* Deben borrar la información de inicio de sesión, reiniciar la aplicación e iniciar sesión con su nombre de usuario y contraseña de aplicación.

Puede obtener más información sobre cómo crear contraseñas de aplicación y ver otros [temas de ayuda relacionados con las contraseñas de aplicación en este artículo](multi-factor-authentication-end-user-app-passwords.md).

> [!NOTE]
> Autenticación moderna para clientes de Office 2013
> 
> Los clientes de Office 2013 (como Outlook) admiten nuevos protocolos de autenticación. Puede configurar Office 2013 para que admita la autenticación multifactor. Cuando configure Office 2013, no necesitará las contraseñas de aplicación para los clientes de Office 2013. Para obtener más información, consulte [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Anuncio de la versión preliminar pública de la autenticación moderna de Office 2013).
> 
> 

**P.: ¿Qué puede hacer un usuario si no recibe un mensaje de texto o si responde a uno bidireccional, pero se agota el tiempo de espera de la comprobación?**

El servicio Azure Multi-Factor Authentication envía mensajes de texto mediante agregadores SMS. Son muchos los factores que pueden afectar a la confiabilidad de entrega y recepción de mensajes de texto, entre ellos el agregador que se use, el país de destino, la compañía de telefonía móvil del usuario y la intensidad de la señal. Por lo tanto, no están garantizadas ni la entrega de mensajes de texto ni la recepción de respuestas SMS en comunicaciones SMS bidireccionales. En la medida de lo posible, recomendamos usar SMS unidireccionales en lugar de bidireccionales. Esto se debe a que los SMS unidireccionales son más seguros y evitan que los usuarios incurran en cargos SMS globales como consecuencia de responder a un mensaje de texto enviado desde otro país.

Las comprobaciones de mensaje de texto también son más confiables en algunos países como Estados Unidos y Canadá. Se aconseja a los usuarios que experimentan dificultades para recibir mensajes de texto de forma confiable al utilizar Azure Multi-Factor Authentication que seleccionen los métodos de aplicación móvil o llamada de teléfono en su lugar. El método de autenticación de aplicación móvil es muy útil, ya que el usuario puede recibir notificaciones de aplicación móvil con conexiones Wi-Fi y de red de telefonía móvil. Además, el código de acceso de aplicación móvil se muestra aunque el dispositivo no tenga señal. La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).

**P.: ¿Puedo utilizar tokens de hardware con Servidor Microsoft Azure Multi-Factor Authentication?**

Si utiliza Servidor Microsoft Azure Multi-Factor Authentication, puede importar los tokens de contraseña de un solo uso (TOTP) y los de autenticación abierta de duración definida (OATH) de terceros y, después, utilizarlos para realizar la autenticación multifactor. Actualmente se admite la importación de tokens de TOTP y OATH de terceros en un formato anterior de contenedor de claves simétricas portátil (PSKC) que puede producir Gemalto para sus tokens. También se pueden importar tokens en formato CSV. Al importar tokens en este formato, el archivo CSV debe contener un número de serie, la clave secreta en formato Base32 y un intervalo de tiempo (normalmente, 30 segundos).

Puede utilizar tokens de ActiveIdentity del tipo OATH y TOTP si puede colocar el archivo de clave secreta en un archivo CSV que puede importar en Servidor Microsoft Azure Multi-Factor Authentication. Puede utilizar tokens de OATH con Active Directory Federation Services (ADFS), Servicio de autenticación remota telefónica de usuario (RADIUS) —cuando el sistema cliente pueda procesar las respuestas de desafío de acceso— y con autenticación basada en formularios de Internet Information Server (IIS).

**P.: ¿Puedo utilizar Servidor Microsoft Azure Multi-Factor Authentication para proteger Terminal Services?**

Sí, pero si utiliza Windows Server 2012 R2 o una versión posterior, puede hacerlo con el servicio Puerta de enlace de Escritorio remoto.

Gracias a los cambios de seguridad realizados en Windows Server 2012 R2, se ha modificado la forma en que Servidor Microsoft Azure Multi-Factor Authentication se conecta al paquete de seguridad de autoridad de seguridad local (LSA) en Windows Server 2012 y versiones anteriores. Para las versiones de Terminal Services en Windows 2012 o versiones anteriores, puede simplemente [proteger una aplicación con la autenticación de Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Sin embargo, si va a utilizar Windows Server 2012 R2, necesitará el servicio Puerta de enlace de Escritorio remoto.

**P.: ¿Por qué un usuario recibe una llamada de Multi-Factor Authentication de un llamador anónimo después de configurar la identificación de llamadas?**

A veces, cuando las llamadas de Multi-Factor Authentication se realizan a través de la red telefónica pública, se enrutan a través de un operador que no admite la característica de identificación de llamadas. Por este motivo, los identificadores de llamada no están garantizados, aunque el sistema de Multi-Factor Authentication los envíe siempre.

## Errors
**P.: ¿Qué puede hacer un usuario si ve un mensaje de error que indica que la solicitud de autenticación no es para una cuenta activada cuando el usuario se autentica con las notificaciones de aplicación móvil?**

Utilice este procedimiento:

1. Vaya a [su perfil del Portal de Azure](https://account.activedirectory.windowsazure.com/profile/) e inicie sesión con la cuenta de la organización.
2. Si es necesario, haga clic en **Other verification options** (Otras opciones de comprobación) y seleccione una opción diferente para completar la comprobación de la cuenta.
3. Haga clic en **Comprobación de seguridad adicional**.
4. Quite la cuenta existente de la aplicación móvil.
5. Haga clic en **Configurar** y siga las instrucciones para volver a configurar la aplicación móvil.

**P.: ¿Qué puede hacer un usuario si ve un mensaje de error 0x800434D4L cuando trata de iniciar sesión con una aplicación sin explorador?**

En estos momentos, los usuarios solo pueden utilizar la comprobación de seguridad adicional con aplicaciones y servicios a los que pueda acceder mediante el explorador. Las aplicaciones sin explorador (también denominadas "*aplicaciones cliente enriquecidas*") que están instaladas en un equipo local como Windows PowerShell no funcionarán con cuentas que son necesarias para la comprobación de seguridad adicional. En este caso, el usuario podría ver que la aplicación genera un error 0x800434D4L.

Una forma de solucionar este problema es tener una cuenta de usuario independiente para las operaciones relacionadas con la administración y otra para las no administrativas. Más adelante, puede vincular los buzones entre la cuenta de administrador y una sin derechos administrativos; de este modo, podrá iniciar sesión en Outlook con la cuenta sin derechos administrativos. Para obtener más información, consulte [Dar a un administrador la capacidad de abrir y ver el contenido del buzón de correo de un usuario](http://help.outlook.com/141/gg709759.aspx?sl=1).

<!---HONumber=AcomDC_0921_2016-->