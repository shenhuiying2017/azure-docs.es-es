---
title: ActiveDirectoryInteractive se conecta a SQL | Microsoft Docs
description: Ejemplo de código de C#, con explicaciones, para conectarse a Azure SQL Database mediante el modo SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
author: GithubMirek
manager: jhubbard
ms.service: sql-database
ms.custom: active directory
ms.topic: article
ms.date: 04/06/2018
ms.author: MirekS
ms.reviewer: GeneMi
ms.openlocfilehash: 163b55e0f1727cc2ba9e3ee84eb58fe29f7a005d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779754"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Uso del modo ActiveDirectoryInteractive para conectarse a Azure SQL Database

En este artículo se proporciona un ejemplo de código de C# ejecutable que permite conectar con su instancia de Microsoft Azure SQL Database. El programa de C# usa el modo de autenticación interactivo, que es compatible con la autenticación multifactor (MFA) de Azure AD. Por ejemplo, un intento de conexión puede incluir un código de verificación que se envía a su teléfono móvil.

Para más información sobre la compatibilidad con MFA en las herramientas de SQL, consulte [Compatibilidad de Azure Active Directory con SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>Valor de enumeración de .ActiveDirectoryInteractive de SqlAuthenticationMethod

A partir de la versión 4.7.2 de .NET Framework, la enumeración [**SqlAuthenticationMethod**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) tiene un nuevo valor **.ActiveDirectoryInteractive**. Cuando se usa en un programa cliente de C#, este valor de enumeración indica al sistema que use el modo interactivo de Azure AD compatible con MFA para la autenticación en Azure SQL Database. El usuario que ejecuta el programa puede ver entonces los cuadros de diálogo siguientes:

1. Un cuadro de diálogo que muestra un nombre de usuario de Azure AD y que solicita la contraseña de dicho usuario.
    - Este cuadro de diálogo no se muestra si no se necesita ninguna contraseña. No se necesita ninguna contraseña si el dominio del usuario está federado con Azure AD.

    Si la directiva establecida en Azure AD impone MFA sobre el usuario, se muestran a continuación los cuadros de diálogo siguientes.

2. Solo la primera vez que el usuario experimente el escenario MFA el sistema muestra un cuadro de diálogo adicional. El cuadro de diálogo solicita un número de teléfono móvil al que se enviarán los mensajes de texto. Cada mensaje proporciona el *código de verificación* que el usuario debe especificar en el cuadro de diálogo siguiente.

3. Otro cuadro de diálogo que pide el código de verificación de MFA, que el sistema ha enviado a un teléfono móvil.

Para información sobre cómo configurar Azure AD para solicitar MFA, consulte [Introducción a Azure Multi-Factor Authentication en la nube](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para obtener capturas de pantalla de estos cuadros de diálogo, consulte [Configuración de la autenticación multifactor para SQL Server Management Studio y Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Nuestra página de búsqueda general para todo tipo de API de .NET Framework está disponible en el vínculo siguiente a nuestra práctica herramienta **Explorador de API de .NET**:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Si se agrega el nombre del tipo al parámetro **?term=** anexado opcional, la página de búsqueda puede tener listo nuestro resultado y esperándonos:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Preparativos para C# mediante Azure Portal

Se supone que ya ha creado un [servidor de Azure SQL Database](sql-database-get-started-portal.md) y está disponible.


### <a name="a-create-an-app-registration"></a>A. Crear un registro de aplicación

Para usar la autenticación de Azure AD, el programa cliente de C# debe proporcionar un GUID en forma de un *clientId* cuando el programa intente conectarse. Al completar un registro de aplicación se genera y se muestra el GUID en Azure Portal, etiquetado como **Id. de aplicación**. Los pasos de navegación son los siguientes:

1. Azure Portal &gt; **Azure Active Directory** &gt; **Registro de aplicación**

    ![Registro de aplicación](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. El valor **Id. de aplicación** se genera y se muestra.

    ![Identificador de aplicación mostrado](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Aplicación registrada** &gt; **Configuración** &gt; **Permisos necesarios** &gt; **Agregar**

    ![Configuración de permisos para la aplicación registrada](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Permisos necesarios** &gt; **Agregar acceso de API** &gt; **Seleccionar una API** &gt; **Azure SQL Database**

    ![Adición de acceso a la API de Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Acceso de API** &gt; **Seleccionar permisos** &gt; **Permisos delegados**

    ![Delegación de permisos en la API de Azure SQL Database](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Configuración del administrador de Azure AD en el servidor de SQL Database

Cada servidor de Azure SQL Database tiene su propio servidor SQL lógico de Azure AD. En nuestro caso de C#, debe establecer un administrador de Azure AD para el servidor de Azure SQL.

1. **SQL Server** &gt; **Administrador de Active Directory** &gt; **Establecer administrador**

    - Para más información sobre los administradores y usuarios de Azure AD en Azure SQL Database, consulte las capturas de pantalla en [Configuración y administración de la autenticación de Azure Active Directory con SQL Database](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), en la sección **Aprovisionamiento de un administrador de Azure Active Directory para el servidor de Azure SQL Database**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Preparación de un usuario de Azure AD para conectarse a una base de datos específica

En la instancia de Azure AD específica del servidor de Azure SQL Database, puede agregar un usuarios que tendrá acceso a una base de datos determinada.

Para más información, consulte [Uso de la autenticación de Azure Active Directory para autenticación con SQL Database, Instancia administrada o SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Adición de un usuario no administrador a Azure AD

El administrador de Azure AD del servidor SQL Database se puede usar para conectarse a dicho servidor. Sin embargo, un caso más general es agregar un usuario no administrador a Azure AD. Cuando el usuario no administrador se usa para conectarse, la secuencia de MFA se invoca si Azure AD impone MFA sobre ese usuario.




## <a name="azure-active-directory-authentication-library-adal"></a>Biblioteca de autenticación de Azure Active Directory (ADAL)

El programa de C# se basa en el espacio de nombres **Microsoft.IdentityModel.Clients.ActiveDirectory**. Las clases de este espacio de nombres se encuentran en el ensamblado con el mismo nombre.

- Use NuGet para descargar e instalar el ensamblado de ADAL.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Agregue una referencia al ensamblado para admitir una compilación del programa de C#.




## <a name="sqlauthenticationmethod-enum"></a>Enumeración SqlAuthenticationMethod

Uno de los espacios de nombres en los que se basa el ejemplo de C# es **System.Data.SqlClient**. De especial interés es la enumeración **SqlAuthenticationMethod**. Esta enumeración tiene los siguientes valores:

- **SqlAuthenticationMethod.ActiveDirectory*Interactive***:&nbsp;: use este valor con un nombre de usuario de Azure AD para conseguir la autenticación multifactor (MFA).
    - Este valor es el enfoque del presente artículo. Produce una experiencia interactiva ya que se muestran los cuadros de diálogo de la contraseña de usuario y, luego, de la validación de MFA si MFA se ha impuesto sobre este usuario.
    - Este valor está disponible a partir de .NET Framework 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory*Integrated***:&nbsp;: use este valor con cuentas *federadas*. En una cuenta federada, el nombre del usuario se conoce en el dominio de Windows. Este método no admite MFA.

- **SqlAuthenticationMethod.ActiveDirectory*Password***:&nbsp;: use este valor si la autenticación requiere un usuario de Azure AD y la contraseña del usuario. Azure SQL Database realiza la autenticación. Este método no admite MFA.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Preparación de los valores de parámetro de C# desde Azure Portal

Para una ejecución correcta del programa de C#, debe asignar los valores adecuados a los siguientes campos estáticos. Estos campos estáticos actúan como parámetros en el programa. Los campos se muestran aquí con valores ficticios. También se muestran las ubicaciones en Azure Portal desde las que puede obtener los valores adecuados:


| Nombre del campo estático | Valor ficticio | Dónde está en Azure Portal |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-favorite-sqldb-svr.database.windows.net" | **Servidores SQL** &gt; **Filtrar por nombre** |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **Azure Active Directory** &gt; **Usuario** &gt; **Nuevo usuario invitado** |
| Initial_DatabaseName | "master" | **Servidores SQL** &gt; **Bases de datos SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** &gt; **Registros de aplicaciones**<br /> &nbsp; &nbsp; &gt; **Buscar por nombre** &gt; **Id. de aplicación** |
| RedirectUri | new Uri( "https://bing.com/") | **Azure Active Directory** &gt; **Registros de aplicaciones**<br /> &nbsp; &nbsp; &gt; **Buscar por nombre** &gt; *[Su registro de aplicaciones]* &gt;<br /> &nbsp; &nbsp; **Configuración** &gt; **RedirectURIs**<br /><br />En este artículo, cualquier valor válido sirve para RedirectUri. El valor no se usa realmente en nuestro caso. |
| &nbsp; | &nbsp; | &nbsp; |


Según el escenario concreto, puede que no necesite los valores de todos los parámetros de la tabla anterior.




## <a name="run-ssms-to-verify"></a>Ejecución de SSMS para verificar

Antes de ejecutar el programa de C#, resulta útil ejecutar SQL Server Management Studio (SSMS). Al ejecutar SSMS se verifica que las diversas configuraciones son correctas. Así, cualquier error del programa de C# se puede restringir a solo su código fuente.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificación de las direcciones IP del firewall de SQL Database

Ejecute SSMS desde el mismo equipo, en el mismo edificio, donde ejecutará posteriormente el programa de C#. Puede usar el modo de **autenticación** que le resulte más sencillo. Si hay algún indicio de que el firewall del servidor de base de datos no va a aceptar su dirección IP, puede resolver este problema como se muestra en [Reglas de firewall de nivel de servidor y de nivel de base de datos de Azure SQL Database](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Verificación de la autenticación multifactor (MFA) para Azure AD

Vuelva a ejecutar SSMS, en esta ocasión con la opción **Autenticación** establecida en **Active Directory - Universal compatible con MFA**. Para usar esta opción debe tener la versión 17.5 de SSMS o posterior.

Para más información, consulte [Configuración de la autenticación multifactor para SSMS y Azure AD](sql-database-ssms-mfa-authentication-configure.md).




## <a name="c-code-example"></a>Ejemplo de código de C#

Para compilar este ejemplo de C#, debe agregar una referencia al ensamblado DLL denominado **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Documentación de referencia

- Espacio de nombres de **System.Data.SqlClient**:
    - Búsqueda:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Directo:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- Espacio de nombres de **Microsoft.IdentityModel.Clients.ActiveDirectory**:
    - Búsqueda:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Directo:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>Código fuente de C#, en dos partes

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Segunda mitad del programa de C#

Para una mejor presentación visual, el programa de C# se divide en dos bloques de código. Para ejecutar el programa, pegue los dos bloques de código juntos.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Salida de prueba real de C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

&nbsp;


## <a name="next-steps"></a>Pasos siguientes

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

