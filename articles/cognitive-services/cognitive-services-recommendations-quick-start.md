<properties
	pageTitle="Guía de inicio rápido: API de recomendaciones de Aprendizaje automático | Microsoft Azure"
	description="Recomendaciones de aprendizaje automática de Azure - Guía de inicio rápido"
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
	ms.date="05/26/2016"
	ms.author="luisca"/>

# Guía de inicio rápido para la API de recomendaciones de Cognitive Services

En este documento se explica cómo incorporar su servicio o aplicación para usar la [API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759710). Puede encontrar más detalles sobre la API de recomendaciones y otros servicios cognitivos (Cognitive Services) [aquí](http://go.microsoft.com/fwlink/?LinkId=759709). En esta guía, también puede encontrar la útil [referencia de la API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759348).


<a name="Overview"></a>
## Información general

Este documento es una guía paso a paso. Nuestro objetivo es guiarle a través de los pasos necesarios para entrenar un modelo, así como indicarle los recursos con los que podrá utilizar dicho modelo desde su entorno de producción.

La duración de este ejercicio es de unos 30 minutos.

Para usar la [API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759710), debe realizar los pasos siguientes:

1. Crear un modelo: un modelo es un contenedor de los datos de uso, datos del catálogo y el modelo de recomendación.
1. Importar datos del catálogo: un catálogo contiene información de metadatos de los artículos.
1. Importar datos de uso: los datos de uso se pueden cargar en una de las dos formas siguientes (o ambas):
  -  Mediante la carga de un archivo que contiene los datos de uso.
  -  Mediante el envío de eventos de adquisición de datos. Normalmente, carga un archivo de uso para poder crear un modelo de recomendación inicial (arranque) y usarlo hasta que el sistema reúne suficientes datos con el formato de adquisición de datos.
1. Compilar un modelo de recomendación: se trata de una operación asincrónica en la que el sistema de recomendación toma todos los datos de uso y crea un modelo de recomendación. Esta operación puede tardar varios minutos o varias horas, según el tamaño de los datos y los parámetros de configuración de compilación. Al desencadenar la compilación, obtendrá un identificador de compilación. Utilícelo para comprobar cuándo ha finalizado el proceso de compilación antes de empezar a consumir recomendaciones.
1. Recomendaciones de uso: obtenga recomendaciones para un elemento específico o una lista de elementos.

<a name="GetStarted"></a>
### Comencemos.

Empezará creando un modelo de recomendaciones. Después, le indicaremos cómo usar los resultados que genera el modelo para mostrar recomendaciones en un sitio de comercio electrónico.

<a name="Ex1Task1"></a>
#### Tarea 1: Registrarse en la API de recomendaciones ####

En esta tarea, deberá registrarse en el servicio de la API de recomendaciones y crear un modelo de recomendaciones.

1. Vaya al **Portal de Azure** visitando [http://portal.azure.com/](http://portal.azure.com/) e inicie sesión con su cuenta de Azure.

1.  Haga clic en **+ New** (+ Nuevo).

1. Seleccione la opción **Intelligence** (Inteligencia).

1. Seleccione el producto **Cognitive Services APIs** (API de Cognitive Services). Este producto le permitirá iniciar una suscripción en cualquiera de las API de Cognitive Services (Face, Text Analytics, Computer Vision, etc.). Hoy, nos centraremos en la API de recomendaciones.

1. En la página de aterrizaje de la API de Cognitive Services, especifique el **nombre de la cuenta** de la suscripción a Recomendaciones (por ejemplo, "misRecomendaciones"). No debe incluir espacios en blanco en el nombre.

1. En **API type** (Tipo de API), seleccione **Recomendaciones**.

1. En **Nivel de precios**, puede seleccionar un plan. Puede seleccionar el nivel **Gratis** de 10 000 transacciones/mes**. Se trata de un plan gratuito, así que es una buena forma de empezar a probar el sistema. Al pasar al entorno de producción, se recomienda que tenga en cuenta el volumen de solicitudes y que cambie el tipo de plan en consecuencia.

1. Seleccione un **grupo de recursos**, o bien cree uno nuevo si aún no dispone de ninguno.

1. Puede cambiar otros artículos en el cuadro de diálogo Crear. Debemos señalar que, actualmente, el proveedor de recursos solo es compatible con centros de datos de Estados Unidos.

1. Una vez que haya terminado con todas las selecciones, haga clic en **Crear**.

1. Espere unos minutos para que el recurso se implemente. Una vez implementado, puede ir a la sección **Claves** de la hoja **Configuración**, donde se le proporcionará una clave principal y secundaria para usar la API. Copie la clave principal, ya que la necesitará al crear el primer modelo.

<a name="Ex1Task2"></a>
#### Tarea 2: ¿Ha transferido sus datos? ####

La API de recomendaciones aprenderá del catálogo y las transacciones con el fin de proporcionar buenas recomendaciones de productos. Es decir, necesita alimentarla con datos de calidad de sus productos (a esto lo denominamos archivo de **"catálogo"**) y un conjunto de transacciones lo suficientemente grande como para buscar patrones de consumo interesantes (a esto lo llamamos **"uso"**).

1. Normalmente, realizaría una consulta en la base de datos de transacciones para estos elementos de información. En caso de que no los tenga a mano, hemos proporcionado algunos datos de ejemplo basados en los datos de transacciones de Microsoft Store.

 Puede descargar los datos [aquí](http://aka.ms/RecoSampleData). Copie y desempaquete el archivo MsStoreData.Zip en una carpeta de la máquina local.

 > ** Nota:** El código de ejemplo que descargará y ejecutará en la tarea 3 tiene datos de ejemplo ya incrustados, por lo que esta tarea es opcional. Es decir, aquí podrá descargar conjuntos de datos más realistas y entender mejor las entradas en la API de recomendaciones.

1.	Ahora, analicemos el archivo de catálogo. Desplácese hasta la ubicación en la que copió los datos. Abra el archivo de catálogo en el **Bloc de notas**.

 Observará que el archivo de catálogo es bastante sencillo. Tiene el siguiente formato: `<itemid>,<item name>,<product category>`

 >  AAA-04294,OfficeLangPack 2013 32/64 E34 Online DwnLd,Office <br> AAA-04303,OfficeLangPack 2013 32/64 ET Online DwnLd,Office <br> C9F-00168,KRUSELL Kiruna Flip Cover for Nokia Lumia 635 - Camel,Accessories

 Debemos señalar que un archivo de catálogo puede ser mucho más completo, por ejemplo, puede agregar metadatos de los productos (a esto lo denominamos *"características de artículos"*). Debe consultar la sección [catalog format](http://go.microsoft.com/fwlink/?LinkID=760716) (formato de catálogo) de la referencia de la API para obtener más información sobre el formato de catálogo.

1. Realizaremos el mismo procedimiento con los datos de uso. Observará que la fecha de uso tiene el formato `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase 0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase 0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase 00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase 0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase 00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

Observe que los tres primeros elementos son obligatorios. El tipo de evento es opcional. Puede consultar el [formato de uso](http://go.microsoft.com/fwlink/?LinkID=760712) para más información sobre este tema.

 > **¿Cuántos datos necesita?**
 <p>
>  Dependerá realmente de los propios datos de uso. El sistema aprende cuando los usuarios compran artículos diferentes. En algunas compilaciones como FBT, es importante saber qué artículos se compran en las mismas transacciones (a esto lo llamamos "concurrencias"). Le recomendamos que la mayoría de los artículos tengan 20 transacciones o más, por tanto, si el catálogo tiene más de 10&#160;000 artículos, le sugerimos que no mantenga una cantidad superior a 20 veces ese número de transacciones (o aproximadamente 200&#160;000 transacciones). Le recordamos de nuevo que tan solo se trata de una recomendación. Tendrá que experimentar con sus datos.
> </p>

<a name="Ex1Task3"></a>
#### Tarea 3: Creación de un modelo de recomendaciones ####

Ahora que ya dispone de una cuenta y tiene datos, vamos a crear el primer modelo.

En esta tarea, utilizaremos la aplicación de ejemplo para crear el primer modelo.

1. En primer lugar, debe tener en cuenta la [referencia de la API de recomendaciones](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Descargue la [aplicación de ejemplo](http://go.microsoft.com/fwlink/?LinkID=759344) en una carpeta local.

1. Abra en Visual Studio la solución **RecommendationsSample.sln** que se encuentra en la carpeta **C#**.

1. Abra el archivo **SampleApp.cs**. Observe los pasos siguientes en el archivo:
 + Creación de un modelo
 + Adición de un archivo de catálogo
 + Adición de un archivo de uso
 + Desencadenamiento de una compilación para el modelo
 + Obtención de una recomendación basada en un par de artículos
<p></p>

1. Reemplace el valor del campo **AccountKey** por la clave de la tarea 1.

1. Vaya a la solución y verá cómo se crea un modelo.

1. Trate de reemplazar los archivos de catálogo y uso que acaba de cargar con el fin de crear un nuevo modelo para Microsoft Store o recomendaciones de libros. Tendrá que cambiar también el nombre del modelo y los artículos de los que pide recomendaciones.

1. Cuando se cree el modelo, tome nota del **id. del modelo**, ya que lo necesitará al pedir recomendaciones en el entorno de producción.

>  Obtenga más información sobre los tipos de compilación y la evaluación de la calidad de las compilaciones [aquí](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### Envío del modelo al entorno de producción ###

Ahora que sabe cómo crear un modelo y recomendaciones de uso, el siguiente paso consiste en enviarlo al entorno de producción en su sitio web o aplicación móviles, o bien integrarlo en el sistema CRM o ERP. Evidentemente, cada una de estas implementaciones sería diferente. Puesto que la API de recomendaciones se pide como un servicio web, podrá invocarla fácilmente desde cualquiera de estos entornos diferentes.

**Importante:** Si desea mostrar recomendaciones desde un cliente público (por ejemplo, su sitio de comercio electrónico), debe crear un servidor proxy para proporcionarlas. Esto es importante para que la clave de API no esté expuesta a entidades externas (que posiblemente no sean confiables).

Aquí presentamos algunas ideas de ubicaciones donde se pueden utilizar las recomendaciones:

### Página de producto

**Recomendaciones de artículos**
<p>Si el modelo se entrenó en datos de compra, permitirá a su cliente descubrir *productos que suelen ser de interés para las personas que han comprado el artículo de origen*.</p>
<p>Si el modelo se entrenó en datos de clics, permitirá a su cliente descubrir *productos que suelen visitar las personas que han comprado el artículo de origen*. Este tipo de modelo puede devolver artículos similares.</p>

**Recomendaciones para artículos que con frecuencia se compran juntos**
<p>Podría entrenarse una compilación de artículos que con frecuencia se compran juntos, por lo que puede obtener conjuntos de artículos que suelen adquirirse junto con este.</p>

### Página de pago

**Recomendaciones de artículos**
<p>El modelo de recomendaciones podría seleccionar una lista de artículos como entrada. Por tanto, podría transmitir todos los artículos de la cesta como entrada para obtener recomendaciones. En este caso, el modelo proporcionará recomendaciones interesantes basadas en todos los artículos de la cesta.
</p>

### Página de aterrizaje

**Recomendaciones de usuario**
<p>
Un modelo de recomendaciones puede tomar como entrada el id. de usuario, que usará el historial de transacciones de ese usuario para proporcionar recomendaciones personalizadas al usuario especificado.
</p>

Consulte la [documentación de obtención de recomendaciones de artículos](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### Pasos siguientes
Enhorabuena por haber llegado hasta este paso. Para más información, puede visitar la sección completa [Recommendations API Reference](http://go.microsoft.com/fwlink/?LinkId=759348) (Referencia de la API de recomendaciones). Si tiene alguna pregunta, no dude en ponerse en contacto con nosotros en mlapi@microsoft.com.

<!---HONumber=AcomDC_0601_2016-->