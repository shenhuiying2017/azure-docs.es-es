---
title: "Uso de Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure Data Lake Store"
description: "En este tutorial se muestra cómo usar Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure Data Lake Store."
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
ms.openlocfilehash: ad9b92f11571b013f371eb36c0676afb3e9e5213
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Uso de Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

En este tutorial se muestra cómo usar Managed Service Identity (MSI) en una máquina virtual (VM) Windows para acceder a Azure Data Lake Store. Las identidades de MSI son administradas automáticamente por Azure y le permiten autenticar los servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código. Aprenderá a:

> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Windows 
> * Conceder a una máquina virtual acceso a Azure Data Lake Store
> * Obtener un token de acceso mediante la identidad de máquina virtual y usarlo para acceder a Azure Data Lake Store

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows.  También puede habilitar MSI en una máquina virtual existente.

1. Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2. Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**. 
3. Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4. Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5. Para seleccionar un nuevo **grupo de recursos** en el que crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6. Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la página de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

   ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual 

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que poner las credenciales en el código. Al habilitar MSI se indica a Azure que cree una identidad administrada para la máquina virtual. Al habilitar MSI suceden dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita MSI en Azure Resource Manager.

1. Seleccione la **máquina virtual** en la que quiera habilitar MSI.  
2. En la barra de navegación de la izquierda, haga clic en **Configuración**. 
3. Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No. 
4. No olvide hacer clic en **Guardar** para guardar la configuración.  
   ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que están en esta máquina virtual, haga clic en **Extensiones**. Si MSI está habilitado, **ManagedIdentityExtensionforWindows** aparece en la lista.

   ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

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

Azure Data Lake Store admite de forma nativa la autenticación de Azure AD, por lo que puede aceptar directamente tokens de acceso obtenidos mediante MSI.  Para autenticarse en el sistema de archivos de Data Lake Store, se envía un token de acceso emitido por Azure AD al punto de conexión del sistema de archivos de Data Lake Store, en un encabezado de autorización con el formato "Bearer <ACCESS_TOKEN_VALUE>".  Para más información sobre la compatibilidad de Data Lake Store con la autenticación de Azure AD, lea [Autenticación con Data Lake Store mediante Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

> [!NOTE]
> Los SDK de cliente del sistema de archivos de Data Lake Store no admiten aún Managed Service Identity.  Este tutorial se actualizará cuando se agregue esta compatibilidad al SDK.

En este tutorial, se autenticará en la API de REST del sistema de archivos de Data Lake Store mediante PowerShell para realizar solicitudes de REST. Para usar la identidad MSI de máquina virtual en la autenticación, debe realizar las solicitudes desde la máquina virtual.

1. En el portal, vaya a **Virtual Machines**, vaya a la máquina virtual Windows, y en **Información general**, haga clic en **Conectar**.
2. Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la máquina virtual Windows. 
3. Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra **PowerShell** en la sesión remota. 
4. Mediante el comando `Invoke-WebRequest` de PowerShell, realice una solicitud al punto de conexión de MSI local para obtener un token de acceso para Azure Data Lake Store.  El identificador de recurso de Data Lake Store es "https://datalake.azure.net/".  Data Lake busca una coincidencia exacta en el identificador del recurso y la barra diagonal final es importante.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Convierta la respuesta de un objeto JSON a un objeto de PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extraiga el token de acceso de la respuesta.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Con el comando "Invoke-WebRequest" de PowerShell, realice una solicitud a su punto de conexión de REST de Data Lake Store para mostrar las carpetas que contiene la carpeta raíz.  Se trata de una manera sencilla de comprobar que todo está configurado correctamente.  Es importante que la cadena "Bearer" en el encabezado de autorización tenga la "B" en mayúscula.  Puede encontrar el nombre de su instancia de Data Lake Store en la sección **Información general** de la hoja de Data Lake Store de Azure Portal.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Una respuesta correcta tiene el siguiente aspecto:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Ahora puede intentar cargar un archivo en su instancia de Data Lake Store.  En primer lugar, cree un archivo para cargarlo.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Con el comando `Invoke-WebRequest` de PowerShell, realice una solicitud al punto de conexión de REST de Data Lake Store para cargar el archivo en la carpeta que creó anteriormente.  Esta solicitud implica dos pasos.  En el primer paso, se realiza una solicitud y se obtiene un redirección al lugar donde se debe cargar el archivo.  En el segundo paso, se carga realmente el archivo.  No olvide configurar el nombre de la carpeta y el archivo correctamente si ha usado valores diferentes a los que se muestran en este tutorial. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Si examina el valor de `$HdfsRedirectResponse`, debería ser parecido a la siguiente respuesta:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Para finalizar la carga, envíe una solicitud al punto de conexión de redirección:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Una respuesta correcta se parece a esta:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
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