<properties
	pageTitle="Uso del codificador Premium con varios archivos de entrada y propiedades de los componentes | Microsoft Azure"
	description="En este tema se explica cómo usar setRuntimeProperties para usar varios archivos de entrada y pasar datos personalizados al procesador de multimedia del flujo de trabajo del Codificador multimedia Premium."
	services="media-services"
	documentationCenter=""
	authors="xpouyat"
	manager="erikre"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"  
	ms.author="xpouyat;anilmur;juliako"/>

# Uso del codificador Premium con varios archivos de entrada y propiedades de los componentes

## Información general

Hay escenarios en los que podría ser necesario personalizar las propiedades de los componentes, especificar el contenido XML de lista de clips o enviar varios archivos de entrada al enviar una tarea con el procesador de multimedia del **flujo de trabajo de Codificador multimedia Premium**. A continuación, se indican algunos ejemplos:

- Superposición de texto en vídeo y definición del valor de texto (por ejemplo, la fecha actual) en tiempo de ejecución para cada vídeo de entrada
- Personalización del XML de la lista de clips (para especificar uno o varios archivos de código fuente, con o sin recorte, etc.)
- Superposición de una imagen de logotipo en la entrada de vídeo durante la codificación

Para permitir que el **flujo de trabajo de Codificador multimedia Premium** sepa que va a cambiar algunas propiedades del flujo de trabajo al crear la tarea o enviar varios archivos de entrada, tiene que usar una cadena de configuración que contenga **setRuntimeProperties** o **transcodeSource**. En este tema se explica cómo usarlos.


## Sintaxis de la cadena de configuración

La cadena de configuración para establecer en la tarea de codificación usa un documento XML con el siguiente aspecto:

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Este es el código de C# que lee la configuración XML de un archivo y lo pasa a la tarea de un trabajo:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## Personalización de las propiedades de los componentes  

### Propiedad con un valor simple
En algunos casos resulta útil personalizar una propiedad de componente junto con el archivo de flujo de trabajo que va a ejecutar el flujo de trabajo de Codificador multimedia Premium.

Supongamos que ha diseñado un flujo de trabajo que superpone texto en los vídeos y este (por ejemplo, la fecha actual) debe establecerse en tiempo de ejecución. Puede hacerlo si envía el texto como valor nuevo para la propiedad de texto del componente de superposición de la tarea de codificación. Puede utilizar este mecanismo para cambiar otras propiedades de un componente en el flujo de trabajo (por ejemplo, la posición o color de la superposición, la velocidad de bits del codificador AVC, etc.).

**setRuntimeProperties** se usa para reemplazar una propiedad de los componentes del flujo de trabajo.

Ejemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### Propiedad con un valor XML

Para establecer una propiedad que espere un valor XML, enciérrela con `<![CDATA[ and ]]>`.

Ejemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Asegúrese de no colocar un retorno de carro justo después de `<![CDATA[`.


### Valor de propertyPath

En los ejemplos anteriores, el valor de propertyPath era "/Media File Input/filename", "/inactiveTimeout" o "clipListXml". En general, es el nombre del componente seguido del nombre de la propiedad. La ruta de acceso puede tener más o menos niveles, como /primarySourceFile (si la propiedad se encuentra en la raíz del flujo de trabajo) o /Video Processing/Graphic Overlay/Opacity (si la superposición se encuentra en un grupo).

Para comprobar la ruta de acceso y el nombre de propiedad, utilice el botón de acción justo al lado de cada propiedad. Puede hacer clic en este botón de acción y seleccione **Editar**. Esto le mostrará el nombre de la propiedad y, justo encima, el espacio de nombres.

