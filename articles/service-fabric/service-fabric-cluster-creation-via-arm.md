<properties
   pageTitle="Configuración de un clúster de Service Fabric mediante una plantilla de ARM | Microsoft Azure"
   description="Configuración de un clúster de Service Fabric mediante una plantilla de ARM"
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

# Configuración de un clúster de Service Fabric mediante una plantilla de ARM

Esta página le ayuda con la configuración de un clúster de Service Fabric mediante una plantilla de ARM. Se supone que su suscripción tiene suficientes núcleos para implementar las máquinas virtuales de IaaS que conformarán este clúster.

## Requisitos previos

- Si quiere configurar un clúster seguro, asegúrese de que ha cargado un certificado X509 en el almacén de claves. Necesitará la dirección URL del almacén de origen, la dirección URL del certificado y la huella digital del certificado.
-  Consulte [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md) para más detalles sobre el proceso.

## Adquisición de una plantilla de ARM de ejemplo

1. Las plantillas de ARM de ejemplo están disponibles en la [Galería de plantillas de inicio de rápido Azure en github](https://github.com/Azure/azure-quickstart-templates). Todas las plantillas de Service Fabric empiezan por el nombre "service-fabric..". Puede buscar "fabric" en el repositorio o simplemente desplazarse hacia abajo hasta el conjunto de plantillas de ejemplo.
2. Para ayudar a encontrar rápidamente lo que puede estar buscando, las plantillas se han nombrado de la siguiente manera:
	1. [service-fabric-unsecure-cluster-5-node-1-nodetype](http://go.microsoft.com/fwlink/?LinkId=716923) para indicar una plantilla de clúster no seguro de nodo único de 5 nodos. 
	3. [service-fabric-secure-cluster-5-node-1-nodetype-wad](http://go.microsoft.com/fwlink/?LinkID=716924) para indicar una plantilla de clúster seguro de nodo único de 5 nodos habilitada para WAD. 
	4. [service-fabric-secure-cluster-10-node-2-nodetype-wad](http://go.microsoft.com/fwlink/?LinkId=716925) para indicar una plantilla de clúster seguro de dos nodos de 10 nodos habilitada para WAD. 
	

## Creación de una plantilla de ARM personalizada

2. Aquí tiene dos opciones. 
	1. Puede adquirir una plantilla de ejemplo en la [Galería de plantillas de inicio rápido de Azure en github](https://github.com/Azure/azure-quickstart-templates) y realizar cambios en ella.
	2. Iniciar sesión en el portal de Azure y usar las páginas del portal de Service Fabric para generar la plantilla para personalizarla. A continuación se describe el proceso para ello.
3. Inicie sesión en el Portal de Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).
2. Siga el proceso de creación del clúster, tal como se describe en [Creación del clúster de Service Fabric a través del portal](service-fabric-cluster-creation-via-portal.md), pero no haga clic en ****create**, en su lugar, vaya a Resumen y descargue la plantilla.

 ![DownloadTemplate][DownloadTemplate]

## Implementación de la plantilla de ARM en Azure con Azure PS

Consulte [Implementación de plantillas de ARM con PS](resource-group-template-deploy.md) para obtener instrucciones detalladas sobre cómo implementar la plantilla con PowerShell.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Pasos siguientes
- [Seguridad de los clústeres de Service Fabric](service-fabric-cluster-security.md) 
- [Administración de aplicaciones de Service Fabric en Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Introducción al modelo de estado de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[DownloadTemplate]: ./media/service-fabric-cluster-creation-via-arm/DownloadTemplate.png

<!---HONumber=AcomDC_1210_2015-->