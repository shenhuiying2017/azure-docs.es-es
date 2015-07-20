<properties 
	pageTitle="Implementación del Portal de usuarios para Servidor Azure Multi-Factor Authentication" 
	description="En esta página de Azure Multi-Factor Authentication, se describe cómo empezar a trabajar con Azure MFA y el Portal de usuarios." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Implementación del Portal de usuarios para Servidor Azure Multi-Factor Authentication

El Portal de usuarios permite al administrador instalar y configurar el Portal de usuarios de Azure Multi-Factor Authentication. El Portal de usuarios es un sitio web de IIS que permite a los usuarios inscribirse en Azure Multi-Factor Authentication y mantener sus cuentas. Un usuario puede cambiar el número de teléfono, modificar el PIN o evitar Azure Multi-Factor Authentication durante su próximo inicio de sesión.

Los usuarios iniciarán sesión en el Portal de usuarios mediante el nombre de usuario y la contraseña normales y completarán una llamada de Azure Multi-Factor Authentication o responderán a preguntas de seguridad para completar la autenticación. Si se permite la inscripción de usuarios, el usuario configurará el número de teléfono y el PIN la primera vez que inicien sesión en el Portal de usuarios.

Es posible que se configuren administradores para el Portal de usuarios y que se les conceda permiso para agregar usuarios nuevos y actualizar los existentes.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## Implementación del Portal de usuarios en el mismo servidor que Servidor Azure Multi-Factor Authentication

Los siguientes requisitos previos son obligatorios para instalar el Portal de usuarios en el mismo servidor que Servidor Azure Multi-Factor Authentication:

- IIS debe estar instalado, incluido ASP.NET y Compatibilidad con la metabase de IIS 6 (para IIS 7 o posterior). 
- El usuario que haya iniciado sesión debe tener derechos de administrador para el equipo y el dominio, si corresponde. La razón es que la cuenta necesita permisos para crear grupos de seguridad de Active Directory.

### Para implementar el Portal de usuarios para Servidor Azure Multi-Factor Authentication

<ol>
<li>En Servidor Azure Multi-Factor Authentication: haga clic en el icono Portal de usuarios en el menú de la izquierda y después en el botón Instalar portal de usuarios. <li>Haga clic en Siguiente. <li>Haga clic en Siguiente. <li>Si el equipo está unido a un dominio y la configuración de Active Directory para proteger la comunicación entre el Portal de usuarios y el servicio Azure Multi-Factor Authentication está incompleta, se mostrará el paso de Active Directory. Haga clic en el botón Siguiente para completar la configuración de forma automática. <li>Haga clic en Siguiente. <li>Haga clic en Siguiente. <li>Haga clic en Cerrar. <li>Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth). Asegúrese de que no aparezca ningún error ni advertencia de certificado.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## Implementación del Portal de usuarios de Servidor Azure Multi-Factor Authentication en otro servidor

Para usar la aplicación Azure Multi-Factor Authentication, se requiere lo siguiente para que pueda comunicarse correctamente con el Portal de usuarios:

Consulte los requisitos de hardware y software:

- Debe usar la versión 6.0 o posterior del Servidor Azure Multi-Factor Authentication.
- El Portal de usuarios debe estar instalado en un servidor web con conexión a Internet con Microsoft® Internet Information Services (IIS) 6.x, IIS 7.x o posterior.
- Si utiliza IIS 6.x, asegúrese de que ASP.NET versión 2.0.50727 esté instalado, registrado y establecido en Permitido.
- Entre los servicios de rol necesarios cuando se usan IIS 7.x o versiones posteriores, se incluyen ASP.NET y Compatibilidad con la metabase de IIS 6.
- El Portal de usuarios debe estar protegido con un certificado SSL.
- Debe estar instalado el SDK del servicio web de Azure Multi-Factor Authentication en IIS 6.x, IIS 7.x o posterior en el servidor donde esté instalado Servidor Azure Multi-Factor Authentication.
- El SDK del servicio web de Azure Multi-Factor Authentication debe estar protegido con un certificado SSL.
- El Portal de usuarios debe ser capaz de conectarse al SDK del servicio web de Azure Multi-Factor Authentication a través de SSL.
- El Portal de usuarios debe poder autenticarse en el SDK del servicio web de Azure Multi-Factor Authentication mediante las credenciales de una cuenta de servicio que pertenezca a un grupo de seguridad llamado "PhoneFactor Admins". La cuenta de servicio y el grupo existen en Active Directory si Servidor Azure Multi-Factor Authentication se ejecuta en un servidor unido al dominio. La cuenta de servicio y el grupo existen localmente en Servidor Azure Multi-Factor Authentication si no está unido al dominio.

Para instalar el Portal de usuarios en un servidor diferente a Servidor Azure Multi-Factor Authentication, se deben seguir estos tres pasos:

1. Instalación del SDK del servicio web
2. Instalación del Portal de usuarios
3. Establecimiento de la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication


### Instalación del SDK del servicio web

Si el SDK del servicio web de Azure Multi-Factor Authentication aún no está instalado en Servidor Azure Multi-Factor Authentication, vaya a dicho servidor y abra el servidor Azure Multi-Factor Authentication. Haga clic en el icono SDK del servicio web, haga clic en el botón Instalar SDK del servicio web y siga las instrucciones. El SDK del servicio web debe estar protegido con un certificado SSL. Un certificado autofirmado es adecuado para este propósito, pero debe importarse al almacén "Entidades de certificación de raíz de confianza" de la cuenta Equipo local en el servidor web del Portal de usuarios para que confíe en ese certificado cuando inicie la conexión SSL.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### Instalación del Portal de usuarios

