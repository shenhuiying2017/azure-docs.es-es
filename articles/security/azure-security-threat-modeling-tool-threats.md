---
title: 'Amenazas: Microsoft Threat Modeling Tool (Azure) | Microsoft Docs'
description: "Página de categorías de amenazas de Microsoft Threat Modeling Tool, que contiene categorías para todas las amenazas generadas expuestas."
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 704f9995828866d4d2e4969e3aa922ed1e23c4ea
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Amenazas de Microsoft Threat Modeling Tool

Threat Modeling Tool es un elemento básico del Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft. Permite a los arquitectos de software identificar y mitigar los posibles problemas de seguridad en una fase temprana, cuando son relativamente sencillos y poco costosos de resolver. En consecuencia, reduce en gran medida el costo total de desarrollo. Además, hemos diseñado la herramienta pensando en expertos no relacionados con la seguridad, lo que facilita el modelado de amenazas para todos los programadores, ya que se proporcionan instrucciones claras sobre cómo crear y analizar los modelos de amenazas.

> Visite  **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** .

La Herramienta de modelado de amenazas le ayuda a dar respuesta a determinadas preguntas como las siguientes:

* ¿Cómo puede cambiar un atacante los datos de autenticación?
* ¿Cuál es el impacto si un atacante puede leer los datos del perfil de usuario?
* ¿Qué ocurre si se deniega el acceso a la base de datos del perfil de usuario?

## <a name="stride-model"></a>Modelo STRIDE

Para ayudarle a formular mejor los tipos de pregunta planteados, Microsoft utiliza el modelo STRIDE, que clasifica los distintos tipos de amenazas y simplifica las conversaciones de seguridad global.

| Categoría | Descripción |
| -------- | ----------- |
| **Suplantación de identidad** | Implica el acceso y uso ilícitos de la información de autenticación de otro usuario, como el nombre de usuario y la contraseña. |
| **Alteración de datos** | Implica la modificación malintencionada de los datos. Entre los ejemplos se incluyen los cambios no autorizados realizados en los datos persistentes, como los que se encuentran en una base de datos, y la alteración de datos a medida que fluyen entre dos equipos a través de una red abierta, como Internet. |
| **Rechazo** | Está asociado a usuarios que deniegan la ejecución de una acción sin que otras partes tengan forma alguna de comprobarlo de otro modo, por ejemplo, un usuario realiza una operación ilegal en un sistema que no tiene la capacidad de realizar el seguimiento de las operaciones prohibidas. El no rechazo hace referencia a la capacidad de un sistema de contrarrestar las amenazas de rechazo. Por ejemplo, es posible que un usuario que adquiera un elemento deba firmar por el elemento al recibirlo. El proveedor puede utilizar el recibo firmado como prueba de que el usuario ha recibido el paquete. |
| **Divulgación de información** | Implica la exposición de información a personas que no deberían tener acceso a ella, por ejemplo, la capacidad de los usuarios de leer un archivo al que no se les ha concedido acceso o la capacidad de un intruso de leer datos en tránsito entre dos equipos. |
| **Denegación de servicio** | Los ataques por denegación de servicio (DoS) deniegan el servicio a usuarios válidos, por ejemplo, al hacer que un servidor web esté temporalmente inutilizable o no disponible. Debe protegerse contra determinado tipo de amenazas de denegación de servicio simplemente para mejorar la confiabilidad y disponibilidad del sistema. |
| **Elevación de privilegios** | Un usuario sin privilegios obtiene acceso con privilegios y, por tanto, tiene acceso suficiente para poner en peligro o destruir todo el sistema. Entre las amenazas de elevación de privilegios se incluyen aquellas situaciones en las que un atacante ha superado de manera eficaz todas las defensas del sistema y se ha convertido en parte del sistema de confianza en sí, una situación verdaderamente peligrosa. |

## <a name="next-steps"></a>Pasos siguientes

Continúe con  **[Mitigaciones de Threat Modeling Tool](./azure-security-threat-modeling-tool-mitigations.md)**  para conocer las distintas maneras en que puede mitigar estas amenazas con Azure.