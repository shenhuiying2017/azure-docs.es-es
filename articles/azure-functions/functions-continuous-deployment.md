<properties
   pageTitle="Implementación continua para Funciones de Azure | Microsoft Azure"
   description="Utilice las funciones de implementación continua de Servicio de aplicaciones de Azure para publicar las Funciones de Azure."
   services="functions"
   documentationCenter="na"
   authors="ggailey777"
   manager="erikre"
   editor=""
   tags=""
   />

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="glenga"/>


# <a name="continuous-deployment-for-azure-functions"></a>Implementación continua para Funciones de Azure 

Funciones de Azure facilita la configuración de la implementación continua de la aplicación de función. Funciones aprovecha la integración del Servicio de aplicaciones de Azure con BitBucket, Dropbox, GitHub y Visual Studio Team Services (VSTS) para habilitar un flujo de trabajo de implementación continua en el que Azure extrae las actualizaciones del código de las funciones cuando se publican en uno de estos servicios. Si no está familiarizado con Funciones de Azure, consulte primero [Información general sobre Funciones de Azure](functions-overview.md).

La implementación continua representa una buena opción para los proyectos donde se integran contribuciones diversas y frecuentes. También le permite mantener el control de código fuente en el código de las funciones. Actualmente se admiten los siguientes orígenes de implementación:

