
<properties
   pageTitle="Protección de un clúster de Service Fabric: autenticación de cliente con Azure Active Directory | Microsoft Azure"
   description="En este artículo se describe cómo crear un clúster de Service Fabric con Azure Active Directory (AAD) para la autenticación de cliente"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/22/2016"
   ms.author="seanmck"/>

# VISTA PREVIA: Crear un clúster de Service Fabric con Azure Active Directory para la autenticación de cliente

Puede proteger el acceso a los puntos de conexión de administración de un clúster de Service Fabric con Azure Active Directory (AAD). Este artículo trata sobre cómo crear los artefactos de AAD necesarios, cómo rellenarlos durante la creación de clústeres y cómo conectarse a esos clústeres posteriormente.

>[AZURE.IMPORTANT] La integración de AAD con clústeres de Service Fabric está actualmente en vista previa. Todo lo que se describe en este artículo está disponible en el tiempo de ejecución de Service Fabric 5.0, pero se recomienda que no lo use para clústeres de producción en este momento.

## Modelar un clúster de Service Fabric en AAD

AAD permite que las organizaciones (conocidas como inquilinos) administren el acceso de los usuarios a las aplicaciones, que se dividen en aplicaciones con interfaz de usuario de inicio de sesión basada en web y aplicaciones con experiencia de cliente nativa. En este documento, se da por supuesto que ya ha creado un inquilino. En caso de que no lo haya hecho, lea [Obtención de un inquilino de Azure Active Directory](../active-directory/active-directory-howto-tenant.md).

