<properties
   pageTitle="Azure Service Fabric en Linux | Microsoft Azure"
   description="Los clústeres de Service Fabric admiten Linux y Java, lo que significa que podrá implementar y hospedar aplicaciones de Service Fabric escritas en Java para Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="SubramaR"/>

# Service Fabric en Azure

La versión preliminar estará disponible públicamente el 26 de septiembre, como se anunció [en esta entrada de blog](https://azure.microsoft.com/blog/service-fabric-on-linux-support-available-this-month/). La versión preliminar de Service Fabric en Linux permite compilar, implementar y administrar aplicaciones de alta disponibilidad y escalabilidad en ese entorno de la misma forma que en Windows. Además, ahora los marcos de Service Fabric de alto nivel (Reliable Services y Reliable Actors) se encuentran disponibles en Java (Linux).

> [AZURE.VIDEO service-fabric-linux-preview]

## Sistemas operativos y lenguajes de programación compatibles

La versión preliminar limitada admite la creación de clústeres de desarrollo one-box, así como clústeres de varias máquinas en Azure con Ubuntu Server 16.04.

Puede compilar [servicios ejecutables invitados](service-fabric-deploy-existing-app.md) con cualquier lenguaje o marco. También puede usar Java o C# para crear servicios basados en los marcos de Reliable Services y Reliable Actors, además de organizar los contenedores de Docker.

>[AZURE.NOTE] Reliable Collections aún no es compatible con Linux.

Service Fabric en Linux es conceptualmente equivalente a Service Fabric en Windows (excepto las características del sistema operativo y la compatibilidad con lenguajes de programación). Por lo tanto, la mayoría de nuestra [documentación actual](http://aka.ms/servicefabricdocs) tiene como objetivo ayudarlo a familiarizarse con la tecnología.

## Pasos siguientes

Familiarizarse con los marcos de [Reliable Actors](service-fabric-reliable-actors-introduction.md) y [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0921_2016-->