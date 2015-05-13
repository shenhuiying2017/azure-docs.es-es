<properties 
	pageTitle="Codificación de elementos multimedia con Dolby Digital Plus" 
	description="En este tema se describe cómo codificar el contenido multimedia con Dolby Digital Plus." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2015" 
	ms.author="juliako"/>

#Codificación de elementos multimedia con Dolby Digital Plus

El Codificador multimedia de Azure admite la codificación **Dolby (r) Digital Plus**. Dolby(r) Digital Plus, o Enhanced AC-3 (E-AC-3), es un códec de audio de sonido envolvente avanzado diseñado específicamente para contenido multimedia en constante evolución. Desde centros de entretenimiento y PC a teléfonos móviles y streaming en línea, Dolby Digital Plus define el audio de alta fidelidad. Conseguirá la distinguida experiencia de cine de Dolby con todo su entretenimiento. Dolby Digital Plus se basa en tecnologías básicas de Dolby Digital, el estándar establecido para cines, difusión y sonido envolvente de centro de entretenimiento. A medida que proliferan los dispositivos móviles, Dolby Digital Plus también emerge como el estándar del entretenimiento móvil. Sus nuevas tecnologías avanzadas para la mejora de audio proporcionan la mejor calidad de sonido y un ahorro de ancho de banda adicional. Como resultado, obtiene un contenido excelente con menos interrupciones, incluso cuando el ancho de banda es limitado.


##Configuración del Codificador multimedia de Azure para codificar con Dolby Digital Plus

###Obtención del procesador del codificador multimedia de Azure 

Dolby Digital Plus es compatible con el Codificador multimedia de Azure. Para obtener una referencia del **Codificador multimedia de Azure**, consulte el tema [Obtención de procesadores multimedia](media-services-get-media-processor.md).

###<a id="configure_preset"></a>Configuración de opciones del Codificador multimedia de Azure

Al configurar las opciones de codificación para su uso con el Codificador multimedia de Azure, había varios valores predefinidos representados por cadenas fáciles de recordar. El codificador Dolby Digital Plus ofrece una amplia gama de controles, vea [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx) para obtener más información. Por consiguiente, no hay ningún valor preestablecidos de cadena pregenerado que use este códec. Debe especificar la configuración deseada del codificador en un archivo XML y enviar estos datos con su tarea, tal como se muestra en el ejemplo de código siguiente:
	
	string configuration = File.ReadAllText(pathToXMLConfigFile));

    ITask task = job.Tasks.AddNew("My Dolby Digital Plus Encode Task",
        processor,
        configuration,
        _clearConfig);

