---
title: Administrar Key Vault en Azure Stack mediante PowerShell | Microsoft Docs
description: "Aprenda cómo administrar Key Vault en Azure Stack mediante PowerShell."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 41cbe1526368dd88fe98f92937c6ef2b65f24682
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-in-azure-stack-using-the-portal"></a>Administrar Key Vault en Azure Stack mediante el portal

Puede administrar KeyValut en Azure Stack mediante el portal de Azure Stack. En este artículo, encontrará ayuda para empezar a crear y administrar Key Vault en Azure Stack. 

## <a name="prerequisites"></a>Requisitos previos  

* Debe suscribirse a una oferta que incluya el servicio Key Vault.  
 
## <a name="create-a-key-vault"></a>Creación de un Almacén de claves 

1. Inicie sesión en el portal de usuarios (https://portal.local.azurestack.external).  

2. En el panel, haga clic en **Nuevo > Seguridad e identidad > Key Vault**.  

    ![Pantalla de KV](media/azure-stack-kv-manage-portal/image1.png)  

3. En la hoja **Crear Key Vault**, asigne un **Nombre** al almacén. El nombre del almacén puede contener solo caracteres alfanuméricos y el carácter especial de guion (-), y no debe comenzar con un número.  

4. Elija una **Suscripción** de la lista de suscripciones disponibles. Todas las suscripciones que ofrece el servicio KeyVault se muestran en la lista desplegable.  

5. Seleccione un **Grupo de recursos** existente o cree uno.  

6. Seleccione el **Plan de tarifa**.  
    >[!NOTE]
    > Los almacenes de claves de Azure Stack Development Kit admiten la SKU **Estándar** únicamente.

7. Elija **Directivas de acceso** existentes o cree una nueva. La directiva de acceso le permite conceder permisos para que un usuario, una aplicación o un grupo de seguridad realicen operaciones con este almacén.  

8. Si quiere, elija una **Directiva de acceso avanzado** para habilitar características, como el acceso a Virtual Machines para la implementación, el acceso a Resource Manager para la implementación de plantillas y el acceso a Azure Disk Encryption para el cifrado de volúmenes. 
  
9.  Después de configurar las opciones, haga clic en **Aceptar** y, luego, en **Crear**. Esto inicia la implementación del almacén de claves. 

## <a name="manage-keys-and-secrets"></a>Administrar claves y secretos

Después de crear un almacén, siga estos pasos para crear y administrar claves y secretos en el almacén.

## <a name="create-a-key"></a>Crear una clave

1. Inicie sesión en el portal de usuarios (https://portal.local.azurestack.external).  

2. En el panel, haga clic en **Todos los recursos** > seleccione el almacén de claves que creó anteriormente > haga clic en el icono **Claves**.  

3. En la hoja **Claves**, haga clic en **Agregar**. 

4. En la hoja **Crear una clave**, forme la lista de **Opciones** y elija el método que quiera usar para crear una clave. Puede **Generar** una nueva clave, **Cargar** una clave existente o **Restaurar copia de seguridad** de una clave.  

5. Escriba un **Nombre** para la clave. El nombre de la clave puede contener solo caracteres alfanuméricos y el carácter especial de guion (-).  

6. Como alternativa, configure los valores de **Establecer la fecha de activación** y **Establecer fecha de expiración** para la clave.  

7. Haga clic en **Crear** para iniciar la implementación.  

Después de que la clave se cree correctamente, puede seleccionarla en la hoja **Claves** y ver o modificar sus propiedades. La sección de propiedades contiene el **Identificador de clave**, un URI a través del cual las aplicaciones externas pueden tener acceso a esta clave. Para limitar las operaciones en esta clave, configure las opciones en **Operaciones permitidas**.

![URI de clave](media/azure-stack-kv-manage-portal/image4.png)  

## <a name="create-a-secret"></a>Crear un secreto 

1. Inicie sesión en el portal de usuarios (https://portal.local.azurestack.external).  
2. En el panel, haga clic en **Todos los recursos** > seleccione el almacén de claves que creó anteriormente > haga clic en el icono **Secretos**.  

3. En la hoja **Secretos**, haga clic en **Agregar**.  

4. En la hoja **Crear un secreto**, en la lista de **Opciones de carga**, elija una opción para crear un secreto. Puede crear un secreto **Manualmente** especificando un valor para el secreto o cargar un **Certificado** del equipo local.  

5. Escriba un **Nombre** para el secreto. El nombre del secreto puede contener solo caracteres alfanuméricos y el carácter especial de guion (-).  

6. Como alternativa, especifique el **Tipo de contenido** y configure los valores de **Establecer la fecha de activación** y **Establecer fecha de expiración** para el secreto.  

7. Haga clic en Crear para iniciar la implementación.  

Después de que el secreto se cree correctamente, puede seleccionarlo en la hoja **Secretos** y ver o modificar sus propiedades. La sección de propiedades contiene el **Identificador secreto**, un URI a través del cual las aplicaciones externas pueden tener acceso a este secreto. 

![URI de secreto](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Pasos siguientes
* [Implementar una VM mediante la recuperación de la contraseña almacenada en un almacén de claves](azure-stack-kv-deploy-vm-with-secret.md)  
* [Implementar una VM con un certificado almacenado en un almacén de claves](azure-stack-kv-push-secret-into-vm.md)     


