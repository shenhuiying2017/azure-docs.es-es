<properties 
    pageTitle="Integración del control de código fuente en Automatización de Azure | Microsoft Azure"
    description="En este artículo se describe la integración del control de código fuente con GitHub en Automatización de Azure."
    services="automation"
    documentationCenter=""
    authors="SnehaGunda"
    manager="stevenka"
    editor="tysonn" />    
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="11/04/2015"
    ms.author="sngun" />

# Integración del control de código fuente en Automatización de Azure

La integración del control de código fuente permite asociar runbooks y scripts de PowerShell de su cuenta de Automatización con un repositorio de control de código fuente de GitHub. Proporciona el entorno donde se puede colaborar fácilmente con el equipo, realizar el seguimiento de los cambios y revertir a las versiones anteriores de los runbooks. Por ejemplo, el control de código fuente permite sincronizar distintas ramas de su desarrollo, cuentas de Automatización de prueba o de producción y ayuda a promover el código en el entorno de producción que se probó en el entorno de desarrollo. Puede insertar código de Automatización de Azure en el control de código fuente o extraer sus runbooks del control de código fuente a Automatización de Azure. En este artículo se describe cómo configurar el control de código fuente en su entorno de Automatización de Azure. Se empezará por la creación de un repositorio de GitHub y se recorrerá la configuración de Automatización de Azure para acceder a los repositorios de GitHub.


