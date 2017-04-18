Estos requisitos de uso y presentación se aplican a la implementación del contenido y la información asociada (por ejemplo, relaciones, metadatos y otras señales) disponible a través de llamadas a las búsquedas de las API Bing Web Search, Bing Image Search y Bing Video Search, y a las API Bing News Search, Bing Spell Check y Bing Autosuggest. Los detalles de implementación relacionados con estos requisitos se pueden encontrar en la documentación de las características y resultados específicos.

## <a name="1--bing-spell-check-api-and-bing-autosuggest-api--you-must-not"></a>1.  BING SPELL CHECK API y BING AUTOSUGGEST API.  No debe:

- copiar, almacenar o almacenar en caché ningún dato que reciba desde las API Bing Spell Check o Bing Autosuggest; o usar datos que reciba desde las API Bing Spell Check o Bing Autosuggest como parte de cualquier aprendizaje automático o actividad algorítmica similar para entrenar, evaluar o mejorar los servicios nuevos o existentes que usted o terceros pueden ofrecer.

- usar datos que reciba desde las API Bing Spell Check o Bing Autosuggest como parte de cualquier aprendizaje automático o actividad algorítmica similar para entrenar, evaluar o mejorar los servicios nuevos o existentes que usted o terceros pueden ofrecer.

## <a name="2--bing-web-search-image-search-news-search-and-video-search-apis-the-search-apis"></a>2.  LAS API BING WEB SEARCH, BING IMAGE SEARCH, NEWS SEARCH y BING VIDEO SEARCH (las "API de búsqueda"):

**Definiciones.** Las siguientes definiciones se aplican en las Secciones desde la 2 a la 7 de estos requisitos de uso y presentación:

- "respuesta" hace referencia a una categoría de resultados devueltos en una respuesta. Por ejemplo, una respuesta de Bing Web Search API puede incluir respuestas en las categorías de los resultados de página web, imágenes, vídeo y noticias;
- "respuesta" significa una y todas las respuestas y los datos asociados recibidos en respuesta a una sola llamada a una API de búsqueda;
- "resultado" hace referencia a un elemento de información en una respuesta.  Por ejemplo, el conjunto de datos conectados con un único artículo de noticias es un resultado en una respuesta de noticias.

**Experiencia de búsqueda en Internet.** Todos los datos devueltos en las respuestas solo pueden usarse en experiencias de búsqueda en Internet. Una experiencia de búsqueda en Internet significa que el contenido presentado, según corresponda:

- es relevante y responde a la consulta directa del usuario final u otra indicación del interés y propósito de búsqueda del usuario (por ejemplo, una consulta de búsqueda indicada por el usuario); 

- ayuda a los usuarios buscar y navegar a los orígenes de datos (por ejemplo, las direcciones URL proporcionadas se implementan como hipervínculos por lo que el contenido o la atribución es un vínculo interactivo que se presenta claramente con los datos); 

- incluye varios resultados para que el usuario final seleccione entre ellos (por ejemplo, se presentan varios resultados de la respuesta de noticias o se devuelven todos los resultados si se devuelven muy pocos); 

- está limitado a una cantidad apropiada para atender al propósito de búsqueda (por ejemplo, las imágenes en miniatura se muestran en un tamaño miniatura que esté en proporción con la presentación del usuario); 

- incluye una indicación visible para el usuario final que informa de que el contenido se compone de resultados de búsqueda de Internet (por ejemplo, una instrucción de que el contenido es "de la Web"); e

- incluye cualquier otra combinación de las medidas necesarias para garantizar que el uso de los datos recibidos de las API de búsqueda no incumple ninguna ley aplicable, ni vulnera los derechos de terceros.  Consulte con sus asesores legales para determinar las medidas que pueden ser adecuadas.

La única excepción a los requisitos de experiencia de búsqueda de Internet es para la detección de direcciones URL tal como se describe más abajo en la sección 7 (Detección de direcciones URL fuera de presentación).

**General.** No debe: 

- copiar, almacenar o almacenar en caché ningún dato de las respuestas (excepto la retención en la medida permitida por la sección "Continuidad del servicio" más adelante); 

- modificar el contenido de los resultados (en otra forma que no sea al volver a darles un formato que no infrinja cualquier otro requisito); 

- omitir la atribución y las direcciones URL asociadas al contenido de resultado;

- volver a ordenar (incluyendo por omisión) los resultados que se presentan en una respuesta cuando se proporciona un orden o una clasificación;

- presentar otro contenido dentro de cualquier parte de una respuesta de forma que pueda dar lugar a que un usuario final crea que el otro contenido es parte de la respuesta; 

- presentar publicidad que no proporcione Microsoft en cualquier página que muestre cualquier parte de una respuesta;

- usar datos recibidos desde las API de búsqueda como parte de cualquier aprendizaje automático o actividad algorítmica similar para entrenar, evaluar, o mejorar los servicios nuevos o existentes que usted o terceros pueden ofrecer.

## <a name="3-advertising"></a>3. Publicidad.
La publicidad (ya sea proporcionado por Microsoft u otro proveedor) no debe presentarse con las respuestas (i) de las API Bing Image Search, Bing News Search o Bing Video Search; o (ii) que se filtran o limitan principalmente (o exclusivamente) en resultados de imagen, noticias o vídeo de otras API de búsqueda.

