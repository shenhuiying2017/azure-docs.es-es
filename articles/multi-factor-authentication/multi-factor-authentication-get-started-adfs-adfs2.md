<properties 
	pageTitle="Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0" 
	description="En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS 2.0." 
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
# Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0

Si su organización está federada con Azure Active Directory y tiene recursos locales o en la nube que desea proteger, puede hacerlo mediante el Servidor Azure Multi-Factor Authentication y configurarlo para que funcione con AD FS de modo que la autenticación multifactor se desencadene para extremos de alto valor.

Esta documentación trata del uso de Servidor Azure Multi-Factor Authentication con AD FS 2.0. Para obtener información sobre el uso de Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS, consulte [Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).


## Proxy AD FS 2.0
Para proteger AD FS 2.0 con un proxy, instale Servidor Azure Multi-Factor Authentication en el servidor proxy ADFS y configure el Servidor según los siguientes pasos.

### Protección de AD FS 2.0 con un proxy
<ol>
<li>En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.</li>
<li>Haga clic en la pestaña Basados en formularios.</li>
<li>Haga clic en el botón Agregar...</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>

<li>Para detectar el nombre de usuario, la contraseña y las variables de dominio automáticamente, escriba la dirección URL de inicio de sesión (por ejemplo, https://sso.contoso.com/adfs/ls) en el cuadro de diálogo Configuración automática de sitio web basado en formularios y haga clic en Aceptar.</li>
<li>Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica.</li>
<li>Si no se pueden detectar automáticamente las variables de página, haga clic en el botón Especificar manualmente... en el cuadro de diálogo Configuración automática de sitio web basado en formularios.</li>
<li>En el cuadro de diálogo Agregar sitio web basado en formularios , escriba la dirección URL a la página de inicio de sesión ADFS en el campo URL de envío (por ejemplo, https://sso.contoso.com/adfs/ls) y escriba un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil. Consulte el archivo de ayuda para obtener más información sobre la dirección URL de envío.</li>
<li>Establezca el formato de solicitud en "POST or GET".</li>
<li>Especifique Variable de nombre de usuario (ctl00$ContentPlaceHolder1$UsernameTextBox) y Variable de contraseña (ctl00$ContentPlaceHolder1$PasswordTextBox). Si la página de inicio de sesión basada en formularios muestra un cuadro de texto de dominio, especifique también Variable de dominio. Es posible que deba ir a la página de inicio de sesión en un explorador web, hacer doble clic en la página y seleccionar "Ver código fuente " para buscar los nombres de los cuadros de entrada de la página de inicio de sesión.</li>
<li>Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>

<li>Haga clic en el botón Avanzadas... para revisar la configuración avanzada, incluida la capacidad de seleccionar un archivo de página de denegación personalizada, a fin de almacenar en caché las autenticaciones correctas en el sitio web durante un período de tiempo mediante cookies y seleccionar cómo autenticar las credenciales principales.</li>
<li>Puesto que no es probable que el servidor proxy ADFS se una al dominio, es posible utilizar LDAP para conectarse al controlador de dominio para la importación de usuario y la autenticación previa. En el cuadro de diálogo Sitio web basado en formularios avanzado, haga clic en la pestaña Autenticación principal y seleccione "Enlace LDAP" para el tipo de autenticación de autenticación previa.</li>
<li>Cuando haya terminado, haga clic en el botón Aceptar para volver al cuadro de diálogo Agregar sitio web basado en formularios. Consulte el archivo de ayuda para obtener más información sobre las configuraciones avanzadas.</li>
<li>Haga clic en el botón Aceptar para cerrar el cuadro de diálogo.</li>
<li>Una vez detectadas o introducidas las variables de página y dirección URL, los datos del sitio web se mostrarán en el panel Basados en formularios.</li>
<li>Haga clic en la pestaña Módulo nativo y seleccione el servidor, el sitio web en el que se ejecuta el proxy ADFS (por ejemplo, "Sitio web predeterminado") o la aplicación de proxy ADFS (por ejemplo, "ls" en "adfs") para habilitar el complemento de IIS en el nivel deseado.</li>
<li>Haga clic en el cuadro Habilitar autenticación IIS en la parte superior de la pantalla.</li>
<li>Ahora la autenticación de IIS está habilitada. Sin embargo, debe configurar la conexión LDAP con el controlador de dominio para poder realizar la autenticación previa a su Active Directory (AD) a través de LDAP. Para ello, haga clic en el icono Integración de directorios.</li>
<li>En la pestaña Configuración, seleccione el botón de radio Usar la configuración LDAP específica.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>

<li>Haga clic en el botón Editar…</li>
<li>En el cuadro de diálogo Editar configuración de LDAP, rellene los campos con la información necesaria para conectarse al controlador de dominio de AD. Se incluyen descripciones de los campos en la tabla siguiente. Nota: esta información también se incluye en el archivo de ayuda de Servidor Azure Multi-Factor Authentication.</li>

Campo| Descripción
:------------- | :-------------
Server|Escriba el nombre de host o dirección IP del servidor que ejecuta el directorio LDAP. También puede especificar un servidor de copia de seguridad separado por un punto y coma.<br> **Nota:** cuando el tipo de enlace es SSL, se requiere generalmente un nombre de host que debe coincidir con el nombre del certificado SSL instalado en el servidor del directorio LDAP. 
DN base|Escriba el nombre distintivo del objeto de directorio base desde el que se iniciarán todas las consultas de directorio. Por ejemplo, dc=contoso,dc=com.
Tipo de enlace|Seleccione el tipo de enlace apropiado al enlazar para buscar en el directorio LDAP. Se utiliza para importaciones, sincronización y resolución de nombres de usuario.<ul><li>**Anónimo**: se realizará un enlace anónimo. No se utilizarán DN de enlace ni Contraseña de enlace. Esto solo funcionará si el directorio LDAP permite el enlace anónimo y los permisos permiten la consulta de los registros y atributos correspondientes.</li><li>**Simple**: DN de enlace y Contraseña de enlace se pasarán como texto sin formato para enlazar con el directorio LDAP. Esto debería utilizarse solo a modo de prueba para verificar que se puede tener acceso al servidor y que la cuenta de enlace tiene el acceso adecuado. En su lugar, se recomienda usar SSL una vez instalado el certificado adecuado.</li><li>**SSL**: DN de enlace y Contraseña de enlace se cifrarán mediante SSL para enlazar con el directorio LDAP. Esto requiere que esté instalado un certificado en el servidor del directorio LDAP en el que confíe Servidor Azure Multi-Factor Authentication.</li><li>**Windows**: Nombre de usuario de enlace y Contraseña de enlace se utilizarán para conectarse de forma segura a un controlador de dominio de Active Directory o a un directorio ADAM. Si se deja en blanco Usuario de enlace, se utilizará la cuenta de usuario utilizará para enlazar.</li></ul> 
Nombre de usuario de enlace|Escriba el nombre distintivo del registro de usuario para la cuenta que se va a utilizar al enlazar con el directorio LDAP. El nombre distintivo del enlace solo se utiliza cuando el tipo de enlace es Simple o SSL.    
Contraseña de enlace|Escriba la contraseña de enlace para DN de enlace o el nombre de usuario que se usa para enlazar con el directorio LDAP. Para configurar la contraseña para el servicio AdSync del Servidor Multi-Factor Auth, debe estar habilitada la sincronización y el servicio debe estar ejecutándose en el equipo local. La contraseña se guardará en usuarios y contraseñas almacenados en Windows en la cuenta en la que se está ejecutando el servicio AdSync de Servidor Azure Multi-factor Authentication. La contraseña también se guardará en la cuenta en la que se ejecuta la interfaz de usuario del Servidor Multi-Factor Auth y en la cuenta en la que se ejecuta el servicio del Servidor Azure Multi-factor Authentication. Nota: puesto que la contraseña se almacena solo en usuarios y contraseñas almacenados en Windows del servidor local, este paso deberá realizarse en cada Servidor Multi-Factor Auth que necesite tener acceso a la contraseña. 
Límite de tamaño de consulta|Especifique el límite de tamaño para el número máximo de usuarios que devolverá una búsqueda de directorio. Este límite debe coincidir con la configuración en el directorio LDAP. Para búsquedas grandes en las que no se admite paginación, la importación y sincronización intentarán recuperar usuarios por lotes. Si el límite de tamaño especificado aquí es mayor que el límite configurado en el directorio LDAP, pueden faltar algunos usuarios. 



<li>Pruebe la conexión LDAP haciendo clic en el botón Probar.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
<li>Si la prueba de conexión LDAP se realizó correctamente, haga clic en el botón Aceptar.</li>
<li>A continuación, haga clic en el icono Configuración de la empresa y seleccione la pestaña Resolución de nombre de usuario.</li>
<li>Seleccione el botón de radio Usar el atributo del identificador único LDAP para los nombres de usuario coincidentes.</li>
<li>Si los usuarios escriben su nombre de usuario en el formulario de inicio de sesión del proxy ADFS en formato "dominio\nombreDeUsuario", el Servidor debe ser capaz de eliminar el dominio del nombre de usuario cuando crea la consulta LDAP. Esto puede hacerse mediante un valor del Registro.</li>
<li>Abra el editor del Registro y vaya a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor en un servidor de 64 bits. Si está en un servidor de 32 bits, saque "Wow6432Node" fuera de la ruta de acceso. Cree una nueva clave de Registro DWORD denominada "UsernameCxz_stripPrefixDomain" y establezca el valor en 1. Azure Multi-Factor Authentication protege ahora el proxy ADFS. Asegúrese de que se han importado los usuarios de Active Directory al Servidor. Consulte la siguiente sección IP de confianza si desea agregar a la lista blanca direcciones IP internas, con lo que ya no sería necesaria la autenticación de dos factores al iniciar sesión en el sitio web desde esas ubicaciones.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 Direct sin proxy

Para proteger AD FS cuando no se usa el proxy AD FS, instale Servidor Azure Multi-Factor Authentication en el servidor AD FS y configure el Servidor según los siguientes pasos.

### Protección de AD FS 2.0 sin proxy
<ol>
<li>En Servidor Azure Multi-Factor Authentication, haga clic en el icono Autenticación de IIS en el menú izquierdo.</li>
<li>Haga clic en la pestaña HTTP.</li>
<li>Haga clic en el botón Agregar...</li>
<li>En el cuadro de diálogo Agregar URL base, escriba la dirección URL para el sitio web ADFS donde se ejecuta la autenticación HTTP (por ejemplo, https://sso.domain.com/adfs/ls/auth/integrated) en el campo URL base y escriba un nombre de aplicación (opcional). El nombre de la aplicación aparece en los informes de Azure Multi-Factor Authentication y puede mostrarse en los mensajes de autenticación SMS o de aplicación móvil.</li>
<li>Si lo desea, ajuste los tiempos Tiempo de espera de inactividad y Sesión máxima.</li>
<li>Active la casilla Requerir coincidencia de usuario de Azure Multi-Factor Authentication si todos los usuarios se importaron o importarán al Servidor y están sujetos a la autenticación multifactor. Si aún no se importó al Servidor un número significativo de usuarios o se van a excluir de la autenticación multifactor, deje la casilla desactivada. Consulte el archivo de ayuda para obtener información adicional acerca de esta característica.</li>
<li>Active la casilla de la caché de cookies si lo desea.</li>

<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>

<li>Haga clic en el botón Aceptar.</li>
<li>Haga clic en la pestaña Módulo nativo y seleccione el servidor, el sitio web en el que se ejecuta ADFS (por ejemplo, "Sitio web predeterminado") o la aplicación ADFS (por ejemplo, "ls" en "adfs") para habilitar el complemento de IIS en el nivel deseado.</li>
<li>Haga clic en el cuadro Habilitar autenticación IIS en la parte superior de la pantalla. Azure Multi-Factor Authentication protege ahora ADFS. Asegúrese de que se han importado los usuarios de Active Directory al Servidor. Consulte la siguiente sección IP de confianza si desea agregar a la lista blanca direcciones IP internas, con lo que ya no sería necesaria la autenticación de dos factores al iniciar sesión en el sitio web desde esas ubicaciones.</li>

## IP de confianza
Las IP de confianza permiten a los usuarios omitir Azure Multi-Factor Authentication para las solicitudes de sitio web procedentes de determinadas direcciones IP o subredes. Por ejemplo, puede que desee excluir a determinados usuarios de Azure Multi-Factor Authentication mientras inicia sesión desde la oficina. Para ello, debe especificar la subred de la oficina como entrada de IP de confianza.

### Configuración de IP de confianza

<ol>
<li>En la sección Autenticación de IIS, haga clic en la pestaña IP de confianza.</li>
<li>Haga clic en el botón Agregar...</li>
<li>Cuando aparezca el cuadro de diálogo Agregar IP de confianza, seleccione el botón de radio IP única, Intervalo IP o Subred.</li>
<li>Escriba la dirección IP, el intervalo IP o la subred que debe estar en la lista blanca. Si introduce una subred, seleccione la máscara de red adecuada y haga clic en el botón Aceptar. Se ha agregado a la lista blanca.</li>


<center>![Setup](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>

<!----HONumber=July15_HO2-->