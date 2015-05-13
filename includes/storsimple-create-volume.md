<properties 
   pageTitle="Crear un volumen"
   description="Se explica cómo agregar un volumen a un dispositivo StorSimple."
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
   ms.date="04/28/15"
   ms.author="v-sharos" />

### Para crear un volumen

1. En la página **Inicio rápido** del dispositivo, haga clic en **Agregar un volumen**. Se iniciará el Asistente para agregar volúmenes.

2. En el Asistente para agregar volúmenes, en **Configuración básica**:
   1. Escriba un **Nombre** para el volumen.
   2. Especifique la **Capacidad aprovisionada** para el volumen. **La capacidad del volumen debe ser de entre 1 GB y 64 TB.**
   3. En la lista desplegable, seleccione el **Tipo de uso** para el volumen. Para los datos de archivado a los que se accede con menos frecuencia, seleccione un **Volumen de archivado**. Para los demás tipos de datos, seleccione **Volumen principal**.
   4. Haga clic en el icono de flecha ![icono de flecha](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) para ir a la página siguiente.

     ![Agregar volumen](./media/storsimple-create-volume/HCS_AddVolume1M-include.png)

3. En el cuadro de diálogo **Configuración adicional**, agregue un nuevo registro de control de acceso \(ACR\):
   1. Proporcione un **Nombre** para el ACR.
   2. En **Nombre del iniciador iSCSI**, proporcione el nombre completo del iSCSI \(IQN\) del host de Windows. Si no tiene el IQN, vaya a [Obtener el IQN de un host de Windows Server](#get-the-iqn-of-a-windows-server-host).
   3. En **¿Copia de seguridad predeterminada para este volumen?**, seleccione la casilla **Habilitar**. La copia de seguridad predeterminada creará una directiva que se ejecuta a las 22:30 cada día \(hora del dispositivo\) y crea una instantánea en la nube de este volumen.

     >[AZURE.NOTE]Después de habilitar aquí la copia de seguridad, no se puede revertir. Necesitará modificar el volumen para modificar esta configuración.

     ![Agregar volumen](./media/storsimple-create-volume/HCs_AddVolume2M-include.png)

4. Haga clic en el icono de marca de verificación ![icono de marca de verificación](./media/storsimple-create-volume/HCS_CheckIcon-include.png). Se creará un volumen con la configuración especificada.




<!--HONumber=52-->
