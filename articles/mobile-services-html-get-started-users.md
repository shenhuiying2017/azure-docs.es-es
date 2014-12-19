<properties urlDisplayName="Get Started with Authentication (HTML5)" pageTitle="Introducción a la autenticación (HTML 5) | Centro de desarrollo móvil" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your HTML app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with authentication in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Incorporación de autenticación a la aplicación de Servicios móviles 

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tema muestra cómo autenticar usuarios en servicios móviles de Azure desde su aplicación HTML o PhoneGap.   En este tutorial, podrá agregar la autenticación al proyecto de inicio rápido mediante un proveedor de identidad admitido por servicios móviles. Una vez que se haya realizado la autenticación y autorización correctamente en los Servicios móviles, se mostrará el valor de identificador de usuario.  

En este tutorial se realiza un recorrido por los pasos básicos para habilitar la autenticación en su aplicación:

1. [Registro de la aplicación para la autenticación y configuración de Servicios móviles]
2. [Restricción de los permisos de tabla a usuarios autenticados]
3. [Incorporación de autenticación a la aplicación]

Este tutorial está basado en el inicio rápido de Servicios móviles. Primero debe completar el tutorial [Introducción a Servicios móviles]. 

##<a name="register"></a>Registro de la aplicación para la autenticación y configuración de Servicios móviles

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>Restricción de los permisos para los usuarios autenticados

[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. En el directorio de aplicaciones, inicie uno de los archivos de comandos siguientes desde la subcarpeta **server**.

	+ **launch-windows** (equipos con Windows) 
	+ **launch-mac.command** (equipos con Mac OS X)
	+ **launch-linux.sh** (equipos con Linux)

	>[WACOM.NOTE]En un equipo con Windows, escriba `R` cuando PowerShell le pida que confirme que desea ejecutar el script. Su explorador web podría advertirle de no ejecutar el script porque se ha descargado de Internet. Cuando esto ocurra, debe solicitar que el explorador continúe con la carga del script.

	De este modo se inicia un servidor web en su equipo local para hospedar la nueva aplicación.

2. Abra la dirección URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> en un explorador web para iniciar la aplicación. 

	No se pueden cargar los datos. Esto se produce porque la aplicación intenta obtener acceso a Servicios móviles como usuario sin autenticar, pero la tabla _TodoItem_ requiere ahora autenticación.

3. (Opcional) Abra el depurador de scripts para su explorador web y vuelva a cargar la página. Compruebe que se produce un error de acceso denegado. 

A continuación, actualizará la aplicación para permitir la autenticación antes de solicitar recursos del servicio móvil.

##<a name="add-authentication"></a>Incorporación de autenticación a la aplicación

>[WACOM.NOTE]Como el inicio de sesión se realiza en una ventana emergente, debería invocar el método <strong>login</strong> desde el evento Click de un botón. De lo contrario, muchos exploradores suprimirían la ventana de inicio de sesión.

1. Abra el archivo del proyecto index.html, localice el elemento H1 y agregue el siguiente fragmento de código debajo de él:

	    <div id="logged-in">
            Ha iniciado sesión como <span id="login-name"></span>.
            <button id="log-out">Log out</button>
        </div>
        <div id="logged-out">
            No ha iniciado sesión.
            <button>Iniciar sesión</button>
        </div>

	Esto le permite iniciar sesión en los Servicios móviles desde la página.

2. En el archivo app.js, localice la línea de código situada al final del archivo que llama a la función refreshTodoItems y reemplácela por el código siguiente: 
	
		function refreshAuthDisplay() {
			var isLoggedIn = client.currentUser !== null;
			$("#logged-in").toggle(isLoggedIn);
			$("#logged-out").toggle(!isLoggedIn);

			if (isLoggedIn) {
				$("#login-name").text(client.currentUser.userId);
				refreshTodoItems();
			}
		}

		function logIn() {
			client.login("facebook").then(refreshAuthDisplay, function(error){
				alert(error);
			});
		}

		function logOut() {
			client.logout();
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');
		}

		// On page init, fetch the data and set up event handlers
		$(function () {
			refreshAuthDisplay();
			$('#summary').html('<strong>You must login to access data.</strong>');		    
			$("#logged-out button").click(logIn);
			$("#logged-in button").click(logOut);
		});

    De este modo se crea un conjunto de funciones para administrar el proceso de autenticación. El usuario se autentica mediante el inicio de sesión en Facebook. Si usa un proveedor de identidades que no sea Facebook, cambie el valor que ha pasado al método <strong>login</strong> anterior por uno de los siguientes: <em>microsoftaccount</em>, <em>facebook</em>, <em>twitter</em>, <em>google</em> o <em>aad</em>.

	>[WACOM.NOTE]En una aplicación de PhoneGap, también debe agregar los siguientes complementos al proyecto:
	><ul><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git</code></li>
	><li><code>phonegap plugin add https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git</code></li></ul>

9. Vuelva al explorador en el que se ejecuta su aplicación y actualice la página. 

	   Cuando haya iniciado sesión correctamente, la aplicación debe ejecutarse sin errores y debe poder consultar a Servicios móviles y realizar actualizaciones de datos.

	>[WACOM.NOTE]Si usa Internet Explorer, podría recibir el error siguiente después de iniciar sesión: <code>Cannot reach window opener. It may be on a different Internet Explorer zone</code>. Esto sucede porque la ventana emergente se ejecuta en una zona de seguridad diferente (Internet) del localhost (intranet). Esto solo afecta a las aplicaciones durante el desarrollo que usan localhost. También puede abrir la pestaña <strong>Seguridad</strong> de <strong>Opciones de Internet</strong>, hacer clic en <strong>Intranet local</strong>, después en <strong>Sitios</strong> y deshabilitar <strong>Detectar redes intranet automáticamente</strong>. Acuérdese de volver a cambiar esta configuración cuando haya terminado la prueba.

## <a name="next-steps"> </a>Pasos siguientes

En el siguiente tutorial, [Autorización de usuarios con scripts], usará el valor de identificador de usuario proporcionado por Servicios móviles basado en un usuario autenticado para filtrar los datos que devuelve Servicios móviles. Encontrará más información acerca de cómo usar los Servicios móviles con HTML/JavaScript en [Referencia conceptual de Servicios móviles HTML/JavaScript]

<!-- Anchors. -->
[Registro de la aplicación para la autenticación y configuración de Servicios móviles]: #register
[Restricción de los permisos de tabla a usuarios autenticados]: #permissions
[Incorporación de autenticación a la aplicación]: #add-authentication
[Pasos siguientes]:#next-steps

<!-- Images. -->

[4]: ./media/mobile-services-html-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-html-get-started-users/mobile-service-uri.png
[13]: ./media/mobile-services-html-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-html-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-html-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Introducción a Servicios móviles]: /es-es/documentation/articles/mobile-services-html-get-started
[Introducción a los datos]: /es-es/documentation/articles/mobile-services-html-get-started-data
[Autorización de usuarios con scripts]: /es-es/documentation/articles/mobile-services-html-authorize-users-in-scripts

[Portal de administración de Azure]: https://manage.windowsazure.com/
[Documentación de referencia conceptual de Servicios móviles con HTML/JavaScript]: /es-es/documentation/articles/mobile-services-html-how-to-use-client-library
