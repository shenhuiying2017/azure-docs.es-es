---
title: "Configuración del codificador Elemental Live para enviar una transmisión en vivo con velocidad de bits única | Microsoft Docs"
description: "En este tema se muestra cómo configurar el codificador Elemental Live para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo."
services: media-services
documentationcenter: 
author: cenkdin
manager: cfowler
editor: 
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 668a3ab46a70c0ee25fa87031d27c0f4333ec89c
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Use el codificador Elemental Live para enviar una transmisión en directo con velocidad de bits única
> [!div class="op_single_selector"]
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

En este tema se muestra cómo configurar el codificador [Elemental Live](http://www.elementaltechnologies.com/products/elemental-live) para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo.  Para obtener más información, consulte [Uso de canales habilitados para Live Encoding con Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

En este tutorial se muestra cómo administrar Azure Media Services (AMS) con la herramienta Explorador de Azure Media Services (AMSE). Esta herramienta solo se ejecuta en Windows PC. Si se encuentra en Mac o Linux, use Azure Portal para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Requisitos previos
* Debe tener un conocimiento práctico del uso de la interfaz web de Elemental Live para crear eventos en directo.
* [Creación de una cuenta de Azure Media Services](media-services-portal-create-account.md)
* Asegúrese de que hay un punto de conexión de streaming en ejecución. Para más información, consulte [Administración de puntos de conexión de streaming en una cuenta de Media Services](media-services-portal-manage-streaming-endpoints.md).
* Debe instalar la última versión de la herramienta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Inicie la herramienta y conéctese a la cuenta de AMS.

## <a name="tips"></a>Sugerencias
* Siempre que sea posible, use una conexión a Internet por cable.
* Una buena regla general al determinar los requisitos de ancho de banda consiste en duplicar las velocidades de bits de streaming. Aunque no se trata de un requisito obligatorio, contribuirá a mitigar el impacto de la congestión de la red.
* Cuando se usen codificadores por software, cierre todos los programas innecesarios.

## <a name="elemental-live-with-rtp-ingest"></a>Elemental Live con entrada RTP
En esta sección se muestra cómo configurar el codificador Elemental Live que envía una transmisión en directo con una velocidad de bits única a través de RTP.  Para obtener más información, consulte [Transmisión MPEG TS sobre RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Crear un canal

1. En la herramienta AMSE, navegue a la pestaña **Directo** y haga clic con el botón derecho dentro del área de canales. Seleccione **Crear canal...** en el menú.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Especifique un nombre de canal (el campo de descripción es opcional). En Configuración de canal, seleccione **Estándar** para la opción Live Encoding, con el protocolo de entrada establecido en **RTP (MPEG-TS)**. Puede dejar todas las demás opciones como están.

    Asegúrese de que la opción **Iniciar el nuevo canal ahora** esté seleccionada.

3. Haga clic en **Crear canal**.

   ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> El canal puede tardar hasta 20 minutos en iniciarse.
>
>

Mientras se inicia el canal puede [configurar el codificador](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Tenga en cuenta que la facturación comienza tan pronto como el canal entra en un estado Listo. Para obtener más información, consulte [Estados del canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Configurar el codificador Elemental Live
En este tutorial se usa la siguiente configuración de salida. En el resto de esta sección se describen los pasos de configuración con más detalle.

**Vídeo**:

* Codec (Códec): H.264
* Profile (Perfil): High (Level 4.0) (Alto [Nivel 4.0])
* Bitrate (Velocidad de bits): 5000 kbps
* Keyframe (Fotograma clave): 2 seconds (60 seconds) (2 segundos [60 segundos])
* Frame Rate (Velocidad de fotogramas): 30

**Audio**:

* Codec (Códec): AAC (LC)
* Bitrate (Velocidad de bits): 192 kbps
* Sample Rate (Frecuencia de muestreo): 44,1 kHz

#### <a name="configuration-steps"></a>Pasos de configuración
1. Vaya a la interfaz web de **Elemental Live** y configure el codificador para el streaming **UDP/TS**.
2. Una vez creado un nuevo evento, desplácese hacia abajo hasta los grupos de salida y agregue el grupo de salida **UDP/TS** .
3. Para crear una salida, seleccione **New Stream** (Nueva transmisión) y haga clic en **Add Output** (Agregar salida).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > Se recomienda que el evento Elemental tenga el código de tiempo establecido en "Reloj del sistema" para ayudar a que el codificador se vuelva a conectar en el caso de un error de transmisión.
   >
   >
4. Ahora que se creó la salida, haga clic en **Agregar transmisión**. Ahora pueden configurarse las opciones de salida.
5. Baje hasta la "Transmisión 1" que acaba de crear, haga clic en la pestaña **Video** (Vídeo) a la izquierda y expanda la sección de configuración **Advanced** (Avanzada).

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Aunque Elemental Live tiene una amplia gama de opciones de personalización disponibles, se recomienda la siguiente configuración para comenzar a usar el streaming a AMS.

   * Resolution (Resolución): 1280 x 720
   * Framerate (Velocidad de fotogramas): 30
   * GOP Size (Tamaño de GOP): 60 fotogramas
   * Interlace Mode (Modo de vídeo entrelazado): Progresivo
   * Bit Rate (Velocidad de bits): 5000000 bits/s (se puede ajustar en función de las limitaciones de red)

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Obtenga la dirección URL de entrada del canal.

    Navegue de nuevo a la herramienta AMSE y compruebe el estado de finalización del canal. Una vez que ha cambiado el estado de **Iniciando** a **En ejecución**, puede obtener la dirección URL de entrada.

    Mientras se ejecuta el canal, haga clic con el botón derecho en el nombre del canal, desplácese hacia abajo y mantenga el puntero sobre **Copy Input URL to clipboard** (Copiar dirección URL de entrada en el Portapapeles) y seleccione **Primary Input URL** (Dirección URL de entrada principal).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Pegue esta información en el campo **Destino principal** de Elemental. Todas las demás configuraciones pueden quedarse con el valor predeterminado.

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Para obtener redundancia adicional, repita estos pasos con la dirección URL de entrada secundaria, creando una pestaña “Salida” independiente para el streaming UDP/TS.
3. Haga clic en **Create** (Crear), si se creó un evento, o en **Update** (Actualizar), si está modificando un evento ya existente, e inicie el codificador.

> [!IMPORTANT]
> Antes de hacer clic en **Start** (Iniciar) en la interfaz web de Elemental Live, **debe** asegurarse de que el canal esté listo.
> Además, asegúrese de no dejar el canal en un estado Listo sin un evento durante más de 15 minutos.
>
>

Cuando la transmisión lleve 30 segundos en ejecución, vuelva a la herramienta AMSE y pruebe la reproducción.  

### <a name="test-playback"></a>Prueba de reproducción

Vaya a la herramienta AMSE y haga clic con el botón derecho en el canal que se va a probar. En el menú, mantenga el puntero sobre **Playback the Preview** (Reproducir la vista previa) y seleccione **with Azure Media Player** (con Azure Media Player).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Si la transmisión aparece en el reproductor, entonces el codificador se configuró correctamente para conectarse a AMS.

Si se recibe un error, se deberá restablecer el canal y ajustar la configuración del codificador. Consulte el tema de [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.   

### <a name="create-a-program"></a>Creación de un programa
1. Una vez confirmada la reproducción de canales, cree un programa. En la pestaña **Live** (Directo) de la herramienta AMSE, haga clic con el botón derecho dentro del área de programas y seleccione **Create New Program** (Crear programa).  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Dé nombre al programa y, si es necesario, ajuste el valor de **Duración de la ventana de archivo** (que de forma predeterminada es 4 horas). También puede especificar una ubicación de almacenamiento o dejar el valor predeterminado.  
3. Active la casilla **Iniciar el programa ahora** .
4. Haga clic en **Crear programa**.  

    >[!NOTE]
    > La creación de programas tarda menos que la creación de canales.   
      
5. Cuando el programa esté en ejecución, confirme la reproducción. Para ello, haga clic con el botón derecho en el programa y vaya a **Playback the program(s)** (Reproducir los programas). Luego, seleccione **with Azure Media Player** (con Azure Media Player).  
6. Una vez confirmada, haga clic con el botón derecho de nuevo en el programa y seleccione **Copy the Output URL to Clipboard** (Copiar la dirección URL de salida en el Portapapeles) o recupere esta información con la opción **Program information and settings**(Información y configuración del programa) en el menú.

La transmisión está ahora preparada para insertarse en un reproductor o distribuirse a una audiencia para su visualización en directo.  

## <a name="troubleshooting"></a>solución de problemas
Consulte el tema de [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
