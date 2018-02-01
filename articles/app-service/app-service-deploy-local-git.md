---
title: "Implementación de Git local a Azure App Service"
description: "Aprenda a habilitar la implementación de Git local en Azure App Service."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: cfowler
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: dariagrigoriu
ms.openlocfilehash: 948c54a2e9be2260d0a7d2cce31b67ffbbd23d03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implementación de Git local a Azure App Service

En este tutorial se muestra cómo implementar la aplicación en [Azure Web Apps](app-service-web-overview.md) desde un repositorio de Git en el equipo local. App Service admite este enfoque con la opción de implementación **GIT local** en [Azure Portal].

## <a name="prerequisites"></a>requisitos previos

Para completar este tutorial, necesita:

* Git. Puede descargar el archivo binario de instalación [aquí](http://www.git-scm.com/downloads).
* Conocimientos básicos de Git.
* Una cuenta de Microsoft Azure. Si aún no tiene ninguna, puede [registrarse para una evaluación gratuita](https://azure.microsoft.com/pricing/free-trial) o [activar las ventajas de suscriptor de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Si desea empezar a trabajar con Azure App Service antes de inscribirse para abrir una cuenta de Azure, vaya a [Probar App Service](https://azure.microsoft.com/try/app-service/), donde podrá crear inmediatamente una aplicación de inicio de corta duración en App Service. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
>

## <a name="Step1"></a>Paso 1: Creación de un repositorio local

Realice las tareas siguientes al crear un nuevo repositorio Git.

1. Inicie una herramienta de línea de comandos, como **GitBash** (Windows) o **Bash** (shell de Unix). En los sistemas OS X puede tener acceso a la línea de comandos mediante la aplicación **Terminal**.
1. Desplácese al directorio donde se ubicará el contenido que se va a implementar.
1. Utilice el comando siguiente para inicializar un nuevo repositorio Git:

  ```bash
  git init
  ```

## <a name="Step2"></a>Paso 2: Confirmación del contenido

App Service admite aplicaciones creadas en varios lenguajes de programación.

1. Si el repositorio todavía no incluye contenido, simplemente agregue un archivo .html estático, tal como se indica a continuación u omita este paso:
   * Con un editor de texto, cree un archivo nuevo denominado **index.html** en la raíz del repositorio Git.
   * Agregue el texto siguiente como contenido del archivo index.htm y guárdelo: *Hello Git!*
1. Desde la línea de comandos, compruebe que está en la raíz de su repositorio de Git. Use el comando siguiente para agregar archivos al repositorio:

        git add -A 
1. A continuación, confirme los cambios en el repositorio mediante el siguiente comando:

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>Paso 3: Habilitación del repositorio de aplicaciones de App Service

Lleve a cabo los pasos siguientes para habilitar un repositorio de Git para su aplicación de App Service.

1. Inicie sesión en el [Azure Portal].
1. En la vista de la aplicación App Service, haga clic en **Configuración > Origen de implementación**. Haga clic en **Elegir recurso**, luego en **Repositorio de Git local** y, finalmente, en **Aceptar**.

    ![Repositorio de Git local](./media/app-service-deploy-local-git/local_git_selection.png)

1. Si esta es la primera vez que configura un repositorio en Azure, tendrá que crear unas credenciales de inicio de sesión para él. Las usará para iniciar sesión en el repositorio de Azure y aplicar cambios desde su repositorio de Git local. En la vista de la aplicación web, haga clic en **Configuración > Credenciales de implementación** y configure el nombre de usuario y la contraseña de la implementación. Cuando haya terminado, haga clic en **Guardar**.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Paso 4: Implementación del proyecto

Siga los pasos que se indican a continuación para publicar una aplicación en App Service mediante un Git local.

1. En la vista de la aplicación web en Azure Portal, haga clic en **Configuración > Propiedades** para la **Dirección URL de Git**.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **Dirección URL de Git** es la referencia remota en la que se realizará la implementación desde el repositorio local. Use esta dirección URL en los pasos siguientes.
1. Mediante la línea de comandos, compruebe que está en la raíz del repositorio de GIT local.
1. Use `git remote` para agregar la referencia remota que aparecía en **Dirección URL de Git** en el paso 1. El comando es similar a lo siguiente:

    ```bash
    git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git
    ```

   > [!NOTE]
   > El comando **remote** agrega una referencia con nombre a un repositorio remoto. En este ejemplo, se crea una referencia llamada "azure" para el repositorio de la aplicación web.
   >

1. Inserte el contenido en App Service mediante el nuevo comando remoto **azure** que acaba de crear.

    ```bash
    git push azure master
    ```

    Se le solicitará la contraseña que creó al restablecer las credenciales de implementación en Azure Portal. Escriba la contraseña (tenga en cuenta que Gitbash no envía los asteriscos a la consola mientras escribe la contraseña). 
1. Vuelva a la aplicación en Azure Portal. En la vista **Implementaciones** debería aparecer una entrada de registro de la última inserción.

    ![](./media/app-service-deploy-local-git/deployment_history.png)

1. Haga clic en el botón **Examinar** situado en la parte superior de la página de la aplicación web para comprobar que el contenido se haya implementado.

## <a name="Step5"></a>Solución de problemas

Estos son los errores o problemas que suelen aparecer al usar Git para publicar en una aplicación de App Service en Azure:

---
**Síntoma**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Causa**: este error puede producirse si la aplicación no está en funcionamiento.

**Resolución**: inicie la aplicación en Azure Portal. La implementación de GIT no está disponible cuando la aplicación web está detenida.

---
**Síntoma**: `Couldn't resolve host 'hostname'`

**Causa**: este error puede ocurrir si la información de dirección introducida al crear el repositorio remoto "azure" no era correcta.

**Resolución**: use el comando `git remote -v` para obtener un listado de todos los remotos, junto con la dirección URL asociada. Compruebe que la URL para el repositorio correcto "azure" es correcta. Si lo necesita, suprima y vuelva a crear este repositorio remoto utilizando la URL correcta.

---
**Síntoma**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Causa**: este error puede producirse si no se especifica ninguna rama durante `git push`, o si no ha configurado el valor `push.default` en `.gitconfig`.

**Resolución**: vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo: 

```bash
git push azure master
```

---
**Síntoma**: `src refspec [branchname] does not match any.`

**Causa**: este error puede tener lugar si intenta insertar una rama que no es la principal en el repositorio remoto "azure".

**Resolución**: vuelva a realizar la operación de inserción, especificando la rama principal. Por ejemplo: 

```bash
git push azure master
```

---
**Síntoma**: `RPC failed; result=22, HTTP code = 5xx.`

**Causa**: este error puede producirse si se trata de insertar un repositorio de git de gran tamaño a través de HTTPS.

**Resolución**: cambie la configuración de git en la máquina local para aumentar el tamaño de postBuffer.

```bash
git config --global http.postBuffer 524288000
```

---
**Síntoma**: `Error - Changes committed to remote repository but your web app not updated.`

**Causa**: este error puede ocurrir si está implementando una aplicación Node.js que contiene un archivo package.json que especifica módulos requeridos adicionales.

**Resolución**: los mensajes adicionales que contienen 'npm ERR!' deben registrarse antes de este error y pueden proporcionar contexto adicional sobre el error. A continuación se indican las causas conocidas de este error y el mensaje 'npm ERR!' correspondiente:

* **Archivo package.json con estructura incorrecta**: npm ERR! No se pudieron leer las dependencias.
* **Módulo nativo que no tiene una distribución binaria para Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      OR
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Recursos adicionales

* [Documentación de Git](http://git-scm.com/documentation)
* [Documentación de Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Implementación continua en Azure App Service](app-service-continuous-deployment.md)
* [Uso de PowerShell para Azure](/powershell/azure/overview)
* [Cómo utilizar la interfaz de línea de comandos de Azure](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Azure Portal]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure Command-Line Interface]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
