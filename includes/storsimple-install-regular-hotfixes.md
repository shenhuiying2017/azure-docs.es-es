<properties
   pageTitle="Instalar revisiones regulares"
   description="Se explica cómo usar Windows PowerShell para StorSimple para instalar revisiones regulares."
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
### Para instalar revisiones regulares a través de Windows PowerShell para StorSimple

1. Conéctese a la consola serie del dispositivo. Para obtener más información, consulte [Para conectarse a través de la consola serie](#to-connect-through-the-serial-console).

2. En el menú de la consola serie, seleccione la opción 1, **iniciar sesión con acceso completo**. Escriba la contraseña. La contraseña predeterminada es **Password1**.

3. En el símbolo del sistema, escriba:

     **Start-HcsHotfix**

   >[AZURE.IMPORTANT]> > - Este comando solo se aplica a las revisiones regulares. Este comando se ejecuta en un solo controlador, pero se actualizarán ambos controladores. > - Es posible que observe una conmutación por error del controlador durante el proceso de actualización. Sin embargo, la conmutación por error no afectará a la disponibilidad o el funcionamiento del sistema.

4. Cuando se le solicite, proporcione la ruta de acceso a la carpeta compartida de red que contiene los archivos de la revisión.

5. Se le pedirá confirmación. Escriba **Y** para continuar con la instalación de la revisión.

<!--HONumber=52-->
