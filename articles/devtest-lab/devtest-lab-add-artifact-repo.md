---
title: "Adición de un repositorio de Git a un laboratorio en Azure DevTest Labs | Microsoft Docs"
description: Aprenda a agregar un repositorio Git de GitHub o Visual Studio Team Services para el origen de artefactos personalizados en Azure DevTest Labs.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-craic
ms.openlocfilehash: 345d095762b75b7a177bf4c6e7a07360a9c14068
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Adición de un repositorio de Git para almacenar artefactos personalizados y plantillas de Resource Manager

También puede [crear artefactos personalizados](devtest-lab-artifact-author.md) para las máquinas virtuales en el laboratorio, o [utilizar plantillas de Azure Resource Manager para crear un entorno de prueba personalizado](devtest-lab-create-environment-from-arm.md). Debe agregar un repositorio Git privado para los artefactos o plantillas de Resource Manager que crea su equipo. El repositorio se puede hospedar en [GitHub](https://github.com) o en [Visual Studio Team Services](https://visualstudio.com).

Ofrecemos un [repositorio de GitHub de artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que puede implementar como está o personalizarlo para sus laboratorios. Al personalizar o crear un artefacto, no se puede almacenar el artefacto en el repositorio público. Debe crear su propio repositorio privado para los artefactos personalizados y para los artefactos que cree. 

Cuando crea una máquina virtual, puede guardar la plantilla de Resource Manager, personalizarla si lo desea y usarla más adelante para crear más máquinas virtuales. Debe crear su propio repositorio privado para almacenar las plantillas personalizadas de Resource Manager.  

* Para obtener información sobre cómo crear un repositorio de GitHub, consulte [Entrenamiento militar de GitHub](https://help.github.com/categories/bootcamp/).
* Para aprender a crear un proyecto Team Services con un repositorio de Git, vea [Conexión con Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

La siguiente ilustración es un ejemplo del aspecto que podría tener en GitHub un repositorio que contiene artefactos:  

![Repositorio de artefactos de GitHub de ejemplo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obtención de la información del repositorio y las credenciales
Para agregar un repositorio al laboratorio, obtenga cierta información clave del repositorio. En las secciones siguientes, se describe el modo de obtener la información requerida para los repositorios hospedados en GitHub y Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obtención de la dirección URL de clonación del repositorio de GitHub y el token de acceso personal

1. Vaya a la página principal del repositorio de GitHub que contiene las definiciones de artefacto o de la plantilla de Azure Resource Manager.
2. Seleccione **Clone or download**(Clonar o descargar).
3. Para copiar la dirección URL en el Portapapeles, seleccione el botón **Dirección URL de clonación de HTTPS**. Guarde la dirección URL para su uso posterior.
4. En la esquina superior derecha de GitHub, seleccione la imagen del perfil y, después, seleccione **Configuración**.
5. En el menú **Configuración personal** de la izquierda, seleccione **Tokens de acceso personal**.
6. Seleccione **Generar nuevo token**.
7. En la página **New personal access token** (Nuevo token de acceso personal), bajo **Descripción de token** escriba una descripción. Acepte los elementos predeterminados en **Seleccionar ámbitos** y, a continuación, seleccione **Generar token**.
8. Guarde el token generado. Lo usará más adelante.
9. Cierre GitHub.   
10. Continúe con la sección [Conexión del laboratorio al repositorio](#connect-your-lab-to-the-repository) .

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Obtención de la dirección URL de clonación de Visual Studio Team Services y del token de acceso personal

1. Abra la página principal de la colección de equipo (por ejemplo, https://contoso-web-team.visualstudio.com) y después seleccione el proyecto.
2. En la página de inicio del proyecto, seleccione **Código**.
3. Para ver la dirección URL de clonación, en la página **Código** del proyecto, seleccione **Clonar**.
4. Guarde la dirección URL. La usará más adelante.
5. Para crear un token de acceso personal, en el menú desplegable de la cuenta de usuario, seleccione **Mi perfil**.
6. En la página de información de perfil, seleccione **Seguridad**.
7. En la pestaña **Seguridad**, seleccione **Agregar**.
8. En la página **Crear un token de acceso personal**:
   1. Escriba la información que desee en **Descripción** para el token.
   2. En la lista **Expira en**, seleccione **180 días**.
   3. En la lista **Cuentas accesibles**, seleccione **Todas las cuentas accesibles**.
   4. Seleccione la opción **Todos los ámbitos**.
   5. Seleccione **Crear token**.
9. El nuevo token aparece en la lista **Tokens de acceso personal** . Seleccione **token**y luego guarde el valor del token para usarlo más adelante.
10. Continúe con la sección [Conexión del laboratorio al repositorio](#connect-your-lab-to-the-repository) .

## <a name="connect-your-lab-to-the-repository"></a>Conexión del laboratorio al repositorio
1. Inicie sesión en el [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Seleccione **Más servicios** y, luego, **DevTest Labs** en la lista de servicios.
3. En la lista de laboratorios, seleccione el suyo. 
4. Seleccione **Configuration and policies** (Directivas y configuración)  > **Repositorios** > **+ Agregar**.

    ![Botón Agregar repositorio](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. En la segunda página **Repositorios**, especifique la información siguiente:
  1. **Nombre**. Escriba un nombre para el repositorio.
  2. **URL de clonación de Git**. Escriba la dirección URL de clonación HTTPS de Git que copió anteriormente de GitHub o de Visual Studio Team Services.
  3. **Rama**. Escriba la rama para obtener las definiciones.
  4. **Token de acceso personal**. Especifique el token de acceso personal que obtuvo anteriormente de GitHub o de Visual Studio Team Services.
  5. **Rutas de acceso de carpeta**. Escriba al menos una ruta de acceso de carpeta con respecto a la dirección URL de clonación que contenga las definiciones del artefacto o de la plantilla de Azure Resource Manager. Cuando especifique un subdirectorio, asegúrese de incluir la barra diagonal en la ruta de acceso de la carpeta.

     ![Área Repositorios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Seleccione **Guardar**.

### <a name="related-blog-posts"></a>Entradas blogs relacionadas
* [Solución de problemas de errores de artefactos en DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Join a VM to existing AD Domain using a resource manager template in DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Unión de una máquina virtual al dominio de AD existente mediante la plantilla de Resource Manager en DevTest Labs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>pasos siguientes
Después de haber creado el repositorio Git privado, puede hacer una o ambas de las acciones siguientes, dependiendo de sus necesidades:
* Almacene sus [artefactos personalizados](devtest-lab-artifact-author.md). Puede usarlos más tarde para crear nuevas máquinas virtuales.
* [Creación de entornos con varias máquinas virtuales y recursos de PaaS mediante plantillas de Resource Manager](devtest-lab-create-environment-from-arm.md). A continuación, puede almacenar las plantillas en el repositorio privado.

Cuando se crea una máquina virtual, podrá comprobar que las plantillas o los artefactos se han agregado al repositorio de Git. Están inmediatamente disponibles en la lista de artefactos o plantillas. El nombre del repositorio privado se muestra en la columna que especifica el origen. 
