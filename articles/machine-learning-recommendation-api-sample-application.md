<properties title="Common operations in the Machine Learning Recommendations API" pageTitle="Operaciones comunes en la API de recomendaciones de aprendizaje automático | Azure" description="Aplicación de ejemplo de recomendación de aprendizaje automático de Azure" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="jaymathe" /> 


# Operaciones comunes en la API de recomendaciones de aprendizaje automático

##Propósito

Este documento muestra el uso de algunas de las API de recomendación de aprendizaje automático de Azure a través de una aplicación de ejemplo.

Esta aplicación no pretende incluir la funcionalidad completa o usar todas las API, sino simplemente mostrar algunas de las operaciones comunes que se pueden realizar la primera vez que desee jugar con el servicio de recomendación de aprendizaje automático de Azure. 

##Introducción a la recomendación

La recomendación a través del servicio de aprendizaje automático de Azure se habilita al compilar un modelo de recomendación basado en los datos siguientes:

* Un repositorio del elemento que desea recomendar, también conocido como catálogo
* Datos que representan el uso de elementos por usuario/sesión (esto se puede adquirir con el tiempo a través de la adquisición de datos, no forma parte de la aplicación de ejemplo)

Una vez compilado un modelo de recomendación puede utilice para predecir elementos a un usuario según un conjunto de elementos (puede ser un elemento único) que selecciona.

Para permitir el escenario anterior, realizará la siguiente operación en el servicio de recomendación de aprendizaje automático de Azure:

* Crear un modelo: se trata de un contenedor lógico que mantiene los datos (catálogo y uso) y los modelos de predicción, cada contenedor de modelo se identifica mediante un identificador único asignado en el momento de su creación (se denomina identificador de modelo) y lo usa la mayor parte de la API 
* Cargar el catálogo: una vez que se crea un contenedor de modelo puede asociar un catálogo al mismo

Nota: los pasos anteriores ('crear modelo' y "cargar el catálogo") normalmente se realizan una vez para el ciclo de vida del modelo.

* Uso de carga: para agregar datos de uso al contenedor del modelo.
* Compilar modelo de recomendación: cuando tenga suficientes datos, desencadenará la compilación del modelo de recomendación. Esta operación usará algoritmos de aprendizaje automático de última generación para crear un modelo de recomendación. Cada compilación está asociada a un identificador único; tendrá que conservar este identificador, ya que es necesario para la funcionalidad de algunas API.
* Supervisar el proceso de compilación: una compilación de un modelo de recomendación es una operación asincrónica y puede tardar desde varios minutos a varias horas, según la cantidad de datos (catálogo y uso) y parámetros de compilación. Por lo tanto, necesitará supervisar la compilación. Un modelo de recomendación solo se compila si su compilación asociada finaliza correctamente.
* (Opcional) Elija una compilación de modelo de recomendación activa, este paso solo es necesario si tiene más de un modelo de recomendación compilado en el contenedor de modelos. Cualquier solicitud para obtener la recomendación sin indicar la compilación activa (modelo de recomendación activo), el sistema la redirigirá automáticamente a la compilación activa predeterminada. 

Nota: Una compilación activa (modelo de recomendación) está lista para producción y se compila para carga de trabajo de producción en contraposición a un modelo de recomendación no activo que permanece en un entorno de prueba (a veces denominado ensayo)

* Obtener Recomendación: cuando ya tenga un modelo de recomendación, puede desencadenar la recomendación para un solo elemento o una lista de elementos que elija. 

Generalmente invocará a Obtener recomendación durante un período de tiempo determinado; mientras tanto puede redirigir los datos de uso al sistema de recomendación de aprendizaje automático de Azure que agregará este uso al contenedor especificado de modelos. Cuando ya tenga suficientes datos de uso, puede desencadenar una nueva compilación de modelo de recomendación para usar los datos más actuales. 

##Requisitos previos

* Visual Studio 2013
* Acceso a Internet 

##Solución de aplicación de ejemplo de aprendizaje automático de Azure

La solución contiene el código fuente, el archivo de uso y de catálogo de ejemplo y las directivas para descargar los paquetes de nuget necesarios para la compilación.

##La API usada

La aplicación usa solo un pequeño subconjunto de la funcionalidad de recomendación de aprendizaje automático de Azure a través de un subconjunto de las API disponibles. Las siguientes API se muestran en la aplicación:

* Crear modelo: cree el contenedor lógico para mantener los modelos de datos y recomendación. Un modelo se identifica mediante un nombre; un usuario no puede crear un modelo con el mismo nombre dos veces.
* Cargar archivo de catálogo: para cargar los datos del catálogo
* Cargar archivo de uso: para cargar los datos de uso
* Desencadenar la compilación: para crear un modelo de recomendación
* Supervisar la ejecución de la compilación: supervisar el estado de una compilación de modelo de recomendación
* Elegir un modelo compilado para recomendación: para indicar qué modelo de recomendación usar de forma predeterminada para un determinado contenedor de modelos. Este paso solamente es necesario si tiene más de un modelo de recomendación y desea una compilación no activa como compilación activa.
* Obtener recomendación: para recuperar un elemento recomendado según una único elemento o un conjunto de elementos. 

Para obtener una descripción completa de la API, consulte la documentación de Microsoft Azure Marketplace. 

Nota: Un modelo puede tener varias compilaciones con el tiempo (no de forma simultánea), cada una de ellas creada con el catálogo o con el catálogo actualizado y con datos de uso adicionales.

## Dificultades habituales

* Necesita proporcionar su nombre de usuario y la clave de la cuenta principal de Microsoft Azure Marketplace como una línea de comandos para ejecutar el ejemplo
* La ejecución de la aplicación de ejemplo de forma consecutiva provocará errores: el flujo de la aplicación administra el monitor de creación, carga y compilación y obtiene la recomendación de un nombre de modelo predefinido, por lo tanto, se producirán errores cuando se ejecute de forma consecutiva si no cambia el nombre del modelo entre invocaciones
* La recomendación podría no devolver datos: la aplicación de ejemplo utiliza un archivo de catálogo y uso muy pequeño, por lo que el modelo de recomendación creado no es significativo como consecuencia de que algún elemento del catálogo no tendrá elementos recomendados.

## Renuncia de responsabilidades
La aplicación de ejemplo no está diseñada para ejecutarse para producción, los datos proporcionados en el catálogo y uso son muy pequeños y no proporcionarán un modelo de recomendación significativo, sino que solamente se proporciona como demostración. 

## Información legal
Este documento se proporciona "como está". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. 
Algunos ejemplos mencionados se proporcionan únicamente con fines ilustrativos y son ficticios. No se pretende ninguna asociación o conexión real ni debe deducirse. 
Este documento no proporciona ningún derecho legal a la propiedad intelectual de ningún producto de Microsoft. Puede copiar y usar este documento con fines internos y de referencia. 
(c) 2014 Microsoft. Todos los derechos reservados. 

