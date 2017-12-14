---
title: "Uso de Managed Service Identity (MSI) en una máquina virtual Linux para acceder a Azure Data Lake Store"
description: "En este tutorial se muestra cómo usar Managed Service Identity (MSI) en una máquina virtual Linux para acceder a Azure Data Lake Store."
services: active-directory
documentationcenter: 
author: skwan
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: 98ff833fe541e1207a87421d54a8ce2dfc17cda1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Uso de Managed Service Identity (MSI) en una máquina virtual Linux para acceder a Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

En este tutorial se muestra cómo usar Managed Service Identity (MSI) en una máquina virtual Linux para acceder a Azure Data Lake Store. Las identidades de MSI son administradas automáticamente por Azure y le permiten autenticar los servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código. Aprenderá a:

> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Linux 
> * Conceder a una máquina virtual acceso a Azure Data Lake Store
> * Obtener un token de acceso mediante la identidad de máquina virtual y usarlo para acceder a Azure Data Lake Store

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Linux en un nuevo grupo de recursos

En este tutorial, vamos a crear una nueva máquina virtual Linux. También puede habilitar MSI en una máquina virtual existente.

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute**y, después, seleccione **Ubuntu Server 16.04 LTS**.
3. Escriba la información de la máquina virtual. En **Tipo de autenticación**, seleccione **Clave pública SSH** o **Contraseña**. Las credenciales creadas le permiten iniciar sesión en la máquina virtual.

   ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Elija una **Suscripción** para la máquina virtual en la lista desplegable.
5. Para seleccionar un nuevo **Grupo de recursos** en el que desearía crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro de tipo de disco admitido. En la hoja de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que incluir las credenciales en el código. Al habilitar MSI suceden dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita MSI en Azure Resource Manager.  

1. Seleccione la **máquina virtual** en la que desee habilitar MSI.
2. En la barra de navegación de la izquierda, haga clic en **Configuración**.
3. Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No.
4. No olvide hacer clic en **Guardar** para guardar la configuración.

   ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que están en esta **máquina virtual Linux**, haga clic en **Extensiones**. Si MSI está habilitado, **ManagedIdentityExtensionforLinux** aparece en la lista.

   ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Concesión a una máquina virtual del acceso a Azure Data Lake Store

