---
title: "Uso de Managed Service Identity (MSI) de máquina virtual Windows para acceder a Azure SQL"
description: "Tutorial que indica cómo usar Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure SQL."
services: active-directory
documentationcenter: 
author: skwan
manager: mtillman
editor: bryanla
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: dfa5c75c803be0cda05b2906c3615300ad84bca6
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-sql"></a>Uso de Managed Service Identity (MSI) en una máquina virtual Windows para acceder a Azure SQL

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

En este tutorial se muestra cómo usar Managed Service Identity (MSI) para acceder a un servidor SQL de Azure. Las identidades de MSI son administradas automáticamente por Azure y le permiten autenticar los servicios que admiten la autenticación de Azure AD sin necesidad de insertar credenciales en el código. Aprenderá a:

> [!div class="checklist"]
> * Habilitar MSI en una máquina virtual Windows 
> * Conceder a una máquina virtual el acceso a un servidor de Azure SQL
> * Obtener un token de acceso mediante la identidad de máquina virtual y usarla para consultar un servidor SQL de Azure

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Creación de una máquina virtual Windows en un nuevo grupo de recursos

En este tutorial, se crea una nueva máquina virtual Windows.  También puede habilitar MSI en una máquina virtual existente.

1.  Haga clic en el botón **Nuevo** de la esquina superior izquierda de Azure Portal.
2.  Seleccione **Compute**y, después, seleccione **Windows Server 2016 Datacenter**. 
3.  Escriba la información de la máquina virtual. El **nombre de usuario** y la **contraseña** creados aquí son las credenciales que se usan para iniciar sesión en la máquina virtual.
4.  Elija la **suscripción** adecuada de la máquina virtual en la lista desplegable.
5.  Para seleccionar un nuevo **grupo de recursos** en el que crear la máquina virtual, elija **Crear nuevo**. Cuando haya terminado, haga clic en **Aceptar**.
6.  Seleccione el tamaño de la máquina virtual. Para ver más tamaños, seleccione **Ver todo** o cambie el filtro **Supported disk type** (Tipo de disco admitido). En la página de configuración, conserve los valores predeterminados y haga clic en **Aceptar**.

    ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Habilitación de MSI en la máquina virtual 

Una identidad MSI de máquina virtual le permite obtener tokens de acceso de Azure AD sin tener que poner las credenciales en el código. Al habilitar MSI se indica a Azure que cree una identidad administrada para la máquina virtual. Al habilitar MSI suceden dos cosas en segundo plano: se instala la extensión MSI en la máquina virtual y se habilita MSI en Azure Resource Manager.

