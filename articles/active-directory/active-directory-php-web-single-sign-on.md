<properties 
	pageTitle="Inicio de sesión único con Azure Active Directory (PHP)" 
	description="Aprenda a crear una aplicación web PHP que use el inicio de sesión único con Azure Active Directory." 
	services="active-directory" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="07/17/2015" 
	ms.author="tomfitz"/>

# Inicio de sesión web único con PHP y Azure Active Directory

##<a name="introduction"></a>Introducción

Este tutorial mostrará a los desarrolladores de PHP cómo utilizar Azure Active Directory para permitir el inicio de sesión único para usuarios de clientes de Office 365. Aprenderá a:

* Aprovisionar la aplicación web en el inquilino de un cliente
* Proteger la aplicación con WS-Federation

###Requisitos previos
Para este tutorial se necesitan los siguientes requisitos previos para el entorno de desarrollo:

* [Código de ejemplo PHP para Azure Active Directory]
* [Eclipse PDT 3.0.x All In Ones]
* PHP 5.3.1 (a través del instalador de plataforma web)
* Internet Information Services (IIS) 7.5 con SSL habilitado
* Windows PowerShell
* [Commandlets de Office 365 PowerShell]

## Paso 1: crear una aplicación PHP
Este paso describe cómo crear una aplicación PHP simple que representará un recurso protegido. El acceso a este recurso se otorgará a través de la autenticación federada administrada por el STS de la empresa, que se describe más adelante en este tutorial.

1. Abra una instancia nueva de Eclipse.
2. En el menú **File** (Archivo), haga clic en **New** (Nuevo) y, a continuación, en **New PHP Project** (Nuevo proyecto PHP). 
3. En el cuadro de diálogo **New PHP Project** (Nuevo proyecto PHP), asigne al proyecto el nombre *phpSample* y, a continuación, haga clic en **Finish** (Finalizar).
4. En el menú **PHP Explorer** (Explorador PHP) que aparece a la izquierda, haga clic con el botón derecho en *phpProject*, haga clic en **New** (Nuevo) y, a continuación, en **PHP File** (Archivo PHP).
5. En el cuadro de diálogo **New PHP File** (Nuevo archivo PHP), asigne al archivo el nombre **index.php** y, a continuación, haga clic en **Finish** (Finalizar).
6. Reemplace el marcado generado por lo siguiente y, a continuación, guarde el archivo **index.php**:

		<!DOCTYPE>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index</title>
		</head>
		<body>
			##Index Page
		</body>
		</html> 

7. Para abrir el **Administrador de Internet Information Services (IIS)**, escriba *inetmgr* en el símbolo del sistema para ejecutar y haga clic en Entrar.

8. En el Administrador de IIS, expanda la carpeta **Sitios** en el menú de la izquierda, haga clic con el botón derecho en **Sitio web predeterminado** y, a continuación, haga clic en **Agregar aplicación....**.

9. En el cuadro de diálogo **Agregar aplicación**, defina el valor **Alias** en *phpSample* y la **Ruta de acceso física** en la ruta de acceso del archivo donde creó el proyecto PHP.

10. En Eclipse, en el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar).

11. En el menú **Run PHP Web Application** (Ejecutar aplicación web PHP), haga clic en **OK** (Aceptar).

12. Se abrirá la página **index.php** en una pestaña nueva en Eclipse. La página debe mostrar simplemente el texto: *Index Page* (Página de índice).

## Paso 2: aprovisionar la aplicación en el inquilino de Active Directory de la empresa
Este paso describe cómo un administrador de un cliente de Azure Active Directory aprovisiona la aplicación PHP en su inquilino y configura el inicio de sesión único. Una vez que se completa este paso, los empleados de la empresa se pueden autenticar en la aplicación web mediante sus cuentas de Office 365.

El proceso de aprovisionamiento comienza con la creación de una nueva entidad de servicio para la aplicación. Azure Active Directory utiliza las entidades de servicio para registrar y autenticar las aplicaciones en el directorio.