![Acción o edición](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propiedad](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## Varios archivos de entrada

Cada tarea que se envía al **flujo de trabajo de Codificador multimedia Premium** requiere dos recursos:

- El primero es un *recurso de flujo de trabajo* que contiene un archivo de flujo de trabajo. Los archivos de flujo de trabajo pueden crearse con [Diseñador de flujo de trabajo](media-services-workflow-designer.md).
- El segundo es un *recurso multimedia* que contiene los archivos multimedia para codificar.

Al enviar varios archivos multimedia al codificador del **flujo de trabajo de Codificador multimedia Premium** se aplican las restricciones siguientes:

- Todos los archivos multimedia deben encontrarse en el mismo *recurso multimedia*. No se admite el uso de varios recursos multimedia.
- En el recurso multimedia debe definir el archivo principal (idealmente, el archivo de vídeo principal que se pide procesar al codificador).
- Es necesario pasar al procesador los datos de configuración que incluyen el elemento **setRuntimeProperties** o **transcodeSource**.
  - **setRuntimeProperties** se usa para reemplazar el nombre de archivo u otra propiedad de los componentes del flujo de trabajo.
  - **transcodeSource** sirve para especificar el contenido XML de la lista de clips.

Conexiones en el flujo de trabajo:

 - Si utiliza uno o varios componentes de entrada de archivos multimedia y planea usar **setRuntimeProperties** para especificar el nombre de archivo, no conecte a estos la clavija del componente del archivo principal. Asegúrese de que no hay ninguna conexión entre el objeto del archivo principal y las entradas del archivo multimedia.
 - Si prefiere usar XML de lista de clips y un componente de origen multimedia, puede conectarlos entre sí.

![Sin conexión desde el archivo de origen principal para a la entrada del archivo multimedia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Si utiliza setRuntimeProperties para establecer el nombre de archivo, el archivo principal no se conecta a los componentes de entrada del archivo multimedia.*

![Conexión de la opción de XML de la lista de clips con el origen de lista de clips](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Puede conectar el XML de la lista de clips al origen multimedia y usar transcodeSource*


### Personalización del XML de lista de clips
Puede especificar el XML de la lista de clips en el flujo de trabajo en tiempo de ejecución con **sourceTranscode** en la configuración de la cadena XML. Esto requiere la conexión de la clavija de XML de la lista de clips al componente de origen multimedia en el flujo de trabajo.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Si quiere especificar /primarySourceFile para usar esta propiedad para denominar los archivos de salida mediante "expresiones", se recomienda pasar el XML de la lista de clips como propiedad *después* de la propiedad /primarySourceFile, para evitar que la lista de clips se reemplace por el valor de /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Con un recorte preciso de marco adicional:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## Ejemplo

Considere un ejemplo en el que desea superponer una imagen de logotipo en el vídeo de entrada durante la codificación. En este ejemplo, el vídeo de entrada se denomina "MyInputVideo.mp4" y el logotipo se denomina "MyLogo.png". Debe realizar los pasos siguientes:

- Creación de un recurso de flujo de trabajo con el archivo de flujo de trabajo (consulte el siguiente ejemplo)
- Creación de un recurso multimedia, que contenga dos archivos: MyInputVideo.mp4 como archivo principal y MyLogo.png.
- Envío de una tarea al procesador multimedia del flujo de trabajo premium de Codificador multimedia con los recursos de entrada anteriores y especificación de la siguiente cadena de configuración

Configuración:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


En el ejemplo anterior, el nombre del archivo de vídeo se envía al componente de entrada de archivos multimedia y a la propiedad primarySourceFile. El nombre del archivo de logotipo se envía a otra entrada de archivos multimedia que se conecta al componente de superposición gráfica.

>[AZURE.NOTE]El nombre de archivo de vídeo se envía a la propiedad primarySourceFile. Esto se hace para usar esta propiedad en el flujo de trabajo para crear el nombre de archivo de salida correcto mediante expresiones, por ejemplo.


### Creación paso a paso de un flujo de trabajo que superpone un logotipo en la parte superior del vídeo     

Estos son los pasos para crear un flujo de trabajo que toma como entrada dos archivos: un vídeo y una imagen. Añadirá la imagen en la parte superior del vídeo.

Abra **Diseñador de flujo de trabajo** y seleccione **File** (Archivo), **New Workspace** (Nueva área de trabajo), **Transcode Blueprint** (Transcodificar plano).

El nuevo flujo de trabajo muestra tres elementos:

- Primary Source File (Archivo de origen principal)
- Clip List XML (XML de la lista de clips)
- Output File/Asset (Recurso/Archivo de salida)

![Nuevo flujo de trabajo de codificación](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nuevo flujo de trabajo de codificación*


Para aceptar el archivo multimedia de entrada, debe empezar con la incorporación de un componente de entrada de archivo multimedia. Para agregar un componente al flujo de trabajo, busque en el cuadro de búsqueda del repositorio y arrastre la entrada deseada al panel del diseñador.

A continuación, agregue el archivo de vídeo que se usará para diseñar el flujo de trabajo. Para ello, haga clic en el panel de fondo de Diseñador de flujo de trabajo y busque la propiedad de archivo de origen principal en el panel de propiedades de la derecha. Haga clic en el icono de carpeta y seleccione el archivo de vídeo adecuado.

![Primary Source File (Archivo de origen principal)](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primary Source File (Archivo de origen principal)*


A continuación, especifique el archivo de vídeo en el componente de entrada de archivo multimedia.

![Origen de entrada de archivo multimedia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Origen de entrada de archivo multimedia*


En cuanto lo haya hecho, el componente de entrada de archivos multimedia inspeccionará el archivo y rellenará sus clavijas de salida para reflejar el archivo que inspeccionó.

El siguiente paso es agregar un "Video Data Type Updater" (Actualizador de tipo de datos de vídeo) para especificar el espacio de color a Rec.709. Agregue un convertidor de formato de vídeo establecido en las opciones Data Layout/Layout type = Configurable Planar (Diseño de datos/Tipo de diseño = Planar configurable). Esto convertirá la secuencia de vídeo a un formato que puede considerarse el origen del componente de superposición.

![Actualizador de tipo de datos de vídeo y convertidor de formato](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Actualizador de tipo de datos de vídeo y convertidor de formato*

![Layout type = Configurable Planar (Tipo de diseño = Planar configurable)](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*El valor de Layout type es Configurable Planar (Planar configurable)*.

A continuación, agregue un componente de superposición de vídeo y conecte la clavija de vídeo (sin comprimir) a la clavija de vídeo (sin comprimir) de la entrada de archivo multimedia.

Agregue otra entrada de archivo multimedia (para cargar el archivo de logotipo), haga clic en este componente y cambie el nombre a Media File Input Logo (Logotipo de entrada de archivo multimedia), seleccione una imagen (por ejemplo, un archivo .png) de la propiedad de archivo. Conecte la clavija de la imagen sin comprimir al de la imagen sin comprimir de la superposición.

![Componente de superposición y origen del archivo de imagen](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Componente de superposición y origen del archivo de imagen*


Si quiere modificar la posición del logotipo en el vídeo (por ejemplo, ponerlo en el 10 % de la esquina superior izquierda del vídeo), desactive la opción Manual Input (Entrada manual). Puede desactivar esta opción porque se está usando una entrada de archivo multimedia para proporcionar el archivo de logotipo en el componente de superposición.

![Posición de superposición](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posición de superposición*


Para codificar la secuencia de vídeo en H.264, agregue los componentes AVC Video Encoder (Codificador de vídeo AVC) y el codificador AAC a la superficie del diseñador. Conecte las clavijas. Configure el codificador AAC y seleccione Audio Format Conversion/Preset: 2.0 (L, R) (Conversión a formato de audio/predefinido: 2.0 [I, D]).

![Codificadores de audio y vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de audio y vídeo*


Ahora agregue los componentes **ISO Mpeg-4 Multiplexer** (Multiplexor ISO Mpeg-4) y **File Output** (Salida de archivo) y conecte las clavijas tal y como se muestra.

![Multiplexor MP4 y salida de archivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplexor MP4 y salida de archivo*


Debe establecer el nombre del archivo de salida. Haga clic en el componente **File Output** (Salida de archivo) y edite la expresión del archivo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nombre de salida de archivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nombre de salida de archivo*

Puede ejecutar el flujo de trabajo en la máquina para comprobar que lo hace correctamente.

Al finalizar, puede ejecutarlo en Servicios multimedia de Azure.

Primero, prepare un recurso de Servicios multimedia de Azure con dos archivos: el archivo de vídeo y el logotipo. Puede hacerlo mediante .NET o la API de REST. También puede hacerlo con el Portal de Azure o el [Explorador de Servicios multimedia de Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Este tutorial muestra cómo administrar recursos con AMSE. Hay dos maneras de agregar archivos a un recurso:

- Cree una carpeta local, copie en ella los dos archivos y arrastre y coloque la carpeta en la pestaña **Recurso**.
- Cargue el archivo de vídeo como un recurso, muestre la información del recurso, vaya a la pestaña Archivos y cargar un archivo adicional (logotipo).

>[AZURE.NOTE]Asegúrese de establecer un archivo principal en el recurso (el archivo de vídeo principal).

![Archivos de recursos en AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Archivos de recursos en AMSE*


Seleccione el recurso y elija codificarlo con el codificador Premium. Cargue el flujo de trabajo y selecciónelo.

Haga clic en el botón para pasar datos al procesador y agregue el siguiente código XML para establecer las propiedades del sistema de tiempo de ejecución:

![Codificador Premium en AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium en AMSE*


A continuación, pegue los datos XML siguientes. Debe especificar el nombre del archivo de vídeo tanto para la entrada del archivo multimedia como para el archivo de origen principal. Especifique el nombre del nombre del archivo también para el logotipo.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Si utiliza el SDK de .Net para crear y ejecutar la tarea, estos datos XML deben pasarse como la cadena de configuración.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Cuando se complete el trabajo, el archivo MP4 en el recurso de salida mostrará la superposición.

![Superposición en el vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Superposición en el vídeo*


Puede descargar el flujo de trabajo de ejemplo desde [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## Consulte también

- [Introducción de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Uso de la codificación Premium en Servicios multimedia de Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Codificación de contenido a petición con Servicios multimedia de Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Códecs y formatos de flujo de trabajo del Codificador multimedia Premium](media-services-premium-workflow-encoder-formats.md)

- [Archivos de flujo de trabajo de ejemplo](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Explorador de Servicios multimedia de Azure](http://aka.ms/amse)

## Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0803_2016-->