<properties
    pageTitle="Publicación de una aplicación en un clúster remoto con Visual Studio | Microsoft Azure"
    description="Obtenga información acerca de los pasos necesarios para publicar una aplicación en un clúster de Service Fabric remoto con Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Publicación de una aplicación en un clúster remoto con Visual Studio

Las herramientas de Service Fabric de Visual Studio proporcionan una forma fácil, repetible y que permite ejecutar scripts de publicar una aplicación en un clúster de Service Fabric. Esto se realiza mediante un script de implementación de PowerShell con perfiles de publicación. Los perfiles de publicación son archivos de formato .XML que almacenan información esencial de publicación. El cuadro de diálogo **Publicar aplicación de Service Fabric** también se ha agregado para permitir a los usuarios publicar fácilmente una aplicación en un clúster local o remoto. Se capturan los cambios de configuración realizados a través del cuadro de diálogo Publicar en los perfiles de publicación. Esto le permite usar manualmente la configuración de edición de publicación en un proceso de automatización.

## Artefactos necesarios para publicar una aplicación en un clúster de Service Fabric

### Deploy-FabricApplication.ps1

Se trata de un script de PowerShell que usa una ruta de acceso del perfil de publicación como un parámetro para la publicación de aplicaciones de Service Fabric.

### Perfiles de publicación

La carpeta del proyecto de aplicación de Service Fabric denominada **PublishProfiles** contiene los archivos **Cloud.XML** y **Local.XML**. Estos son los "perfiles de publicación" que almacenan información esencial para publicar una aplicación, como: -Parámetros de conexión del clúster de Service Fabric -Ruta de acceso a un archivo de parámetros de la aplicación -Configuración de actualizaciones

### Archivos de parámetros de la aplicación

La carpeta del proyecto de aplicación de Service Fabric denominada **ApplicationParameters** contiene archivos XML para los valores de los parámetros de manifiesto de la aplicación especificados por el usuario. Los archivos de manifiesto de la aplicación pueden tener parámetros para que se puedan usar otros valores para la configuración de implementación.

Por ejemplo, puede cambiar el número de particiones para que se adapten a entornos diferentes en cada implementación. Cuando se crea un proyecto, la configuración del manifiesto de la aplicación, como **TargetReplicaSetSize** y **PartitionCount**, se convierte en parámetros. El valor predeterminado de estos parámetros se especifica en la sección **Parámetros** del archivo de manifiesto de la aplicación (ApplicationManifest.XML, ubicado en el proyecto de aplicación de Service Fabric). Los valores se agregan al archivo de parámetros de la aplicación invalidarán los valores predeterminados en el archivo de manifiesto de la aplicación.

>[AZURE.NOTE]Para los servicios de actores, debe crear primero el proyecto para generar los parámetros en el archivo de manifiesto.

Este es un ejemplo de un archivo de manifiesto de aplicación:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Publicación de una aplicación en un clúster de Service Fabric mediante el cuadro de diálogo Publicar aplicación de Service Fabric

Los pasos siguientes muestran cómo publicar una aplicación mediante el uso del cuadro de diálogo **Publicar aplicación de Service Fabric** proporcionado por las herramientas de Service Fabric de Visual Studio.

1. En el menú contextual del proyecto de aplicación de Service Fabric, elija **Publicar...** para ver el cuadro de diálogo **Publicar aplicación de Service Fabric**.

    ![][0]

    El archivo seleccionado en el cuadro de lista desplegable **Perfil objetivo** es donde se guardan todas las configuraciones, excepto las **versiones del manifiesto**. Puede volver a usar un perfil existente o a crear uno nuevo en **<Administrar perfiles...>** en el cuadro de lista desplegable **Perfil objetivo**. Cuando se elige un perfil de publicación, su contenido aparece en los campos correspondientes del cuadro de diálogo. Para guardar los cambios en cualquier momento, elija el vínculo **Guardar perfil**.

1. La sección **Punto de conexión** le permite especificar el punto de conexión de la publicación del clúster de Service Fabric local o remoto. Para agregar o cambiar el punto de conexión, elija el botón **Seleccionar...**. El cuadro de diálogo **Seleccionar clúster de Service Fabric** muestra los puntos de conexión disponibles del clúster de Service Fabric en los que se puede publicar. (Si aún no está registrado para una suscripción de Azure, se le pedirá que lo haga). Elija un punto de conexión.

    ![][1]

    Una vez elegido un punto de conexión, Visual Studio valida la conexión con el clúster de Service Fabric seleccionado. Si el clúster no está protegido, Visual Studio podrá conectarse a él inmediatamente. Sin embargo, si el clúster está protegido, deberá instalar un certificado en el equipo local antes de continuar. Consulte [Configuración de conexiones seguras](service-fabric-visualstudio-configure-secure-connections.md) para obtener más información. Cuando haya terminado, elija el botón **Aceptar**. El clúster seleccionado aparece en el cuadro de diálogo **Publicar aplicación de Service Fabric**.

1. El cuadro de lista desplegable **Archivo de parámetros de la aplicación** permite desplazarse a un archivo de parámetros de la aplicación. Un archivo de parámetros de la aplicación contiene los valores especificados por el usuario para los parámetros del archivo de manifiesto de aplicación. Para agregar o cambiar un parámetro, elija el botón **Editar**. Escriba o cambie el valor del parámetro en la cuadrícula **Parámetros**. Cuando haya terminado, elija el botón **Guardar**.

    ![][2]

1. La casilla **Actualizar la aplicación** le permite especificar si esta acción de publicación es una actualización. Las acciones de publicación de actualización difieren de las acciones de publicación habituales. Consulte [Actualización de la aplicación de Service Fabric](service-fabric-application-upgrade.md) para ver una lista de las diferencias. Para configurar opciones de actualización, elija el vínculo **Configurar valores de actualización**. Se muestra el editor de parámetros de actualización. Consulte [Configuración de la actualización de una aplicación de Service Fabric](service-fabric-visualstudio-configure-upgrade.md) para obtener más información sobre los parámetros de actualización.

1. Seleccione el botón **Versiones de manifiesto** para ver el cuadro de diálogo **Editar versiones**. Debe actualizar la aplicación y las versiones de servicio para que tenga lugar la actualización. Consulte el [Tutorial de actualización de aplicación de Service Fabric](service-fabric-application-upgrade-tutorial.md) para saber cómo influyen en un proceso de actualización las versiones de la aplicación y del manifiesto de servicio.

    ![][3]

    Si las versiones de servicio y de aplicación usan el control de versiones semántico como 1.0.0 o valores numéricos en formato de 1.0.0.0, seleccione la opción **Actualizar automáticamente las versiones de aplicación y de servicio**. Si elige esta opción, los números de versión del servicio y de la aplicación se actualizan automáticamente cada vez que se actualiza una versión del código, configuración o paquete de datos. Si prefiere editar manualmente las versiones, desactive la casilla para deshabilitar esta característica.

    >[AZURE.NOTE]Para que todas las entradas de paquete aparezcan para un proyecto de actor, primero cree el proyecto para generar las entradas en los archivos de manifiesto de servicio.

1. Cuando haya terminado de especificar todos los valores necesarios, elija el botón **Publicar** para publicar su aplicación en el clúster de Service Fabric seleccionado. La configuración especificada se aplica al proceso de publicación.

## Pasos siguientes

Para obtener información sobre cómo automatizar el proceso de publicación en un entorno de integración continua, consulte [Configuración de la integración continua de Service Fabric](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->