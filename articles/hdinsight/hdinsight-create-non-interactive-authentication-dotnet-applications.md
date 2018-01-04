---
title: "Creación de aplicaciones .NET de autenticación no interactivas en Azure HDInsight | Microsoft Docs"
description: "Aprenda a crear aplicaciones Microsoft .NET de autenticación no interactivas en Azure HDInsight."
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
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: b2b24747ce4ea8499c999c693f00fb09178d52b0
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="create-a-non-interactive-authentication-net-hdinsight-application"></a>Creación de aplicaciones .NET para HDInsight con autenticación no interactivas
Puede ejecutar su aplicación Microsoft .NET para Azure HDInsight en la propia identidad de la aplicación (no interactiva) o en la identidad del usuario con sesión iniciada de la aplicación (interactiva). En este artículo se muestra cómo crear aplicaciones .NET de autenticación no interactivas para conectarse a Azure y administrar HDInsight. Para ver un ejemplo de una aplicación interactiva, consulte [Conexión a Azure HDInsight](hdinsight-administer-use-dotnet-sdk.md#connect-to-azure-hdinsight). 

Desde su aplicación de .NET no interactiva, necesita:

* El identificador de inquilino de su suscripción a Azure (también conocido como *identificador de directorio*). Consulte [Obtención del identificador de inquilino](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
* El id. de cliente de la aplicación de Azure Active Directory (Azure AD). Consulte [Create an Azure Active Directory application](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) (Creación de una aplicación de Azure Active Directory) y el apartado sobre cómo [conseguir un id. de aplicación](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
* La clave secreta de la aplicación de Azure AD. Consulte [Obtención de la clave de autenticación](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).

## <a name="prerequisites"></a>requisitos previos
* Un clúster de HDInsight. Consulte el [tutorial de introducción](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).

## <a name="assign-a-role-to-the-azure-ad-application"></a>Asignación de un rol a la aplicación de Azure AD
Asigne un [rol](../active-directory/role-based-access-built-in-roles.md) a la aplicación de Azure AD para concederle permiso para realizar acciones. Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga. En este tutorial, establecerá el ámbito en el nivel del grupo de recursos. Para más información, consulte [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).

**Para agregar el rol de propietario a la aplicación de Azure AD**

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el menú de la izquierda, seleccione **Grupo de recursos**.
3. Seleccione el grupo de recursos que tiene el clúster de HDInsight en el que se ejecutará su consulta de Hive posteriormente en este tutorial. Si tiene un gran número de grupos de recursos, puede usar el filtro para buscar el que desee.
4. En el menú de grupo de recursos, haga clic en **Control de acceso (IAM)**.
5. En **Usuarios**, seleccione **Agregar**.
6. Siga las instrucciones para agregar el rol Propietario a la aplicación de Azure AD. Después de agregar el rol correctamente, la aplicación aparece en **Usuarios**, con el rol Propietario. 

## <a name="develop-an-hdinsight-client-application"></a>Desarrollo de una aplicación de cliente de HDInsight

1. Cree una aplicación de consola en C#
2. Agregue los siguientes paquetes NuGet:

        Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.Resources -Pre

3. Ejecute el código siguiente:

    ```csharp
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
    
            private static Guid SubscriptionId = new Guid("<Enter your Azure subscription ID>");
            private static string tenantID = "<Enter your tenant ID (also called directory ID)>";
            private static string applicationID = "<Enter your application ID>";
            private static string secretKey = "<Enter the application secret key>";
    
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
    
            /// Get the access token for a service principal and provided key.          
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
    ```


## <a name="next-steps"></a>pasos siguientes
* [Creación de una aplicación de Azure Active Directory y una entidad de servicio mediante Azure Portal](../azure-resource-manager/resource-group-create-service-principal-portal.md)
* Aprenda a [autenticar una entidad de servicio con Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md).
* Aprenda sobre el [control de acceso basado en roles (RBAC) de Azure](../active-directory/role-based-access-control-configure.md).