## <a name="4-branding"></a>4. Personalización de marca.
Deberá atribuir cada respuesta (o parte de una respuesta) presentada a Microsoft, como se describe en https://go.microsoft.com/fwlink/?linkid=833278, a menos que Microsoft especifique lo contrario por escrito para su uso particular.


## <a name="5--transferring-responses"></a>5.  Transferencia de las respuestas.
Si permite a un usuario transferir una respuesta a otro usuario, por ejemplo a través de una aplicación de mensajería o de su publicación en redes sociales, se aplica lo siguiente:

* Las respuestas transferidas tienen que:
  * constar de contenido que no se haya modificado en relación al contenido de las respuestas que se presentaron al usuario que realiza la transferencia (los cambios de formato están permitidos);
  * no incluir ningún dato en formato de metadatos;
  * presentar texto que indique que la respuesta se obtuvo a través de una experiencia de búsqueda de Internet usando la tecnología de Bing (por ejemplo, "Con tecnología de Bing", "Obtener más información sobre esta imagen en Bing" o "Explorar más sobre esta imagen en Bing" o mediante el uso del logotipo de Bing);
  * presentar de forma destacada la consulta completa utilizada para generar la respuesta; e
  * incluir un vínculo destacado o atribución similar con el origen subyacente de la respuesta, ya sea directamente o a través de bing.com o m.bing.com.

* No puede automatizar la transferencia de las respuestas.  Una transferencia tiene que iniciarse a partir de una acción del usuario que evidencie claramente un intento de transferir una respuesta.
* Solo puede habilitar a un usuario para transferir las respuestas que haya obtenido como resultado de la consulta de usuario para realizar la transferencia.

##<a name="6-continuity-of-service"></a>6. Continuidad del servicio.
No debe copiar, almacenar o almacenar en caché ningún dato de las respuestas. Sin embargo, para habilitar la continuidad del acceso al servicio y la representación de datos, puede conservar los resultados únicamente con las siguientes condiciones:

**Dispositivo.**  Se puede permitir a los usuarios finales conservar los resultados en un dispositivo durante (i) 24 horas desde el momento de la consulta o (ii) hasta que un usuario final envía otra consulta para obtener resultados actualizados, lo que ocurra primero, y siempre que los resultados retenidos se utilicen solamente:

- para permitir al usuario final tener acceso a los resultados devueltos anteriormente a ese usuario final en ese dispositivo (por ejemplo, en caso de interrupción del servicio); o

- para almacenar los resultados devueltos para la consulta proactiva personalizada en previsión de las necesidades del usuario final basadas en las señales del usuario final (por ejemplo, en caso de interrupción prevista del servicio).

**Servidor.**  Puede conservar los resultados específicos de un solo usuario final de forma segura en un servidor bajo su control y presentar los resultados retenidos solo:

- para permitir al usuario final tener acceso a un informe histórico de resultados devueltos anteriormente para ese usuario en la solución, siempre que los resultados (i) no se conserven más de 21 días desde el momento de la consulta inicial del usuario final y (ii) no se presenten en respuesta a una consulta nueva o repetida de usuario final; o

- para almacenar los resultados devueltos en una consulta proactiva personalizada en previsión de las necesidades de un usuario final basadas en las señales del usuario final durante (i) 24 horas desde el momento de la consulta o (ii) hasta que un usuario final envía otra consulta para obtener resultados actualizados, lo que ocurra primero.

Cuando se conserven, los resultados para un usuario específico no se pueden entremezclar con los resultados de otro usuario, es decir, los resultados de cada usuario tiene que conservarse y entregarse por separado.

**General.** Para cualquier presentación de los resultados retenidos, tiene que:

- incluir un aviso claro y visible de la hora en la que se envió la consulta,

- presentar al usuario un botón o medio similar para volver a consultar y obtener resultados actualizados, 

- conservar la personalización de marca de Bing en la presentación de los resultados y

- eliminar (y actualizar con una consulta nueva si es necesario) los resultados almacenados dentro de los períodos de tiempo especificados.


##<a name="7--non-display-url-discovery"></a>7.  Detección de direcciones URL fuera de presentación.
Solo puede usar las respuestas de búsqueda en una experiencia de búsqueda sin conexión a Internet con el único fin de detectar direcciones URL de los orígenes de la información, en respuesta a una consulta desde el usuario o el cliente. Puede copiar estas direcciones URL en un informe o una respuesta similar que usted proporcione (i) solo a ese usuario o cliente, en respuesta a una consulta en particular y (ii) que incluya un contenido significativo adicional que tenga valor y sea relevante para la consulta. Los requisitos en las secciones de la 2 a la 6 de estos requisitos de uso y presentación no se aplican a este uso fuera de presentación, excepto en los siguientes casos: 

* No debe almacenar en caché, copiar o almacenar ningún dato o contenido de, o derivado de, la respuesta de búsqueda, que no sea la copia limitada de dirección URL que se ha descrito anteriormente;
* Tiene que garantizar que el uso de los datos (incluidas las direcciones URL) recibidos de las API de búsqueda no incumple ninguna ley aplicable ni vulnera los derechos de terceros; y
* No debe usar datos (incluidas las direcciones URL) recibidos desde las API de búsqueda como parte de cualquier índice de búsqueda o aprendizaje automático o actividad algorítmica similar para crear, entrenar, evaluar, o mejorar los servicios que usted o terceros pueden ofrecer.
