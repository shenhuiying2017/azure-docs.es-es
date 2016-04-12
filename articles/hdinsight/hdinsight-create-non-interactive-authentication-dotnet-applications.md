<properties
	pageTitle="Crear aplicaciones .NET para HDInsight de autenticación no interactiva| Microsoft Azure"
	description="Obtenga información sobre cómo crear aplicaciones .NET para HDInsight de autenticación no interactiva."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/23/2016"
	ms.author="jgao"/>

# Crear aplicaciones .NET para HDInsight de autenticación no interactiva

Puede ejecutar su aplicación .NET para HDInsight de Azure en la propia identidad de la aplicación (no interactiva) o en la identidad del usuario con sesión iniciada de la aplicación (interactiva). Para obtener un ejemplo de la aplicación interactiva, consulte [Enviar trabajos de Hive/Pig/Sqoop con el SDK .NET para HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). En este artículo se muestra cómo crear aplicaciones .NET de autenticación no interactiva para conectarse a HDInsight de Azure y enviar un trabajo de Hive.

Desde su aplicación .NET, necesitará:

- el identificador de inquilino de su suscripción a Azure
- el identificador de cliente de la aplicación de Azure Directory
- la clave secreta de la aplicación de Azure Directory.  

El proceso principal incluye los siguientes pasos:

2. Crear una aplicación de Azure Directory.
2. Asignar roles a la aplicación de AD.
3. Desarrollar la aplicación cliente.


##Requisitos previos

- Clúster de HDInsight. Puede crear uno usando las instrucciones del [tutorial de introducción](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster). 




## Creación de una aplicación de Azure Active Directory 
Cuando crea una aplicación de Active Directory, en realidad se crea la aplicación y una entidad de servicio. Puede ejecutar la aplicación en la identidad de la aplicación.

Actualmente, debe usar el Portal de Azure clásico para crear una nueva aplicación de Active Directory. Esta capacidad se agregará al portal de Azure en una versión posterior. También puede llevar a cabo estos pasos a través de Azure PowerShell o CLI de Azure. Para obtener más información sobre cómo usar PowerShell o CLI con la entidad de servicio, consulte [Autenticación de una entidad de servicio con Azure Resource Manager](../resource-group-authenticate-service-principal.md).

**Para crear una aplicación de Azure Directory**

1.	Inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com/).
2.	Seleccione **Active Directory** en el panel izquierdo.

    ![Portal de Azure clásico de Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\active-directory.png)
    
3.	Seleccione el directorio que desea usar para crear la nueva aplicación. Será el existente.
4.	Haga clic en **Aplicaciones** desde la parte superior para enumerar las aplicaciones existentes.
5.	Haga clic en **Agregar** desde la parte inferior para agregar una nueva aplicación.
6.	Escriba **Nombre**, seleccione **Aplicación web o Web API** y, después, haga clic en **Siguiente**.

    ![Nueva aplicación de Azure Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application.png)

7.	Escriba **Dirección URL de inicio de sesión** e **Identificador URI del identificador de la aplicación**. Para **DIRECCIÓN URL DE INICIO DE SESIÓN**, proporcione el identificador URI para un sitio web que describe la aplicación. No se valida la existencia del sitio web. Para Identificador URI del identificador de la aplicación, proporcione el identificador URI que identifica la aplicación. Después, haga clic en **Completar**. Lleva algunos minutos crear la aplicación. Una vez creada la aplicación, el portal muestra la página de Vista rápida de la nueva aplicación. No cierre el portal.

    ![Propiedades de la nueva aplicación de Azure Active Directory](.\media\hdinsight-create-non-interactive-authentication-dotnet-application\hdinsight-add-ad-application-properties.png)

**Para obtener el identificador de cliente de la aplicación y la clave secreta**

1.	En la página de la aplicación de AD recién creada, haga clic en **Configurar** en el menú superior.
2.	Realice una copia del **Identificador de cliente**. Lo necesitará en su aplicación .NET.
3.	En **Claves**, haga clic en **Seleccionar duración** del menú desplegable y seleccione **1 año** o **2 años**. El valor de la clave no se mostrará hasta que guarde la configuración.
4.	Haga clic en **Guardar** en la parte inferior de la página. Cuando aparezca la clave secreta, realice una copia de la clave. La necesitará en su aplicación .NET.

##Asignar una aplicación AD a un rol

Debe asignar la aplicación a un [rol](../active-directory/role-based-access-built-in-roles.md) para concederle permisos para realizar acciones. Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores de ámbito (por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga). En este tutorial, establecerá el ámbito en el nivel del grupo de recursos. Como el Portal de Azure clásico no admite grupos de recursos, esta parte debe realizarse desde el Portal de Azure.

**Para agregar el rol de propietario a la aplicación de AD**

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2.	Haga clic en **Grupo de recursos** en el panel izquierdo.
3.	Haga clic en el grupo de recursos que contiene el clúster de HDInsight donde ejecutará la consulta de Hive más adelante en este tutorial. Si hay demasiados grupos de recursos, puede usar el filtro.
4.	Haga clic en **Acceso** desde la hoja del clúster.

    ![icono de nube y rayo = inicio rápido](./media/hdinsight-hadoop-create-linux-cluster-portal/quickstart.png)
5.	Haga clic en **Agregar** desde la hoja **Usuarios**.
6.	Siga las instrucciones para agregar el rol de **Propietario** a la aplicación de AD que creó en el último procedimiento. Cuando lo complete correctamente, verá la aplicación enumerada en la hoja Usuarios con el rol de propietario.


##Desarrollar la aplicación de cliente de HDInsight

Cree una aplicación de consola de C# .net siguiendo las instrucciones que encontrará en [Enviar trabajos de Hadoop en HDInsight](hdinsight-submit-hadoop-jobs-programmatically/#submit-hivepigsqoop-jobs-using-hdinsight-net-sdk). Después, reemplace el método GetTokenCloudCredentials por lo siguiente:

    public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
    {
        var authFactory = new AuthenticationFactory();

        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };

        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

        var accessToken =
            authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never)
                .AccessToken;

        return new TokenCloudCredentials(accessToken);
    }

Para recuperar el identificador de inquilino mediante PowerShell:

    Get-AzureRmSubscription

O de la CLI de Azure:

    azure account show --json

      
## Consulte también

- [Envío de trabajos de Hadoop en HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
- [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](../resource-group-create-service-principal-portal.md)
- [Autenticar una entidad de servicio con Azure Resource Manager](../resource-group-authenticate-service-principal.md)
- [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)

<!---HONumber=AcomDC_0330_2016-->