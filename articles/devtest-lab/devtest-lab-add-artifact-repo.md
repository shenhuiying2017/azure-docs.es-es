---
title: "Adición de un repositorio de Git a un laboratorio en Azure DevTest Labs | Microsoft Docs"
description: "Adición de un repositorio Git de GitHub o Visual Studio Team Services para el origen de artefactos personalizados en Azure DevTest Labs"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 053f92a65f9ae29154d471fd22ee842620b4f273
ms.contentlocale: es-es
ms.lasthandoff: 08/24/2017

---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-azure-resource-manager-templates"></a>Adición de un repositorio de Git para almacenar artefactos personalizados y plantillas de Azure Resource Manager

Si desea [crear artefactos personalizados](devtest-lab-artifact-author.md) para las VM en el laboratorio, o [utilizar plantillas de Azure Resource Manager para crear un entorno de prueba personalizado](devtest-lab-create-environment-from-arm.md), también debe agregar un repositorio Git privado para los artefactos o plantillas de Azure Resource Manager que crea el equipo. El repositorio se puede hospedar en [GitHub](https://github.com) o en [Visual Studio Team Services (VSTS)](https://visualstudio.com).

Hemos preparado un [repositorio de Github de artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que puede implementar como está o personalizarlo para sus laboratorios. Al personalizar o crear un artefacto, no puede almacenarlo en el repositorio público: debe crear su propio repositorio privado. 

Cuando se crea una VM, puede guardar la plantilla de Azure Resource Manager, personalizarla si desea y, a continuación, usarla más adelante para crear fácilmente más VM. Debe crear su propio repositorio privado para almacenar las plantillas personalizadas de Azure Resource Manager.  

* Para obtener información sobre cómo crear un repositorio de GitHub, consulte [Entrenamiento militar de GitHub](https://help.github.com/categories/bootcamp/).
* Para obtener información sobre cómo crear un proyecto Team Services con un repositorio de Git, vea [Conexión con Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

En la siguiente captura de pantalla se muestra un ejemplo del aspecto que podría tener en GitHub un repositorio que contiene artefactos:   
![Repositorio de artefactos de GitHub de ejemplo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obtención de la información del repositorio y las credenciales
Para agregar un repositorio al laboratorio, primero debe obtener cierta información del repositorio. Las secciones siguientes le guiarán en el modo de obtener esta información para repositorios hospedados en GitHub y Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obtención de la dirección URL de clonación del repositorio de GitHub y el token de acceso personal
Para obtener la dirección URL de clonación del repositorio de GitHub y el token de acceso personal, siga estos pasos:

1. Vaya a la página principal del repositorio de GitHub que contiene las definiciones de artefacto o de la plantilla de Azure Resource Manager.
2. Seleccione **Clone or download**(Clonar o descargar).
3. Seleccione el botón para copiar la **dirección URL de clonación de HTTPS** en el portapapeles y guárdela para usarla más adelante.
4. Seleccione la imagen de perfil en la esquina superior derecha de GitHub y seleccione **Configuración**.
5. En el menú **Configuración personal** de la izquierda, seleccione **Tokens de acceso personal**.
6. Seleccione **Generar nuevo token**.
7. En la página **New personal access token (Nuevo token de acceso personal)** escriba la información que considere oportuno en **Token description (Descripción del token)**, acepte los elementos predeterminados en el **Select scopes (Seleccionar ámbitos)** y después elija **Generate Token (Generar token)**.
8. Guarde el token generado, ya que lo necesitará más adelante.
9. Ahora puede cerrar GitHub.   
10. Continúe con la sección [Conexión del laboratorio al repositorio](#connect-your-lab-to-the-repository) .

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Obtención de la dirección URL de clonación de Visual Studio Team Services y del token de acceso personal
Para obtener la dirección URL de clonación del repositorio de Visual Studio Team Services y el token de acceso personal, siga estos pasos:

1. Abra la página principal de la colección de equipo (por ejemplo, `https://contoso-web-team.visualstudio.com`) y después seleccione el proyecto.
2. En la página de inicio del proyecto, seleccione **Código**.
3. Para ver la dirección URL de clonación, en la página **Código** del proyecto, seleccione **Clonar**.
4. Guarde la dirección URL, ya que la necesitará más adelante en este tutorial.
5. Para crear un token de acceso personal, seleccione **Mi perfil** en el menú desplegable de la cuenta de usuario.
6. En la página de información de perfil, seleccione **Seguridad**.
7. En la pestaña **Seguridad**, seleccione **Agregar**.
8. En la página **Crear un token de acceso personal** :

   * Escriba la información que desee en **Descripción** para el token.
   * Seleccione **180 días** en la lista **Expira en**.
   * Elija **Todas las cuentas accesibles** en la lista **Cuentas**.
   * Elija la opción **Todos los ámbitos** .
   * Elija **Crear token**.
9. Cuando termine, el nuevo token aparecerá en la lista **Tokens de acceso personal** . Seleccione **token**y luego guarde el valor del token para usarlo más adelante.
10. Continúe con la sección [Conexión del laboratorio al repositorio](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Conexión del laboratorio al repositorio
1. Inicie sesión en [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista.
3. En la lista de laboratorios, seleccione el laboratorio que desee.   
4. En el panel izquierdo, seleccione **Configuration and policies** (Directivas y configuración).
5. En el área **Configuration and policies** (Directivas y configuración) del laboratorio, seleccione **Repositorios**.
6. En el área **Repositorios**, seleccione **+ Agregar**.

    ![Adición del botón de repositorio](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
7. En la segunda página **Repositorios**, especifique la información siguiente:

   * **Nombre** , escriba un nombre para el repositorio.
   * **URL de Git Clone**: escriba la dirección URL de clonación HTTPS de Git que copió anteriormente de GitHub o de Visual Studio Team Services.
   * **Rama** : escriba la rama para obtener las definiciones.
   * **Token de acceso personal** : especifique el token de acceso personal que obtuvo anteriormente de GitHub o de Visual Studio Team Services.
   * **Rutas de acceso de carpeta**: escriba al menos una ruta de acceso de carpeta con respecto a la dirección URL de clonación que contenga las definiciones del artefacto o de la plantilla de Azure Resource Manager. Cuando se especifique un subdirectorio, asegúrese de incluir la barra diagonal en la ruta de acceso de la carpeta.

     ![Área Repositorios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
8. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes
Después de haber creado el repositorio Git privado, puede hacer una o ambas de las acciones siguientes, dependiendo de sus necesidades:
* Almacenar los [artefactos personalizados](devtest-lab-artifact-author.md), que puede utilizar después para crear nuevas VM.
* [Crear entornos de varias VM y recursos de PaaS con plantillas de Azure Resource Manager](devtest-lab-create-environment-from-arm.md) y luego almacenar las plantillas en el repositorio privado.

Cuando se crea una máquina virtual, podrá comprobar que las plantillas o los artefactos se han agregado al repositorio de Git. Están disponibles inmediatamente en la lista de artefactos o plantillas, con el nombre del repositorio privado en la columna que especifica el origen. 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [How to troubleshoot failing Artifacts in AzureDevTestLabs](devtest-lab-troubleshoot-artifact-failure.md) (Cómo solucionar errores de artefactos en Azure DevTest Labs)
* [Join a VM to existing AD Domain using a resource manager template in Azure DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Unión de una máquina virtual al dominio de AD existente mediante la plantilla de Resource Manager en Azure DevTest Labs)

