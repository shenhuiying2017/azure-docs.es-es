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
   ms.date="05/13/2016"
   ms.author="SubramaR"/>

# Service Fabric en Azure

Service Fabric está disponible en estos momentos como una vista previa limitada en Linux. Permite compilar, implementar y administrar aplicaciones de alta disponibilidad y escalabilidad en ese entorno de la misma forma que en Windows. Además, ahora los marcos de Service Fabric de alto nivel (Reliable Services y Reliable Actors) se pueden compilar en Java.

## Sistemas operativos y lenguajes de programación compatibles

La vista previa limitada admite la creación de clústeres de desarrollo one-box, así como clústeres de varias máquinas en Azure con Ubuntu Server 15.10.

Puede compilar [servicios ejecutables invitados](service-fabric-deploy-existing-app.md) con cualquier lenguaje o marco. También puede usar Java para crear servicios basados en los marcos de Reliable Services y Reliable Actors.

>[AZURE.NOTE] Reliable Collections aún no es compatible con Java.

## Participación en la vista previa

Si está interesado en participar en el programa de vista previa limitada, [rellene la encuesta](http://aka.ms/sflinuxsurvey) para que podamos comprender mejor su escenario y sus requisitos. La vista previa será muy limitada al principio e irá ampliándose con el tiempo.

Tenga en cuenta que la versión de Service Fabric para Linux, desde el punto de vista conceptual, equivale a la que está disponible en Windows (excepto las características del sistema operativo y la compatibilidad con lenguajes de programación), por lo que la mayor parte de nuestra [documentación](http://aka.ms/servicefabricdocs) es pertinente y le ayudará a familiarizarse con la tecnología.

## Pasos siguientes

Familiarizarse con los marcos de [Reliable Actors](service-fabric-reliable-actors-introduction.md) y [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0518_2016-->