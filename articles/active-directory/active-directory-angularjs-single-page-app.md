<properties
	pageTitle="Creación de una aplicación de una sola página AngularJS con Azure AD"
	description="Se demuestra el uso de la Biblioteca de autenticación de Active Directory (ADAL) para Javascript con el objeto de proteger una aplicación de una página basada en AngularJS con un back-end ASP.NET Web API, que llama a otra ASP.NET Web API mediante CORS."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="terrylan"
	editor="LisaToft"/>

<tags
	ms.service="active-directory"
	ms.devlang="javascript"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="05/11/2015"
	ms.author="justinha"/>


# Creación de una aplicación de una sola página AngularJS con Azure AD

En este tutorial se demuestra el uso de la Biblioteca de autenticación de Active Directory (ADAL) para JavaScript con el objeto de proteger una aplicación de una página basada en AngularJS, implementada con un back-end ASP.NET Web API, que llama a otra ASP.NET Web API mediante CORS. Para ver el código de ejemplo que se usa en este tutorial, consulte [AzureADSamples/SinglePageApp-AngularJS-DotNet](https://github.com/AzureADSamples/SinglePageApp-AngularJS-DotNet) en GitHub.

ADAL para Javascript es una biblioteca de código abierto. Si desea obtener información sobre las opciones de distribución, el código fuente y las contribuciones, compruebe el [repositorio de JS para ADAL](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Para obtener más información sobre cómo funcionan los protocolos en este y otros escenarios, consulte [Escenarios de autenticación para Azure AD](http://go.microsoft.com/fwlink/?LinkId=394414).

## Ejecución de este ejemplo

Comenzar es fácil. Para ejecutar este ejemplo, necesitará:

- Visual Studio 2013
- Una conexión a Internet
- Una suscripción a Azure (es suficiente con una [versión de evaluación gratuita](https://account.windowsazure.com/organization))

Cada suscripción de Azure lleva asociado un inquilino de Azure Active Directory (Azure AD). Todas las características de Azure AD que se usan en este ejemplo están disponibles de forma gratuita.

## Clonación o descarga de este repositorio

En el shell o la línea de comandos:`git clone https://github.com/AzureADSamples/SinglePageApp-WebAPI-AngularJS-DotNet.git`

## Registre el servicio To Go API con su inquilino de Azure Active Directory

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en **Active Directory** en el panel de navegación izquierdo.
3. Haga clic en el directorio donde desea registrar la aplicación de ejemplo.
4. Haga clic en la pestaña **Aplicaciones**.
5. En el cajón, haga clic en **Agregar**.
6. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.
7. Escriba un nombre descriptivo para la aplicación, por ejemplo "To Go API", seleccione **Aplicación web** o **Web API** y haga clic en **Siguiente**.
8. Para la URL de inicio de sesión, escriba la dirección URL base para el ejemplo, que es de forma predeterminada `https://localhost:44327/`.
9. Para el URI de identificación de la aplicación, escriba `https://<your_directory_name>/ToGoAPI` y sustituya `<your_directory_name>` por el nombre de su directorio de Azure AD. Guarde la configuración.

Ya está. Antes de continuar con el siguiente paso, debe buscar el URI del Id. de la aplicación de su API.

1. Mientras sigue en el portal de Azure, haga clic en la pestaña **Configurar** de la aplicación.
2. Busque el valor del URI del Id. de la aplicación y cópielo en el Portapapeles.

## Configure la API To Go para usar su inquilino de Azure Active Directory

1. Abra la solución en Visual Studio 2013.
2. En el proyecto ToGoAPI, abra el archivo `web.config`.
3. Busque la clave de aplicación `ida:Tenant` y sustituya el valor por el nombre de su inquilino de Azure AD.
4. Busque la clave de aplicación `ida:Audience` y sustituya el valor por el URI del identificador de la aplicación que copió del portal de Azure.
5. También en el proyecto ToGoAPI, abra el archivo `Controllers/ToGoListController.cs`. En el atributo `[EnableCors...]`, escriba la ubicación del cliente To Do SPA. De forma predeterminada, es `https://localhost:44326`. Asegúrese de omitir la barra diagonal final.
5. En el proyecto TodoSPA, abra el archivo `App/Scripts/App.js` y busque la declaración del objeto `endpoints`.
6. Especifique una asignación de la ubicación del extremo To Go API a su identificador de recursos, o URI del Id. de la aplicación. El nombre de la propiedad del objeto `endpoints` debe ser la ubicación del servicio To Go API. De forma predeterminada, es `https://localhost:44327/`. El valor de esta propiedad debe ser el URI del identificador de la aplicación que copió del portal, por ejemplo, `https://<your_tenant_name>/ToGoAPI`.
8. No se preocupe aún por los demás valores de configuración de este archivo, volveremos a ello en unos segundos.
9. También en el proyecto TodoSPA, abra el archivo `App/Scripts/toGoListSvc.js`. Sustituya el valor de la variable `apiEndpoint` por la ubicación de su To Go API. De forma predeterminada, es `https://localhost:44327/`.

## Registre la aplicación de una sola página To Do con el inquilino de Azure Active Directory.

1. Inicie sesión nuevamente en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en **Active Directory** en el panel de navegación izquierdo.
3. Haga clic en el inquilino donde desea registrar la aplicación de ejemplo.
4. Haga clic en la pestaña **Aplicaciones**.
5. En el cajón, haga clic en **Agregar**.
6. Haga clic en **Agregar una aplicación que mi organización está desarrollando**.
7. Escriba un nombre descriptivo para la aplicación, por ejemplo "To Do SPA", seleccione **Aplicación web o Web API** y haga clic en **Siguiente**.
8. Para la URL de inicio de sesión, escriba la dirección URL base para el ejemplo, que es de forma predeterminada `https://localhost:44326/`.
9. Para el URI de identificación de la aplicación, escriba `https://<your_directory_name>/ToDoSPA` y sustituya `<your_directory_name>` por el nombre de su directorio de Azure AD.
10. En la sección **Permisos para otras aplicaciones**, haga clic en **Agregar aplicación**. Seleccione **Otras** en la lista desplegable **Mostrar** y haga clic en la marca de comprobación superior. Busque y haga clic en To Go API y haga clic en la marca de comprobación inferior para agregar la aplicación. Seleccione **Acceso a To Go API** en la lista desplegable **Permisos delegados** y guarde la configuración.

Ya está. Antes de continuar con el paso siguiente, deberá buscar el Id. de cliente de la aplicación.

1. Mientras sigue en el portal de Azure, haga clic en la pestaña **Configurar** de la aplicación.
2. Busque el valor de Id. de cliente y cópielo en el Portapapeles.


## Habilitación de la concesión implícita OAuth2 para la aplicación

De forma predeterminada, las aplicaciones aprovisionadas en Azure AD no están habilitadas para usar la concesión implícita OAuth2. Para ejecutar este ejemplo, necesita participar explícitamente.

1. Por los pasos anteriores, su explorador debe estar aún en el Portal de administración de Azure y mostrar en concreto la pestaña **Configurar** de la entrada de su aplicación.
2. Con el botón **Administrar manifiesto** del cajón, descargue el archivo de manifiesto de la aplicación y guárdelo en el disco.
3. Abra el archivo de manifiesto con un editor de texto. Busque la propiedad `oauth2AllowImplicitFlow`. Encontrará que está establecida en `false`; cámbiela a `true` y guarde el archivo.
4. Con el botón **Administrar manifiesto**, cargue el archivo de manifiesto actualizado. Guarde la configuración de la aplicación.

## Configuración de la aplicación To Do SPA para usar el inquilino de Azure Active Directory

1. Abra la solución en Visual Studio 2013.
2. En el proyecto TodoSPA, abra el archivo `web.config`.
3. Busque la clave de aplicación `ida:Tenant` y sustituya el valor por el nombre de su directorio de Azure AD.
4. Busque la clave de aplicación `ida:Audience` y sustituya el valor por el identificador de cliente del Portal de Azure.
5. También en el proyecto TodoSPA, abra el archivo `App/Scripts/App.js` una vez más y busque la línea `adalAuthenticationServiceProvider.init(`.
6. Sustituya el valor de `tenant` por el nombre de su directorio de Azure AD.
7. Sustituya el valor de `clientId` por el identificador del cliente del portal de Azure.

## Ejecución del ejemplo

Limpie la solución, recompílela y ejecútela.

Puede activar la experiencia de inicio de sesión bien haciendo clic en el vínculo de inicio de sesión en la esquina superior derecha o haciendo clic directamente en las pestañas de las listas **To Do** o **To Go**. Examine el ejemplo: inicie sesión, agregue elementos a la lista To Do, quite la cuenta de usuario y comience de nuevo. Agregue lugares a la lista To Go; para ello, realice operaciones CRUD contra To Go API mediante CORS.

## Implementación de este ejemplo en Azure

Para implementar To Do SPA y To Go API en Sitios web Azure, creará dos sitios web, publicará cada proyecto en un sitio web y actualizará ambos proyectos para hacer referencia a las nuevas ubicaciones en lugar de a IIS Express.

### Creación del sitio web de Azure To Go API

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en **Sitios web** en el panel de navegación izquierdo.
3. Haga clic en **Nuevo** en la esquina inferior izquierda, seleccione **Proceso** > **Sitio web** > **Creación personalizada**, seleccione la región y el plan de hospedaje y proporcione un nombre a su sitio web, por ejemplo, togo-contoso.azurewebsites.net. Seleccione la base de datos que usará o cree una nueva. Haga clic en **Crear sitio web**.
4. Una vez creado el sitio web, haga clic en él para administrarlo. Para este conjunto de pasos, descargue el archivo .publishsettings y guárdelo. También se pueden usar otros mecanismos de autenticación, como el control de código fuente. Para obtener más información acerca del uso de un archivo .publishsettings, consulte [Conexión a su suscripción](../install-configure-powershell.md#Connect).

### Creación del sitio web de Azure To Do SPA

1. Vaya al [Portal de administración de Azure](https://manage.windowsazure.com).
2. Haga clic en **Sitios web** en el panel de navegación izquierdo.
3. Haga clic en **Nuevo** en la esquina inferior izquierda, seleccione **Proceso** > **Sitio web** > **Creación personalizada**, seleccione la región y el plan de hospedaje y proporcione un nombre a su sitio web, por ejemplo, todo-contoso.azurewebsites.net. Seleccione la base de datos que se usará; la misma base de datos que la de To Go API servirá. Haga clic en **Crear sitio web**.
4. Una vez creado el sitio web, haga clic en él para administrarlo. Una vez más, descargue el archivo .publishsettings para este sitio y guárdelo.

### Actualización de ambos proyectos para usar Sitios web Azure

1. En Visual Studio, vaya al proyecto TodoSPA.
2. Se requieren dos cambios. En `App\Scripts\app.js`, sustituya el nombre de propiedad del objeto `endpoints` por la nueva ubicación de su To Go API, por ejemplo, `https://togo-contoso.azurewebsites.net/`. En `App\Scripts\toGoListSvc.js`, reemplace la variable `apiEndpoint` por el mismo valor.
3. En el proyecto ToGoAPI, solo se requiere un cambio. En `Controllers\ToGoListController.cs`, actualice el atributo `[EnableCors...]` para que refleje la nueva ubicación de To Do SPA, por ejemplo, `https://todo-contoso.azurewebsites.net`. Una vez más, asegúrese de omitir la barra diagonal final.

### Publicación de To Go API en Sitios web Azure

1. Cambie a Visual Studio y vaya al proyecto ToGoAPI. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y seleccione **Publicar**. Haga clic en **Importar** e importe el perfil de publicación de To Go API que ha descargado.
6. En la pestaña **Conexión**, actualice la URL de destino para que sea https, como https://togo-constoso.azurewebsites.net. Haga clic en **Siguiente**.
7. En la pestaña **Configuración**, asegúrese de que la opción **Habilitar autenticación organizativa** NO esté seleccionada. Haga clic en **Publicar**.
8. Visual Studio publicará el proyecto y abrirá automáticamente un explorador a la dirección URL del proyecto. Si ve la página web predeterminada del proyecto, la publicación ha tenido éxito.

### Publicación de To Do SPA en Sitios web Azure

1. Cambie a Visual Studio y vaya al proyecto TodoSPA. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y seleccione **Publicar**. Haga clic en **Importar** e importe el archivo .publishsettings de To Do SPA que ha descargado.
6. En la pestaña **Conexión**, actualice la URL de destino para que sea https, como https://todo-contoso.azurewebsites.net. Haga clic en **Siguiente**.
7. En la pestaña **Configuración**, asegúrese de que la opción **Habilitar autenticación organizativa** NO esté seleccionada. Haga clic en **Publicar**.
8. Visual Studio publicará el proyecto y abrirá automáticamente un explorador a la dirección URL del proyecto. Si ve la página web predeterminada del proyecto, la publicación ha tenido éxito.

### Actualización de la configuración de To Do SPA en el inquilino de AD

1. Vaya al [Portal de administración de Azure](https://manage.windowsazure.com).
2. En el panel de navegación izquierdo, haga clic en** Active Directory** y seleccione su inquilino.
3. En la pestaña **Aplicaciones**, seleccione la aplicación **To Do SPA**.
4. En la pestaña **Configurar**, actualice los campos **URL de inicio de sesión** y **URL de respuesta** con la dirección de su SPA; por ejemplo, https://todo-contoso.azurewebsites.net. Guarde la configuración.

## Acerca del código

Los archivos principales que contienen la lógica de autenticación son los siguientes:

- **App.js**: inserta la dependencia del módulo ADAL, proporciona los valores de configuración de la aplicación que usa ADAL para controlar las interacciones de los protocolos con Azure AD e indica a qué rutas no se debe tener acceso sin autenticarse previamente.

- **index.html**: contiene una referencia a adal.js

- **HomeController.js**: muestra cómo aprovechar los métodos login() y logOut() en ADAL.

- **UserDataController.js**: muestra cómo extraer información de usuario del id_token almacenado en caché.

Un agradecimiento especial a @matvelloso por su ayuda en la creación de este tutorial.


## Pasos siguientes

Estos son algunos otros recursos que le pueden ayudar a usar Azure AD para agregar autenticación y autorización a sus aplicaciones web y API web:

+ [Ejemplos de código de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn646737.aspx)
+ [Escenarios de autenticación para Azure AD](https://msdn.microsoft.com/library/azure/dn499820.aspx)

<!---HONumber=58--> 