>[AZURE.NOTE]El control de código fuente admite la extracción e inserción de [runbooks del flujo de trabajo de PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) así como [runbooks de PowerShell](automation-runbook-types.md#powershell-runbooks), los [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) todavía no son compatibles.<br><br>


Hay dos pasos sencillos necesarios para configurar el control de código fuente para su cuenta de Automatización y solo uno si ya tiene una cuenta GitHub. Son los siguientes:
## Paso 1: Crear un repositorio GitHub

Si ya tiene una cuenta de GitHub y un repositorio que desea vincular a Automatización de Azure, inicie sesión en su cuenta existente y comience desde el paso 2. De lo contrario, vaya a [GitHub](https://github.com/), suscríbase a una cuenta nueva y [cree un nuevo repositorio](https://help.github.com/articles/create-a-repo/).


## Paso 2: Configurar el control de código fuente

* En la hoja Cuenta de Automatización del Portal de vista previa de Azure, haga clic en **Configurar control de código fuente.**<br> ![Configurar control de código fuente](media/automation-source-control-integration/automation_01_SetUpSourceControl.png)
* Se abre la hoja **Control de código fuente**, donde puede configurar los detalles de la cuenta de GitHub. A continuación se muestra la lista de parámetros de configuración:<br>

|Parámetro |Descripción |
|:---|:---| 
|Elegir origen | Seleccione el origen. Actualmente, solo se admite **GitHub**. |
|Autorización | Haga clic en el botón **Autorizar** para conceder acceso a Automatización de Azure al repositorio de GitHub. Si ya inició sesión con su cuenta de GitHub en una ventana diferente, se usan las credenciales de dicha cuenta. Cuando la autorización sea correcta, la hoja mostrará su nombre de usuario de GitHub en **Propiedad de autorización**. |
|Elegir repositorio | Seleccione un repositorio de GitHub en la lista de repositorios disponibles. |
|Elegir rama | Seleccione una rama en la lista de ramas disponibles. Solo se muestra la rama **principal** si no creó ninguna rama. |
|Ruta de acceso de la carpeta de runbook | La ruta de acceso de la carpeta de runbook especifica la ruta de acceso en el repositorio de GitHub desde el que desea insertar o extraer el código. Debe especificarse en formato **/nombreCarpeta/nombreDeSubcarpeta**. Solo se pueden sincronizar los scripts de la ruta de acceso de la carpeta de runbook con la cuenta de Automatización. Los scripts de las subcarpetas de la ruta de acceso de la carpeta de runbook **no** se sincronizarán. Use **/** para sincronizar todos los scripts de PowerShell en el repositorio. |


Por ejemplo, si tiene un repositorio denominado **scriptsDePowerShell** que contiene una carpeta denominada **carpetaRaíz**, que a su vez contiene una carpeta denominada **subCarpeta**. Si todas las carpetas y el repositorio contienen runbooks que desea sincronizar:

  1. Para sincronizar runbooks desde el **repositorio**, la ruta de acceso de la carpeta de runbook es */*.
  2. Para sincronizar runbooks desde la **carpetaRaíz**, la ruta de acceso de la carpeta de runbook es */carpetaRaíz*.
  3. Para sincronizar runbooks desde la **subCarpeta**, la ruta de acceso de la carpeta de runbook es */carpetaRaíz/subCarpeta*.<br>

* Después de configurar los parámetros, se muestran en la hoja **Configurar control de código fuente.**

![Hoja Configurar](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)


* La integración del control de código fuente se configura ahora para su cuenta de Automatización y debe actualizarse con la información de GitHub, puede hacer clic en esta parte para ver todo el historial de trabajos de sincronización de control de código fuente. <br> ![Valores de repositorio](media/automation-source-control-integration/automation_03_RepoValues.png)

* Una vez configurado el control de código fuente, se crearán los siguientes recursos de Automatización se creará en su cuenta de Automatización:

Se crean dos [activos de variable](automation-variables.md).
      

  * La variable **Microsoft.Azure.Automation.SourceControl.Connection** contiene los valores de la cadena de conexión, tal como se muestra a continuación.<br>

|Parámetro |Valor |
|:---|:---|
| Nombre | Microsoft.Azure.Automation.SourceControl.Connection |
| Tipo | Cadena |
| Valor | {"Branch":<*nombreDeRama*>,"RunbookFolderPath":<*rutaDeCarpetaDeRunbook*>,"ProviderType":<*tiene un valor de 1 para GitHub*>,"Repository":<*nombreDelRepositorio*>,"Username":<*nombreDe UsuarioDeGitHub*>} |


  * La variable **Microsoft.Azure.Automation.SourceControl.OauthToken** contiene el valor cifrado seguro de OAuthToken. <br>

|Parámetro |Valor |
|:---|:---|
| Nombre | Microsoft.Azure.Automation.SourceControl.OauthToken |
| Tipo | Unknown(Encrypted) |
| Valor | <*OAuthToken cifrado*> |

![Variables](media/automation-source-control-integration/automation_04_Variables.png)

Se agrega el **control de código fuente de Automatización** como una aplicación autorizada a su cuenta de GitHub. Para ver la aplicación: desde la página principal de GitHub, navegue a **perfil** > **Configuración** > **Aplicaciones**. Esta aplicación permite que Automatización de Azure sincronice el repositorio de GitHub con una cuenta de Automatización.

![Aplicación Git](media/automation-source-control-integration/automation_05_GitApplication.png)


## Uso del control de código fuente en Automatización


### Protección de un runbook de Automatización de Azure en el control de código fuente

La protección de runbooks permite insertar los cambios realizados en un runbook en el repositorio de control de código fuente. A continuación se muestran los pasos necesarios para proteger un runbook:

* Desde su cuenta de Automatización, [cree un nuevo runbook textual](automation-first-runbook-textual.md) o [edite un runbook textual existente](automation-edit-textual-runbook.md). Este runbook puede ser un flujo de trabajo de PowerShell o un runbook de scripts de PowerShell.  
* Después de editar el runbook, guárdelo y haga clic en **Proteger** en la hoja **Editar**. ![Botón Proteger](media/automation-source-control-integration/automation_06_CheckinButton.png)


 >[AZURE.NOTE]La protección de la Automatización de Azure sobrescribirá el código existente en el control de código fuente. La instrucción de línea de comandos de Git equivalente para la protección es **git add + git commit + git push**.

* Al hacer clic en **Proteger**, se mostrará un mensaje de confirmación y haga clic en Sí para continuar. ![Mensaje de protección](media/automation-source-control-integration/automation_07_CheckinMessage.png)
* La protección se inicia en el runbook: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Este runbook se conecta a GitHub y aplica los cambios realizados de Automatización de Azure al repositorio. Para ver el historial de trabajos de protección, vuelva a la pestaña **Integración del control de código fuente** y haga clic para abrir la hoja Sincronización de repositorio. Esta hoja muestra todos los trabajos de control de código fuente. Seleccione el trabajo que desea ver y haga clic para ver los detalles. ![Runbook de protección](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
* Si hay errores en el trabajo de protección, el estado del trabajo debe suspenderse y podrá ver más detalles sobre el error en la hoja del trabajo. La sección **Todos los registros** mostrará todas las transmisiones de PowerShell asociadas a ese trabajo. Esto le proporcionará detalles para ayudarle a solucionar los problemas.  
* El nombre del runbook modificado se envía como un parámetro de entrada al runbook de protección. También puede [ver los detalles del trabajo](automation-runbook-execution.md#viewing-job-status-using-the-azure-management-portal) expandiendo el runbook en la hoja **Sincronización de repositorio**. ![Entrada de protección](media/automation-source-control-integration/automation_09_CheckinInput.png)
* Actualice el repositorio de GitHub cuando finalice el trabajo para ver los cambios. Debe haber una confirmación en el repositorio con un mensaje de confirmación: ***Nombre de runbook* actualizado en Automatización de Azure.**  



### Sincronización de runbooks de control de código fuente a la Automatización de Azure 

El botón de sincronización que se encuentra en la hoja Sincronización de runbooks permite extraer todos los runbooks en la ruta de acceso de la carpeta de runbook del repositorio a su cuenta de Automatización. El mismo repositorio puede sincronizarse con más de una cuenta de Automatización. A continuación se muestran los pasos necesarios para sincronizar un runbook:

* En la cuenta de Automatización donde se configuró el control de código fuente, abra la hoja **Integración del control de código fuente/Sincronización de repositorio** y haga clic en **Sincronizar**, y después de que se muestre un mensaje, haga clic en **Sí** para continuar. ![Botón Sincronizar](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
* La sincronización inicia el runbook: **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Este runbook se conecta a GitHub y aplica los cambios realizados del repositorio a Automatización de Azure. Para ver el runbook, vuelva a la hoja **Sincronización del control de código fuente/Sincronización de repositorio** y expándala. ![Runbook de sincronización](media/automation-source-control-integration/automation_11_SyncRunbook.png)

 
>[AZURE.NOTE]Una sincronización del control de código fuente sobrescribe la versión de borrador de los runbooks que existen actualmente en la cuenta de Automatización para **todos** los runbooks que están actualmente en el control del código fuente. La instrucción de línea de comandos de Git equivalente para realizar la sincronización es **git pull**.


## Desconexión del control de código fuente

Para desconectarse de su cuenta de GitHub, abra la hoja Sincronización de repositorio y haga clic en **Desconectar**. Cuando desconecte el control de código fuente, los runbooks que se sincronizaron seguirán estando en su cuenta de Automatización, pero no se habilitará la hoja Sincronización de repositorio. ![Botón Desconectar](media/automation-source-control-integration/automation_12_Disconnect.png)



## Pasos siguientes

Para obtener más información acerca de la integración del control de código fuente, consulte los siguientes recursos:- [Automatización de Azure: integración del control de código fuente en Automatización de Azure](https://azure.microsoft.com/blog/azure-automation-source-control-13/) - [Vote por su sistema de control de código fuente favorito](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d) - [Automatización de Azure: integración del control de código fuente de runbook mediante Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)

<!---HONumber=Nov15_HO3-->