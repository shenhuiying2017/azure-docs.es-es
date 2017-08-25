---
title: "Creación de un registro privado de Docker: Azure Portal | Microsoft Docs"
description: "Introducción a la creación y la administración de registros de contenedor privados de Docker mediante Azure Portal"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 7fbbb56d775ee96c9a44363a4e41d4fc3c630582
ms.contentlocale: es-es
ms.lasthandoff: 08/21/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Creación de un registro de contenedor privado de Docker con Azure Portal
Use Azure Portal para crear un registro de contenedor y administrar su configuración. También puede crear y administrar registros de contenedor mediante los [comandos de la CLI de Azure 2.0](container-registry-get-started-azure-cli.md), [Azure PowerShell](container-registry-get-started-powershell.md) o mediante programación con la [API de REST](https://go.microsoft.com/fwlink/p/?linkid=834376) de Container Registry.

Para más información sobre el entorno y los conceptos, consulte [la información general](container-registry-intro.md).

## <a name="create-a-container-registry"></a>Creación de un registro de contenedor
1. En [Azure Portal](https://portal.azure.com), haga clic **+ Nuevo**.
2. Busque en el Marketplace **Azure Container Registry**.
3. Seleccione **Azure Container Registry**, con el publicador **Microsoft**.
    ![Servicio Container Registry en Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Haga clic en **Crear**. Aparece la hoja **Azure Container Registry**.

    ![Configuración de Container Registry](./media/container-registry-get-started-portal/container-registry-settings.png)
5. En la hoja **Azure Container Registry**, escriba la siguiente información. Cuando haya terminado, haga clic en **Crear**.

    a. **Nombre del registro**: un nombre de dominio de nivel superior único global para el registro específico. En este ejemplo, el nombre del registro es *myRegistry01*, pero puede sustituirlo por un nombre único de su elección. El nombre puede contener solo letras y números.

    b. **Grupo de recursos**: seleccione un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) existente o escriba el nombre para crear uno.

    c. **Ubicación**: seleccione una ubicación para el centro de datos de Azure en la que el servicio esté [disponible](https://azure.microsoft.com/regions/services/) como, por ejemplo, **centro-sur de EE. UU**.

    d. **Usuario administrador**: si lo desea, habilite un usuario administrador para acceder al registro. Puede cambiar esta configuración después de crear el registro.

      > [!IMPORTANT]
      > Además de proporcionar acceso a través de una cuenta de usuario de administrador, los registros de contenedor admiten la autenticación respaldada por entidades de servicio de Azure Active Directory. Para más información y otras consideraciones, consulte [Authenticate with the container registry](container-registry-authentication.md) (Autenticación con el registro de contenedor).
      >

    e. **Cuenta de almacenamiento**: use la configuración predeterminada para crear una [cuenta de almacenamiento](../storage/common/storage-introduction.md) o seleccione una cuenta de almacenamiento existente en la misma ubicación. Premium Storage no se admite actualmente.

## <a name="manage-registry-settings"></a>Administración de la configuración del registro
Después de crear el registro, busque la configuración de este iniciando la hoja **Registros de contenedor** del portal. Por ejemplo, necesitará la configuración para iniciar sesión en el registro, o puede que desee habilitar o deshabilitar el usuario administrador.

1. En la hoja **Registros de contenedor**, haga clic en el nombre del registro.

    ![Hoja Registro de contenedor](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Para administrar la configuración de acceso, haga clic en **Clave de acceso**.

    ![Acceso al registro de contenedor](./media/container-registry-get-started-portal/container-registry-access.png)
3. Tenga en cuenta la siguiente configuración:

   * **Servidor de inicio de sesión**: el nombre completo que usa para iniciar sesión en el registro. En este ejemplo, es `myregistry01.azurecr.io`.
   * **Usuario administrador**: elija habilitar o deshabilitar la cuenta de usuario administrador del registro.
   * **Nombre de usuario** y **contraseña**: las credenciales de la cuenta de usuario administrador (si está habilitado) que puede usar para iniciar sesión en el registro. Si lo desea, puede volver a generar la contraseña. Se crean dos contraseñas para que pueda mantener las conexiones en el registro mediante una contraseña mientras vuelve a generar la contraseña de la otra. Para autenticarse con una entidad de servicio en su lugar, consulte [Autenticación con un registro de contenedor privado de Docker](container-registry-authentication.md).

## <a name="next-steps"></a>Pasos siguientes
* [Insertar la primera imagen mediante la CLI de Docker](container-registry-get-started-docker-cli.md)

