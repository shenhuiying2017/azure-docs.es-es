---
title: Qué hacer si se produce una interrupción del servicio de Azure que afecta al Almacén de claves de Azure | Microsoft Docs
description: Descubra qué hacer en caso de que se produzca una interrupción del servicio de Azure que afecte al Almacén de claves de Azure.
services: key-vault
documentationcenter: ''
author: adamglick
manager: mbaldwin
editor: ''

ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: sumedhb;aglick

---
# Redundancia y disponibilidad del Almacén de claves de Azure
El Almacén de claves de Azure tiene varias capas de redundancia para garantizar la disponibilidad de las claves y los secretos para su aplicación, aunque se produzcan errores de componentes individuales del servicio.

El contenido del almacén de claves se replica dentro de la región, así como en una región secundaria que se encuentra a una distancia mínima de 241 km (pero dentro de la misma ubicación geográfica). Así se mantiene la durabilidad de las claves y los secretos.

Si se produce un error en algún componente individual dentro del servicio Almacén de claves, los componentes alternativos de la región se encargan de atender la solicitud para garantizar que no se pierde funcionalidad. No es necesario realizar ninguna acción para ello. Se llevará a cabo automáticamente y será transparente para el programador.

En el caso excepcional de que toda una región de Azure pase a estar no disponible, las solicitudes efectuadas a Almacén de claves de Azure de esa región se enrutarán automáticamente (un proceso conocido como "conmutación por error") a una región secundaria. Cuando la región primaria vuelva a estar disponible, las solicitudes se enrutarán a ella nuevamente ("conmutación por recuperación"). Conviene insistir en que no es necesaria ninguna acción, ya que este proceso se realizará automáticamente.

Hay algunas advertencias que deben tenerse en cuenta:

* La conmutación por error de región, en caso de producirse, puede tardar varios minutos en completarse. Las solicitudes realizadas durante este intervalo podrían no efectuarse correctamente mientras no se complete la conmutación.
* Una vez finalizada una conmutación por error, el almacén de claves se encontrará en modo de ***solo lectura***. Las solicitudes compatibles con este modo son las siguientes:
  * Enumeración de almacenes de claves
  * Obtención de propiedades de los almacenes de claves
  * Enumeración de secretos
  * Obtención de secretos
  * Enumeración de claves
  * Obtención de (propiedades de) claves
  * encrypt
  * Descifrado
  * Encapsulado
  * Desencapsulado
  * Comprobación
  * Firma
  * backup
* Cuando tenga lugar la conmutación por recuperación, todos los tipos de solicitud (es decir, de lectura ***y*** escritura) pasarán a estar disponibles.

<!---HONumber=AcomDC_0831_2016-->