<properties
	pageTitle="Adición de un repositorio de artefactos Git a un laboratorio en Azure DevTest Labs | Microsoft Azure"
	description="Adición de un repositorio Git de GitHub o Visual Studio Team Services para el origen de artefactos personalizados en Azure DevTest Labs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="tarcher"/>

# Adición de un repositorio de artefactos Git a un laboratorio en Azure DevTest Labs

> [AZURE.VIDEO how-to-add-your-private-artifacts-repository-in-a-devtest-lab]

En Azure DevTest Labs, los artefactos son *acciones*, como instalar software o ejecutar scripts y comandos, cuando se crea una máquina virtual. De forma predeterminada, un laboratorio incluye artefactos del repositorio oficial de artefactos de Azure DevTest Labs. Puede agregar un repositorio de artefactos de Git al laboratorio para incluir los artefactos que crea el equipo. El repositorio se puede hospedar en [GitHub](https://github.com) o en [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Para obtener información sobre cómo crear un repositorio de GitHub, consulte [Entrenamiento militar de GitHub](https://help.github.com/categories/bootcamp/).
- Para obtener información sobre cómo crear un proyecto Team Services con un repositorio de Git, vea [Conexión con Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

En la siguiente captura de pantalla se muestra un ejemplo del aspecto que podría tener en GitHub un repositorio que contiene artefactos: ![Repositorio de artefactos de GitHub de ejemplo](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)


## Obtención de la información del repositorio y las credenciales

Para agregar un repositorio de artefactos al laboratorio, primero debe obtener cierta información del repositorio. Las secciones siguientes le guiarán en el modo de obtener esta información para repositorios de artefactos hospedados en GitHub y Visual Studio Team Services.

### Obtención de la dirección URL de clonación del repositorio de GitHub y el token de acceso personal

Para obtener la dirección URL de clonación del repositorio de GitHub y el token de acceso personal, siga estos pasos:

1. Vaya a la página principal del repositorio de GitHub que contiene las definiciones de artefacto.

1. Seleccione **Clone or download** (Clonar o descargar).

1. Seleccione el botón para copiar la **dirección URL de clonación de HTTPS** en el portapapeles y guárdela para usarla más adelante.

1. Seleccione la imagen de perfil en la esquina superior derecha de GitHub y seleccione **Configuración**.

1. En el menú **Configuración personal** de la izquierda, seleccione **Tokens de acceso personal**.

1. Seleccione **Generar nuevo token**.

1. En la página **New personal access token (Nuevo token de acceso personal)** escriba la información que considere oportuno en **Token description (Descripción del token)**, acepte los elementos predeterminados en el **Select scopes (Seleccionar ámbitos)** y después elija **Generate Token (Generar token)**.

1. Guarde el token generado, ya que lo necesitará más adelante.

1. Ahora puede cerrar GitHub.

1. Continúe con la sección [Conexión del laboratorio al repositorio de artefactos](#connect-your-lab-to-the-artifact-repository).

### Obtención de la dirección URL de clonación de Visual Studio Team Services y del token de acceso personal

Para obtener la dirección URL de clonación del repositorio de Visual Studio Team Services y el token de acceso personal, siga estos pasos:

1. Abra la página principal de la colección de equipo (por ejemplo, `https://contoso-web-team.visualstudio.com`) y después seleccione el proyecto de artefacto.

1. En la página de inicio del proyecto, seleccione **Código**.

1. Para ver la dirección URL de clonación, en la página **Código** del proyecto, seleccione **Clonar**.

1. Guarde la dirección URL, ya que la necesitará más adelante en este tutorial.

1. Para crear un token de acceso personal, seleccione **Mi perfil** en el menú desplegable de la cuenta de usuario.

1. En la página de información de perfil, seleccione **Seguridad**.

1. En la pestaña **Seguridad** ficha, seleccione **Agregar**.

1. En la página **Crear un token de acceso personal**:

    - Escriba la información que desee en **Descripción** para el token.
    - Seleccione **180 días** en la lista **Expira en**.
    - Elija **Todas las cuentas accesibles** en la lista **Cuentas**.
    - Elija la opción **Todos los ámbitos**.
    - Elija **Crear token**.

1. Cuando termine, el nuevo token aparecerá en la lista **Tokens de acceso personal**. Seleccione **token** y luego guarde el valor del token para usarlo más adelante.

1. Continúe con la sección [Conexión del laboratorio al repositorio de artefactos](#connect-your-lab-to-the-artifact-repository).

##Conexión del laboratorio al repositorio de artefactos

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Más servicios** y luego seleccione **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.

1. En la hoja del laboratorio, seleccione **Configuración**.

1. En la hoja **Configuración** del laboratorio, seleccione **Artifacts Repositories** (Repositorios de artefactos).

1. En la hoja **Artifacts Repositories** (Repositorios de artefactos), seleccione **+ Agregar**.

	![Botón Agregar repositorio de artefactos](./media/devtest-lab-add-artifact-repo/add-artifact-repo.png)
 
1. En la segunda hoja **Artifacts Repositories** (Repositorios de artefactos), especifique lo siguiente:

    - En **Nombre**, escriba un nombre para el repositorio.
    - **URL de clonación de Git**: escriba la dirección URL de clonación HTTPS de Git que copió anteriormente de GitHub o de Visual Studio Team Services.
    - **Ruta de la carpeta**: escriba la ruta de la carpeta relativa a la dirección URL de clonación que contiene las definiciones de artefacto.
    - **Rama**: escriba la rama para obtener las definiciones de artefacto.
    - **Token de acceso personal**: especifique el token de acceso personal que obtuvo anteriormente de GitHub o de Visual Studio Team Services.
     
	![Hoja Repositorio de artefactos](./media/devtest-lab-add-artifact-repo/artifact-repo-blade.png)

1. Seleccione **Guardar**.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Entradas blogs relacionadas
- [How to troubleshoot failing Artifacts in AzureDevTestLabs (Cómo solucionar errores de artefactos en Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
- [Join a VM to existing AD Domain using ARM template in Azure DevTest Labs (Unir una máquina virtual al dominio de AD existente mediante la plantilla ARM en Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

<!---HONumber=AcomDC_0907_2016-->