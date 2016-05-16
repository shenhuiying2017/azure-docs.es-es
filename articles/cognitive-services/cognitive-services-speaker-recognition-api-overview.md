<properties
   pageTitle="Información general: Speaker Recognition API de Cognitive Services | Microsoft Azure"
   description="Obtenga información sobre cómo el reconocimiento del hablante puede mejorar las aplicaciones. Las Speaker Recognition API tienen algoritmos avanzados para la comprobación e identificación del hablante."
   services="cognitive-services"
   documentationCenter="na"
   authors="cjgronlund"
   manager="paulettem"
   editor="cjgronlund"/>

<tags
   ms.service="cognitive-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="cgronlun"/>

# Información general: Speaker Recognition API de Azure Cognitive Services

Obtenga información sobre cómo las funcionalidades de reconocimiento del hablante pueden mejorar las aplicaciones. Las Speaker Recognition API son API basadas en la nube que proporcionan los algoritmos más avanzados para la comprobación e identificación del hablante. El reconocimiento del hablante se puede dividir en dos categorías:

  * Comprobación del hablante
  * Identificación del hablante

## Comprobación del hablante

La voz tiene características únicas que se pueden usar para identificar a una persona, de manera similar a una huella digital. Usar la voz como señal en escenarios de autenticación y control de acceso surgió como una nueva herramienta de innovación; en esencia, se ofrece un nivel más de seguridad que simplifica la experiencia de autenticación de los clientes.

Las Speaker Verification API pueden comprobar y autenticar automáticamente los usuarios a través de su voz.

### Inscripción de comprobación del hablante

La inscripción de la comprobación del hablante depende del texto, lo que significa que los hablantes deben elegir una frase de contraseña específica para usar durante las fases de inscripción y comprobación.

En la inscripción, la voz del hablante se graba diciendo una frase específica y, luego, se extraen diversas características y se reconoce la frase elegida. En conjunto, las características extraídas y la frase elegida conforman una firma de voz única.

### Comprobación de la frase y la voz de entrada

En la comprobación, se compara una frase y una voz de entrada contra la frase y la voz de entrada de la inscripción, con el fin de comprobar si pertenecen o no a la misma persona y si se dice la frase correcta.

Para más información sobre la comprobación del hablante, consulte la [documentación relacionada con la comprobación de Speaker Recognition API](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## Identificación del hablante

Las Speaker Identification API pueden identificar automáticamente a la persona que habla en un archivo de audio dentro de un grupo de posibles hablantes. El audio de entrada se compara con el grupo de hablantes proporcionado y, en caso de que exista una coincidencia, se devuelve la identidad del hablante.

Todos los hablantes deben someterse primero a un proceso de inscripción para grabar su voz en el sistema y para que se cree una huella de voz.

### Inscripción de identificación del hablante

La inscripción para la identificación del hablante depende del texto, lo que significa que no hay restricciones con respecto a lo que el hablante dice en el audio. Se graba la voz del hablante y se extraen diversas características para confirmar una firma de voz única.

### Reconocimiento del hablante: coincidencia con la voz de entrada

Durante la fase de reconocimiento, se proporciona el audio del hablante desconocido, junto con el grupo de posibles hablantes. La voz de entrada se compara con todos los hablantes a fin de determinar a quién pertenece la voz y, en caso de que se encuentre una coincidencia, se devuelve la identidad del hablante.

Para más información sobre la identificación del hablante, consulte la [documentación relacionada con la identificación de Speaker Recognition API](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).

<!---HONumber=AcomDC_0504_2016-->