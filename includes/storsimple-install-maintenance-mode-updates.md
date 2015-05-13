<properties
   pageTitle="Instalar actualizaciones en el modo de mantenimiento"
   description="Se explica cómo usar Windows PowerShell para StorSimple para instalar actualizaciones en el modo de mantenimiento."
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
   ms.date="04/21/2015"
   ms.author="v-sharos" />

### Para instalar actualizaciones en el modo de mantenimiento a través de Windows PowerShell para StorSimple

1. Si aún no lo hizo, acceda a la consola serie del dispositivo y seleccione la opción 1, **Iniciar sesión con acceso completo**. 

2. Escriba la contraseña. La contraseña predeterminada es **Password1**.

3. En el símbolo del sistema, escriba:

   **Get-HcsUpdateAvailability**
    
    You will be notified if updates are available and whether the updates are disruptive or non-disruptive.

4. Para aplicar actualizaciones problemáticas, deberá poner el dispositivo en modo de mantenimiento. Consulte [Para acceder al modo de mantenimiento](#to-enter-maintenance-mode) para obtener instrucciones.

5. Cuando el dispositivo se encuentre en modo de mantenimiento, en el símbolo del sistema, escriba:

    **Start-HcsUpdate**

6. Se le pedirá confirmación. Después de confirmar las actualizaciones, se instalarán en el controlador al que esté accediendo en ese momento. Una vez instaladas las actualizaciones, se reiniciará el controlador.

7. Cuando finalice el reinicio del primer controlador, conéctese al otro controlador y lleve a cabo los pasos del 1 al 6.

8. Una vez actualizados ambos controladores, salga del modo de mantenimiento. Consulte [Para salir del modo de mantenimiento](#to-exit-maintenance-mode\) para obtener instrucciones.

<!--HONumber=52-->
