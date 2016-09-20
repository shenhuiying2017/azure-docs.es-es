<properties
	pageTitle="Recopilación de datos para entrenar modelos: API de recomendaciones de Aprendizaje automático | Microsoft Azure"
	description="Recomendaciones de Aprendizaje automático de Azure: recopilación de datos para entrenar modelos"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="luisca"/>

#  Recopilación de datos para entrenar modelos #

La API de recomendaciones aprende de las transacciones anteriores para buscar los artículos que deben recomendarse a un usuario determinado.

Después de crear un modelo, debe proporcionar los dos elementos de información antes de realizar cualquier proceso de entrenamiento: un archivo de catálogo y los datos de uso.

>   Si aún no lo ha hecho, le animamos a que complete la [Guía de inicio rápido](cognitive-services-recommendations-quick-start.md).


## Datos del catálogo ##

### Formato de archivo de catálogo ###

El archivo de catálogo contiene información sobre los artículos que ofrece al cliente. Los datos del catálogo deben seguir el siguiente formato:

- Sin características: `<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Con características: `<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### Filas de ejemplo de un archivo de catálogo

Sin características:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Con características:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### Detalles de formato

| Nombre | Obligatorio | Tipo | Description |
|:---|:---|:---|:---|
| Id. de elemento |Sí | [A-z], [a-z], [0-9], [\_] &#40;Carácter de subrayado&#41;, [-] &#40;Guion&#41;<br> Longitud máxima: 50 | Identificador único de un elemento. |
| Nombre del elemento | Sí | Cualquier carácter alfanumérico<br> Longitud máxima: 255 | Nombre del elemento. |
| Categoría del elemento | Sí | Cualquier carácter alfanumérico <br> Longitud máxima: 255 | La categoría a la que pertenece este elemento (por ejemplo, Libros de cocina, Drama...); puede estar vacía. |
| Description | No, a menos que haya características (pero puede estar vacía) | Cualquier carácter alfanumérico <br> Longitud máxima: 4000 | Descripción de este elemento. |
| Lista de características | No | Cualquier carácter alfanumérico <br> Longitud máxima: 4000; número máximo de características: 20 | Lista de nombres de característica = valores de característica separados por coma que se pueden usar para mejorar la recomendación del modelo.|

#### Carga de un archivo de catálogo

Consulte la [referencia de API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) para cargar un archivo de catálogo.

Tenga en cuenta que el contenido del archivo de catálogo debe pasarse como el cuerpo de solicitud.

Si carga varios archivos de catálogo para el mismo modelo con varias llamadas, solo insertaremos los nuevos elementos de catálogo. Los elementos existentes permanecerán con los valores originales. Los datos del catálogo no se pueden actualizar con este método.

>   Nota: el tamaño máximo de archivo es de 200 MB. El número máximo de artículos que se admite en el catálogo es 100 000.


## ¿Por qué se deben agregar características al catálogo?

El motor de recomendaciones crea un modelo estadístico que indica qué artículos es probable que gusten a un cliente o que vaya a comprar. Cuando tenga un producto nuevo con el que nunca haya interactuado, no podrá crear un modelo de concurrencias. Supongamos que comienza ofreciendo un nuevo "violín para niños" en su tienda. Como nunca antes ha vendido ese producto, no podrá saber qué otros artículos recomendar con ese violín.

Es decir, si el motor conoce la información de ese violín (es decir, es un instrumento musical para niños con edades de entre 7 y 10 niños que no es costoso, etc.), el motor podrá aprender de otros productos con características similares. Por ejemplo, ha vendido violines en otro momento y, normalmente, las personas que compran este tipo de producto suelen adquirir CD de música clásica y soportes para partituras. El sistema puede encontrar estas conexiones entre las características y ofrecer recomendaciones basadas en dichas características, aunque el uso del nuevo violín sea reducido.

Las características se importan como parte de los datos del catálogo y, luego, su clasificación (o la importancia de la característica del modelo) se asocia cuando se realiza una compilación de clasificación. El rango de las características puede cambiar según el patrón de los datos de uso y el tipo de elementos. Pero para que el uso y los elementos sean coherentes, el rango debe tener solo pequeñas fluctuaciones. El rango de características es un número no negativo. El número 0 significa que la característica no fue clasificada (sucede si se invoca esta API antes de completar la primera compilación de rango). La fecha en que se atribuye el rango se conoce como la actualización de la puntuación.


###Las características son categóricas

