Bing admite el resaltado de aciertos, que marca los términos de la consulta (u otros términos que Bing considere relevantes) en las cadenas de presentación de algunas de las respuestas. Por ejemplo, los campos `name`, `displayUrl` y `snippet` de una página web pueden marcar los términos de la consulta.

De forma predeterminada, Bing no incluye marcadores de resaltado en las cadenas de presentación. Para incluir los marcadores, incluya el parámetro `textDecorations` en la solicitud de consulta y establézcalo en **true**. Bing marca los términos de la consulta con los caracteres Unicode E000 y E001 para designar el principio y final del término. Por ejemplo, si el término de consulta es Sailing Dinghy y cualquiera de los términos existe en el campo, el término se incluye entre caracteres de resaltado de aciertos, tal y como se muestra en el ejemplo siguiente:  
  
![Resaltado de aciertos](./media/cognitive-services-bing-hit-highlighting/bing-hit-highlighting.PNG) 

Antes de mostrar la cadena en la interfaz de usuario, podría reemplazar los caracteres Unicode por caracteres que sean adecuados para el formato de presentación. Por ejemplo, si se muestra el texto como HTML, puede resaltar el término de la consulta reemplazando E000 por < b\> y E001 por < /b\>. Si no desea aplicar formato, quite los marcadores de la cadena. 

Bing proporciona la opción de usar caracteres Unicode o etiquetas HTML como marcadores. Para especificar los marcadores que se van a usar, incluya el parámetro de consulta `textFormat`. Para marcar el contenido con caracteres Unicode, establezca `textFormat` en Raw (valor predeterminado) y para marcar el contenido con etiquetas HTML, establezca `textFormat` en HTML. 
  
Si `textDecorations` es **true**, Bing puede incluir los siguientes marcadores en las cadenas de presentación de las respuestas. Si no hay ningún equivalente en código HTML, la celda de la tabla HTML aparece vacía.

|Unicode|HTML|Descripción
|-|-|-
|U+E000|\<b>|Marca el principio del término de la consulta (resaltado de aciertos)
|U+E001|\</b>|Marca el final del término de la consulta
|U+E002|\<i>|Marca el principio del contenido en cursiva 
|U+E003|\</i>|Marca el final del contenido en cursiva
|U+E004|\<br/>|Marca un salto de línea
|U+E005||Marca el principio de un número de teléfono
|U+E006||Marca el final de un número de teléfono
|U+E007||Marca el principio de una dirección
|U+E008||Marca el final de una dirección
|U+E009|\&nbsp;|Marca un espacio de no separación
|U+E00C|\<strong>|Marca el principio del contenido en negrita
|U+E00D|\</strong>|Marca el final del contenido en negrita
|U+E00E||Marca el principio del contenido cuyo fondo debe ser más claro que el fondo circundante
|U+E00F||Marca el final del contenido cuyo fondo debe ser más claro que el fondo circundante
|U+E010||Marca el principio del contenido cuyo fondo debe ser más oscuro que el fondo circundante
|U+E011||Marca el final del contenido cuyo fondo debe ser más oscuro que el fondo circundante
|U+E012|\<del>|Marca el principio del contenido que debe tacharse
|U+E013|\</del>|Marca el final del contenido que debe tacharse
|U+E016|\<sub>|Marca el principio del contenido de subíndice
|U+E017|\</sub>|Marca el final del contenido de subíndice
|U+E018|\<sup>|Marca el principio del contenido de superíndice
|U+E019|\</sup>|Marca el final del contenido de superíndice

El ejemplo siguiente muestra una respuesta `Computation` que contiene marcadores de subíndice del término de consulta log(2). El campo `expression` contiene los marcadores solo si `textDecoration` es **true**.

![marcadores de cálculo](./media/cognitive-services-bing-hit-highlighting/bing-markers-computation.PNG) 

Si la solicitud no incluía decoraciones, la expresión sería log10(2). 
  
