<properties
   pageTitle="Configuración de un clúster de Service Fabric en el Portal de Azure | Microsoft Azure"
   description="Configuración de un clúster de Service Fabric en el Portal de Azure"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Configuración de un clúster de Service Fabric en el Portal de Azure

Esta página le ayuda con la configuración de un clúster de Service Fabric. Se supone que su suscripción tiene suficientes núcleos para implementar las máquinas virtuales de IaaS que conformarán este clúster.


## Creación del clúster

1. Inicie sesión en el Portal de Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Haga clic en el **+ Nuevo** para agregar una nueva plantilla de recursos. Busque nuestra plantilla en Marketplace en Todo, se llama **Service Fabric Cluster** (puede llegar hasta **Todo** haciendo clic en la categoría de nivel superior Marketplace > y luego buscando "Fabric" dentro de "Todo" y presionando Entrar. Algunas veces el filtro automático no funciona, por eso asegúrese de que **presiona Entrar**) ![SearchforServiceFabricClusterTemplate][SearchforServiceFabricClusterTemplate]

3. Seleccione "Service Fabric Cluster" en la lista
4. Vaya hasta la hoja Service Fabric Cluster, haga clic en **Crear** y proporcionen los detalles del clúster
5. Cree un **nuevo Grupo de recursos (RG)** con el mismo nombre que el clúster (de esta forma es más fácil encontrarlos en el futuro, lo cual es particularmente útil cuando está intentando realizar cambios en la implementación o eliminar el clúster).

  	Nota: Aunque puede usar un grupo de recursos existente, es aconsejable crear uno nuevo. Con ello se facilita mucho la eliminación de clústeres que ya no necesita

 	 ![CreateRG][CreateRG]


6. No olvide seleccionar la **suscripción** en la que desea que se implemente el clúster, especialmente si tiene varias suscripciones.

7. Seleccione una **ubicación** en la lista desplegable (si desea crear otra ubicación, el valor predeterminado es Oeste de EE. UU.)

8. Configure el **Tipo de nodo**. Los tipos de nodo pueden considerarse como equivalentes a las "Funciones" en los servicios en la nube. Definen los tamaños de máquina virtual, el número de máquinas virtuales y sus propiedades. El clúster puede tener más de un tipo de nodo. La única restricción es que necesitará al menos un tipo de nodo (el principal o el primero que defina en el portal) que tenga un mínimo de 5 máquinas virtuales.
	1. Seleccione el tamaño de máquina virtual o plan de tarifa que necesita (el valor predeterminado es D4 estándar, si solo va a usar este clúster para probar la aplicación, puede seleccionar D2 o cualquier tamaño más pequeño de máquina virtual)	
	2. Elija el número de máquinas virtuales, más adelante puede ampliar o reducir el número de máquinas en un tipo de nodo, de todas formas el tipo de nodo principal o el primer tipo tiene que tener al menos 5 máquinas virtuales
	3. Elija un nombre para el tipo de nodo (1 a 12 caracteres de longitud y que contenga únicamente caracteres alfanuméricos )	
	4. Elija el nombre de usuario y la contraseña del escritorio remoto de máquina virtual
	5. **Consideraciones acerca del tipo de nodo cuando tiene varios tipos de nodo**. Si planea implementar un clúster con un solo tipo de nodo, vaya al paso siguiente.

		- Para explicar este concepto, veamos un ejemplo. Si desea implementar una aplicación que contiene un servicio "Front-End" y un servicio "Back-End" y desea colocar el servicio "Front-End" en máquinas virtuales más pequeñas (con tamaños de máquina virtual como A2, D2 etc.) que tengan puertos abiertos a Internet, y el servicio "Back-End", de proceso intensivo, en máquinas virtuales de mayor tamaño (con tamaños como D4, D6, D12, etc) que no son accesibles desde Internet.
		- Aunque puede poner ambos servicios en un solo tipo de nodo, se recomienda que los coloque en un clúster con dos tipos de nodos; cada tipo puede tener distintas propiedades, como la conectividad a Internet, el número de máquinas virtuales y el tamaño de las mismas, y su escala se puede modificar por separado.
		- Defina primero el tipo de nodo que va a tener un mínimo de 5 máquinas virtuales. Los demás tipos de nodo pueden tener un mínimo de 1 máquina virtual.
					

  	![CreateNodeType][CreateNodeType]

9. **Puertos de la aplicación**: si tiene previsto implementar las aplicaciones en el clúster de forma inmediata, agregue los puertos que desea abrir para sus aplicaciones en este tipo de nodo (o en los tipos de nodos que haya creado). Puede agregar puertos al tipo de nodo más adelante mediante la modificación del equilibrador de carga asociado a este tipo de nodo (tendrá que agregar un sondeo y, a continuación, agregar el sondeo a las reglas del equilibrador de carga). Si lo hace ahora será más fácil, ya que la automatización de portal agregará los sondeos y las reglas que sean necesarios al equilibrador de carga.
	1. Puede encontrar los puertos de la aplicación en los manifiestos de servicio que forman parte del paquete de aplicación. Vaya a cada una de las aplicaciones, abra los manifiestos de servicio y tome nota de todos los puntos de conexión de "entrada" que necesitan su aplicaciones para comunicarse con el mundo exterior.
	2. Agregue todos los puertos, separados por comas, en el campo "Puntos de conexión de entrada de la aplicación". El punto de conexión del cliente TCP es 19000 de forma predeterminada, por lo que no es necesario especificarlo. Por ejemplo, mi aplicación necesita que el puerto "83" esté abierto. Encontrará esto en el archivo servicemanifest.xml en el paquete de aplicación (puede haber más de un servicemanifest.xml).

  La mayoría de las aplicaciones de ejemplo usan los puertos 80 y 8081, así que agréguelos si planea implementar ejemplos en este clúster.

  ![Puertos][Ports]



