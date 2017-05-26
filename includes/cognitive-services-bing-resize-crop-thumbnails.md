Algunas respuestas de Bing incluyen direcciones URL de las imágenes en miniatura servidas por Bing. Puede cambiar el tamaño y recortar las imágenes en miniatura. 

> [!NOTE]
> Asegúrese de que el tamaño y el recorte de la miniatura proporciona un escenario de búsqueda y respeta los derechos de terceros, según lo especificado en los [requisitos de visualización y uso de Search API](/useanddisplayrequirements.md).


Para cambiar el tamaño de una imagen, incluya los parámetros de consulta w (ancho) y h (alto) en la dirección URL de la miniatura. Especifique el ancho y alto en píxeles. Por ejemplo:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Si cambia el tamaño de la imagen, se mantiene su proporción de aspecto. Para mantener la proporción de aspecto, se puede agregar relleno blanco al borde de la imagen. Por ejemplo, si cambia el tamaño de una imagen de 480 x 359 a 200 x 200 sin recortar, el ancho total contiene la imagen, pero el alto contiene 25 píxeles de relleno blanco en la parte superior e inferior de la imagen. Lo mismo ocurriría si la imagen fuese de 359 x 480, excepto que los bordes izquierdo y derecho contendrían el relleno blanco. Si recorta la imagen, no se agrega relleno blanco.  
  
La siguiente imagen muestra el tamaño original de una imagen en miniatura (480 x 300).  
  
![Imagen original en horizontal](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
La siguiente imagen muestra la imagen cambiada a un tamaño de 200 x 200. La proporción de aspecto se mantiene y los bordes superior e inferior se rellenan de blanco (el borde negro se incluye para mostrar el relleno).  
  
![Imagen con el tamaño cambiado en horizontal](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  
  
Si especifica dimensiones mayores que el ancho y el alto de la imagen original, la imagen se rellena de blanco en los bordes izquierdo y superior.  
  
Para recortar una imagen, incluya el parámetro de consulta c (recortar). Estos son los valores posibles que puede especificar.  
  
- 4&mdash;Blind Ratio (proporción oculta)  
- 7&mdash;Smart Ratio (proporción inteligente)  
  
Si solicita recortar con Smart Ratio (c=7), la imagen se recorta desde el centro de la región de interés de la imagen hacia fuera manteniendo la proporción de aspecto de la imagen. La región de interés de la imagen es el área de la imagen que Bing determina que contiene las partes de mayor importancia. A continuación se muestra un ejemplo de región de interés.  
  
![Región de interés](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Si cambia el tamaño de una imagen y solicita recortar con Smart Ratio, la imagen se reduce al tamaño solicitado y se mantiene la proporción de aspecto. La imagen se recorta conforme a las nuevas dimensiones. Por ejemplo, si el nuevo ancho es igual o inferior al alto, la imagen se recorta por la izquierda y derecha del centro de la región de interés. En caso contrario, la imagen se recorta por la parte superior e inferior del centro de la región de interés.  
  
A continuación se muestra la imagen que se reduce a 200 x 200 usando recortar con Smart Ratio (proporción inteligente).  
  
![Imagen en horizontal recortada a 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
A continuación se muestra la imagen que se reduce a 200 x 100 usando recortar con Smart Ratio (proporción inteligente).  
   
![Imagen en horizontal recortada a 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
A continuación se muestra la imagen que se reduce a 100 x 200 usando recortar con Smart Ratio (proporción inteligente).  
  
![Imagen en horizontal recortada a 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)
  
Si Bing no puede determinar la región de interés de la imagen, usa recortar con Blind Ratio (proporción oculta).  
  
Si solicita recortar con Blind Ratio (proporción oculta) (c=4), Bing usa las reglas siguientes para recortar la imagen.  
  
- Si (ancho de la imagen original / alto de la imagen original) < (ancho de la imagen solicitado / alto de la imagen solicitado), la imagen se mide desde la esquina superior izquierda y se recorta la parte inferior.  
- Si (ancho de la imagen original / alto de la imagen original) > (ancho de la imagen solicitado / alto de la imagen solicitado), la imagen se mide desde el centro y se recorta por la izquierda y derecha.  
  
A continuación se muestra una imagen vertical de 225 x 300.  
  
![Imagen de girasol original](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
A continuación se muestra la imagen reducida a 200 x 200 usando recortar con Blind Ratio (proporción oculta). La imagen se mide desde la esquina superior izquierda y se recorta la parte inferior de la imagen.  
  
![Imagen de girasol recortada a 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
A continuación se muestra la imagen reducida a 200 x 100 usando recortar con Blind Ratio (proporción oculta). La imagen se mide desde la esquina superior izquierda y se recorta la parte inferior de la imagen.  
  
![Imagen de girasol recortada a 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
A continuación se muestra la imagen reducida a 100 x 200 usando recortar con Blind Ratio (proporción oculta). La imagen se mide desde el centro y se recortan las partes izquierda y derecha de la imagen.  
  
![Imagen de girasol recortada a 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)
