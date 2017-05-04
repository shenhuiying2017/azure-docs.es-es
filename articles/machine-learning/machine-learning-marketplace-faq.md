---

ROBOTS: NOINDEX, NOFOLLOW
redirect_url: https://gallery.cortanaintelligence.com/
translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 4f6e2816a55eb48fe947cc5bf21ee466e8116feb
ms.lasthandoff: 05/03/2017


---
# <a name="deprecated-publishing-and-using-machine-learning-apps-in-the-azure-marketplace-faq"></a>(obsoleto) Publicación y uso de aplicaciones de Machine Learning en Azure Marketplace: preguntas frecuentes

> [!NOTE]
> DataMarket y Data Services están en proceso de retirada, y las suscripciones existentes se retirarán y cancelarán a partir del 31 de marzo de 2017. Como resultado, este artículo se ha quedado obsoleto. 
> 
> Aún tiene la posibilidad de publicar sus experimentos de Machine Learning en la [Galería de Cortana Intelligence](https://gallery.cortanaintelligence.com/) en beneficio de la comunidad de la ciencia de datos. Para obtener más información, consulte [Uso compartido y descubrimiento de soluciones en la Galería de Cortana Intelligence](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish).


## <a name="questions-about-consuming-from-marketplace"></a>Preguntas acerca del consumo en Marketplace
**1. Por qué obtengo el siguiente mensaje de error tras especificar una entrada para el servicio web:**

**La solicitud ha ocasionado un tiempo de espera de back-end o un error de back-end. El equipo está investigando el problema. Lamentamos los inconvenientes. (500)**

Los parámetros de entrada no pueden ajustarse al formato requerido para el servicio web específico. Consulte el vínculo correspondiente de la documentación para encontrar el formato correcto de los parámetros de entrada y las limitaciones de este servicio web.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2. Si copio el vínculo de la API del servicio web que aparece en la página "Explorar este conjunto de datos" y lo pego en otra ventana del explorador, ¿qué credenciales tengo que usar para obtener acceso a los resultados y cómo puedo verlos?**

Debe usar la cuenta de Marketplace como nombre de usuario y la clave de la cuenta principal como contraseña. La clave de la cuenta principal se puede encontrar en la página **Explorar este conjunto de datos** en la descripción del servicio web (haga clic en el botón **Mostrar**). El resultado se puede mostrar en el explorador o estar disponible para descargarlo, en función de qué explorador use.

**3. ¿Por qué obtengo el siguiente mensaje de error tras especificar una entrada para el servicio web en la página "Explorar este conjunto de datos":**? 

**Se ha producido un error inesperado al procesar la solicitud. Vuelva a intentarlo.**

Uno o varios parámetros de entrada del servicio web pueden haber excedido el límite de longitud al consumir el servicio web en la página **Explorar este conjunto de datos** de Marketplace. Los servicios pueden llamarse con una longitud de entrada mediante los métodos HTTP POST. Para obtener ejemplos, consulte [Soluciones de ejemplo con R en Aprendizaje automático y publicados en Marketplace](machine-learning-r-csharp-web-service-examples.md).

**4. ¿Por qué no veo nada en la pestaña "EXPLORADOR DE API" en el almacén en el Portal de Azure clásico?** 

Se trata de un problema conocido con el Portal de Azure clásico Marketplace. El equipo está trabajando para resolver este problema. 

## <a name="questions-about-publishing-from-azure-machine-learning-on-marketplace"></a>Preguntas acerca de la publicación desde Aprendizaje automático de Azure en Marketplace
**1. ¿Por qué mis transacciones de logotipos o imágenes no se actualizan en mi servicio web?** 

Los logotipos y las imágenes se almacenan en caché en el portal de publicación y la actualización del nuevo logotipo o de la nueva imagen en el portal puede tardar hasta 10 días en completarse.

**2. ¿Por qué aparece un error en la pestaña "Detalles" del servicio web de Marketplace?**

Hay un problema conocido de Marketplace al conectarse a Aprendizaje automático de Azure para obtener detalles del servicio. El equipo está trabajando para resolver este problema.

**3. ¿Por qué no funciona el código de ejemplo R en los servicios web de Aprendizaje automático de Azure para consumir los servicios web en Marketplace?**

Los sistemas de autenticación son diferentes al conectarse a servicios web de Aprendizaje automático de Azure directamente y al conectarse a estos servicios web a través de Marketplace. Los servicios de Marketplace son servicios de OData y se les puede llamar con los métodos GET o POST. 

**4. ¿Por qué los vínculos de soporte técnico de las ofertas del servicio web no se actualizan correctamente para algunas de mis ofertas?**

Los vínculos de soporte técnico son globales por publicador, no por oferta. 

**5. ¿Cómo puedo publicar un servicio web con el modo de entrada por lotes en Marketplace?**

El modo de entrada por lotes no se admite actualmente en los servicios web de Marketplace.

**6. ¿Con quién debo comunicarme para obtener ayuda si tengo preguntas sobre cómo convertirme en un publicador de datos o si tengo problemas durante la publicación?**

Póngase en contacto con el equipo de Azure Marketplace en <mailto:datamarketbd@microsoft.com> para más información.


