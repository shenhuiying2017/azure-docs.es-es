<properties
	pageTitle="Aprenda a utilizar el conector de Twitter en aplicaciones lógicas | Microsoft Azure"
	description="Información general del conector de Twitter con parámetros de la API de REST"
	services=""
	documentationCenter="" 
	authors="msftman"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="deonhe"/>


# Introducción al conector de Twitter

Con el conector de Twitter puede:

- Publicar y obtener tweets
- Acceder a escalas de tiempo, amigos y seguidores
- Ejecutar cualquiera de las demás acciones y desencadenadores que se describen a continuación

Para poder usar [un conector](./apis-list.md), primero debe crear una aplicación lógica. Por tanto, puede comenzar [creando una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conexión a Twitter

Para que la aplicación lógica pueda acceder a un servicio, primero debe crear una *conexión* con dicho servicio. Una [conexión](./connectors-overview.md) proporciona conectividad entre una aplicación lógica y otro servicio.

### Creación de una conexión a Twitter

>[AZURE.INCLUDE [Pasos para crear una conexión a Twitter](../../includes/connectors-create-api-twitter.md)]

## Uso de un desencadenador de Twitter

Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

En este ejemplo, se mostraré cómo usar el desencadenador **When a new tweet is posted** (Cuando se publica un nuevo tweet) para buscar #Seattle y, si se encuentra #Seattle, actualizar un archivo en Dropbox con el texto del tweet. En un ejemplo de la empresa, puede buscar el nombre de su compañía y actualizar una base de datos SQL con el texto del tweet.

1. Escriba *twitter* en el cuadro de búsqueda del Diseñador de aplicaciones lógicas y seleccione el desencadenador **Twitter - When a new tweet is posted** (Twitter - Cuando se publica un nuevo tweet)  
![Imagen de desencadenador de Twitter 1](./media/connectors-create-api-twitter/trigger-1.png)  
- Escriba *#Seattle* en el control **Search Text** (Buscar texto)  
![Imagen de desencadenador de Twitter 2](./media/connectors-create-api-twitter/trigger-2.png)  

En este punto, la aplicación lógica se ha configurado con un desencadenador que iniciará una ejecución de los otros desencadenadores y acciones en el flujo de trabajo.

>[AZURE.NOTE]Para que una aplicación lógica sea funcional, debe contener al menos un desencadenador y una acción. Siga los pasos que se describen en la sección siguiente para agregar una acción.

## Agregar una condición
Puesto que solo estamos interesados en tweets de usuarios con más de 50 seguidores, primero se debe agregar a la aplicación lógica una condición que confirme el número de seguidores.

1. Seleccione **+ Nuevo paso** para agregar la acción que desea que se ejecute cuando se encuentre #Seattle en un nuevo tweet  
![Imagen de acción de Twitter 1](../../includes/media/connectors-create-api-twitter/action-1.png)  
- Seleccione el vínculo **Add a condition** (Agregar una condición).  
![Imagen de condición de Twitter 1](../../includes/media/connectors-create-api-twitter/condition-1.png)  
Se abrirá el control **Condition** (Condición) donde puede comprobar las condiciones como *is equal to* (es igual a), *is less than* (es menor que), *is greater than* (es mayor que), *contains* (contiene), etc.  
![Imagen de condición de Twitter 2](../../includes/media/connectors-create-api-twitter/condition-2.png)  
- Seleccione el control **Choose a value** (Elegir un valor). En este control, puede seleccionar una o varias de las propiedades de cualquier acción o desencadenador anterior como el valor cuya condición se evaluará como true o false.  
![Imagen de condición de Twitter 3](../../includes/media/connectors-create-api-twitter/condition-3.png)  
- Seleccione **...** para expandir la lista de propiedades y ver todas las propiedades que están disponibles.  
![Imagen de condición de Twitter 4](../../includes/media/connectors-create-api-twitter/condition-4.png)  
- Seleccione la propiedad **Followers count** (Número de seguidores).  
![Imagen de condición de Twitter 5](../../includes/media/connectors-create-api-twitter/condition-5.png)  
- Observe que la propiedad de número de seguidores ahora está en el control de valor.  
![Imagen de condición de Twitter 6](../../includes/media/connectors-create-api-twitter/condition-6.png)  
- Seleccione **is greater than** (es mayor que) de la lista de operadores.  
![Imagen de condición de Twitter 7](../../includes/media/connectors-create-api-twitter/condition-7.png)  
- Escriba 50 como el operando para el operador *is greater than* (es mayor que). Se agrega la condición. Guarde su trabajo mediante el vínculo **Save** (Guardar) en el menú superior.  
![Imagen de condición de Twitter 8](../../includes/media/connectors-create-api-twitter/condition-8.png)  

## Uso de una acción de Twitter

Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Ahora que ha agregado un desencadenador, siga estos pasos para incorporar una acción que publicará un nuevo tweet con el contenido de los tweets detectados por el desencadenador. Para los fines de este tutorial, solo se publicarán tweets de los usuarios con más de 50 seguidores.

En el paso siguiente, agregará una acción de Twitter que publicará un tweet utilizando algunas de las propiedades de cada tweet publicado por un usuario que tenga más de 50 seguidores.

1. Seleccione **Add an action** (Agregar una acción). Esto abre el control de búsqueda, donde puede buscar otras acciones y desencadenadores.  
![Imagen de condición de Twitter 9](../../includes/media/connectors-create-api-twitter/condition-9.png)  
- Escriba *twitter* en el cuadro de búsqueda, a continuación, seleccione la acción **Twitter - Post a tweet** (Twitter - Publicar un tweet). Se abrirá el control **Post a tweet** (Publicar un tweet) donde especificará todos los detalles para el tweet publicado.  
![Imagen de acción de Twitter 1-5](../../includes/media/connectors-create-api-twitter/action-1-5.png)  
- Seleccione el control **Tweet text** (Texto del tweet). Todos los resultados de las acciones y desencadenadores anteriores de la aplicación lógica ya están visibles. Puede seleccionar cualquiera de ellos y usarlos como parte del texto del tweet del nuevo tweet.  
![Imagen de acción de Twitter 2](../../includes/media/connectors-create-api-twitter/action-2.png)  
- Seleccione **User name** (Nombre de usuario).
- Vaya a *says:* (dice:) en el control del texto del tweet. Hágalo justo después del nombre de usuario.
- Seleccione *Tweet text* (Texto del tweet).  
![Imagen de acción de Twitter 3](../../includes/media/connectors-create-api-twitter/action-3.png)  
- Guarde el trabajo y envíe un tweet con el hashtag #Seattle para activar el flujo de trabajo.

## Detalles técnicos

Estos son los detalles sobre los desencadenadores, las acciones y las respuestas compatibles con esta conexión:

## Desencadenadores de Twitter

El conector de Twitter tiene el siguiente desencadenador o desencadenadores:

|Desencadenador | Descripción|
|--- | ---|
|[Cuando se publique un nuevo tweet](connectors-create-api-twitter.md#when-a-new-tweet-is-posted)|Esta operación desencadena un flujo al publicar un nuevo tweet que coincide con una consulta de búsqueda determinada.|


## Acciones de Twitter

El conector de Twitter tiene las siguientes acciones:


|Acción|Descripción|
|--- | ---|
|[Obtener biografía de usuario](connectors-create-api-twitter.md#get-user-timeline)|Esta operación obtiene una lista de los tweets publicados por un usuario determinado más recientes.|
|[Obtener escala de tiempo de inicio](connectors-create-api-twitter.md#get-home-timeline)|Esta operación obtiene los tweets y retweets más recientes publicados por mí y mis seguidores.|
|[Buscar tweets](connectors-create-api-twitter.md#search-tweets)|Esta operación obtiene una lista de tweets relevantes que coinciden con la consulta de búsqueda.|
|[Obtener seguidores](connectors-create-api-twitter.md#get-followers)|Esta operación obtiene la lista de usuarios que siguen a un usuario determinado.|
|[Obtener mi seguidores](connectors-create-api-twitter.md#get-my-followers)|Esta operación obtiene la lista de usuarios que me siguen.|
|[Obtener a quienes se sigue](connectors-create-api-twitter.md#get-following)|La operación obtiene la lista de personas a las que el usuario determinado sigue.|
|[Obtener a quienes sigo](connectors-create-api-twitter.md#get-my-following)|Esta operación obtiene la lista de usuarios a los que sigo.|
|[Obtener usuario](connectors-create-api-twitter.md#get-user)|Esta operación obtiene los detalles del perfil para un usuario determinado, como el nombre de usuario, descripción, número de seguidores y mucho más.|
|[Publicar un tweet](connectors-create-api-twitter.md#post-a-tweet)|Esta operación publica un nuevo tweet.|
## Detalles de la acción

Estos son los detalles de las acciones y desencadenadores de este conector, junto con sus respuestas:



### Obtener biografía de usuario
Esta operación obtiene una lista de los tweets publicados por un usuario determinado más recientes.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|userName*|Nombre de usuario|Controlador de Twitter para el usuario|
|maxResults|Número máximo de resultados|Número máximo de tweets que se devuelven|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

TweetModel: representación de objeto tweet


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|TweetText|cadena|Contenido de texto del tweet|
|TweetId|cadena|Identificador del tweet|
|CreatedAt|cadena|Hora a la que se ha publicado el tweet|
|RetweetCount|integer|Número total de retweets para el tweet|
|TweetedBy|cadena|Nombre del usuario que ha publicado el tweet|
|MediaUrls|array|Dirección URL de los medios publicados con el tweet|
|TweetLanguageCode|cadena|Código de idioma del tweet|
|TweetInReplyToUserId|cadena|Id. del usuario del autor del tweet al que responde el tweet actual|
|Favorited|boolean|Indica si el tweet está marcado como favorito o no|
|UserMentions|array|Lista de usuarios que se menciona en el tweet|
|OriginalTweet|not defined|Tweet original desde el que se retwittea el tweet actual|
|UserDetails|not defined|Detalles del usuario que ha twitteado|




### Obtener escala de tiempo de inicio
Esta operación obtiene los tweets y retweets más recientes publicados por mí y mis seguidores.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|maxResults|Número máximo de resultados|Número máximo de tweets que se devuelven|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

TweetModel: representación de objeto tweet


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|TweetText|cadena|Contenido de texto del tweet|
|TweetId|cadena|Identificador del tweet|
|CreatedAt|cadena|Hora a la que se ha publicado el tweet|
|RetweetCount|integer|Número total de retweets para el tweet|
|TweetedBy|cadena|Nombre del usuario que ha publicado el tweet|
|MediaUrls|array|Dirección URL de los medios publicados con el tweet|
|TweetLanguageCode|cadena|Código de idioma del tweet|
|TweetInReplyToUserId|cadena|Id. del usuario del autor del tweet al que responde el tweet actual|
|Favorited|boolean|Indica si el tweet está marcado como favorito o no|
|UserMentions|array|Lista de usuarios que se menciona en el tweet|
|OriginalTweet|not defined|Tweet original desde el que se retwittea el tweet actual|
|UserDetails|not defined|Detalles del usuario que ha twitteado|




### Buscar tweets
Esta operación obtiene una lista de tweets relevantes que coinciden con la consulta de búsqueda.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|searchQuery*|Buscar texto|Búsqueda de términos como "happy hour", #haiku, love OR hate|
|maxResults|Número máximo de resultados|Número máximo de tweets que se devuelven|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

TweetModel: representación de objeto tweet


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|TweetText|cadena|Contenido de texto del tweet|
|TweetId|cadena|Identificador del tweet|
|CreatedAt|cadena|Hora a la que se ha publicado el tweet|
|RetweetCount|integer|Número total de retweets para el tweet|
|TweetedBy|cadena|Nombre del usuario que ha publicado el tweet|
|MediaUrls|array|Dirección URL de los medios publicados con el tweet|
|TweetLanguageCode|cadena|Código de idioma del tweet|
|TweetInReplyToUserId|cadena|Id. del usuario del autor del tweet al que responde el tweet actual|
|Favorited|boolean|Indica si el tweet está marcado como favorito o no|
|UserMentions|array|Lista de usuarios que se menciona en el tweet|
|OriginalTweet|not defined|Tweet original desde el que se retwittea el tweet actual|
|UserDetails|not defined|Detalles del usuario que ha twitteado|




### Obtener seguidores
Esta operación obtiene la lista de usuarios que siguen a un usuario determinado.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|userName*|Nombre de usuario|Controlador de Twitter para el usuario|
|maxResults|Número máximo de resultados|Número máximo de usuarios que se van a devolver|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

UserDetailsModel: detalles del usuario de Twitter


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|FullName|cadena|Nombre del usuario|
|Ubicación|cadena|Ubicación del usuario|
|Id|integer|Identificador del usuario de Twitter|
|UserName|cadena|Nombre de pantalla del usuario|
|FollowersCount|integer|Número de seguidores|
|Descripción|cadena|Descripción del usuario|
|StatusesCount|integer|Número de estados de usuario|
|FriendsCount|integer|Número de amigos|
|FavouritesCount|integer|Número de tweets que el usuario tiene como favoritos|
|ProfileImageUrl|cadena|Dirección URL de la imagen del perfil|




### Obtener mi seguidores
Esta operación obtiene la lista de usuarios que me siguen.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|maxResults|Número máximo de resultados|Número máximo de usuarios que se va a obtener|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

UserDetailsModel: detalles del usuario de Twitter


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|FullName|cadena|Nombre del usuario|
|Ubicación|cadena|Ubicación del usuario|
|Id|integer|Identificador del usuario de Twitter|
|UserName|cadena|Nombre de pantalla del usuario|
|FollowersCount|integer|Número de seguidores|
|Descripción|cadena|Descripción del usuario|
|StatusesCount|integer|Número de estados de usuario|
|FriendsCount|integer|Número de amigos|
|FavouritesCount|integer|Número de tweets que el usuario tiene como favoritos|
|ProfileImageUrl|cadena|Dirección URL de la imagen del perfil|




### Obtener a quienes se sigue
La operación obtiene la lista de personas a las que el usuario determinado sigue.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|userName*|Nombre de usuario|Controlador de Twitter para el usuario|
|maxResults|Número máximo de resultados|Número máximo de usuarios que se van a devolver|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

UserDetailsModel: detalles del usuario de Twitter


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|FullName|cadena|Nombre del usuario|
|Ubicación|cadena|Ubicación del usuario|
|Id|integer|Identificador del usuario de Twitter|
|UserName|cadena|Nombre de pantalla del usuario|
|FollowersCount|integer|Número de seguidores|
|Descripción|cadena|Descripción del usuario|
|StatusesCount|integer|Número de estados de usuario|
|FriendsCount|integer|Número de amigos|
|FavouritesCount|integer|Número de tweets que el usuario tiene como favoritos|
|ProfileImageUrl|cadena|Dirección URL de la imagen del perfil|




### Obtener a quienes sigo
Esta operación obtiene la lista de usuarios a los que sigo.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|maxResults|Número máximo de resultados|Número máximo de usuarios que se van a devolver|

El símbolo * indica que la propiedad es obligatoria.



#### Detalles de salida

UserDetailsModel: detalles del usuario de Twitter


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|FullName|cadena|Nombre del usuario|
|Ubicación|cadena|Ubicación del usuario|
|Id|integer|Identificador del usuario de Twitter|
|UserName|cadena|Nombre de pantalla del usuario|
|FollowersCount|integer|Número de seguidores|
|Descripción|cadena|Descripción del usuario|
|StatusesCount|integer|Número de estados de usuario|
|FriendsCount|integer|Número de amigos|
|FavouritesCount|integer|Número de tweets que el usuario tiene como favoritos|
|ProfileImageUrl|cadena|Dirección URL de la imagen del perfil|




### Obtener usuario
Esta operación obtiene los detalles del perfil para un usuario determinado, como el nombre de usuario, descripción, número de seguidores y mucho más.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|userName*|Nombre de usuario|Controlador de Twitter para el usuario|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

UserDetailsModel: detalles del usuario de Twitter


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|FullName|cadena|Nombre del usuario|
|Ubicación|cadena|Ubicación del usuario|
|Id|integer|Identificador del usuario de Twitter|
|UserName|cadena|Nombre de pantalla del usuario|
|FollowersCount|integer|Número de seguidores|
|Descripción|cadena|Descripción del usuario|
|StatusesCount|integer|Número de estados de usuario|
|FriendsCount|integer|Número de amigos|
|FavouritesCount|integer|Número de tweets que el usuario tiene como favoritos|
|ProfileImageUrl|cadena|Dirección URL de la imagen del perfil|




### Publicar un tweet
Esta operación publica un nuevo tweet.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|tweetText|Texto del tweet|Texto que se va a publicar|
|body|Multimedia|Elementos multimedia que se van a publicar|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

TweetResponseModel: modelo que representa el tweet publicado


| Nombre de propiedad | Tipo de datos | Descripción |
|---|---|---|
|TweetId|cadena|Identificador del tweet recuperado|




### Cuando se publique un nuevo tweet
Esta operación desencadena un flujo al publicar un nuevo tweet que coincide con una consulta de búsqueda determinada.


|Nombre de propiedad| Display Name (Nombre para mostrar)|Descripción|
| ---|---|---|
|searchQuery*|Buscar texto|Búsqueda de términos como "happy hour", #haiku, love OR hate|

El símbolo * indica que la propiedad es obligatoria.

#### Detalles de salida

TriggerBatchResponse[TweetModel]


| Nombre de propiedad | Tipo de datos |
|---|---|
|value|array|



## Respuestas HTTP

Las acciones y los desencadenadores anteriores pueden devolver uno o varios de los siguientes códigos de estado HTTP:

|Name|Descripción|
|---|---|
|200|OK|
|202|Accepted|
|400|Bad Request|
|401|No autorizado|
|403|Prohibido|
|404|No encontrado|
|500|Error interno del servidor. Error desconocido.|
|default|Error en la operación.|









## Pasos siguientes
[Creación de una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0727_2016-->
