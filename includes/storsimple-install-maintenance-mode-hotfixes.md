<properties
   pageTitle="Instalar revisiones en el modo de mantenimiento"
   description="Se explica cómo usar Windows PowerShell para StorSimple para instalar revisiones en el modo de mantenimiento."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="tysonn" /> 
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/23/2015"
   ms.author="v-sharos" />

### Para instalar revisiones en el modo de mantenimiento a través de Windows PowerShell para StorSimple

> [AZURE.IMPORTANT]En el modo de mantenimiento, deberá aplicar la revisión primero en un controlador y, a continuación, en el otro controlador.

1. Active el modo de mantenimiento del dispositivo. Consulte Cambiar los modos del dispositivo para obtener instrucciones sobre cómo acceder al modo de mantenimiento.

2. Para aplicar la revisión, escriba:

     **Start-HcsHotfix**

3. Cuando se le solicite, proporcione la ruta de acceso a la carpeta compartida de red que contiene los archivos de la revisión.

4. Se le pedirá confirmación. Escriba **Y** para continuar con la instalación de la revisión.

5. Después de aplicar la revisión en un controlador, inicie sesión en el otro controlador. Aplique la revisión tal y como lo hizo con el controlador anterior.

6. Una vez aplicadas las revisiones, salga del modo de mantenimiento. Consulte [Para salir del modo de mantenimiento](#to-exit-maintenance-mode) para obtener instrucciones.

<!--HONumber=52-->