1. Si todavía no lo ha hecho, descargue e instale los commandlets de PowerShell para Office 365.
2. Desde el menú **Inicio**, ejecute el **módulo Azure Active Directory para la consola Windows PowerShell**. Esta consola proporciona un entorno de línea de comandos para configurar atributos acerca de su inquilino de Office 365, tales como la creación y la modificación de entidades de servicio.
3. Para importar el módulo **MSOnlineExtended** necesario, escriba el siguiente comando y presione Entrar:

		Import-Module MSOnlineExtended -Force
4. Para conectarse a su directorio de Office 365, necesitará proporcionar las credenciales de administrador de la empresa. Escriba el siguiente comando y presione Entrar y, a continuación, escriba las credenciales en el símbolo del sistema:

		Connect-MsolService
5. Ahora creará una nueva entidad de servicio para la aplicación. Escriba el siguiente comando y presione Entrar:

		New-MsolServicePrincipal -ServicePrincipalNames @("phpSample/localhost") -DisplayName "Federation Sample Website" -Type Symmetric -Usage Verify -StartDate "12/01/2012" -EndDate "12/01/2013" 
Este paso dará como resultado información similar a la siguiente:

		The following symmetric key was created as one was not supplied qY+Drf20Zz+A4t2w e3PebCopoCugO76My+JMVsqNBFc=
		DisplayName           : Federation Sample PHP Website
		ServicePrincipalNames : {phpSample/localhost}
		ObjectId              : 59cab09a-3f5d-4e86-999c-2e69f682d90d
		AppPrincipalId        : 7829c758-2bef-43df-a685-717089474505
		TrustedForDelegation  : False
		AccountEnabled        : True
		KeyType               : Symmetric
		KeyId                 : f1735cbe-aa46-421b-8a1c-03b8f9bb3565
		StartDate             : 12/01/2012 08:00:00 a.m.
		EndDate               : 12/01/2013 08:00:00 a.m.
		Usage                 : Verify 
> [AZURE.NOTE]debe guardar esta salida, en especial la clave simétrica generada. La clave solo se revelará durante la creación de la entidad de servicio y no podrá recuperarla en el futuro. Los otros valores son necesarios para utilizar la API Graph para leer y escribir información en el directorio.

6. El paso final define la URL de respuesta para su aplicación. La URL de respuesta es donde se envían las respuestas después de los intentos de autenticación. Escriba los siguientes comandos y presione Entrar:

		$replyUrl = New-MsolServicePrincipalAddresses –Address "https://localhost/phpSample" 

		Set-MsolServicePrincipal –AppPrincipalId "7829c758-2bef-43df-a685-717089474505" –Addresses $replyUrl 
	
La aplicación web ahora se aprovisionó en el directorio y los empleados de la empresa la pueden utilizar para el inicio de sesión web único.

## Paso 3: proteger la aplicación con WS-Federation para el inicio de sesión de empleado
Este paso le mostrará cómo agregar compatibilidad para el inicio de sesión federado con Windows Identity Foundation (WIF) y las bibliotecas simpleSAML.php que descargó con el código de ejemplo en los requisitos previos. También agregará una página de inicio de sesión y configurará la confianza entre la aplicación y el inquilino de directorio.

1. En Eclipse, haga clic con el botón derecho en el proyecto **phpSample** y luego haga clic en **New** (Nuevo) y, a continuación, en **File** (Archivo). 

2. En el cuadro de diálogo **New File** (Nuevo archivo), asigne al archivo el nombre **federation.ini** y, a continuación, haga clic en **Finish** (Finalizar).

3. En el archivo **federation.ini**, escriba la siguiente información y suministre los valores con la información que guardó en el paso 2 cuando creó la entidad de servicio:

		federation.trustedissuers.issuer=https://accounts.accesscontrol.windows.net/v2/wsfederation
		federation.trustedissuers.thumbprint=qY+Drf20Zz+A4t2we3PebCopoCugO76My+JMVsqNBFc=
		federation.trustedissuers.friendlyname=Fabrikam
		federation.audienceuris=spn:7829c758-2bef-43df-a685-717089474505
		federation.realm=spn:7829c758-2bef-43df-a685-717089474505
		federation.reply=https://localhost/phpSample/index.php 


	> [AZURE.NOTE]Los valores **audienceuris** y **realm** deben ir precedidos por "spn:".