Antes de instalar el Portal de usuarios en un servidor independiente, tenga en cuenta lo siguiente:

- Resulta útil abrir un explorador web en un servidor web con conexión a Internet e ir a la dirección URL del SDK del servicio web que se especificó en el archivo web.config. Si el explorador puede obtener acceso al servicio web correctamente, debería solicitarle credenciales. Escriba el nombre de usuario y la contraseña que se especificaron en el archivo web.config tal y como aparecen en el archivo. Asegúrese de que no aparezca ningún error ni advertencia de certificado.
- Si existe un proxy inverso o un firewall en el servidor web del Portal de usuarios que está realizando la descarga de SSL, puede editar el archivo web.config del Portal de usuarios y agregar la siguiente clave en la sección <appSettings> para que el Portal de usuarios pueda usar http en lugar de https. <add key="SSL_REQUIRED" value="false"/>

#### Para instalar el Portal de usuarios

<ol>




<li>Abra el Explorador de Windows en el servidor Azure Multi-Factor Authentication y vaya a la carpeta donde esté instalado Servidor Azure Multi-Factor Authentication (por ejemplo, C:\Archivos de programa\Multi-Factor Authentication Server). Elija la versión de 32 o 64 bits del archivo de instalación MultiFactorAuthenticationUserPortalSetup según corresponda al servidor donde se instalará el Portal de usuarios. Copie el archivo de instalación en el servidor con conexión a Internet.</li>

<li>En el servidor web con conexión a Internet, se debe ejecutar el archivo de instalación con derechos de administrador. La manera más fácil de hacerlo es abrir un símbolo del sistema como administrador e ir a la ubicación donde se copió el archivo de instalación.</li>

<li>Ejecute el archivo de instalación MultiFactorAuthenticationUserPortalSetup64 y cambie el nombre de directorio virtual y sitio si lo desea.</li>

<li>Después de finalizar la instalación del Portal de usuarios, vaya a C:\inetpub\wwwroot\MultiFactorAuth (o el directorio correspondiente según el nombre del directorio virtual) y edite el archivo web.config.</li>

<li>Busque la clave USE_WEB_SERVICE_SDK y cambie el valor de false a true. Busque las claves WEB_SERVICE_SDK_AUTHENTICATION_USERNAME y WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD y establezca los valores en el nombre de usuario y la contraseña de la cuenta de servicio que pertenece al grupo de seguridad PhoneFactor Admins (consulte la sección de requisitos anterior). Asegúrese de especificar el nombre de usuario y la contraseña entre comillas al final de la línea (value=””/>). Se recomienda usar un nombre de usuario completo (por ejemplo, dominio\nombreDeUsuario o equipo\nombreDeUsuario).</li>

<li>Busque la configuración pfup_pfwssdk_PfWsSdk y cambie el valor de "http://localhost:4898/PfWsSdk.asmx" a la dirección URL del SDK del servicio web que se ejecuta en Servidor Azure Multi-Factor Authentication (por ejemplo, https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Como se usa SSL para esta conexión, debe hacer referencia el SDK del servicio web por el nombre del servidor y no por la dirección IP, ya que el certificado SSL se habrá emitido para el nombre del servidor y la dirección URL usada debe coincidir con el nombre del certificado. Si el nombre del servidor no se resuelve en una dirección IP del servidor con conexión a Internet, agregue una entrada al archivo hosts en ese servidor para asignar el nombre de Servidor Azure Multi-Factor Authentication a su dirección IP. Una vez realizados los cambios, guarde el archivo web.config.</li>

<li>Si el sitio web donde se instaló el Portal de usuarios (por ejemplo, Sitio web predeterminado) aún no está enlazado con un certificado firmado públicamente, instale el certificado en el servidor si aún no lo está, abra el Administrador de IIS y enlace el certificado al sitio web.</li>

<li>Abra un explorador web desde cualquier equipo y vaya a la dirección URL donde se instaló el Portal de usuarios (por ejemplo, https://www.publicwebsite.com/MultiFactorAuth). Asegúrese de que no aparezca ningún error ni advertencia de certificado.</li>

## Establecimiento de la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication
Ahora que el portal está instalado, debe configurar el servidor Azure Multi-Factor Authentication para que funcione con el portal.

### Para establecer la configuración del Portal de usuarios en Servidor Azure Multi-Factor Authentication




1. En Servidor Azure Multi-Factor Authentication, haga clic en el icono Portal de usuarios. En la pestaña Configuración, escriba la dirección URL del Portal de usuarios en el cuadro de texto URL del portal de usuarios. Esta dirección URL se insertará en los mensajes de correo electrónico que se envían a los usuarios cuando se importan a Servidor Azure Multi-Factor Authentication si se habilitó la funcionalidad de correo electrónico.
2. Elija la configuración que desea usar en el Portal de usuarios. Por ejemplo, si se permite que los usuarios controlen sus métodos de autenticación, asegúrese de que activar Permitir a los usuarios seleccionar el método junto con los métodos entre los que pueden elegir.
3. Haga clic en el vínculo Ayuda en la esquina superior derecha para obtener ayuda sobre cualquiera de las opciones que aparecen.

<center>![Setup](./media/multi-factor-authentication-get-started-portal/config.png)</center>

<!---HONumber=July15_HO2-->