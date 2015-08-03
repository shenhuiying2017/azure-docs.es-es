<properties 
	pageTitle="Introducción a Azure AD Connect" 
	description="Obtenga información acerca de cómo descargar, instalar y ejecutar el asistente para instalación de Azure AD Connect." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="billmath"/>

# Introducción a Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="¿Qué es?">¿Qué es?</a> <a href="../active-directory-aadconnect-how-it-works/" title="¿Cómo funciona?">¿Cómo funciona?</a> <a href="../active-directory-aadconnect-get-started/" title="Introducción" class="current">Introducción</a> <a href="../active-directory-aadconnect-whats-next/" title="¿Qué sigue?">¿Qué sigue?</a> <a href="../active-directory-aadconnect-learn-more/" title="Más información">Más información</a>
</div>


La siguiente documentación le ayudará a empezar a trabajar con Azure Active Directory Connect. Esta documentación trata del uso de la instalación rápida para Azure AD Connect. Para obtener información sobre una instalación personalizada, consulte [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md). Para obtener información sobre la actualización de DirSync a Azure AD Connect, consulte [Actualizar DirSync a Azure Active Directory Connect.](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## Descarga de Azure AD Connect



Para empezar a utilizar Azure AD Connect puede descargar la versión más reciente mediante [Descarga de Azure AD Connect](http://go.microsoft.com/fwlink/?LinkID=615771).

## Antes de instalar Azure AD Connect
Antes de instalar Azure AD Connect con Configuración rápida, hay algunas cosas que necesitará.


 
- Una suscripción de Azure o una [suscripción de prueba de Azure](http://azure.microsoft.com/pricing/free-trial/): solo es necesario para el acceso al Portal de Azure, no para usar Azure AD Connect. Si usa PowerShell u Office 365 no es necesario utilizar una suscripción de Azure para usar Azure AD Connect.
- Una cuenta de administrador global de Azure AD para el inquilino de Azure AD con el que desea realizar la integración
- Un controlador de dominio de AD o un servidor miembro con Windows Server 2008 o posterior
- Una cuenta de administrador de empresa para su Active Directory local
- Opcional: una cuenta de usuario de prueba para comprobar la sincronización. 

### Requisitos de hardware de Azure AD Connect
La tabla siguiente muestra los requisitos mínimos de un equipo con Azure AD Connect.

| Cantidad de objetos en Active Directory | CPU | Memoria | Tamaño de disco duro |
| ------------------------------------- | --- | ------ | --------------- |
| Menos de 10.000 | 1,6 GHz | 4 GB | 70 GB |
| 10.000–50.000 | 1,6 GHz | 4 GB | 70 GB |
| 50.000–100.000 | 1,6 GHz | 16 GB | 100 GB* |
| Para 100.000 o más objetos, se requiere la versión completa de SQL Server| | | |
| 100.000–300.000 | 1,6 GHz | 32 GB | < 300 GB |
| 300.000–600.000 | 1,6 GHz | 32 GB | 450 GB |
| Más de 600.000 | 1,6 GHz | 32 GB | 500 GB |




Para ver opciones personalizadas como varios bosques o un inicio de sesión federado, obtenga información sobre los requisitos adicionales [aquí.](active-directory-aadconnect-get-started-custom.md)


## Instalación rápida de Azure AD Connect
Configuración rápida es la opción predeterminada que se selecciona y es uno de los escenarios más comunes. Al hacer esto, Azure AD Connect implementa la sincronización con la opción de sincronización de hash de contraseña. Esto es para un solo bosque y permite que los usuarios utilicen su contraseña local para iniciar sesión en la nube. La Configuración rápida activa automáticamente una sincronización una vez completada la instalación (puede elegir no hacerlo). Con esta opción, ampliará su directorio local a la nube con tan solo unos pocos clics.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/welcome.png)</center>

### Para instalar Azure AD Connect mediante la configuración rápida
--------------------------------------------------------------------------------------------

1. Inicie sesión en el servidor en el que desea para instalar Azure AD Connect como administrador de organización. Debe ser el servidor que quiere que sea el servidor de sincronización.
2. Navegue hasta el archivo AzureADConnect.msi y haga doble clic en él.
3. En la pantalla de bienvenida, active la casilla que acepta los términos de licencia y haga clic en **Continuar**.
4. En la pantalla Configuración rápida, haga clic en **Usar configuración rápida**.
<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/express.png)</center>
6. En la pantalla Conectar a Azure AD, escriba el nombre de usuario y la contraseña de un administrador global de Azure para su Azure AD. Haga clic en **Siguiente**.
8. En la pantalla Conectar con AD DS, escriba el nombre de usuario y la contraseña para una cuenta de administrador de empresa. Haga clic en **Siguiente**.
<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/install4.png)</center>
9. En la pantalla Listo para configurar, haga clic en **Instalar**.
	- En la página Listo para configurar puede desactivar la casilla “**Inicie el proceso de sincronización en cuanto se complete la configuración inicial**”. Si lo hace, el asistente configurará la sincronización, pero dejará la tarea deshabilitada por lo que no se ejecutará hasta que la habilite manualmente en el Programador de tareas. Una vez habilitada la tarea, la sincronización se ejecutará cada tres horas.
	- Opcionalmente, también puede configurar los servicios de sincronización para **Implementación híbrida de Exchange**; para ello, active la casilla correspondiente. Si no planea tener buzones de Exchange locales y en la nube, no es necesario hacerlo.

<center>![Welcome to Azure AD Connect](./media/active-directory-aadconnect-get-started/readyinstall.png)</center>
8. Una vez completada la instalación, haga clic en **Salir**.


<br> <br>

Para ver un vídeo sobre el uso de la instalación exprés, consulte lo siguiente:

<center>[AZURE.VIDEO azure-active-directory-connect-express-settings]</center>



## Comprobación de la instalación

Después de haber instalado correctamente Azure AD Connect, para comprobar que la sincronización se produce, inicie sesión en el Portal de Azure y compruebe la última hora de sincronización.

1.  Inicie sesión en el Portal de Azure.
2.  En la parte izquierda, seleccione Active Directory.
3.  Haga doble clic en el directorio que ha utilizado para instalar Azure AD Connect.
4.  En la parte superior, seleccione Integración de directorios. Anote la hora de la última sincronización.

<center>![Express Installation](./media/active-directory-aadconnect-get-started/verify.png)</center>

## ¿Qué debe hacer a continuación?
Ahora que ha instalado Azure AD Connect puede usar el vínculo [aquí](active-directory-aadconnect-whats-next.md) para empezar a utilizar tareas posteriores a la instalación, como asignar licencias de Azure AD Premium o Enterprise Mobility a los usuarios o configurar opciones adicionales.

**Recursos adicionales**

[Comparación de las herramientas para la integración de directorios](active-directory-aadconnect-get-started-tools-comparison.md)

 

<!---HONumber=July15_HO4-->