4. En Eclipse, haga clic con el botón derecho en el proyecto **phpSample** y luego haga clic en **New** (Nuevo) y, a continuación, en **PHP File** (Archivo PHP).

5. En el cuadro de diálogo **New PHP File** (Nuevo archivo PHP), asigne al archivo el nombre **secureResource.php** y, a continuación, haga clic en **Finish** (Finalizar).

6. En el nuevo archivo **secureResource.php**, escriba el siguiente código y reemplace la ruta de acceso **c:\\phpLibraries** por la ubicación raíz donde descargó el código de ejemplo. La ubicación raíz debe incluir el archivo **simpleSAML.php** y la carpeta **federation**:

		<?php
		ini_set('include_path', ini_get('include_path').';c:\phpLibraries\;');
		require_once ('federation/FederatedLoginManager.php');
		session_start();
		$token = $_POST['wresult'];
		$loginManager = new FederatedLoginManager();
		if (!$loginManager->isAuthenticated()) {
			if (isset ($token)) {
				try {
					$loginManager->authenticate($token);
				} catch (Exception $e) {
					print_r($e->getMessage());
				}  
			} else {
				$returnUrl = "https://" . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'];
				header('Pragma: no-cache');
				header('Cache-Control: no-cache, must-revalidate');
				header("Location: " . FederatedLoginManager :: getFederatedLoginUrl($returnUrl), true, 302);
				exit();
			}
		}
		?> 

7. Abra la página **index.php**, actualice su contenido para protegerla y, a continuación, guárdela:

		<?php
		require_once (dirname(__FILE__) . '/secureResource.php');
		?>
		<!DOCTYPE html>
		<html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
			<title>Index Page</title>
		</head>
		<body>
			<h2>Index Page</h2>
			<h3>Welcome <strong><?php print_r($loginManager->getPrincipal()->getName()); ?></strong>!</h3>
			<h4>Claim list:</h4>
			<ul>
				<?php
				foreach ($loginManager->getClaims() as $claim) {
					print_r('<li>' . $claim->toString() . '</li>');
				}
				?>  
			</ul>
		</body>
		</html> 

8. En el menú **Run** (Ejecutar), haga clic en **Run** (Ejecutar). Se le redirigirá automáticamente a la página del proveedor de identidades de Office 365, donde puede iniciar sesión con sus credenciales de inquilino de directorio. Por ejemplo, **john.doe@fabrikam.onmicrosoft.com*.

## Resumen
Este tutorial le ha mostrado cómo crear y configurar una aplicación PHP de un solo inquilino que utiliza las capacidades de inicio de sesión único de Azure Active Directory.

Puede encontrar un ejemplo que muestra cómo usar Azure Active Directory y el inicio de sesión único para sitios web PHP en <https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP>.

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).

[Step 1: Create a PHP Application]: #createapp
[Step 2: Provision the Application in a Company's Directory Tenant]: #provisionapp
[Step 3: Protect the Application Using WS-Federation for Employee Sign In]: #protectapp
[Summary]: #summary
[Introduction]: #introduction
[Developing Multi-Tenant Cloud Applications with Azure Active Directory]: http://g.microsoftonline.com/0AX00en/121
[Windows Identity Foundation 3.5 SDK]: http://www.microsoft.com/download/details.aspx?id=4451
[Windows Identity Foundation 1.0 Runtime]: http://www.microsoft.com/download/details.aspx?id=17331
[Commandlets de Office 365 PowerShell]: http://msdn.microsoft.com/library/azure/jj151815.aspx
[ASP.NET MVC 3]: http://www.microsoft.com/download/details.aspx?id=4211
[Eclipse PDT 3.0.x All In Ones]: http://www.eclipse.org/pdt/downloads/
[Código de ejemplo PHP para Azure Active Directory]: https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/WAAD.WebSSO.PHP
 

<!---HONumber=Oct15_HO1-->