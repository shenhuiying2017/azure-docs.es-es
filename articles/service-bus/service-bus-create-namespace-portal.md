<properties
   pageTitle="Creación de un espacio de nombres del Bus de servicio mediante el Portal de Azure | Microsoft Azure"
   description="Para empezar a trabajar con el Bus de servicio, necesitará un espacio de nombres. Aquí le mostramos cómo crear uno mediante el Portal de Azure."
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Creación de un espacio de nombres del Bus de servicio mediante el Portal de Azure
El espacio de nombres es un contenedor común para todos los componentes de mensajería. Varias colas y temas pueden residir en un único espacio de nombres, y los espacios de nombres suelen servir de contenedores de aplicación. Actualmente existen dos formas diferentes de crear espacios de nombres del Bus de servicio.

1.	Portal de Azure (este artículo)

2.	[Plantillas de ARM][create-namespace-using-arm]

##Creación de un espacio de nombres en el Portal de Azure
[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

¡Enhorabuena! Ha creado un espacio de nombres del Bus de servicio.

##Pasos siguientes

Consulte nuestro repositorio de GitHub, con ejemplos que muestran algunas de las características más avanzadas de mensajería del Bus de servicio de Azure. [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

[create-namespace-using-arm]: ./service-bus-resource-manager-overview.md
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->