Esto significa que se deben crear características similares a una categoría. Por ejemplo, precio = 9,34 no es una característica categórica. Por otro lado, una característica como RangoPrecio = Menos5Dólares sí es una característica categórica. Otro error común es utilizar el nombre del elemento como una característica. Esto convertiría en exclusivo el nombre de un elemento, por lo que no describiría ninguna categoría. Asegúrese de que las características representan las categorías de elementos.


###¿Cuántas y qué características debo utilizar?


En última instancia, la compilación de recomendaciones admite crear un modelo con 20 características como máximo. Puede asignar más de 20 características a los elementos del catálogo, pero se espera que realice una compilación de clasificación y seleccione únicamente las características de rango alto. (Una característica con una clasificación de 2.0 o más es una característica realmente buena para usarla).


###¿Cuándo se usan realmente las características?

El modelo utiliza características cuando no hay suficientes datos de transacción para proporcionar recomendaciones solo como información sobre transacciones. Por tanto, las características tendrán mayor repercusión en “elementos fríos”, que son los que tienen menos transacciones. Si todos los elementos tienen suficiente información sobre transacciones que no necesite para enriquecer el modelo con características.


###Uso de características de producto

Para utilizar las características como parte de la compilación, tendrá que realizar estos pasos:

1. Asegúrese de que el catálogo tiene características cuando se carga.

2. Desencadene una compilación de clasificación. Con esta acción, se analizarán importancia y la clasificación de las características.

3. Desencadene una compilación de recomendaciones configurando los siguientes parámetros de compilación: establezca el valor de useFeaturesInModel y de allowColdItemPlacement en True. Además, modelingFeatureList debe establecerse en la lista separada por comas de las características que desea utilizar para mejorar su modelo. Consulte [esta página](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) para más información sobre los parámetros de tipos de compilación de recomendaciones.





## Datos de uso ##
Un archivo de uso contiene información sobre cómo se usan los artículos o las transacciones de su negocio.

#### Detalles de formato de uso
Un archivo de uso es un archivo CSV (valores separados por comas) donde cada fila representa una interacción entre un usuario y un artículo. Cada fila tiene el formato siguiente:<br> `<User Id>,<Item Id>,<Time>,[<Event>]`



| Nombre | Obligatorio | Tipo | Description
|-------|------------|------|---------------
|Id. de usuario| Sí|[A-z], [a-z], [0-9], [\_] &#40;Carácter de subrayado&#41;, [-] &#40;Guion&#41;<br> Longitud máxima: 255 |Identificador único de un usuario.
|Id. de elemento|Sí|[A-z], [a-z], [0-9], [&#95;] &#40;Carácter de subrayado&#41;, [-] &#40;Guion&#41;<br> Longitud máxima: 50|Identificador único de un elemento.
|Hora|Sí|La fecha en este formato: AAAA/MM/DDTHH:MM:SS (por ejemplo, 2013/06/20T10:00:00)|Hora de los datos.
|Evento|No | Uno de los siguientes:<br>• haga clic en<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• compra| El tipo de transacción. |

#### Filas de ejemplo de un archivo de uso

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### Carga de un archivo de uso

Consulte la [referencia de API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) para cargar archivos de uso. Tenga en cuenta que debe pasar el contenido del archivo de uso como el cuerpo de la llamada HTTP.

>  Nota:

>  Tamaño de archivo máximo: 200 MB. Puede cargar varios archivos de uso.

>  Debe cargar un archivo de catálogo antes de empezar a agregar datos de uso al modelo. Solo se utilizarán los elementos del archivo de catálogo durante la fase de aprendizaje. Se omitirán el resto de los elementos.

## ¿Cuántos datos necesita?

La calidad del modelo depende en gran medida la calidad y la cantidad de los datos. El sistema aprende cuando los usuarios compran artículos diferentes (a esto lo denominamos "concurrencias"). En algunas compilaciones de FBT, es importante saber qué artículos se compran en las mismas transacciones.

Le recomendamos que la mayoría de los artículos tengan 20 transacciones o más, por tanto, si el catálogo tiene más de 10 000 artículos, le sugerimos que no mantenga una cantidad superior a 20 veces ese número de transacciones (o aproximadamente 200 000 transacciones). Le recordamos de nuevo que tan solo se trata de una recomendación. Tendrá que experimentar con sus datos.

Una vez haya creado un modelo, puede realizar una [evaluación sin conexión](cognitive-services-recommendations-buildtypes.md) para comprobar las probabilidades que hay de que el modelo funcione correctamente.

<!---HONumber=AcomDC_0907_2016-->