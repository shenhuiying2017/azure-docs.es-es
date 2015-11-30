<properties
   pageTitle="Configuración de conexiones seguras admitidas por el clúster de Service Fabric | Microsoft Azure"
   description="Obtenga información acerca de cómo usar Visual Studio para configurar conexiones seguras que son compatibles con el clúster de Azure Service Fabric."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />

<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/08/2015"
   ms.author="cawaMS" />

# Configuración de las conexiones seguras en un clúster de Service Fabric desde Visual Studio

Obtenga información sobre cómo usar Visual Studio para obtener acceso de forma segura a un clúster de Service Fabric con directivas de control de acceso configuradas.

## Tipos de conexión del clúster

Existen dos tipos de conexiones admitidas por el clúster de Azure Service Fabric: conexiones **no seguras** y conexiones seguras **basadas en certificados x509**. En los clústeres de Service Fabric hospedados localmente también se admiten las autenticaciones de **Windows** y **DST**. Tendrá que configurar el tipo de conexión del clúster cuando se cree el clúster. Una vez creado, no se puede cambiar el tipo de conexión.

Las herramientas de Visual Studio Service Fabric admiten todos los tipos de autenticación para conectarse a un clúster para la publicación. Vea [Configuración de un clúster de Service Fabric en el Portal de Azure](service-fabric-cluster-creation-via-portal.md) para obtener instrucciones acerca de cómo configurar un clúster de Service Fabric seguro.

## Configuración de las conexiones del clúster en perfiles de publicación

Si publica un proyecto de Service Fabric desde Visual Studio, el cuadro de diálogo **Publicar** le permite elegir un clúster de Azure Service Fabric haciendo clic en el botón **Seleccionar** en la sección **Extremo de conexión**. Puede iniciar sesión en su cuenta de Azure y, a continuación, seleccione un clúster existente en las suscripciones.

![El cuadro de diálogo de publicación permite a los usuarios configurar la conexión de Service Fabric.][publishdialog]

El cuadro de diálogo **Seleccionar clúster de Service Fabric** valida automáticamente la conexión del clúster. Si la validación es correcta, significa que el sistema tiene instalados los certificados correctos para conectarse al clúster de forma segura, o el clúster no es seguro. Pueden producirse errores de validación debido a problemas de red o si el sistema no se ha configurado correctamente para conectarse a un clúster seguro.

![El cuadro de diálogo Seleccionar clúster de Service Fabric permite a los usuarios configurar la conexión eligiendo una existente o creando una nueva conexión del clúster de Service Fabric.][selectsfcluster]

### Para conectarse a un clúster seguro

1.	Asegúrese de que puede obtener acceso a uno de los certificados de cliente de confianza para el clúster de destino. Normalmente, el certificado se comparte como un archivo de intercambio de información personal (.pfx, Personal Information Exchange). Vea [Configuración de un clúster de Service Fabric desde el Portal de Azure](service-fabric-cluster-creation-via-portal.md) para saber cómo configurar el servidor para conceder acceso a un cliente.

2.	Instale el certificado de confianza. Para ello, haga doble clic en el archivo .pfx o use el script de PowerShell Import-PfxCertificate para importar los certificados. Instale el certificado en la ubicación **Cert:\\LocalMachine\\My**. Acepte toda la configuración predeterminada cuando importe el certificado.

3.	Elija el comando **Publicar...** en el menú contextual del proyecto para abrir el cuadro de diálogo **Publicar aplicación de Azure** y, a continuación, seleccione el clúster de destino. La herramienta resuelve automáticamente la conexión y guarda los parámetros de conexión segura en el perfil de publicación.

4.	[Opcional]: puede editar el perfil de publicación para especificar una conexión de clúster segura.

    Dado que está editando manualmente el archivo XML de perfil de publicación para especificar la información del certificado, asegúrese de anotar el nombre de almacén de certificados, ubicación de almacén y huella digital de certificado. Necesitará proporcionar estos valores para la ubicación del almacén y el nombre del almacén del certificado. Consulte [Recuperación de la huella digital de un certificado] (https://msdn.microsoft.com/es-ES/library/ms734695(v=vs.110).aspx) para obtener más información.

    Los parámetros *ClusterConnectionParameters* le permiten especificar los parámetros de PowerShell que usar cuando se conecte con el clúster de Service Fabric. Los parámetros válidos son los aceptados por el cmdlet Connect-ServiceFabricCluster. Vea [Conectar ServiceFabricCluster](https://msdn.microsoft.com/es-ES/library/mt125938.aspx) para obtener una lista de parámetros disponibles.

    Si va a publicar en un clúster remoto, deberá especificar los parámetros adecuados para ese clúster concreto. El siguiente es un ejemplo de para conectarse a un clúster no seguro:

    `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`

    Este es un ejemplo para conectarse a un clúster seguro basado en el certificado x509:

    ```
    <ClusterConnectionParameters
    ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
    X509Credential="true"
    ServerCertThumbprint="0123456789012345678901234567890123456789"
    FindType="FindByThumbprint"
    FindValue="9876543210987654321098765432109876543210"
    StoreLocation="CurrentUser"
    StoreName="My" />
    ```

5.	Edite cualquier otra configuración necesaria, como los parámetros de actualización y la ubicación del archivo de parámetros de aplicaciones y, a continuación, publique la aplicación desde el cuadro de diálogo **Publicar aplicación de Service Fabric** en Visual Studio.

## Pasos siguientes
Para obtener más información acerca del acceso a los clústeres de Service Fabric, vea [Visualización del clúster mediante el Explorador de Service Fabric](service-fabric-visualizing-your-cluster.md).

<!--Image references-->
[publishdialog]: ./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]: ./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png

<!---HONumber=Nov15_HO4-->