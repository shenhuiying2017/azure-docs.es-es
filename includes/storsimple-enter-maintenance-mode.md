<properties
   pageTitle="Acceder al modo de mantenimiento"
   description="Se explica cómo poner el dispositivo StorSimple en el modo de mantenimiento."
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

### Para acceder al modo de mantenimiento

1. En el menú de la consola serie, seleccione la opción 1, **Iniciar sesión con acceso completo**.

2. Escriba la contraseña. La contraseña predeterminada es **Password1**.

3. En el símbolo del sistema, escriba

    **Enter-HcsMaintenanceMode**

4. Verá un mensaje de advertencia que indica que el modo de mantenimiento interrumpe todas las solicitudes de E/S y la conexión al Portal de administración de servidor. También se le solicitará la confirmación. Escriba **Y** para acceder al modo de mantenimiento.

    Ambos controladores se reiniciarán. Una vez completado el reinicio, aparecerá otro mensaje que indica que el dispositivo está en modo de mantenimiento.

<!--HONumber=52-->