1.  Seleccione la **máquina virtual** en la que quiera habilitar MSI.  
2.  En la barra de navegación de la izquierda, haga clic en **Configuración**. 
3.  Verá **Managed Service Identity**. Para registrar y habilitar MSI, seleccione **Sí**; si desea deshabilitarla, elija No. 
4.  No olvide hacer clic en **Guardar** para guardar la configuración.  
    ![Texto alternativo de imagen](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si desea comprobar las extensiones que están en esta máquina virtual, haga clic en **Extensiones**. Si MSI está habilitado, **ManagedIdentityExtensionforWindows** aparece en la lista.

    ![Texto alternativo de imagen](media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-database-in-an-azure-sql-server"></a>Conceda a la máquina virtual acceso a un servidor SQL de Azure

Ahora puede conceder a una máquina virtual acceso a un servidor SQL de Azure.  En este paso, puede utilizar un servidor SQL existente o crear uno nuevo.  Para crear un servidor y una base de datos con Azure Portal, siga esta [Guía de inicio rápido de Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal). También hay guías de inicio rápido que utilizan la CLI de Azure y Azure PowerShell en la [documentación de Azure SQL](https://docs.microsoft.com/azure/sql-database/).

Hay tres pasos para conceder a una máquina virtual el acceso a una base de datos:
1.  Crear un grupo en Azure AD y hacer que la identidad MSI de la máquina virtual sea miembro del grupo.
2.  Habilitar la autenticación de Azure AD para el servidor SQL.
3.  Crear un **usuario contenido** en la base de datos que representa el grupo de Azure AD.

> [!NOTE]
> Normalmente, se crearía un usuario contenido que se asigna directamente a la identidad MSI de la máquina virtual.  En la actualidad, Azure SQL no permite la entidad de seguridad de servicio de Azure AD que representa la MSI de la máquina virtual que se asigna a un usuario contenido.  Como solución alternativa admitida, convierta la identidad MSI de la máquina virtual en miembro de un grupo de Azure AD; a continuación, cree un usuario contenido en la base de datos que representa el grupo.


### <a name="create-a-group-in-azure-ad-and-make-the-vm-msi-a-member-of-the-group"></a>Cree un grupo en Azure AD y convierta la identidad MSI de la máquina virtual en miembro del grupo

Puede utilizar un grupo de Azure AD existente o crear uno nuevo con PowerShell de Azure AD.  

En primer lugar, instale el módulo [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). A continuación, inicie sesión con `Connect-AzureAD` y ejecute el siguiente comando para crear el grupo y guardarlo en una variable:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
```

El resultado es similar al siguiente, que también examina el valor de la variable:

```powershell
$Group = New-AzureADGroup -DisplayName "VM MSI access to SQL" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$Group
ObjectId                             DisplayName          Description
--------                             -----------          -----------
6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 VM MSI access to SQL
```

A continuación, agregue la identidad MSI de la máquina virtual al grupo.  Necesita el **ObjectId** de la MSI, que puede obtener con Azure PowerShell.  En primer lugar, descargue [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). A continuación, inicie sesión con `Login-AzureRmAccount` y ejecute los comandos siguientes para:
- Asegurarse de que el contexto de la sesión se establece en la suscripción de Azure deseada, si tiene varias.
- Enumerar los recursos disponibles en su suscripción de Azure, y comprobar el grupo de recursos y los nombres de máquina virtual correctos.
- Obtener las propiedades de la máquina virtual de MSI, con los valores adecuados para `<RESOURCE-GROUP>` y `<VM-NAME>`.

```powershell
Set-AzureRMContext -subscription "bdc79274-6bb9-48a8-bfd8-00c140fxxxx"
Get-AzureRmResource
$VM = Get-AzureRmVm -ResourceGroup <RESOURCE-GROUP> -Name <VM-NAME>
```

El resultado es similar al siguiente, que también examina el identificador de objeto de la entidad de servicio de la identidad MSI de la máquina virtual:
```powershell
$VM = Get-AzureRmVm -ResourceGroup DevTestGroup -Name DevTestWinVM
$VM.Identity.PrincipalId
b83305de-f496-49ca-9427-e77512f6cc64
```

Ahora, agregue la identidad MSI de la máquina virtual al grupo.  Solo puede agregar una entidad de servicio a un grupo mediante Azure AD PowerShell.  Ejecute este comando:
```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
```

Si también examina la pertenencia al grupo más adelante, el resultado es como sigue:

```powershell
Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId $VM.Identity.PrincipalId
Get-AzureAdGroupMember -ObjectId $Group.ObjectId

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
b83305de-f496-49ca-9427-e77512f6cc64 0b67a6d6-6090-4ab4-b423-d6edda8e5d9f DevTestWinVM
```

### <a name="enable-azure-ad-authentication-for-the-sql-server"></a>Habilite la autenticación de Azure AD para el servidor SQL

Ahora que ha creado el grupo y agregado la identidad MSI de la máquina virtual a la pertenencia, puede [configurar la autenticación de Azure AD para el servidor SQL](/azure/sql-database/sql-database-aad-authentication-configure#provision-an-azure-active-directory-administrator-for-your-azure-sql-server) mediante los siguientes pasos:

1.  En Azure Portal, seleccione **Servidores SQL Server** en el panel de navegación izquierdo.
2.  Haga clic en el servidor SQL para habilitarlo para la autenticación de Azure AD.
3.  En la sección **Configuración** de la hoja, haga clic en **Administrador de Active Directory**.
4.  En la barra de comandos, haga clic en **Establecer administrador**.
5.  Seleccione una cuenta de usuario de Azure AD para que se convierta en administrador del servidor y haga clic en **Seleccionar.**
6.  En la barra de comandos, haga clic en **Guardar**

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Cree un usuario contenido en la base de datos que representa el grupo de Azure AD

En el paso siguiente, necesitará [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS). Antes de comenzar, también puede ser útil revisar los artículos siguientes para obtener información sobre la integración de Azure AD:

- [Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)](/azure/sql-database/sql-database-ssms-mfa-authentication.md)
- [Configuración y administración de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse](/azure/sql-database/sql-database-aad-authentication-configure.md)

1.  Inicie SQL Server Management Studio.
2.  En el cuadro de diálogo **Conectar al servidor**, escriba el nombre del servidor SQL en el campo **Nombre del servidor**.
3.  En el campo **Autenticación**, seleccione **Active Directory - Universal compatible con MFA**.
4.  En el campo **Nombre de usuario**, escriba el nombre de la cuenta de Azure AD que estableció como administradora del servidor, por ejemplo,helen@woodgroveonline.com
5.  Haga clic en **Opciones**.
6.  En el campo **Conectar una base de datos**, escriba el nombre de la base de datos que no es del sistema y que desea configurar.
7.  Haga clic en **Conectar**.  Complete el proceso de inicio de sesión.
8.  En el **Explorador de objetos**, expanda la carpeta **Bases de datos**.
9.  Haga clic con el botón derecho en una base de datos de usuario y haga clic en **Nueva consulta**.
10.  En la ventana de consulta, escriba la línea siguiente y haga clic en **Ejecutar** en la barra de herramientas:
    
     ```
     CREATE USER [VM MSI access to SQL] FROM EXTERNAL PROVIDER
     ```
    
     El comando debería completarse correctamente y se creará el usuario contenido para el grupo.
11.  Limpie la ventana de consulta, escriba la línea siguiente y haga clic en **Ejecutar** en la barra de herramientas:
     
     ```
     ALTER ROLE db_datareader ADD MEMBER [VM MSI access to SQL]
     ```

     El comando debería completarse correctamente, concediendo al usuario contenido la capacidad de leer la base de datos completa.

El código que se ejecuta en la máquina virtual ahora puede obtener un token de MSI y usar el token para autenticarse en el servidor SQL.

## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-call-azure-sql"></a>Obtenga un token de acceso mediante la identidad de la máquina virtual y úselo para llamar a Azure SQL 

Azure SQL admite de forma nativa la autenticación de Azure AD, por lo que puede aceptar directamente los tokens de acceso obtenidos mediante MSI.  Se usa el método **token de acceso** para la creación de una conexión a SQL.  Forma parte de la integración de Azure SQL con Azure AD y es diferente de proporcionar las credenciales en la cadena de conexión.

Este es un ejemplo de código .Net para abrir una conexión a SQL con un token de acceso.  Este código debe ejecutarse en la máquina virtual para poder acceder al punto de conexión de la identidad MSI de la máquina virtual.  Se requiere **.NET framework 4.6** o posterior para usar el método de token de acceso.  Reemplace los valores de SQL-SERVERNAME y DATABASE en consecuencia.  Tenga en cuenta que el identificador de recurso de Azure SQL es "https://database.windows.net/".

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call MSI endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the SQL server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

Como alternativa, un método rápido para probar la configuración de extremo a otro extremo sin tener que escribir e implementar una aplicación en la máquina virtual es usar PowerShell.

1.  En el portal, vaya a **Virtual Machines** y diríjase a la máquina virtual Windows. A continuación, en **Introducción**, haga clic en **Conectar**. 
2.  Escriba su **nombre de usuario** y **contraseña** que agregó cuando creó la máquina virtual Windows. 
3.  Ahora que ha creado una **conexión a Escritorio remoto** con la máquina virtual, abra **PowerShell** en la sesión remota. 
4.  Mediante `Invoke-WebRequest` de PowerShell, realice una solicitud al punto de conexión de la identidad MSI local para obtener un token de acceso para Azure SQL.

    ```powershell
       $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://database.windows.net/"} -Headers @{Metadata="true"}
    ```
    
    Convierta la respuesta de un objeto JSON a un objeto de PowerShell. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    Extraiga el token de acceso de la respuesta.
    
    ```powershell
    $AccessToken = $content.access_token
    ```

5.  Abra una conexión al servidor SQL. No olvide reemplazar los valores de AZURE-SQL-SERVERNAME y DATABASE.
    
    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    Después, cree una consulta y envíela al servidor.  No olvide reemplazar el valor de TABLE.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

Examine el valor de `$DataSet.Tables[0]` para ver los resultados de la consulta.  Enhorabuena, ha consultado la base de datos mediante una identidad MSI de máquina virtual y sin necesidad de proporcionar credenciales.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](../active-directory/msi-overview.md) (Introducción a Managed Service Identity).
- Más información sobre la [compatibilidad de Azure SQL con la autenticación de Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).
- Más información sobre la [configuración de la compatibilidad de Azure SQL con la autenticación de Azure AD](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure).
- Más información sobre la [autenticación y el acceso en SQL Server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.
