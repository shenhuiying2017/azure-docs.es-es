---
title: "Configuración del codificador FMLE para enviar una transmisión en vivo con velocidad de bits única | Microsoft Docs"
description: "En este tema se muestra cómo configurar el codificador Flash Media Live Encoder (FMLE) para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: e831048f34ecf6e89595adc4bfd58b5977e04bdb
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Uso del codificador FMLE para enviar una transmisión por secuencias en directo de velocidad de bits única
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

En este tema se muestra cómo configurar el codificador [Flash Media Live Encoder](http://www.adobe.com/products/flash-media-encoder.html) (FMLE) para enviar una transmisión con velocidad de bits única a canales AMS habilitados para la codificación en directo. Para obtener más información, consulte [Uso de canales habilitados para realizar la codificación en directo con Servicios multimedia de Azure](media-services-manage-live-encoder-enabled-channels.md).

En este tutorial se muestra cómo administrar Servicios multimedia de Azure (AMS) con la herramienta Explorador de Servicios multimedia de Azure (AMSE). Esta herramienta solo se ejecuta en Windows PC. Si se encuentra en Mac o Linux, use Azure Portal para crear [canales](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) y [programas](media-services-portal-creating-live-encoder-enabled-channel.md).

Tenga en cuenta que en este tutorial se describe el uso de AAC. Sin embargo, FMLE no es compatible con AAC de forma predeterminada. Deberá adquirir un complemento para la codificación de AAC como el de MainConcept: [Complemento de AAC](http://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

## <a name="prerequisites"></a>Requisitos previos
* [Creación de una cuenta de Azure Media Services](media-services-portal-create-account.md)
* Asegúrese de que hay un punto de conexión de streaming en ejecución. Para obtener más información, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-portal-manage-streaming-endpoints.md)
* Debe instalar la última versión de la herramienta [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Inicie la herramienta y conéctese a la cuenta de AMS.

## <a name="tips"></a>Sugerencias
* Siempre que sea posible, use una conexión a Internet por cable.
* Una buena regla general al determinar los requisitos de ancho de banda consiste en duplicar las velocidades de bits de streaming. Aunque no se trata de un requisito obligatorio, contribuirá a mitigar el impacto de la congestión de la red.
* Cuando se usen codificadores por software, cierre todos los programas innecesarios.

## <a name="create-a-channel"></a>Crear un canal
1. En la herramienta AMSE, navegue a la pestaña **Directo** y haga clic con el botón derecho dentro del área de canales. Seleccione **Crear canal...** en el menú.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Especifique un nombre de canal (el campo de descripción es opcional). En Configuración de canal, seleccione **Estándar** para la opción Live Encoding, con el protocolo de entrada establecido en **RTMP**. Puede dejar todas las demás opciones como están.

    Asegúrese de que la opción **Iniciar el nuevo canal ahora** esté seleccionada.

3. Haga clic en **Crear canal**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> El canal puede tardar hasta 20 minutos en iniciarse.
>
>

Mientras se inicia el canal puede [configurar el codificador](media-services-configure-fmle-live-encoder.md#configure_fmle_rtmp).

> [!IMPORTANT]
> Tenga en cuenta que la facturación comienza tan pronto como el canal entra en un estado Listo. Para obtener más información, consulte [Estados del canal](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Configuración del codificador FMLE
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

### <a name="configuration-steps"></a>Pasos de configuración
1. Vaya a la interfaz de Flash Media Live Encoder (FMLE) en el equipo que esté usando.

    La interfaz es una página principal de configuración. Tome nota de la siguiente configuración recomendada para empezar a trabajar con streaming mediante FMLE.

   * Format (Formato): H.264, Frame Rate (Velocidad de fotogramas): 30.00
   * Input Size (Tamaño de entrada): 1280 x 720
   * Bit Rate (Velocidad de bits): 5000 kbps (se puede ajustar en función de las limitaciones de red)  

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Al usar orígenes entrelazados, marque la opción "Deinterlace" (Desentrelazar).
2. Seleccione el icono de llave junto a Format (Formato). Estas opciones de configuración adicionales deben ser:

   * Profile (Perfil): Main (Principal)
   * Level (Nivel): 4.0
   * Keyframe Frequency (Frecuencia de fotogramas clave): 2 seconds (2 segundos)

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Establezca la siguiente configuración de audio importante:

   * Format (Formato): AAC
   * Sample Rate (Frecuencia de muestreo): 44100 kHz
   * Bitrate (Velocidad de bits): 192 kbps

     ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Obtenga la dirección URL de entrada del canal para asignarla al **punto de conexión de RTMP**de FMLE.

    Navegue de nuevo a la herramienta AMSE y compruebe el estado de finalización del canal. Una vez que ha cambiado el estado de **Iniciando** a **En ejecución**, puede obtener la dirección URL de entrada.

    Mientras se ejecuta el canal, haga clic con el botón derecho en el nombre del canal, desplácese hacia abajo y mantenga el puntero sobre **Copy Input URL to clipboard** (Copiar dirección URL de entrada en el Portapapeles) y seleccione **Primary Input URL** (Dirección URL de entrada principal).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Pegue esta información en el campo **Dirección URL de FMS** de la sección de salida y asigne un nombre de transmisión.

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Para obtener redundancia adicional, repita estos pasos con la dirección URL de entrada secundaria.
6. Seleccione **Conectar**.

> [!IMPORTANT]
> Antes de hacer clic en **Conectar**, **debe** asegurarse de que el canal esté listo.
> Además, asegúrese de no dejar el canal en un estado Listo sin una fuente de contribución de entrada durante más de 15 minutos.
>
>

## <a name="test-playback"></a>Reproducción de pruebas

Vaya a la herramienta AMSE y haga clic con el botón derecho en el canal que se va a probar. En el menú, mantenga el puntero sobre **Playback the Preview** (Reproducir la vista previa) y seleccione **with Azure Media Player** (con Azure Media Player).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Si la transmisión aparece en el reproductor, entonces el codificador se configuró correctamente para conectarse a AMS.

Si se recibe un error, se deberá restablecer el canal y ajustar la configuración del codificador. Consulte el tema de [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.  

## <a name="create-a-program"></a>Creación de un programa
1. Una vez confirmada la reproducción de canales, cree un programa. En la pestaña **Live** (Directo) de la herramienta AMSE, haga clic con el botón derecho dentro del área de programas y seleccione **Create New Program** (Crear programa).  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Dé nombre al programa y, si es necesario, ajuste el valor de **Duración de la ventana de archivo** (que de forma predeterminada es 4 horas). También puede especificar una ubicación de almacenamiento o dejar el valor predeterminado.  
3. Active la casilla **Iniciar el programa ahora** .
4. Haga clic en **Crear programa**.  

    >[!NOTE]
    >La creación de programas tarda menos que la creación de canales.
        
5. Cuando el programa esté en ejecución, confirme la reproducción. Para ello, haga clic con el botón derecho en el programa y vaya a **Playback the program(s)** (Reproducir los programas). Luego, seleccione **with Azure Media Player** (con Azure Media Player).  
6. Una vez confirmada, haga clic con el botón derecho de nuevo en el programa y seleccione **Copy the Output URL to Clipboard** (Copiar la dirección URL de salida en el Portapapeles) o recupere esta información con la opción **Program information and settings**(Información y configuración del programa) en el menú.

La transmisión está ahora preparada para insertarse en un reproductor o distribuirse a una audiencia para su visualización en directo.  

## <a name="troubleshooting"></a>solución de problemas
Consulte el tema de [solución de problemas](media-services-troubleshooting-live-streaming.md) para obtener instrucciones.

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