Ahora puede conceder a una máquina virtual el acceso a los archivos y las carpetas de una instancia de Azure Data Lake Store.  En este paso, puede usar una instancia ya existente de Data Lake Store o crear una nueva.  Para crear una nueva instancia de Data Lake Store mediante Azure Portal, siga esta [guía de inicio rápido de Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). También se pueden encontrar guías de inicio rápido que utilizan la CLI de Azure y Azure PowerShell en la [documentación de Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

En Data Lake Store, cree una nueva carpeta y conceda permisos de MSI a la máquina virtual para leer, escribir y ejecutar archivos en esa carpeta:

1. En Azure Portal, haga clic en **Data Lake Store** en el panel de navegación izquierdo.
2. Haga clic en la instancia de Data Lake Store que quiere usar en este tutorial.
3. Haga clic en **Explorador de datos** en la barra de comandos.
4. Se selecciona la carpeta raíz de Data Lake Store.  Haga clic en **Acceso** en la barra de comandos.
5. Haga clic en **Agregar**.  En el campo **Seleccionar**, escriba el nombre de la máquina virtual, por ejemplo **DevTestVM**.  Haga clic para seleccionar la máquina virtual de los resultados de búsqueda y luego haga clic en **Seleccionar**.
6. Haga clic en **Seleccionar permisos**.  Seleccione **Lectura** y **Ejecutar**, agréguelos a **Esta carpeta** y agréguelos como **un solo permiso de acceso**.  Haga clic en **Aceptar**.  El permiso se debe haber agregado correctamente.
7. Cierre la hoja **Acceso**.
8. En este tutorial, cree una nueva carpeta.  Haga clic en **Nueva carpeta** en la barra de comandos y asígnele un nombre, por ejemplo **TestFolder**.  Haga clic en **Aceptar**.
9. Haga clic en la carpeta que ha creado y, luego, en **Acceso** en la barra de comandos.
10. Igual que en el paso 5, haga clic en **Agregar**, en el campo **Seleccionar** escriba el nombre de su máquina virtual, selecciónelo y haga clic en **Seleccionar**.
11. Igual que en el paso 6, haga clic en **Seleccionar permisos**, seleccione **Lectura**, **Escritura** y **Ejecutar**, agréguelos a **Esta carpeta** y agréguelos como **Una entrada de permiso de acceso y una entrada de permiso predeterminado**.  Haga clic en **Aceptar**.  El permiso se debe haber agregado correctamente.

La identidad MSI de máquina virtual puede realizar ahora todas las operaciones en los archivos de la carpeta que ha creado.  Para más información sobre cómo administrar el acceso a Data Lake Store, lea este artículo sobre el [control de acceso en Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Obtención de un token de acceso mediante la identidad MSI de máquina virtual y su uso para llamar al sistema de archivos de Azure Data Lake Store

Azure Data Lake Store admite de forma nativa la autenticación de Azure AD, por lo que puede aceptar directamente tokens de acceso obtenidos mediante MSI.  Para autenticarse en el sistema de archivos de Data Lake Store, se envía un token de acceso emitido por Azure AD al punto de conexión del sistema de archivos de Data Lake Store, en un encabezado de autorización con el formato "Bearer \<ACCESS_TOKEN_VALUE\>".  Para más información sobre la compatibilidad de Data Lake Store con la autenticación de Azure AD, lea [Autenticación con Data Lake Store mediante Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

En este tutorial, se autenticará en la API de REST del sistema de archivos de Data Lake Store mediante CURL para realizar solicitudes de REST.

> [!NOTE]
> Los SDK de cliente del sistema de archivos de Data Lake Store no admiten aún Managed Service Identity.  Este tutorial se actualizará cuando se agregue esta compatibilidad al SDK.

Para completar estos pasos, necesitará un cliente SSH. Si usa Windows, puede usar el cliente SSH en el [Subsistema de Windows para Linux](https://msdn.microsoft.com/commandline/wsl/about). Si necesita ayuda para configurar las claves del cliente de SSH, consulte [Uso de SSH con Windows en Azure](../virtual-machines/linux/ssh-from-windows.md) o [Creación y uso de un par de claves SSH pública y privada para máquinas virtuales Linux en Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. En el portal, vaya a la máquina virtual Linux y, en **Información general**, haga clic en **Conectar**.  
2. **Conéctese** a la máquina virtual con el cliente SSH que elija. 
3. En la ventana del terminal, con CURL, realice una solicitud al punto de conexión local de MSI para obtener un token de acceso para el sistema de archivos de Data Lake Store.  El identificador de recurso de Data Lake Store es "https://datalake.azure.net/".  Es importante incluir la barra diagonal final en el identificador de recurso.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Una respuesta correcta devuelve el token de acceso que se utiliza para autenticarse en Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Con CURL, realice una solicitud al punto de conexión de REST del sistema de archivos de Data Lake Store para mostrar las carpetas que contiene la carpeta raíz.  Se trata de una manera sencilla de comprobar que todo está configurado correctamente.  Copie el valor del token de acceso del paso anterior.  Es importante que la cadena "Bearer" en el encabezado de autorización tenga la "B" en mayúscula.  Puede encontrar el nombre de su instancia de Data Lake Store en la sección **Información general** de la hoja de Data Lake Store de Azure Portal.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Una respuesta correcta tiene el siguiente aspecto:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Ahora puede intentar cargar un archivo en su instancia de Data Lake Store.  En primer lugar, cree un archivo para cargarlo.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Con CURL, realice una solicitud al punto de conexión de RES del sistema de archivos de Data Lake Store para cargar el archivo en la carpeta que creó anteriormente.  La carga implica una redirección y CURL sigue la redirección automáticamente. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Una respuesta correcta tiene el siguiente aspecto:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Mediante otras API del sistema de archivos de Data Lake Store, puede anexar o descargar archivos, entre otras muchas cosas.

¡Enhorabuena!  Se ha autenticado en el sistema de archivos de Data Lake Store mediante una identidad MSI de máquina virtual.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](../active-directory/msi-overview.md) (Introducción a Managed Service Identity).
- Para las operaciones de administración, Data Lake Store usa Azure Resource Manager.  Para más información sobre el uso de una identidad MSI de máquina virtual para autenticarse en Resource Manager, lea [Uso de Managed Service Identity (MSI) con una máquina virtual Linux para tener acceso Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Aprenda más sobre [Autenticación con Data Lake Store mediante Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Aprenda más sobre [Operaciones de sistema de archivos en Azure Data Lake Store con la API de REST](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) o las [API del sistema de archivos de WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Aprenda más sobre [Control de acceso en Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.