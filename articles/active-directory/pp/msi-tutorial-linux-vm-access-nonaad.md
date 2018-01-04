---
title: "Uso de una identidad MSI de máquina virtual Linux para acceder a Azure Key Vault"
description: "Tutorial que le guía a través del proceso para usar Managed Service Identity (MSI) con una máquina virtual Linux para acceder a Azure Resource Manager."
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3531d6e406e7cf44daaaf03c73c93ec3662f108c
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Uso de Managed Service Identity (MSI) en una máquina virtual Linux para acceder a Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

En este tutorial se muestra cómo habilitar Managed Service Identity (MSI) para una máquina virtual Linux y, a continuación, usar esa identidad para acceder a Azure Key Vault. Actuando como un arranque, Key Vault hace posible que la aplicación de cliente use el secreto para tener acceso a recursos que no están protegidos por Azure Active Directory (AD). Las identidades de MSI son administradas automáticamente por Azure y le permiten autenticar los servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código. 

Aprenderá a:

> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Linux 
> * Concesión de acceso a la máquina virtual a un secreto almacenado en un almacén de claves 
> * Obtener un token de acceso mediante la identidad de máquina virtual y usarlo para recuperar el secreto del almacén de claves 
 
## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure
Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Linux en un nuevo grupo de recursos

En este tutorial, vamos a crear una nueva máquina virtual Linux. También puede habilitar MSI en una máquina virtual existente.

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute**y, después, seleccione **Ubuntu Server 16.04 LTS**.
3. Escriba la información de la máquina virtual. En **Tipo de autenticación**, seleccione **Clave pública SSH** o **Contraseña**. Las credenciales creadas le permiten iniciar sesión en la máquina virtual.

    ![Texto alternativo de imagen](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Elija una **Suscripción** para la máquina virtual en la lista desplegable.
5. Para seleccionar un nuevo **Grupo de recursos** en el que desearía crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro de tipo de disco admitido. En la página de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. Al habilitar MSI, se hacen dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita Managed Service Identity para la máquina virtual.  

1. Seleccione la **máquina virtual** en la que desee habilitar MSI.
2. En la barra de navegación de la izquierda, haga clic en **Configuración**.
3. Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No.
4. No olvide hacer clic en **Guardar** para guardar la configuración.

    ![Texto alternativo de imagen](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que están en esta **máquina virtual Linux**, haga clic en **Extensiones**. Si MSI está habilitado, **ManagedIdentityExtensionforLinux** aparece en la lista.

    ![Texto alternativo de imagen](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Concesión de acceso a la máquina virtual a un secreto almacenado en un almacén de claves  

Con MSI, el código puede obtener tokens de acceso para autenticarse en los recursos que admitan la autenticación de Azure Active Directory. Sin embargo, no todos los servicios de Azure admiten la autenticación de Azure AD. Para usar MSI con esos servicios, almacene las credenciales del servicio en Azure Key Vault y use MSI para tener acceso a Key Vault y recuperar las credenciales. 

En primer lugar, es necesario crear un almacén de claves y conceder a la identidad de la máquina virtual acceso al almacén de claves.   

1. En la parte superior de la barra de navegación izquierda, seleccione **+ Nuevo**, luego, **Seguridad e identidad** y, finalmente, **Key Vault**.  
2. Proporcione un **nombre** para el nuevo almacén de claves. 
3. Busque el almacén de claves en la misma suscripción y el mismo grupo de recursos que la máquina virtual que creó anteriormente. 
4. Seleccione **Directivas de acceso** y haga clic en **Agregar nueva**. 
5. En Configurar a partir de plantilla, seleccione **Administración de secretos**. 
6. Elija **Seleccionar la entidad de seguridad** y, en el campo de búsqueda, escriba el nombre de la máquina virtual que creó anteriormente.  Seleccione la máquina virtual de la lista de resultados y haga clic en **Seleccionar**. 
7. Haga clic en **Aceptar** para terminar de agregar la nueva directiva de acceso, y en **Aceptar** para finalizar la selección de la directiva de acceso. 
8. Haga clic en **Crear** para terminar de crear el almacén de claves. 

    ![Texto alternativo de imagen](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

A continuación, agregue un secreto al almacén de claves, de forma que más adelante pueda recuperarlo mediante código que se ejecuta en la máquina virtual: 

1. Seleccione **Todos los recursos** y busque y seleccione el almacén de claves que acaba de crear. 
2. Seleccione **Secretos** y haga clic en **Agregar**. 
3. En **Opciones de carga**, seleccione **Manual**. 
4. Escriba un nombre y un valor para el secreto.  El valor puede ser cualquiera de su elección. 
5. Deje la fecha de activación y la fecha de expiración y deje la opción **Habilitado** en **Sí**. 
6. Haga clic en **Crear** para crear el secreto. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Obtención de un token de acceso mediante la identidad de máquina virtual y usarlo para recuperar el secreto de Key Vault  

Para completar estos pasos, necesitará un cliente SSH.  Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Si necesita ayuda para configurar las claves del cliente de SSH, consulte [Uso de SSH con Windows en Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) o [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. En el portal, vaya a la máquina virtual Linux y, en **Información general**, haga clic en **Conectar**. 
2. **Conéctese** a la máquina virtual con el cliente SSH que elija. 
3. En la ventana del terminal, con CURL, realice una solicitud al punto de conexión local de MSI para obtener un token de acceso para Azure Key Vault.  
 
    La solicitud CURL para el token de acceso está debajo.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    La respuesta incluye el token de acceso que necesita para acceder a Resource Manager. 
    
    Respuesta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Puede usar este token de acceso para autenticarse en Azure Key Vault.  La siguiente solicitud CURL muestra cómo leer un secreto desde el almacén de claves usando CURL y la API de REST de Key Vault.  Necesitará la dirección URL de su almacén de claves, que se encuentra en la sección de **Información esencial** de la página **Introducción** del almacén de claves.  También necesitará el token de acceso que obtuvo en la llamada anterior. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    La respuesta tendrá un aspecto similar al siguiente: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Cuando haya recuperado el secreto del almacén de claves, podrá usarlo para autenticarse en un servicio que requiere un nombre y una contraseña.


## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.




