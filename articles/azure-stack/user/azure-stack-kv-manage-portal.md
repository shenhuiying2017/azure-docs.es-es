---
title: "Administración de Key Vault en Azure Stack mediante el portal | Microsoft Docs"
description: Aprenda a administrar Key Vault en Azure Stack mediante el portal
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: d76a1e188c5a5bf008ac2fba9b43741a6a8d97b1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Administración de Key Vault en Azure Stack mediante el portal

Puede administrar KeyValut en Azure Stack mediante el portal de Azure Stack. En este artículo, encontrará ayuda para empezar a crear y administrar un almacén de claves en Azure Stack. 

## <a name="prerequisites"></a>Requisitos previos  

Debe suscribirse a una oferta que incluya el servicio Azure Key Vault.
 
## <a name="create-a-key-vault"></a>Creación de un Almacén de claves 

1. Inicie sesión en el [portal de usuario](https://portal.local.azurestack.external).  

2. En el panel, seleccione **Nuevo** > **Seguridad e identidad** > **Key Vault**.  

    ![Pantalla de Key Vault](media/azure-stack-kv-manage-portal/image1.png)  

3. En el panel **Crear Key Vault**, asigne un **Nombre** al almacén. Los nombres de almacén pueden contener solo caracteres alfanuméricos y el carácter especial de guion (-). No deben comenzar por un número.  

4. Elija una **Suscripción** de la lista de suscripciones disponibles. Todas las suscripciones que ofrece el servicio KeyVault se muestran en la lista desplegable.  

5. Seleccione un **Grupo de recursos** existente o cree uno.  

6. Seleccione el **Plan de tarifa**.  
    >[!NOTE]
    > Los almacenes de claves de Azure Stack Development Kit solo admiten las SKU **Estándar**.

7. Elija una de las **Directivas de acceso** existentes o cree una nueva. La directiva de acceso le permite conceder permisos para que un usuario, una aplicación o un grupo de seguridad realicen operaciones con este almacén.  

8. Si quiere, elija una **Directiva de acceso avanzado** para habilitar características, como el acceso a máquinas virtuales (VM) para la implementación, el acceso a Resource Manager para la implementación de plantillas y el acceso a Azure Disk Encryption para el cifrado de volúmenes. 
  
9.  Después de realizar la configuración, seleccione **Aceptar**y, a continuación, seleccione **Crear**. Esto inicia la implementación del almacén de claves. 

## <a name="manage-keys-and-secrets"></a>Administrar claves y secretos

Después de crear un almacén, siga estos pasos para crear y administrar claves y secretos en el almacén.

### <a name="create-a-key"></a>Crear una clave

1. Inicie sesión en el [portal de usuario](https://portal.local.azurestack.external).  

2. En el panel, Seleccione **Todos los recursos**, seleccione el almacén de claves que creó anteriormente y, a continuación, haga clic en el icono **Claves**.  

3. En el panel **Claves**, seleccione **Agregar**. 

4. En el panel **Crear una clave**, forme la lista de **Opciones** y elija el método que quiera usar para crear una clave. También puede **generar** una nueva clave, **cargar** una clave existente, o usar **Restaurar copia de seguridad** para seleccionar una copia de seguridad de una clave.  

5. Escriba un **Nombre** para la clave. El nombre de la clave puede contener solo caracteres alfanuméricos y el carácter especial de guion (-).  

6. Como alternativa, configure los valores de **Establecer la fecha de activación** y **Establecer fecha de expiración** para la clave.  

7. Seleccione **Crear** para iniciar la implementación.  

Después de que la clave se cree correctamente, puede seleccionarla en **Claves** y ver o modificar sus propiedades. La sección de propiedades contiene el **Identificador de clave**, que es un identificador uniforme de recursos o URI a través del cual las aplicaciones externas pueden tener acceso a esta clave. Para limitar las operaciones en esta clave, configure las opciones en **Operaciones permitidas**.

![URI de clave](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>Crear un secreto 

1. Inicie sesión en el [portal de usuario](https://portal.local.azurestack.external).  
2. En el panel, seleccione **Todos los recursos**, seleccione el almacén de claves que creó anteriormente y, a continuación, haga clic en el icono **Secretos**.  

3. En **Secretos**, seleccione **Agregar**.  

4. En **Crear un secreto**, en la lista de **Opciones de carga**, elija una opción para crear un secreto. Puede crear un secreto **Manualmente** especificando un valor para el secreto o cargar un **Certificado** de la máquina local.  

5. Escriba un **Nombre** para el secreto. El nombre del secreto puede contener solo caracteres alfanuméricos y el carácter especial de guion (-).  

6. Como alternativa, especifique el **Tipo de contenido** y configure los valores de **Establecer la fecha de activación** y **Establecer fecha de expiración** para el secreto.  

7. Seleccione **Crear** para iniciar la implementación.  

Después de que el secreto se cree correctamente, puede seleccionarlo en **Secretos** y ver o modificar sus propiedades. La sección de propiedades contiene un **Identificador secreto**, un URI a través del cual las aplicaciones externas pueden tener acceso a este secreto. 

![URI de secreto](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Pasos siguientes
* [Implementar una máquina virtual mediante la recuperación de la contraseña almacenada en Key Vault](azure-stack-kv-deploy-vm-with-secret.md) 
* [Implementación de una máquina virtual con un certificado almacenado en un almacén de claves](azure-stack-kv-push-secret-into-vm.md)     


