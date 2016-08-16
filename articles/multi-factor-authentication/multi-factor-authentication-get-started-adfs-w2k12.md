<properties
	pageTitle="Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS | Microsoft Azure"
	description="En este artículo se describe cómo empezar a trabajar con Azure Multi-Factor Authentication y AD FS en Windows Server 2012 R2."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/04/2016"
	ms.author="billmath"/>


# Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS en Windows Server 2012 R2

Si su organización usa los Servicios de federación de Active Directory (AD FS) y desea proteger los recursos en la nube y los locales, puede implementar y configurar Servidor Azure Multi-Factor Authentication con AD FS. Esta configuración desencadena la autenticación multifactor para los puntos de conexión de alto valor.

En este artículo se describe el uso del Servidor Azure Multi-Factor Authentication con AD FS en Windows Server 2012 R2. Para más información, lea sobre la [protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Protección de Windows Server 2012 R2 AD FS con Servidor Azure Multi-Factor Authentication

Al instalar Servidor Azure Multi-Factor Authentication tiene las siguientes opciones:

- Instalar Servidor Azure Multi-Factor Authentication localmente en el mismo servidor que AD FS
- Instalar el adaptador de Azure Multi-Factor Authentication localmente en el servidor AD FS e instalar Servidor Multi-Factor Authentication en un equipo diferente

Antes de comenzar, tenga en cuenta lo siguiente:

- No es necesario que instale Servidor Azure Multi-Factor Authentication en el servidor AD FS. Sin embargo, debe instalar el adaptador de Multi-Factor Authentication para AD FS en un equipo con Windows Server 2012 R2 en el que se ejecuta AD FS. Puede instalar el servidor en un equipo diferente si es una versión compatible e instala el adaptador de AD FS por separado en el servidor de federación de AD FS. Consulte los procedimientos siguientes para aprender a instalar el adaptador por separado.
- Cuando se diseñó el adaptador de AD FS del Servidor Multi-Factor Authentication, se estimó que AD FS podría pasar el nombre del usuario de confianza al adaptador que podría utilizarse como nombre de aplicación. Sin embargo, no fue así. Si la organización usa métodos de autenticación de aplicación móvil o mensaje de texto, las cadenas definidas en la configuración de la compañía contienen un marcador de posición "<$*application\_name*$>". Este marcador de posición no se reemplaza automáticamente cuando se usa el adaptador de AD FS. Se recomienda quitar el marcador de posición de las cadenas adecuadas cuando proteja AD FS.

- La cuenta que use para iniciar sesión debe tener derechos de usuario para crear grupos de seguridad en el servicio de Active Directory.

- El asistente para la instalación del adaptador de AD FS de Multi-Factor Authentication crea un grupo de seguridad denominado PhoneFactor Admins en la instancia de Active Directory y, a continuación, agrega la cuenta de servicio de AD FS del servicio de federación a este grupo. Se recomienda que compruebe en el controlador de dominio que se ha creado realmente el grupo PhoneFactor Admins y que la cuenta de servicio de AD FS es miembro de este grupo. Si es necesario, agregue la cuenta de servicio de AD FS manualmente al grupo PhoneFactor Admins en el controlador de dominio.
- Para obtener información sobre cómo instalar el SDK del servicio web con el portal de usuarios, consulte [Implementación del Portal de usuarios para Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-portal.md).


### Instalación de Servidor Azure Multi-Factor Authentication localmente en el servidor AD FS

1. Descargue e instale Servidor Azure Multi-Factor Authentication en el servidor de federación de AD FS. Para obtener información sobre la instalación, consulte la [introducción a Servidor Azure Multi-Factor Authentication](multi-factor-authentication-get-started-server.md).
2. En la consola de administración de Servidor Azure Multi-Factor Authentication, haga clic en el icono de **AD FS** y elija las opciones **Permitir inscripción de usuario** y **Permitir a los usuarios seleccionar el método**.
3. Seleccione las opciones adicionales que desee especificar para su organización.
4. Haga clic en **Instalar adaptador de AD FS**.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Si el equipo está unido a un dominio y la configuración de Active Directory para proteger la comunicación entre el adaptador de AD FS y el servicio Multi-Factor Authentication está incompleta, se mostrará la ventana de **Active Directory**. Haga clic en **Siguiente** para completar esta configuración automáticamente o marque la casilla **Omitir configuración automática de Active Directory y configurar los parámetros manualmente** y, después, haga clic en **Siguiente**.
6. Si el equipo no está unido a un dominio y la configuración de grupo local para proteger la comunicación entre el adaptador de AD FS y el servicio Multi-Factor Authentication está incompleta, se mostrará la ventana de **Grupo local**. Haga clic en **Siguiente** para completar esta configuración automáticamente o marque la casilla **Omitir configuración automática de grupo local y configurar los parámetros manualmente** y, después, haga clic en **Siguiente**.
7. En el asistente para la instalación, haga clic en **Siguiente**. Servidor Azure Multi-Factor Authentication crea el grupo PhoneFactor Admins y agrega la cuenta de servicio de AD FS al grupo PhoneFactor Admins.
<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. En la página **Iniciar instalador**, haga clic en **Siguiente**.
9. En el instalador del adaptador de AD FS de Multi-Factor Authentication, haga clic en **Siguiente**.
10. Cuando se haya completado la instalación, haga clic en **Cerrar**.
11. Una vez instalado el adaptador, debe registrarlo con AD FS. Abra Windows PowerShell y ejecute el siguiente comando:<br> `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Modifique la directiva de autenticación global en AD FS para poder usar el adaptador recién registrado. En la consola de administración de AD FS, vaya al nodo **Directivas de autenticación**. En la sección **Multi-factor Authentication**, haga clic en el vínculo **Editar** situado junto a la sección **Configuración Global**. En la ventana **Editar directiva de autenticación global**, seleccione **Multi-Factor Authentication** como método de autenticación adicional y haga clic en **Aceptar**. El adaptador está registrado como WindowsAzureMultiFactorAuthentication. Debe reiniciar el servicio de AD FS para que surta efecto el registro.

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

En este punto, Servidor Multi-Factor Authentication está configurado para ser un proveedor de autenticación adicional y utilizarlo con AD FS.

## Instalación de una instancia independiente del adaptador de AD FS mediante el SDK del servicio web
1. Instale el SDK del servicio web en el servidor que ejecuta Servidor Multi-Factor Authentication.
2. Copie los siguientes archivos del directorio \\Archivos de programa\\Multi-Factor Authentication Server en el servidor en el que planea instalar el adaptador de AD FS:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.config
3. Ejecute el archivo de instalación MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. En el instalador del adaptador de AD FS de Multi-Factor Authentication, haga clic en **Siguiente** para ejecutar la instalación.
5. Cuando se haya completado la instalación, haga clic en **Cerrar**.
6. Edite el archivo MultiFactorAuthenticationAdfsAdapter.config haciendo lo siguiente:

|Paso MultiFactorAuthenticationAdfsAdapter.config| Paso secundario|
|:------------- | :------------- |
|Establezca el nodo **UseWebServiceSdk** en **true**.||
|Establezca el valor de **WebServiceSdkUrl** en la dirección URL del SDK del servicio web de Multi-Factor Authentication.||
|Configure el SDK del servicio web.<br><br>*Opción 1*: mediante un nombre de usuario y contraseña|<ol type="a"><li>Establezca el valor de **WebServiceSdkUsername** en una cuenta que sea miembro del grupo de seguridad PhoneFactor Admins. Use el formato &lt;dominio&gt;&#92;&lt;nombre de usuario&gt;.<li>Establezca el valor de **WebServiceSdkPassword** en la contraseña de la cuenta adecuada.</li></ol>
|Configure el SDK del servicio web, *continuación*<br><br>*Opción 2*: mediante un certificado de cliente|<ol type="a"><li>Obtenga un certificado de cliente de una entidad emisora de certificados para el servidor que ejecuta el SDK del servicio web. Aprenda a [obtener certificados de cliente](https://technet.microsoft.com/library/cc770328.aspx).</li><li>Importe el certificado de cliente en el almacén de certificados personales del equipo local en el servidor que ejecuta el SDK del servicio web. Nota: Asegúrese de que el certificado público de la entidad de certificación está en el almacén de los certificados raíz de confianza.</li><li>Exporte las claves pública y privada del certificado de cliente a un archivo .pfx.</li><li>Exporte la clave pública en formato Base64 a un archivo .cer.</li><li>En el Administrador de servidores, compruebe que está instalada la característica (IIS)\\Web Server\\Security\\IIS Client Certificate Mapping Authentication. Si no lo está, elija **Agregar roles y características** para agregar esta característica.</li><li>En el Administrador de IIS, haga doble clic en el **Editor de configuración** en el sitio web que contiene el directorio virtual del SDK del servicio web. Nota: Es muy importante hacerlo en el nivel de sitio web y no en el nivel de directorio virtual.</li><li>Vaya a la sección **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.</li><li>Establezca **enabled** en **true**.</li><li>Establezca **oneToOneCertificateMappingsEnabled** en **true**.</li><li>Haga clic en el botón**...** situado junto a **oneToOneMappings** y, a continuación, haga clic en el vínculo **Agregar**.</li><li>Abra el archivo .cer de Base64 exportado anteriormente. Quite *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* y cualquier salto de línea. Copie la cadena resultante.</li><li>Configure el **certificado** en la cadena copiada en el paso anterior.</li><li>Establezca **enabled** en **true**.</li><li>Establezca **userName** en una cuenta que sea miembro del grupo de seguridad PhoneFactor Admins. Utilice el formato &lt;dominio&gt;&#92;&lt;nombre de usuario&gt;.</li><li>Establezca la contraseña en la contraseña adecuada de la cuenta y cierre el Editor de configuración.</li><li>Haga clic en el vínculo **Aplicar**.</li><li>En el directorio virtual del SDK del servicio web, haga doble clic en **Autenticación**.</li><li>Compruebe que **Suplantación de ASP.NET** y **Autenticación básica** están **habilitadas** y que los restantes elementos están **deshabilitados**.</li><li>En el directorio virtual del SDK del servicio web, haga doble clic en **Configuración de SSL**.</li><li>Seleccione **Certificados de cliente** y haga clic en **Aceptar**, y, después, haga clic en **Aplicar**.</li><li>Copie el archivo .pfx exportado anteriormente en el servidor que ejecuta el adaptador de AD FS.</li><li>Importe el archivo .pfx al almacén de certificados personales del equipo local.</li><li>Haga clic con el botón derecho y seleccione **Administrar claves privadas** y, a continuación, conceda acceso de lectura a la cuenta que utilizó para iniciar sesión en el servicio de AD FS.</li><li>Abra el certificado de cliente y copie la huella digital de la pestaña **Detalles**.</li><li>En el archivo MultiFactorAuthenticationAdfsAdapter.config, establezca **WebServiceSdkCertificateThumbprint** en la cadena copiada en el paso anterior.</li></ol>
| Edite el script Register-MultiFactorAuthenticationAdfsAdapter.ps1 agregando *-ConfigurationFilePath &lt;path&gt;* al final del comando `Register-AdfsAuthenticationProvider`, donde *&lt;path&gt;* es la ruta de acceso completa al archivo MultiFactorAuthenticationAdfsAdapter.config.||

Ejecute el script \\Archivos de programa\\Multi-Factor Authentication Server\\Register-MultiFactorAuthenticationAdfsAdapter.ps1 en PowerShell para registrar el adaptador. El adaptador está registrado como WindowsAzureMultiFactorAuthentication. Debe reiniciar el servicio de AD FS para que surta efecto el registro.

<!---HONumber=AcomDC_0810_2016-->