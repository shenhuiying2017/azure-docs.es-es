---
title: Azure Service Fabric en Linux | Microsoft Docs
description: "Los clústeres de Service Fabric admiten Linux y Java, lo que significa que podrá implementar y hospedar aplicaciones de Service Fabric escritas en Java y C# para Linux."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 459afade-145d-4ee6-b72b-ddf380ccd1bf
ms.service: service-fabric
ms.devlang: Java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: dddc9f698d9776999d406117b46285a0f90d9620
ms.contentlocale: es-es
ms.lasthandoff: 08/24/2017

---
# <a name="service-fabric-on-linux"></a>Service Fabric en Azure
La versión preliminar de Service Fabric en Linux permite compilar, implementar y administrar aplicaciones de alta disponibilidad y escalabilidad en ese entorno de la misma forma que en Windows. Los marcos de Service Fabric (Reliable Services y Reliable Actors) se encuentran disponibles en Java en Linux, además de en C# (.NET Core).  Puede compilar igualmente [servicios ejecutables invitados](service-fabric-deploy-existing-app.md) con cualquier lenguaje o marco. Además, la vista previa también admite contenedores de Docker de organización. Los contenedores de Docker pueden ejecutar archivos ejecutables invitados o servicios de Service Fabric nativos que utilizan los marcos de Service Fabric.

Service Fabric en Linux es conceptualmente equivalente a Service Fabric en Windows (excepto las características del sistema operativo y la compatibilidad con lenguajes de programación). Por lo tanto, la mayoría de nuestra [documentación actual](http://aka.ms/servicefabricdocs) tiene como objetivo ayudarlo a familiarizarse con la tecnología.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Service-Fabric-Linux-Preview/player]
>
>

## <a name="supported-operating-systems-and-programming-languages"></a>Sistemas operativos y lenguajes de programación compatibles
La versión preliminar limitada admite la creación de clústeres de desarrollo one-box, así como clústeres de varias máquinas en Azure con Ubuntu Server 16.04. La vista previa es compatible con los marcos de Reliable Actors y Reliable Stateless Services en Java y C#, además de los archivos ejecutables invitados y los contenedores de Docker de organización.  

> [!NOTE]
> Reliable Collections aún no es compatible con Linux. No son compatibles tampoco los clústeres independientes: solo se admiten clústeres one box y de varios equipos de Linux de Azure en la vista previa.
>
>


## <a name="supported-tooling"></a>Herramientas compatibles
La versión preliminar admite la interacción con el clúster a través de la CLI de Service Fabric. Para los desarrolladores de Java, se proporciona integración con Eclipse y Yeoman mediante compatibilidad de Eclipse en Linux y OSX. La integración de OSX utiliza una máquina virtual de Linux de forma interna mediante vagrant. Para los desarrolladores de C#, se proporciona integración con Yeoman para generar plantillas de aplicación.

## <a name="next-steps"></a>Pasos siguientes

* Familiarícese con los marcos de programación de [Reliable Actors](service-fabric-reliable-actors-introduction.md) y [Reliable Services](service-fabric-reliable-services-introduction.md)
* [Prepare your development environment on Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Create your first Service Fabric Java application on Linux](service-fabric-create-your-first-linux-application-with-java.md)
* [Setup Service Fabric continuous integration and deployment with Jenkins and GitHub](service-fabric-cicd-your-linux-java-application-with-jenkins.md) (Configurar la integración continua y la implementación de Service Fabric con Jenkins y GitHub)
* [Diferencias entre Service Fabric para Windows y para Linux](service-fabric-linux-windows-differences.md)

