---
title: "Configuración del codificador Haivision KB para enviar una transmisión en vivo con velocidad de bits única | Microsoft Docs"
description: "En este tema se muestra cómo configurar el codificador Haivision KB para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en vivo."
services: media-services
documentationcenter: 
author: dbgeorge
manager: vsood
editor: 
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/02/2018
ms.author: juliako;dbgeorge
ms.openlocfilehash: 25077cd9338a2764c6dff9e755812033685f6641
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Use el codificador Haivision KB para enviar una transmisión en vivo con velocidad de bits única
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

En este tema se muestra cómo configurar el [codificador en vivo Haivision KB](https://www.haivision.com/products/kb-series/) para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en vivo. Para obtener más información, consulte [Uso de canales habilitados para Live Encoding con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

En este tutorial se muestra cómo administrar Azure Media Services (AMS) con la herramienta Explorador de Azure Media Services (AMSE). Esta herramienta solo se ejecuta en Windows PC. Si se encuentra en Mac o Linux, use Azure Portal para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>requisitos previos
*   Acceso a un codificador de Haivision KB, que ejecuta software v5.01, o superior.
* [Creación de una cuenta de Azure Media Services](media-services-portal-create-account.md)
* Asegúrese de que hay un punto de conexión de streaming en ejecución. Para obtener más información, consulte [Administración de extremos de streaming en una cuenta de Media Services](media-services-portal-manage-streaming-endpoints.md)
* Debe instalar la última versión de la herramienta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Inicie la herramienta y conéctese a la cuenta de AMS.

## <a name="tips"></a>Sugerencias
* Siempre que sea posible, use una conexión a Internet por cable.
* Una buena regla general al determinar los requisitos de ancho de banda consiste en duplicar las velocidades de bits de streaming. Aunque no se trata de un requisito obligatorio, contribuye a mitigar el impacto de la congestión de la red.
* Cuando se usen codificadores por software, cierre todos los programas innecesarios.

## <a name="create-a-channel"></a>Crear un canal
1. En la herramienta AMSE, navegue a la pestaña **Live** (Live) y haga clic con el botón derecho dentro del área de canales. Seleccione **Crear canal...** en el menú.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Especifique un nombre de canal (el campo de descripción es opcional). En Configuración de canal, seleccione **Estándar** para la opción Live Encoding, con el protocolo de entrada establecido en **RTMP**. Puede dejar todas las demás opciones como están. Asegúrese de que la opción **Iniciar el nuevo canal ahora** esté seleccionada.
3. Haga clic en **Crear canal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> El canal puede tardar hasta 20 minutos en iniciarse.

## <a name="configure-the-haivision-kb-encoder"></a>Configuración del codificador Haivision KB
En este tutorial se usa la siguiente configuración de salida. En el resto de esta sección se describen los pasos de configuración con más detalle.

Vídeo:
-   Codec (Códec): H.264
-   Profile (Perfil): High (Level 4.0) (Alto [Nivel 4.0])
-   Bitrate (Velocidad de bits): 5000 kbps
-   Keyframe (Fotograma clave): 2 seconds (60 seconds) (2 segundos [60 segundos])
-   Frame Rate (Velocidad de fotogramas): 30

Audio:
-   Codec (Códec): AAC (LC)
-   Bitrate (Velocidad de bits): 192 kbps
-   Sample Rate (Frecuencia de muestreo): 44,1 kHz

## <a name="configuration-steps"></a>Pasos de configuración
1.  Inicie sesión en la interfaz de usuario de Haivision KB.
2.  Haga clic en el **botón de menú** en el centro de control de canal y seleccione **Add Channel** (Agregar canal).  
    ![Captura de pantalla 2017-08-14 at 9.15.09 AM.png](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Escriba el **nombre del canal** en el campo Name (Nombre) y haga clic en Next (Siguiente).  
    ![Captura de pantalla 2017-08-14 at 9.19.07 AM.png](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Seleccione **Channel Input Source** (Origen de entrada del canal) en la lista desplegable **Input Source** (Origen de entrada) y haga clic en Next (Siguiente).
    ![Captura de pantalla 2017-08-14 at 9.20.44 AM.png](./media/media-services-configure-kb-live-encoder/step4.png)
5.  En la lista desplegable **Encoder Template** (Plantilla del codificador), elija **H264-720-AAC-192** y haga clic en Next (Siguiente).
    ![Captura de pantalla 2017-08-14 at 9.23.15 AM.png](./media/media-services-configure-kb-live-encoder/step5.png)
6.  En la lista desplegable **Select New Output** (Seleccionar nueva salida), elija **RTMP** y haga clic en Next (Siguiente).  
    ![Captura de pantalla 2017-08-14 at 9.27.51 AM.png](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Desde la ventana **Channel Output** (Salida de canal), rellene la información de transmisiones de Azure. Pegue el vínculo **RTMP** de la configuración inicial del canal en el área **Server** (Servidor). En el tipo de área **Output Name** (Nombre de salida) en el nombre del canal. En el área Stream Name Template (Plantilla de nombre de transmisión), utilice la plantilla RTMPStreamName_%video_bitrate% para dar nombre a la transmisión.
    ![Captura de pantalla 2017-08-14 at 9.33.17 AM.png](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Haga clic en Next (Siguiente) y después haga clic en Done (Hecho).
9.  Haga clic en el **botón de reproducción** para iniciar el canal del codificador.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Prueba de reproducción
Vaya a la herramienta AMSE y haga clic con el botón derecho en el canal que se va a probar. En el menú, mantenga el puntero sobre Playback the Preview (Reproducir la vista previa) y seleccione with Azure Media Player (con Azure Media Player).

Si la transmisión aparece en el reproductor, entonces el codificador se configuró correctamente para conectarse a AMS.

Si se recibe un error, debe restablecer el canal y ajustar la configuración del codificador. Para más información, consulte el artículo de solución de problemas.

## <a name="create-a-program"></a>Creación de un programa
1.  Una vez confirmada la reproducción de canales, cree un programa. En la pestaña Live (Directo) de la herramienta AMSE, haga clic con el botón derecho dentro del área de programas y seleccione Create New Program (Crear programa).
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Dé nombre al programa y, si es necesario, ajuste el valor de Archive Window Length (Duración de la ventana de archivo), que de forma predeterminada es cuatro horas. También puede especificar una ubicación de almacenamiento o dejar el valor predeterminado.
2.  Active la casilla Start the Program now (Iniciar el programa ahora).
3.  Haga clic en Create Program (Crear programa).
4.  Cuando el programa esté en ejecución, confirme la reproducción. Para ello, haga clic con el botón derecho en el programa y vaya a Playback the program(s) (Reproducir los programas). Luego, seleccione with Azure Media Player (con Azure Media Player).
5.  Una vez confirmada, haga clic con el botón derecho de nuevo en el programa y seleccione Copy the Output URL to Clipboard (Copiar la dirección URL de salida en el Portapapeles) o recupere esta información con la opción Program information and settings (Información y configuración del programa) en el menú.

La transmisión está ahora preparada para insertarse en un reproductor o distribuirse a una audiencia para su visualización en directo.

> [!NOTE]
> La creación de programas tarda menos que la creación de canales.