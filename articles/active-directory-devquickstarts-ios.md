<properties
	pageTitle="Introducción a iOS de Azure AD | Microsoft Azure"
	description="Cómo crear una aplicación iOS que se integra con Azure AD para el inicio de sesión y llama a las API protegidas de Azure AD mediante OAuth."
	services="active-directory"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Integrar Azure AD en una aplicación iOS

[AZURE.INCLUDE [active-directory-devguide](../includes/active-directory-devguide.md)]

Si está desarrollando una aplicación de escritorio, Azure AD le facilita la autenticación de sus usuarios con sus cuentas de Active Directory. También permite a la aplicación consumir con seguridad cualquier API web protegida por Azure AD, como las API de Office 365 o la API de Azure.

Para los clientes de iOS que necesitan tener acceso a recursos protegidos, Azure AD proporciona la biblioteca de autenticación de Active Directory o ADAL. El único propósito en la vida de ADAL es facilitar a su aplicación la obtención de tokens de acceso. Para demostrar lo fácil que es, crearemos aquí una aplicación de lista de tareas pendientes de Objective-C que:

-	Obtenga tokens de acceso para llamar a la API de gráficos de Azure AD utilizando el [protocolo de autenticación OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx).
-	Busque un directorio para los usuarios con un alias determinado.
-	Cierre la sesión de los usuarios.

Para crear la aplicación de trabajo completa, deberá:

2. Registrar la aplicación con Azure AD.
3. Instalar y configurar ADAL.
5. Usar ADAL para obtener tokens de Azure AD.

Para empezar, [descargue el esquema de la aplicación](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip) o [descargue el ejemplo finalizado](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip). También necesitará a un inquilino de Azure AD en el que pueda crear usuarios y registrar una aplicación. Si aún no tiene un inquilino, [descubra cómo conseguir uno](active-directory-howto-tenant.md).

## Paso 1: descargue y ejecute el servidor de ejemplo TODO de la API de REST de .Net o Node.js

Este ejemplo está escrito específicamente para que funcione con nuestro ejemplo existente para crear una API de REST ToDo de un solo inquilino para Microsoft Azure Active Directory. Se trata de un requisito previo para el Inicio rápido.

Para obtener información acerca de cómo configurar esta opción, visite nuestro ejemplo existente aquí:

* [Servicio API REST de ejemplo de Microsoft Azure Active Directory para Node.js](active-directory-devquickstarts-webapi-nodejs.md)

## Paso 2: registre la API web con su inquilino de Microsoft Azure AD

**¿Qué estoy haciendo?**

*Microsoft Active Directory permite agregar dos tipos de aplicaciones. Las API web que ofrecen servicios a usuarios y aplicaciones (ya sea en la Web o una aplicación que se ejecuta en un dispositivo) que tienen acceso a esas API web. En este paso va a registrar la API web que ejecuta localmente para probar este ejemplo. Normalmente esta API web sería un servicio REST que ofrece la funcionalidad que desea que tenga una aplicación para obtener acceso. Microsoft Azure Active Directory puede proteger cualquier extremo.*

*Aquí asumimos que va a registrar la API de REST TODO mencionada anteriormente, pero esto funciona para cualquier API web que desee que Azure Active Directory proteja.*

Pasos para registrar una API web con Microsoft Azure AD

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en Active Directory en el panel de navegación izquierdo.
3. Haga clic en el inquilino de AD donde desea registrar la aplicación de ejemplo.
4. Haga clic en la pestaña Aplicaciones.
5. En el cajón, haga clic en Agregar.
6. Haga clic en "Agregar una aplicación que mi organización está desarrollando".
7. Escriba un nombre descriptivo para la aplicación, por ejemplo "ServicioListaTodo", seleccione "Aplicación web y/o API web" y haga clic en Siguiente.
8. Para la URL de inicio de sesión, escriba la dirección URL base para el ejemplo, que es de forma predeterminada `https://localhost:8080`.
9. Para el URI de id. de aplicación, escriba `https://<your_tenant_name>/TodoListService` y sustituya `<your_tenant_name>` por el nombre de su inquilino de Azure AD. Haga clic en Aceptar para completar el registro.
10. Mientras sigue en el portal de Azure, haga clic en la pestaña Configurar de la aplicación.
11. **Busque el valor de identificador de cliente y cópielo aparte**, lo necesitará más tarde al configurar la aplicación.

## Paso 3: registre la aplicación de cliente nativo de iOS de ejemplo

El registro de su aplicación web es el primer paso. A continuación, deberá proporcionar información a Azure Active Directory sobre su aplicación también. Esto permite a su aplicación comunicarse con la recién registrada API web

**¿Qué estoy haciendo?**

