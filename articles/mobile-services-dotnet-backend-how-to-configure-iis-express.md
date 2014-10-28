<properties pageTitle="Configure IIS Express for local testing of Mobile Service" metaKeywords="Azure Mobile Services, .NET Backend, IIS Express" description="Learn how to configure IIS Express to allow connections to a local mobile service project for testing." authors="glenga" title="Configure the local web server to allow connections to a local mobile service" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Configuración del servidor web local para permitir conexiones a un servicio móvil local

Servicios móviles de Azure le permite crear su servicio móvil en Visual Studio con uno de los lenguajes .NET compatibles y, a continuación, publicarlo en Azure. Una de las ventajas principales de usar un back-end .NET para el servicio móvil es la capacidad de ejecutar, realizar pruebas y depurar el servicio móvil en el equipo local o la máquina virtual antes de publicarlo en Azure.

Para realizar una prueba de un servicio móvil localmente con los clientes que se ejecutan en un emulador, una máquina virtual o una estación de trabajo independiente, tiene que configurar el servidor web local y el equipo host para permitir las conexiones al puerto y la dirección IP de la estación de trabajo. En este tema se muestra cómo configurar IIS Express para permitir conexiones al servicio móvil hospedado localmente.

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express][]]

  [mobile-services-how-to-configure-iis-express]: ../includes/mobile-services-how-to-configure-iis-express.md
