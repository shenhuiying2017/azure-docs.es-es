---
title: 'Azure RemoteApp: ¿cómo funcionan el ancho de banda de red y la calidad de la experiencia conjuntamente? | Microsoft Docs'
description: Obtenga información acerca de cómo puede afectar el ancho de banda de red en Azure RemoteApp a la calidad de la experiencia de los usuarios.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Azure RemoteApp: ¿cómo funcionan el ancho de banda de red y la calidad de la experiencia conjuntamente?
> [!IMPORTANT]
> Azure RemoteApp va a dejar de estar disponible. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Cuando esté analizando el [ancho de banda de red general](remoteapp-bandwidth.md) requerido para Azure RemoteApp, tenga en cuenta los siguientes factores, Todos ellos forman parte de un sistema dinámico que afecta a la experiencia general del usuario.

* **Ancho de banda de red disponible y condiciones de red actuales**: un conjunto de parámetros (pérdida, latencia, vibración) en la misma red en un momento dado puede afectar a la experiencia de descarga de la aplicación, lo que implica una experiencia de usuario general reducida. El ancho de banda disponible en la red es una función de congestión, pérdida aleatoria y latencia, ya que todos estos parámetros afectan al mecanismo de control de congestión, que a su vez controla la velocidad de transmisión para evitar colisiones. Por ejemplo, una red con pérdida de información o con una latencia alta, hará que la experiencia de usuario sea mala incluso en una red con un ancho de banda de 1000 MB. La latencia y la pérdida varían según el número de usuarios que se encuentran en la misma red y en lo que hacen los usuarios (por ejemplo, ver vídeos, descargar o cargar archivos grandes o imprimir).
* **Escenario de uso**: la experiencia depende de lo que hacen los usuarios como individuos y como un grupo en la misma red. Por ejemplo, la lectura de una diapositiva requiere únicamente la actualización de un solo fotograma; si el usuario pasa por encima y se desplaza por el contenido de un documento de texto, necesitará la actualización de un número mayor de fotogramas por segundo. El intercambio de mensajes en el servidor en este escenario consumirá, en definitiva, más ancho de banda de red. Considere también un ejemplo extremo: varios usuarios están viendo vídeos de alta definición (como, por ejemplo, con una resolución de 4K), realizando llamadas de conferencias HD, jugando a videojuegos en 3D o trabajando en sistemas CAD. Todo esto puede hacer que incluso una red con un ancho de banda muy alto sea prácticamente inservible.
* **Resolución de pantalla y el número de pantallas**: se necesita más ancho de banda de red para actualizar completamente las pantallas grandes que las pequeñas. La tecnología subyacente realiza un buen trabajo codificando y transmitiendo solo las regiones de las pantallas que se han actualizado, pero de vez en cuando, es necesario actualizar toda la pantalla. Cuando el usuario tiene una pantalla de resolución superior (por ejemplo, una resolución de 4K), esa actualización requiere más ancho de banda de red que una pantalla con una resolución inferior (por ejemplo, 1024 x 768 px). Esta misma lógica se aplica si usa más de una pantalla de redireccionamiento. El ancho de banda necesita incrementarse con el número de pantallas.
* **Redireccionamiento del portapapeles y los dispositivos**: este no es un problema muy evidente, pero en muchos casos si un usuario almacena un grupo de datos grande en el portapapeles, es necesario un poco de tiempo para transferir esa información desde el cliente de Escritorio remoto al servidor. La experiencia de bajada puede verse afectada por la experiencia de subir el contenido del portapapeles. Lo mismo se aplica para el redireccionamiento de dispositivos: si un escáner o una cámara web produce muchos datos que necesitan subirse al servidor, o una impresora necesita recibir un documento grande o el almacenamiento local necesita estar disponible para que una aplicación que se está ejecutando en la nube copie un archivo de gran tamaño, los usuarios pueden experimentar fotogramas perdidos o vídeos temporalmente "inmovilizados" porque los datos necesarios para el redireccionamiento de dispositivos está aumentando las necesidades del ancho de banda de red.

Al evaluar sus necesidades de ancho de banda de red, asegúrese de tener en cuenta el funcionamiento de todos estos factores en un sistema.

Ahora, vuelva al [artículo de ancho de banda de red principal](remoteapp-bandwidth.md), o continúe para probar su [ancho de banda de red](remoteapp-bandwidthtests.md).

## Más información
* [Calcular el uso del ancho de banda de red de Azure RemoteApp](remoteapp-bandwidth.md)
* [Azure RemoteApp - testing your network bandwidth usage with some common scenarios (Azure RemoteApp: probar su uso de ancho de banda de red con algunos escenarios comunes)](remoteapp-bandwidthtests.md)
* [Ancho de banda de red de Azure RemoteApp: directrices generales (si no puede probarlo usted mismo)](remoteapp-bandwidthguidelines.md)

<!---HONumber=AcomDC_0817_2016-->