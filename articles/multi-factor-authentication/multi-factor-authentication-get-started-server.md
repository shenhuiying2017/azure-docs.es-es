<properties 
	pageTitle="Introducción a Servidor Azure Multi-Factor Authentication" 
	description="En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Servidor Azure Multi-Factor Authentication." 
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

# Introducción a Servidor Azure Multi-Factor Authentication




<center>![Cloud](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Ahora que hemos determinado si se utiliza la autenticación multifactor local, pasamos al paso siguiente. Esta página trata de una nueva instalación del servidor y de su configuración con Active Directory local. Si ya tiene instalado el servidor Phonefactor y desea saber cómo hacerlo, consulte Actualización de Servidor Azure Multi-Factor o si desea obtener información acerca de cómo instalar solo el servicio web, consulte Implementación del servicio web para aplicaciones móviles del servidor Azure Multi-Factor Authentication.



## Descarga de Servidor Azure Multi-Factor Authentication

Hay dos maneras diferentes de descargar Servidor Azure Multi-Factor Authentication. La primera es iniciar sesión en el Portal de Azure y la segunda es directamente desde [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).


### Para descargar Servidor Azure Multi-Factor Authentication del Portal de Azure.
--------------------------------------------------------------------------------

1. Inicie sesión en el Portal de Azure como administrador.
2. En la parte izquierda, seleccione Active Directory.
3. En la página Active Directory, en la parte superior, haga clic en **Proveedores de autenticación multifactor**.
4. Haga clic en **Administrar** en la parte inferior.
5. Haga clic en **Descargas**.
6. Encima de **Generar credenciales de activación**, haga clic en **Descargar**.
7. Guarde el archivo descargado.

### Descarga de Servidor Azure Multi-Factor Authentication directamente
--------------------------------------------------------------------------------

1. Inicie sesión en [https://pfweb.phonefactor.net](https://pfweb.phonefactor.net).
2. Haga clic en **Descargas**.
<center>![Cloud](./media/multi-factor-authentication-get-started-server/download2.png)</center>
3. Encima de **Generar credenciales de activación**, haga clic en **Descargar**: deje esta página abierta por volverá a ella más adelante.
4. Guarde el archivo descargado.



## Instalación y configuración del Servidor Azure Multi-Factor Authentication
Una vez descargado el servidor, puede instalarlo y configurarlo. Asegúrese de que el servidor que va a instalar cumple los requisitos siguientes:



Requisitos del Servidor Azure Multi-Factor Authentication|Descripción|
:------------- | :------------- | 
Hardware|<li>200 MB de espacio en disco duro</li><li>procesadores compatibles x32 o x64</li><li>1 GB o más de RAM</li>
Software|<li>Windows Server 2003 o superior si el host es un sistema operativo de servidor</li><li>Windows Vista o superior si el host es un sistema operativo cliente</li><li>Microsoft .NET 2.0 Framework</li><li>ISS 6.0 o superior si instala el SDK del servicio web o del Portal de usuarios</li>

### Requisitos de firewall del Servidor Azure Multi-Factor Authentication
--------------------------------------------------------------------------------
Cada servidor MFA debe ser capaz de comunicar en el puerto 443 de salida a lo siguiente:

- https://pfd.phonefactor.net
- https://pfd2.phonefactor.net
- https://css.phonefactor.net

Si están restringidos los firewalls de salida en el puerto 443, deberán abrirse los siguientes intervalos de direcciones IP:

Subred IP|Máscara de red|Rango de direcciones IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Si no está utilizando características de confirmación de evento de Azure Multi-Factor Authentication y los usuarios no se autentican con las aplicaciones móviles de Multi-Factor Authentication desde dispositivos en la red corporativa, los rangos de direcciones IP se pueden reducir a lo siguiente:


Subred IP|Máscara de red|Rango de direcciones IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### Para instalar y configurar el Servidor Azure Multi-Factor Authentication
--------------------------------------------------------------------------------


1. Haga doble clic en el archivo ejecutable. Esto comenzará la instalación.
2. En la pantalla Seleccionar carpeta de instalación, asegúrese de que la carpeta sea correcta y haga clic en Siguiente.
3. Una vez completada la instalación, haga clic en Finalizar. Se inicia al asistente de configuración.
4. En la pantalla de bienvenida del Asistente de configuración, coloque una marca de verificación en **Omitir el uso del Asistente para configuración de autenticación** y haga clic en **Siguiente**. Se cerrará al Asistente y se iniciará el servidor.
<center>![Cloud](./media/multi-factor-authentication-get-started-server/skip2.png)</center>

5. De vuelta en la página desde la que hemos descargado el servidor, haga clic en el botón **Generar credenciales de activación**. Copie esta información en Servidor Azure Multi-Factor Authentication en los cuadros correspondientes y haga clic en **Activar**.


Los pasos anteriores muestran una instalación rápida con el Asistente para configuración. Puede volver a ejecutar al Asistente para la autenticación si se selecciona en el menú Herramientas en el servidor.



##Importación de usuarios desde Active Directory

Ahora que está instalado y configurado el servidor, puede importar rápidamente los usuarios en Servidor Azure Multi-Factor Authentication.

### Para importar usuarios desde Active Directory
--------------------------------------------------------------------------------


1. En Servidor Azure Multi-Factor Authentication, a la izquierda, seleccione **Usuarios**.
2. En la parte inferior, seleccione **Importar desde Active Directory**.
3. Ahora puede buscar usuarios individuales o buscar en el directorio de AD las unidades organizativas con usuarios en ellas. En este caso, se especificarán las unidades organizativas de los usuarios.
4. Resalte todos los usuarios de la derecha y haga clic en **Importar**. Debe aparecer una ventana emergente que le indica que la operación se realizó correctamente. Cierre la ventana de importación.

<center>![Cloud](./media/multi-factor-authentication-get-started-server/import2.png)</center>

<!---HONumber=July15_HO2-->