+ [Bitbucket](https://bitbucket.org/)
+ [Dropbox](https://bitbucket.org/)
+ [Repositorio local de GIT](../app-service-web/app-service-deploy-local-git.md)
+ Repositorio externo de GIT
+ [GitHub]
+ Repositorio externo de Mercurial
+ [OneDrive](https://onedrive.live.com/)
+ Visual Studio Team Services

Las implementaciones se configuran para cada aplicación de función. Después de habilitada la implementación continua, el acceso al código de la función en el portal está establecido en acceso *de solo lectura*.

## <a name="continuous-deployment-requirements"></a>Requisitos de implementación continua

Debe tener configurado el origen de la implementación y el código de las funciones en el origen de la implementación antes de configurar la implementación continua. En una implementación determinada de una aplicación de función, cada función se encuentra en un subdirectorio con nombre, allí el nombre del directorio es el nombre de la función. Esta estructura de carpetas es esencialmente su código del sitio. 

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="setting-up-continuous-deployment"></a>Configuración de la implementación continua

Utilice el procedimiento siguiente para configurar la implementación continua de una aplicación de función existente:

1. En la aplicación de función del [portal de Azure Functions](https://functions.azure.com/signin), haga clic en **Function App Settings (Configuración de Function App)** > **Configurar la integración continua** > **Configuración**.

    ![Configurar implementación continua](./media/functions-continuous-deployment/setup-deployment.png)
    
    ![Configurar implementación continua](./media/functions-continuous-deployment/setup-deployment-1.png)
    
    También puede llegar a la hoja de implementaciones desde el inicio rápido de Funciones haciendo clic en **Start from source control**(Iniciar desde el control de código fuente).

2. En la hoja de implementaciones, haga clic en **Elegir origen**; después, rellene la información del origen de implementación que haya seleccionado y haga clic en **Aceptar**.

    ![Elegir origen de la implementación](./media/functions-continuous-deployment/choose-deployment-source.png)

Después de configurar la implementación continua, se copian todos los archivos de cambios del origen de la implementación en la aplicación de función y se desencadena una implementación completa del sitio. Cuando se actualizan los archivos del origen se vuelve a implementar el sitio.


##<a name="deployment-options"></a>Opciones de implementación

Los siguientes son algunos escenarios típicos de implementación:

+ 

###<a name="create-a-staging-deployment"></a>Creación de una implementación de ensayo

Las aplicaciones de función todavía no admiten espacios de implementación. Sin embargo, todavía puede administrar implementaciones de ensayo y producción independientes mediante la integración continua.

El proceso para configurar y trabajar con una implementación de ensayo tiene normalmente este aspecto:

1. Cree dos aplicaciones de función en su suscripción, una para el código de producción y otra para el ensayo. 

2. Cree un origen de la implementación, si aún no tiene uno. Usaremos [GitHub].
 
3. Para la aplicación de función de producción, complete los pasos anteriores descritos en **Configuración de la implementación continua** y establezca la rama de implementación en la rama principal del repositorio de GitHub.

    ![Elegir rama de la implementación](./media/functions-continuous-deployment/choose-deployment-branch.png)

4. Repita este paso para la aplicación de función de ensayo, pero esta vez, seleccione la rama de ensayo en el repositorio de GitHub. Si el origen de la implementación no admite la bifurcación, utilice una carpeta diferente.
 
5. Realice actualizaciones en el código en la rama o carpeta de ensayo y, a continuación, compruebe que esos cambios se reflejan en la implementación de ensayo.

6. Después de la prueba, combine los cambios de la rama de ensayo en la rama principal. Esto desencadenará la implementación en la aplicación de función de producción. Si el origen de la implementación no admite ramas, sobrescriba los archivos de la carpeta de producción con los archivos de la carpeta de ensayo.

###<a name="move-existing-functions-to-continuous-deployment"></a>Movimiento de funciones existentes para la implementación continua

Si dispone de funciones existentes que ha creado y mantenido en el portal, es necesario descargar los archivos de código de la función existente mediante FTP o mediante el repositorio de Git local antes poder configurar la implementación continua como se ha descrito anteriormente. Puede hacerlo en la configuración del Servicio de aplicaciones de la aplicación de función. Una vez descargados los archivos, puede cargarlos en el origen de la implementación continua que haya seleccionado.

>[AZURE.NOTE]Después de configurar la integración continua, ya no podrá modificar los archivos de origen en el portal de Funciones.

####<a name="how-to:-configure-deployment-credentials"></a>Configuración de las credenciales de implementación
Para poder descargar archivos desde la aplicación de función, debe configurar las credenciales para acceder al sitio. Esto lo puede hacer desde el portal. Las credenciales se establecen en el nivel de la aplicación de función.

1. En la aplicación de función del [portal de Azure Functions](https://functions.azure.com/signin), haga clic en **Function App Settings (Configuración de Function App)** > **Go to App Service settings (Ir a la configuración del Servicio de aplicaciones)** > **Credenciales de implementación**.

    ![Configurar credenciales de implementación locales](./media/functions-continuous-deployment/setup-deployment-credentials.png)

2. Escriba un nombre de usuario y una contraseña y haga clic en **Guardar**. Ahora puede usar estas credenciales para tener acceso a la aplicación de función mediante FTP o desde el repositorio de Git integrado.

####<a name="how-to:-download-files-using-ftp"></a>Descarga de archivos mediante FTP

1. En la aplicación de función del [portal de Azure Functions](https://functions.azure.com/signin), haga clic en **Function app settings (Configuración de Function app)** > **Go to App Service settings (Ir a la configuración del Servicio de aplicaciones)** > **Propiedades** y copie los valores de **FTP/usuario de implementación**, **Nombre del host FTP** y **Nombre del host FTPS**.  
**FTP/usuario de implementación** debe escribirse tal como aparece en el portal, incluido el nombre de la aplicación a fin de proporcionar el contexto adecuado para el servidor FTP.

    ![Obtener la información de implementación](./media/functions-continuous-deployment/get-deployment-credentials.png)
    
2. Desde el cliente de FTP, use la información de conexión recopilada para conectarse a la aplicación y descargar los archivos de origen de las funciones.

####<a name="how-to:-download-files-using-the-local-git-repository"></a>Descarga de archivos mediante el repositorio local de Git

1. En la aplicación de función del [portal de Azure Functions](https://functions.azure.com/signin), haga clic en **Function App Settings (Configuración de Function App)** > **Configurar la integración continua** > **Configuración**.

2. En la hoja de implementaciones, haga clic en **Elegir origen**, **Repositorio de Git local** y en **Aceptar**.
 
3. Haga clic en **Go to App Service settings (Ir a la configuración del Servicio de aplicaciones)** > **Propiedades** y anote el valor de dirección URL de Git. 
    
    ![Configurar implementación continua](./media/functions-continuous-deployment/get-local-git-deployment-url.png)

4. Clone el repositorio en la máquina local mediante una línea de comandos compatible con Git o su herramienta de Git favorita. El comando clone de Git tiene el siguiente aspecto:

        git clone https://username@my-function-app.scm.azurewebsites.net:443/my-function-app.git

5. Recupere los archivos de su aplicación de función y póngalos en la copia del equipo local, como en el ejemplo siguiente:

        git pull origin master

    Si se le solicita, proporcione el nombre de usuario y la contraseña para la implementación de la aplicación de función.  


[GitHub]: https://github.com/



<!--HONumber=Oct16_HO2-->