Los clústeres de Service Fabric ofrecen diversos puntos de entrada a su funcionalidad de administración, incluido [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) basado en web y [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Como resultado, creará dos aplicaciones de AAD para controlar el acceso al clúster, una aplicación web y una aplicación nativa.

Para simplificar algunos de los pasos necesarios para configurar AAD con un clúster de Service Fabric, hemos creado un conjunto de scripts de Windows PowerShell.

>[AZURE.NOTE] Debe realizar estos pasos *antes* de crear el clúster. Por eso, en los casos en los que los scripts esperan nombres de clúster y puntos de conexión, debe indicar los valores planeados, no los valores que ya haya creado.

1. [Descargue los scripts][sf-aad-ps-script-download] en su equipo.

2. Haga clic con el botón derecho en el archivo zip, elija **Propiedades** y luego active la casilla **Desbloquear**.

3. Extraiga el archivo ZIP.

4. Ejecute `SetupApplications.ps1` y proporcione TenantId, ClusterName y WebApplicationReplyUrl como parámetros. Por ejemplo:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    Encontrará el valor de **TenantId** si examina la dirección URL del inquilino en el Portal de Azure clásico. El GUID insertado en esa dirección URL es el TenantId. Por ejemplo:

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    El valor de **ClusterName** se usará como prefijo para las aplicaciones de AAD que cree el script. No es necesario que coincida exactamente con el nombre real del clúster, ya que su finalidad es facilitar la asignación de artefactos de AAD al clúster de Service Fabric con el que se usan.

    El valor de **WebApplicationReplyUrl** es el punto de conexión predeterminado al que AAD devolverá a los usuarios después de completar el proceso de inicio de sesión. Debe establecerlo en el punto de conexión de Service Fabric Explorer para el clúster, que de manera predeterminada es el siguiente:

    https://&lt;cluster_domain&gt;:19080/Explorer

    Se le pedirá que inicie sesión en una cuenta que tenga privilegios administrativos para el inquilino de AAD. En cuanto lo haga, el script creará la web y las aplicaciones nativas para representar el clúster de Service Fabric. Si observa las aplicaciones del inquilino en el [Portal de Azure clásico][azure-classic-portal], debería ver dos entradas nuevas:

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    El script imprimirá el código Json requerido por la plantilla de Azure Resource Manager (ARM) al crear el clúster en la sección siguiente, por lo que debe mantener abierta la ventana de PowerShell.

    ![La salida del script SetupApplication incluye el código Json requerido por la plantilla ARM][setupapp-script-output]

## Creación de clústeres

Ahora que ha creado las aplicaciones de AAD, puede crear el clúster de Service Fabric. En este momento, el Portal de Azure no permite configurar la autenticación de AAD para clústeres de Service Fabric, por lo que deberá hacerlo mediante una plantilla ARM en PowerShell o Visual Studio.

Tenga en cuenta que AAD solo se usa para la autenticación de cliente en el clúster. Para crear un clúster seguro también debe proporcionar un certificado, que se usará para proteger la comunicación entre los nodos del clúster y para proporcionar autenticación de servidor para los puntos de conexión de administración del clúster. Encontrará una [plantilla ARM para un clúster seguro en la galería de inicio rápido de Azure][secure-cluster-arm-template], o bien puede seguir las instrucciones proporcionadas en el archivo Léame del [proyecto del grupo de recursos de Service Fabric en Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

Agregue la salida del fragmento de código de la plantilla ARM del script `SetupApplication` como homólogo a fabricSettings, managementEndpoint, etc. Si ha cerrado la ventana, el fragmento también se muestra a continuación:

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

El valor de clusterApplication hace referencia a la aplicación web que se creó en la sección anterior. Encontrará su identificador en la salida del script SetupApplication, donde se indica como `WebAppId`. El valor de clientApplication hace referencia a la aplicación nativa. Su identificador de cliente está disponible en la salida de SetupApplication como NativeClientAppId.

## Asignación de usuarios a roles

Una vez que haya creado las aplicaciones para representar el clúster, debe asignar los usuarios a los roles compatibles con Service Fabric: solo lectura y administrador. Puede hacerlo mediante el [Portal de Azure clásico][azure-classic-portal].

1. Vaya al inquilino y elija Aplicaciones.
2. Elija la aplicación web, que tendrá un nombre parecido a `myTestCluster_Cluster`.
3. Haga clic en la pestaña Usuarios.
4. Elija un usuario para asignar y haga clic en el botón **Asignar** situado en la parte inferior de la pantalla.

    ![Botón para asignar usuarios a roles][assign-users-to-roles-button]

5. Seleccione el rol que quiere asignar al usuario.

    ![Asignación de usuarios a roles][assign-users-to-roles-dialog]

>[AZURE.NOTE] Para más información sobre los roles de Service Fabric, consulte [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

## Conexión al clúster

Cuando vaya a Service Fabric Explorer en un clúster habilitado para AAD, se le redirigirá automáticamente a una página de inicio de sesión seguro.

Para conectarse desde un cliente nativo como Windows PowerShell o Visual Studio, debe indicar AAD como mecanismo de inicio de sesión y después proporcionar una huella digital de certificado de servidor, que sirve para validar la identidad del punto de conexión. A continuación se muestran los detalles de estos dos puntos de entrada.

### Conexión desde Visual Studio

En Visual Studio, puede modificar el perfil de publicación para agregar los atributos necesarios, tal como se muestra a continuación:

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

Al publicar en el clúster, aparecerá en Visual Studio una ventana de inicio de sesión donde puede autenticarse en el clúster.

![Ventana de inicio de sesión de AAD durante la publicación de Visual Studio][vs-publish-aad-login]

### Conexión desde Windows PowerShell

En PowerShell, puede proporcionar los parámetros necesarios para el cmdlet Connect-ServiceFabricCluster tal como se muestra a continuación:

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

Al igual que en Visual Studio, PowerShell mostrará una ventana de inicio de sesión seguro para la autenticación.

>[AZURE.NOTE] De forma predeterminada, la pasarela TCP de Service Fabric que usan PowerShell y Visual Studio escucha en el puerto 19000. Si ha configurado un puerto diferente, debe usarlo al especificar el punto de conexión.

## Problemas conocidos

### Error de autenticación de cliente nativo debido a una dirección de respuesta que no coincide

Al autenticarse desde un cliente nativo, como Visual Studio o PowerShell, podría ver un mensaje de error similar al siguiente:

*Reply address http://localhost/ does not match reply address configured for application &lt;cluster client application GUID&gt;* (La dirección de respuesta http://localhost/ no coincide con la dirección de respuesta configurada para el &lt;GUID de la aplicación cliente del clúster&gt;)

Para solucionar este problema, agregue **http://<i></i>localhost** como identificador URI de redireccionamiento a la definición de la aplicación cliente del clúster en AAD, además de la dirección 'urn:ietf:wg:oauth:2.0:oob' que ya está allí.

## Pasos siguientes

- Más información sobre la [protección de un clúster de Service Fabric](service-fabric-cluster-security.md)
- Más información sobre la [publicación de una aplicación en un clúster remoto con Visual Studio](service-fabric-publish-app-remote-cluster.md)

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/es-ES/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0518_2016-->