---
title: "Crear aplicaciones .NET para HDInsight de autenticación no interactiva| Microsoft Docs"
description: "Obtenga información sobre cómo crear aplicaciones .NET para HDInsight de autenticación no interactiva."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 8e32430f-6404-498a-9fcd-f20338d964af
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 2b8638ffc3287346a71f591370367655c450e376
ms.contentlocale: es-es
ms.lasthandoff: 04/18/2017


---
# <a name="create-non-interactive-authentication-net-hdinsight-applications"></a>Crear aplicaciones .NET para HDInsight de autenticación no interactiva
Puede ejecutar su aplicación .NET para Azure HDInsight en la propia identidad de la aplicación (no interactiva) o en la identidad del usuario con sesión iniciada de la aplicación (interactiva). Para obtener un ejemplo de la aplicación interactiva, consulte [Conexión a Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). En este artículo se muestra cómo crear aplicaciones .NET de autenticación no interactivas para conectarse a Azure y administrar HDInsight.

Desde su aplicación de .NET no interactiva, necesita:

* El identificador de inquilino de su suscripción a Azure (también conocido como identificador de directorio). Consulte [Obtención del identificador de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* El id. de cliente de la aplicación de Azure Active Directory. Consulte [Create an Azure Active Directory application](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) (Creación de una aplicación de Azure Active Directory) y el apartado sobre cómo [conseguir un id. de aplicación](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* La clave secreta de la aplicación de Azure Active Directory. Consulte [Obtención de la clave de autenticación](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key)

## <a name="prerequisites"></a>Requisitos previos
* Clúster de HDInsight. Consulte el [tutorial de introducción](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster).



## <a name="assign-azure-ad-application-to-role"></a>Asignación de una aplicación de Azure AD a un rol
Debe asignar la aplicación a un [rol](../active-directory/role-based-access-built-in-roles.md) para concederle permisos para realizar acciones. Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores de ámbito (por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga). En este tutorial, establecerá el ámbito en el nivel del grupo de recursos. Para obtener más información, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).

**Para agregar el rol de propietario a la aplicación de Azure AD**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Grupo de recursos** en el panel izquierdo.
3. Haga clic en el grupo de recursos que contiene el clúster de HDInsight donde ejecutará la consulta de Hive más adelante en este tutorial. Si hay demasiados grupos de recursos, puede usar el filtro.
4. En el menú de grupo de recursos, haga clic en **Control de acceso (IAM)**.
5. Haga clic en **Agregar** en la hoja **Usuarios**.
6. Siga las instrucciones para agregar el rol de **propietario** a la aplicación de Azure AD que creó en el último procedimiento. Cuando lo complete correctamente, verá la aplicación enumerada en la hoja Usuarios con el rol de propietario.

## <a name="develop-hdinsight-client-application"></a>Desarrollar la aplicación de cliente de HDInsight

1. Cree una aplicación de consola en C#
2. Agregue los siguientes paquetes NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Use el código de ejemplo siguiente:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        
        namespace CreateHDICluster
        {
            internal class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
        
                private static Guid SubscriptionId = new Guid("<Enter Your Azure Subscription ID>");
                private static string tenantID = "<Enter Your Tenant ID (A.K.A. Directory ID)>";
                private static string applicationID = "<Enter Your Application ID>";
                private static string secretKey = "<Enter the Application Secret Key>";
        
                private static void Main(string[] args)
                {
                    var key = new SecureString();
                    foreach (char c in secretKey) { key.AppendChar(c); }

                    var tokenCreds = GetTokenCloudCredentials(tenantID, applicationID, key);
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
        
                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        
                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
        
                    var results = _hdiManagementClient.Clusters.List();
                    foreach (var name in results.Clusters)
                    {
                        Console.WriteLine("Cluster Name: " + name.Name);
                        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
                        Console.WriteLine("\t Cluster location: " + name.Location);
                        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
                    }
                    Console.WriteLine("Press Enter to continue");
                    Console.ReadLine();
                }

                /// Get the access token for a service principal and provided key                
                public static TokenCloudCredentials GetTokenCloudCredentials(string tenantId, string clientId, SecureString secretKey)
                {
                    var authFactory = new AuthenticationFactory();
                    var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
                    var accessToken =
                        authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
        
                    return new TokenCloudCredentials(accessToken);
                }
        
                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
                }
            }
        }

## <a name="next-steps"></a>Pasos siguientes
* [Creación de una aplicación de Azure Active Directory y una entidad de servicio mediante el portal](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* [Autenticar una entidad de servicio con Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [Control de acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)