*Como se estableció anteriormente, Microsoft Active Directory permite agregar dos tipos de aplicaciones. Las API web que ofrecen servicios a usuarios y aplicaciones (ya sea en la Web o una aplicación que se ejecuta en un dispositivo) que tienen acceso a esas API web. En este paso va a registrar la aplicación en este ejemplo. Debe hacerlo para que esta aplicación pueda solicitar acceso a la API web que acaba de registrar. Azure Active Directory rechazará incluso que la aplicación pueda solicitar inicio de sesión a menos que esté registrada. Eso forma parte de la seguridad del modelo.*

*Aquí asumimos que va a registrar esta aplicación de ejemplo mencionada anteriormente, pero esto funciona para cualquier aplicación que desarrolle.*

**¿Por qué pongo una aplicación y una API web en un inquilino?**

*Como posiblemente haya adivinado, podría crear una aplicación con acceso a una API externa registrada en Azure Active Directory desde otro inquilino. Si lo hace, se pedirá a los clientes que acepten el uso de la API en la aplicación. Lo bueno es que la biblioteca de autenticación de Active Directory para iOS se encarga de este consentimiento por usted. A medida que nos encontremos con características más avanzadas, verá que se trata de una parte importante del trabajo necesario para tener acceso al conjunto de API de Microsoft desde Azure y Office, así como cualquier otro proveedor de servicios. Por ahora, como registró su API web y la aplicación en el mismo inquilino, no verá las peticiones de consentimiento. Este suele ser el caso si va a desarrollar una aplicación solo para que la use su propia empresa.*

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en Active Directory en el panel de navegación izquierdo.
3. Haga clic en el inquilino de AD donde desea registrar la aplicación de ejemplo.
4. Haga clic en la pestaña Aplicaciones.
5. En el cajón, haga clic en Agregar.
6. Haga clic en "Agregar una aplicación que mi organización está desarrollando".
7. Escriba un nombre descriptivo para la aplicación, por ejemplo "ClienteListaTodo-iOS", seleccione "Aplicación de cliente nativo" y haga clic en Siguiente.
8. Para el URI de redirección, escriba `http://TodoListClient`. Haga clic en Finalizar.
9. Haga clic en la pestaña Configurar de la aplicación.
10. Busque el valor de identificador de cliente y cópielo aparte, lo necesitará más tarde al configurar la aplicación.
11. En "Permisos para otras aplicaciones", haga clic en "Agregar aplicación". Seleccione "Otras" en la lista desplegable "Mostrar" y haga clic en la marca de comprobación superior. Busque y haga clic en ServicioListaTodo y en la marca de comprobación inferior para agregar la aplicación. Seleccione "Acceso a ServicioListaTodo" en la lista desplegable "Permisos delegados" y guarde la configuración.


## Paso 4: descargue el código de ejemplo de cliente nativo de iOS

* `$ git clone git@github.com:AzureADSamples/NativeClient-iOS.git`

## Paso 5: descargue ADAL para iOS y agréguela al área de trabajo de XCode

#### Descargar la ADAL para iOS

* `git clone git@github.com:MSOpenTech/azure-activedirectory-library-for-ios.git`

#### Importar la biblioteca en su área de trabajo

En XCode, haga clic con el botón derecho del mouse en el directorio del proyecto y seleccione "Agregar archivos a ejemplo de iOS"...

Cuando se le pida, seleccione el directorio donde clonó ADAL para iOS

#### Agregar libADALiOS.a a Marcos y bibliotecas vinculados

Haga clic en el botón Agregar en "Marcos y bibliotecas vinculados" y agregue el archivo de biblioteca de los marcos importados.

Cree el proyecto para asegurarse de que todo se compila correctamente.


## Paso 6: configure el archivo settings.plist con la información de la API web

En "Archivos auxiliares", encontrará un archivo settings.plist. Contiene la siguiente información:

```XML
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>authority</key>
	<string>https://login.windows.net/common/oauth2/token</string>
	<key>clientId</key>
	<string>xxxxxxx-xxxxxx-xxxxxxx-xxxxxxx</string>
	<key>resourceString</key>
	<string>https://localhost/todolistservice</string>
	<key>redirectUri</key>
	<string>http://demo_todolist_app</string>
	<key>userId</key>
	<string>user@domain.com</string>
	<key>taskWebAPI</key>
	<string>https://localhost/api/todolist/</string>
</dict>
</plist>
```

Reemplace la información en el archivo plist por la configuración de la API web.

##### NOTA:

los valores predeterminados actuales se configuran para trabajar con nuestro [servicio de API de REST de ejemplo de Azure Active Directory para Node.js](https://github.com/AzureADSamples/WebAPI-Nodejs). Necesitará especificar clientID de su API web, sin embargo. Si ejecuta su propia API, tendrá que actualizar los extremos según sea necesario.

## Paso 7: compile y ejecute la aplicación

Debe poder conectarse al extremo de la API de REST y recibir una petición de las credenciales de su cuenta de Azure Active Directory.

Para obtener recursos adicionales, consulte:- [AzureADSamples en GitHub >>](https://github.com/AzureAdSamples) -Documentación de Azure AD en [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=58-->