10. **Opcional: Propiedades de selección de ubicación**. No tiene que agregar ninguna configuración aquí, el sistema agrega una propiedad de selección de ubicación predeterminada de "NodeTypeName". Puede agregar más si su aplicación lo necesita. 

  
## Configuraciones de seguridad

En la actualidad, Service Fabric solo admite la protección de clústeres mediante un certificado X509. Antes de comenzar este proceso, tendrá que cargar el certificado en el almacén de claves. Consulte [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md) para obtener más detalles sobre el proceso.

La protección de los clústeres es opcional pero es muy recomendable. Si decide no proteger el clúster, deberá colocar el Modo de seguridad en "Ninguno".

Pueden encontrar información detallada sobre las consideraciones de seguridad y cómo se documentan en [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)

![SecurityConfigs][SecurityConfigs]



## Opcional: Configuración de diagnósticos

De forma predeterminada, los diagnósticos se habilitan en el clúster para ayudar a solucionar los problemas. Si desea deshabilitar los diagnósticos:

1. Navegue hasta la hoja de las configuraciones de diagnósticos.

2. Cambie el estado al valor Desactivado.

## Opcional: Configuración de Fabric

Esta es una opción avanzada, que le permite cambiar la configuración predeterminada para el clúster de Service Fabric. Se recomienda **no cambiar** los valores predeterminados a menos que esté seguro de que la aplicación o el clúster necesitan el cambio.

## Finalización de la creación del clúster
Puede hacer clic en el **Resumen** para ver los valores de configuración que proporcionó o descargar la plantilla del Administrador de recursos de Azure que se usará para implementar el clúster. Una vez que haya proporcionado los valores de configuración obligatorios, se habilitará el botón Crear y podrá iniciar el proceso de creación de clúster.
 

Puede ver el progreso en las NOTIFICACIONES (haga clic en el icono "Campana" cerca de la barra de estado hacia la derecha de la pantalla). Si hizo clic en "anclar a Panel de inicio" al crear el clúster, verá "Implementando el clúster de Service Fabric" anclado en el panel de inicio

![Notificaciones][Notifications]

## Visualización del estado del clúster

Una vez finalizada la implementación, puede inspeccionar el clúster en el portal.

1. Vaya a **Examinar** y haga clic en el recurso **Clústeres de Service Fabric**.

2. Busque el clúster y haga clic en él.

  ![BrowseCluster][BrowseCluster]

3. Ahora puede ver los detalles del clúster en el panel, incluida la dirección de IP pública del clúster. Tenga en cuenta que al mantener el mouse sobre **Dirección IP pública del clúster** aparecerá un portapapeles en el que puede hacer clic para copiarlo.

  ![ClusterDashboard][ClusterDashboard]

  La parte del monitor de nodo en la hoja del panel de clúster, indica el número de máquinas virtuales que funcionan correctamente y las que no. Puede encontrar más detalles sobre el estado de mantenimiento en la sección [Introducción al modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md) de nuestra documentación


## Conexión al clúster e implementación de una aplicación

Con el clúster configurado, ahora puede conectarse y comenzar la implementación de aplicaciones.

1. En un equipo que tenga instalado SDK de Service Fabric, inicie Windows PowerShell.

2. Ejecute uno de los siguientes conjuntos de comandos PS dependiendo de si creó un clúster seguro o no seguro.
 

- Opción 1 **Conexión con un clúster no seguro**. 

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

Ejecute los siguientes comandos para implementar su aplicación, reemplazando las rutas de acceso que se muestran con las apropiadas para su equipo.

 - Opción 2 **Conexión con un clúster seguro**.

Ejecute lo siguiente para configurar el certificado en el equipo que va a usar para ejecutar el comando PS "Connect-serviceFabricCluster"

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Ejecute el siguiente PS para conectarse ahora a un clúster seguro. Los detalles del certificado son los mismos que asignó en el portal

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
Por ejemplo, el comando PS anterior debe ser similar al siguiente.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


Ejecute los siguientes comandos para implementar su aplicación, reemplazando las rutas de acceso que se muestran con las apropiadas para su equipo. En el ejemplo siguiente implementa la aplicación de ejemplo de recuento de palabras.

Copie el paquete en el clúster al que se conectó en el paso anterior.


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
````
Registre el tipo de aplicación con Service Fabric.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
````

Cree una nueva instancia del tipo de aplicación que acaba de registrar.

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
````

Ahora abra el explorador que haya escogido y conecte con el punto de conexión en el que está escuchando la aplicación. Para nuestra aplicación de ejemplo, la dirección URL sería.

http://sfcluster4doc.westus.cloudapp.azure.com:31000



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md)
- [Introducción al modelo de mantenimiento de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!-----HONumber=AcomDC_1203_2015-->