En este tema se describen varios valores preestablecidos XML de ejemplo que configuran las opciones del codificador. El elemento que se usa para configurar la codificación Dolby Digital Plus es [<DolbyDigitalPlusAudioProfile>](https://msdn.microsoft.com/library/azure/dn296500.aspx), que aparece como un nodo secundario del elemento <AudioProfile> en un valor preestablecido XML del Codificador multimedia de Azure. Este elemento XML contiene una serie de atributos que controlan distintos elementos de la codificación.

##Codificación a Dolby Digital Plus 5.1 multicanal

Para codificar a Dolby Digital Plus 5.1 multicanal, establezca los atributos Codec y EncoderMode en "DolbyDigitalPlus". El número de canales codificados se especifica mediante el atributo AudioCodingMode del elemento <DolbyDigitalPlusAudioProfile>. Para una codificación multicanal 5.1, establezca AudioCodingMode en "Mode32". 

El siguiente valor preestablecido XML contiene un valor preestablecido XML completo del Codificador multimedia de Azure que genera un archivo MP4 con vídeo H264 de banda ancha en 1.080 p y audio Dolby Digital Plus 5.1 multicanal. Este valor preestablecido también especifica la codificación de un canal de efectos de baja frecuencia (LFE), que se especifica estableciendo el atributo LFEOn en true. Cualquier atributo no especificado tendrá sus valores predeterminados.

Este valor preestablecido XML se debe pasar al **Codificador multimedia de Azure** para crear un trabajo de codificación, como se describe en [este](media-services-dotnet-codificar-asset.md) tema (solo pasará el valor predefinido XML al completo en lugar de una cadena preestablecida, como se describe [aquí](#configure_preset)).


	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode32"
	              LFEOn="True"
	              SamplesPerSecond="48000"
	              BandwidthLimitingLowpassFilter="True"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="512"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="3"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Balanced"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="64"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	                <StreamInfo
	                  Size="1920, 1080">
	                  <Bitrate>
	                    <ConstantBitrate
	                      Bitrate="6000"
	                      IsTwoPass="False"
	                      BufferWindow="00:00:05" />
	                  </Bitrate>
	                </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codificación en Dolby Digital Plus Stereo

Para codificar en Dolby Digital Plus Stereo, establezca los atributos Codec y EncoderMode en "DolbyDigitalPlus". El número de canales codificados se especifica con el atributo AudioCodingMode. Para una codificación estéreo, establezca AudioCodingMode en "Mode20". El siguiente ejemplo XML preestablecido muestra el elemento <DolbyDigitalPlusAudioProfile> usado para codificar a audio 5.1. Cualquier atributo no especificado tendrá sus valores predeterminados.

Este valor preestablecido XML se debe pasar al **Codificador multimedia de Azure** para crear un trabajo de codificación, como se describe en [este](media-services-dotnet-codificar-asset.md) tema (solo pasará el valor predefinido XML al completo en lugar de una cadena preestablecida, como se describe [aquí](#configure_preset)).

	<?xml version="1.0" encoding="utf-16"?>
	<!--Created for Azure Media Encoder, May 26 2013 -->
	  <Preset
	    Version="5.0">
	    <Job />
	    <MediaFile
	      DeinterlaceMode="AutoPixelAdaptive"
	      ResizeQuality="Super"
	      AudioGainLevel="1"
	      VideoResizeMode="Stretch">
	      <OutputFormat>
	        <MP4OutputFormat
	          StreamCompatibility="Standard">
	        <AudioProfile Condition="SourceContainsAudio">
	            <DolbyDigitalPlusAudioProfile
	              Codec="DolbyDigitalPlus"
	              EncoderMode="DolbyDigitalPlus"
	              AudioCodingMode="Mode20"
	              LFEOn="False"
	              SamplesPerSecond="48000"
	              DialogNormalization="-31">
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="128"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	            </DolbyDigitalPlusAudioProfile>
	        </AudioProfile>
	          <VideoProfile Condition="SourceContainsVideo">
	            <HighH264VideoProfile
	              BFrameCount="1"
	              EntropyMode="Cabac"
	              RDOptimizationMode="Speed"
	              HadamardTransform="False"
	              SubBlockMotionSearchMode="Speed"
	              MultiReferenceMotionSearchMode="Speed"
	              ReferenceBFrames="False"
	              AdaptiveBFrames="True"
	              SceneChangeDetector="True"
	              FastIntraDecisions="False"
	              FastInterDecisions="False"
	              SubPixelMode="Quarter"
	              SliceCount="0"
	              KeyFrameDistance="00:00:05"
	              InLoopFilter="True"
	              MEPartitionLevel="EightByEight"
	              ReferenceFrames="4"
	              SearchRange="32"
	              AutoFit="True"
	              Force16Pixels="False"
	              FrameRate="0"
	              SeparateFilesPerStream="True"
	              SmoothStreaming="False"
	              NumberOfEncoderThreads="0">
	              <Streams
	                AutoSize="False"
	                FreezeSort="False">
	              <StreamInfo
	                Size="852, 480">
	                <Bitrate>
	                  <ConstantBitrate
	                    Bitrate="2200"
	                    IsTwoPass="False"
	                    BufferWindow="00:00:05" />
	                </Bitrate>
	              </StreamInfo>
	              </Streams>
	            </HighH264VideoProfile>
	          </VideoProfile>
	        </MP4OutputFormat>
	      </OutputFormat>
	    </MediaFile>
	  </Preset>

##Codificación a varios archivos MP4 

Puede codificar a varios archivos MP4 dentro de un único valor preestablecido XML. Para cada archivo MP4 que desea generar, agregue un elemento <Valor preestablecido> en la configuración. Cada elemento <Valor preestablecido> puede contener información de configuración de vídeo, audio o ambos.

###Configuración

La configuración siguiente generará los resultados siguientes:

- 8 Video-only MP4 files
	- 1080p Video @ 6000 kbps
	- 1080p Video @ 4700 kbps
	- 720p Video @ 3400 kbps
	- 960 x 540 Video @ 2250 kbps
	- 960 x 540 Video @ 1500 kbps
	- 640 x 380 Video @ 1000 kbps
	- 640 x 380 Video @ 650 kbps
	- 320 x 180 Video @ 400 kbps

- 5 Audio-only MP4 files
	- AAC Audio Stereo @ 128 kbp
	- AAC Audio 5.1 @ 512 kbps
	- Dolby Digital Plus Stereo @ 128 kbps
	- Dolby Digital Plus 5.1 Multichannel @ 512 kbps
	- AAC Stereo @ 56 kbps
- A .ism manifest
- An XML file listing the properties of the generated MP4 files.
		
		<?xml version="1.0" encoding="utf-16"?>
		<!--Created for Azure Media Encoder, May 16 2013 -->
		<Presets>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"   
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <VideoProfile Condition="SourceContainsVideo">
		            <HighH264VideoProfile
		              BFrameCount="3"
		              EntropyMode="Cabac"
		              RDOptimizationMode="Speed"
		              HadamardTransform="False"
		              SubBlockMotionSearchMode="Speed"
		              MultiReferenceMotionSearchMode="Balanced"
		              ReferenceBFrames="False"
		              AdaptiveBFrames="True"
		              SceneChangeDetector="True"
		              FastIntraDecisions="False"
		              FastInterDecisions="False"
		              SubPixelMode="Quarter"
		              SliceCount="0"
		              KeyFrameDistance="00:00:05"
		              InLoopFilter="True"
		              MEPartitionLevel="EightByEight"
		              ReferenceFrames="4"
		              SearchRange="64"
		              AutoFit="True"
		              Force16Pixels="False"
		              FrameRate="0"
		              SeparateFilesPerStream="True"
		              SmoothStreaming="False"
		              NumberOfEncoderThreads="0">
		              <Streams
		                AutoSize="False"
		                FreezeSort="False">
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="6000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1920, 1080">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="4700"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="1280, 720">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="3400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="2250"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="960, 540">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1500"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="1000"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="640, 360">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="650"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		                <StreamInfo
		                  Size="320, 180">
		                  <Bitrate>
		                    <ConstantBitrate
		                      Bitrate="400"
		                      IsTwoPass="False"
		                      BufferWindow="00:00:05" />
		                  </Bitrate>
		                </StreamInfo>
		              </Streams>
		            </HighH264VideoProfile>
		          </VideoProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="PropagateSourceTimeStamps">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="6"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="2"
		              AudioCodingMode="Mode20"
		              LFEOn="False"
		              NinetyDegreePhaseShiftSurrounds="False"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="False"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="128"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile Condition="SourceContainsAudio">
		            <DolbyDigitalPlusAudioProfile
		              Codec="DolbyDigitalPlus"
		              EncoderMode="DolbyDigitalPlus"
		              Channels="6"
		              AudioCodingMode="Mode32"
		              LFEOn="True"
		              NinetyDegreePhaseShiftSurrounds="True"
		              ThreeDBAttenuationSurrounds="False"
		              DolbySurroundMode="NotIndicated"
		              StereoDownmixPreference="NotIndicated"
		              LtRtCenterMixLevel="-3"
		              LoRoCenterMixLevel="-3"
		              LtRtSurroundMixLevel="-3"
		              LoRoSurroundMixLevel="-3"
		              LFELowpassFilter="True"
		              SamplesPerSecond="48000"
		              BandwidthLimitingLowpassFilter="True"
		              DCHighpassFilter="True"
		              LineModeDynamicRangeControl="FilmStandard"
		              RFModeDynamicRangeControl="FilmStandard"
		              DialogNormalization="-31">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="512"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </DolbyDigitalPlusAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		  <Preset
		    Version="5.0">
		    <Job />
		    <MediaFile
		      DeinterlaceMode="AutoPixelAdaptive"
		      ResizeQuality="Super"
		      NormalizeAudio="True"
		      AudioGainLevel="1"
		      VideoResizeMode="Stretch">
		      <OutputFormat>
		        <MP4OutputFormat
		          StreamCompatibility="Standard">
		          <AudioProfile>
		            <AacAudioProfile
		              Codec="AAC"
		              Channels="2"
		              BitsPerSample="16"
		              SamplesPerSecond="48000">
		              <Bitrate>
		                <ConstantBitrate
		                  Bitrate="56"
		                  IsTwoPass="False"
		                  BufferWindow="00:00:00" />
		              </Bitrate>
		            </AacAudioProfile>
		          </AudioProfile>
		        </MP4OutputFormat>
		      </OutputFormat>
		    </MediaFile>
		  </Preset>
		</Presets>

##Creación de servicios de codificación comercial

Es posible que algunos clientes quieran crear un servicio de codificación comercial encima de Servicios multimedia de Azure. Si está creando un servicio "compilado sobre otro", es importante que todos los parámetros de codificación Dolby Digital Plus estén disponibles. Asegúrese de que todos los parámetros dentro de la etiqueta <DolbyDigitalPlusAudioProfile> se muestren y el usuario final los pueda configurar. Póngase en contacto con prolicensingsupport@dolby.com para obtener instrucciones sobre la disponibilidad de estos parámetros.

##Uso de Dolby Professional Loudness Metering (DPLM)

El Codificador multimedia de Azure puede usar el SDK de DPLM para medir la sonoridad del diálogo en el audio de entrada y establecer el valor correcto de DialogNormalization. Esta característica está habilitada solo si el audio se está codificando en Dolby Digital Plus. DPLM se configura en un archivo de configuración preestablecido mediante el elemento <LoudnessMetering>, que es un elemento secundario del elemento <DolbyDigitalPlusAudioProfile>. El valor preestablecido de ejemplo siguiente muestra cómo configurar DPLM:
	
	<?xml version="1.0" encoding="utf-16"?>
	<Preset
	  Version="5.0">
	  <Job />
	  <MediaFile>
	    <OutputFormat>
	      <MP4OutputFormat
	        StreamCompatibility="Standard">
	    <AudioProfile>
	             <DolbyDigitalPlusAudioProfile
	               Codec="DolbyDigitalPlus"
	               EncoderMode="DolbyDigitalPlus"
	               DolbySurroundMode="NotIndicated"
	               StereoDownmixPreference="NotIndicated"
	               SamplesPerSecond="48000"
	               AudioCodingMode="Mode20"
	               Channels="2"
	               BitsPerSample="24">
	               <LoudnessMetering
	                 Mode= "ITU_R_BS_1770_2_DI"
	                 SpeechThreshold="20"
	                 TruePeakEmphasis="false"
	                 TruePeakDCBlock="false" />
	              <Bitrate>
	                <ConstantBitrate
	                  Bitrate="320"
	                  IsTwoPass="False"
	                  BufferWindow="00:00:00" />
	              </Bitrate>
	     </DolbyDigitalPlusAudioProfile>
	    </AudioProfile>
	      </MP4OutputFormat>
	    </OutputFormat>
	  </MediaFile>
	</Preset>

El elemento <LoudnessMetering> solo puede especificarse dentro de un elemento <DolbyDigitalPlusAudioProfile>. Si se usa el elemento <LoudnessMetering> no se debe usar el atributo DialogNormalization. El codificador genera un error si se usan el elemento <LoudnessMetering>  y el atributo DialogNormalization. Todos los atributos de LoudnessMetering son opcionales y el codificador usará de manera predeterminada los valores recomendados por Dolby Laboratories, Inc.

Cada atributo se describe en las secciones siguientes.

###Atributo mode

Este atributo determina el modo de medición de sonoridad. Los valores permitidos son:

 
**ITU_R_BS_1770_2_DI** (predeterminado): indica ITU-R BS.1770-2 más Dialogue Intelligence

**ITU_R_BS_1770_1_DI**: indica ITU-R BS.1770-1 más Dialogue Intelligence

**ITU_R_BS_1770_2**: indica ITU-R BS.1770-2

**LEQA_DI**: indica Leq(A) más Dialogue Intelligence

**Nota:**

el modo** EBU R128** se puede lograr con **ITU_R_BS_1770_2_DI**

El **Leq (A)** se incluye únicamente por motivos de herencia y solo se debe utilizar en flujos de trabajo heredados específicos.

El **ITU** ha lanzado recientemente una actualización con título BS.1770-3, que es equivalente a BS.1770-2 con TruePeakDCBlock y TruePeakEmphasis establecidos en false.

###Atributo SpeechThreshold

Especifica un umbral de voz utilizado por DPLM para producir un resultado de sonoridad integrado (por ejemplo, seleccionar entre acceso de voz, acceso de nivel y ningún acceso). El intervalo del valor de umbral de voz es de 0% a 100%, en incrementos de 1%. Este parámetro tiene un efecto solo cuando DPLM se configura en un modo que usa Dialogue Intelligence, lo que implica que solo se puede especificar si el modo se establece en ITU_R_BS_1770_2_DI o ITU_R_BS_1770_1_DI. El valor predeterminado, cuando el modo es ITU_R_BS_1770_2_DI o ITU_R_BS_1770_1_DI, es 20%. Los valores para este atributo deben establecerse en el intervalo de 0, 1 a 100.

###Atributo TruePeakDCBlock

Este parámetro de entrada especifica si el bloque de controlador de dominio dentro de la medición de true‐peak está habilitado (true) o deshabilitado (false). Para obtener más detalles del bloque de controlador de dominio, consulte ITU‐R BS.1770‐2. El valor predeterminado es false.

###Atributo TruePeakEmphasis

Especifica si el filtro de énfasis dentro de la medición de true‐peak está habilitado (true) o deshabilitado (false). Para obtener más detalles del filtro de énfasis, consulte ITU‐R BS.1770‐2. El valor predeterminado es false.


###Resultados de DPLM

Cuando una tarea de codificación especifica el uso de DPLM, los resultados de la medición de sonoridad se incluirán en los metadatos XML en el recurso de salida. El siguiente es un ejemplo.
	
	<LoudnessMeteringResultParameters 
	     DPLMVersionInformation="Dolby Professional Loudness Metering Development Kit Version 1.0"
	     DialogNormalization="-15" 
	     IntegratedLoudness="-14.8487606" 
	     IntegratedLoudnessGatingMethod="2" 
	     IntegratedLoudnessSpeechPercentage="11.673481" 
	     SamplePeak="-0.7028221" 
	     TruePeak="0.705999851" />

A continuación se describe cada atributo.

**DPLMVersionInformation**: una cadena que representa la versión de SDK de DPLM utilizada.

**DialogNormalization**: el valor de DialNorm, en decibelios, medido desde el audio de entrada que se incrustará en la secuencia DD+ de salida, en el intervalo {-31, -30,..., -1} dB.

**IntegratedLoudness**: la sonoridad integrada, medida por DPLM, en el intervalo de -70 a + 10 LKFS/dBFS (donde dBFS solo se usa cuando el modo se establece en LEQA_DI).

**IntegratedLoudnessGatingMethod**: los valores válidos son: 0 - Ninguno/sin puerta; 1 - Voz controlada; 2 - Nivel controlado.

**IntegratedLoudnessSpeechPercentage**: este resultado contiene el porcentaje de escala de tiempo de los medios de entrada donde se detecta la voz. Los valores varían de 0% a 100%.

**SamplePeak**: este resultado contiene el valor de muestra absoluto más grande en cualquier canal desde que se restableció la medición, y los intervalos varían de -70 a + 10 dBFS.

**TruePeak**: este resultado contiene el valor absoluto true‐peak más grande en cualquier canal desde que se restableció la medición. Para obtener una descripción de pico verdadero, consulte ITU‐R BS.1770‐2. Los valores pueden oscilar entre -70 y 12,04 dBTP.

<!--HONumber=52-->