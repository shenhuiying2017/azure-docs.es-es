---
title: Microsoft Threat Modeling Tool (Azure) | Microsoft Docs
description: "Página principal de Microsoft Threat Modeling Tool, que contiene métodos para mitigar las amenazas generadas más expuestas"
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
ms.date: 02/16/2017
ms.author: rodsan
translationtype: Human Translation
ms.sourcegitcommit: 2ad59f9b463671e2b74708871a9a9faae9bd24eb
ms.openlocfilehash: b75de472508bbf44c6e02e0d751054f37b0e1b8c
ms.lasthandoff: 02/21/2017


---

# <a name="microsoft-threat-modeling-tool"></a>Microsoft Threat Modeling Tool 
Threat Modeling Tool es un elemento básico del Ciclo de vida de desarrollo de seguridad (SDL) de Microsoft. Permite a los arquitectos de software identificar y mitigar los posibles problemas de seguridad en una fase temprana, cuando son relativamente sencillos y poco costosos de resolver. En consecuencia, reduce en gran medida el costo total de desarrollo. Además, hemos diseñado la herramienta pensando en expertos no relacionados con la seguridad, lo que facilita el modelado de amenazas para todos los programadores, ya que se proporcionan instrucciones claras sobre cómo crear y analizar los modelos de amenazas. 

La herramienta permite a cualquier persona:
* Comunicar el diseño de seguridad de sus sistemas
* Analizar los diseños de posibles problemas de seguridad con una metodología probada
* Sugerir y administrar mitigaciones para problemas de seguridad

Estas son algunas funcionalidades e innovaciones de herramientas, solo por nombrar algunas:
* **Automatización:** instrucciones y comentarios en el dibujo de un modelo.
* **STRIDE por elemento:** análisis guiado de amenazas y mitigaciones.
* **Informes:** actividades de seguridad y de prueba en la fase de comprobación.
* **Metodología única:** permite a los usuarios visualizar y comprender mejor las amenazas.
* **Diseñadas para desarrolladores y centradas en el software:** muchos enfoques están centrados en recursos o atacantes. Estamos centrados en el software. Nos basamos en actividades con las que todos los arquitectos y desarrolladores de software están familiarizados, como dibujar imágenes para la arquitectura de su software.
* **Centradas en el análisis de diseño:** el término "modelado de amenazas" puede hacer referencia a un requisito o a una técnica de análisis de diseño. A veces, hace referencia a una compleja combinación de ambas. El enfoque de SDL de Microsoft para el modelado de amenazas es una técnica de análisis de diseño prioritaria.

## <a name="threats"></a>Amenazas

La Herramienta de modelado de amenazas le ayuda a dar respuesta a determinadas preguntas como las siguientes:

* ¿Cómo puede cambiar un atacante los datos de autenticación?
* ¿Cuál es el impacto si un atacante puede leer los datos del perfil de usuario?
* ¿Qué ocurre si se deniega el acceso a la base de datos del perfil de usuario?

Para ayudarle a formular mejor los tipos de pregunta planteados, Microsoft utiliza el modelo STRIDE, que clasifica los distintos tipos de amenazas y simplifica las conversaciones de seguridad global.

| Categoría | Descripción |
| -------- | ----------- |
| Suplantación de identidad | Implica el acceso y uso ilícitos de la información de autenticación de otro usuario, como el nombre de usuario y la contraseña. |
| Alteración de datos | Implica la modificación malintencionada de los datos. Entre los ejemplos se incluyen los cambios no autorizados realizados en los datos persistentes, como los que se encuentran en una base de datos, y la alteración de datos a medida que fluyen entre dos equipos a través de una red abierta, como Internet. |
| Rechazo | Está asociado a usuarios que deniegan la ejecución de una acción sin que otras partes tengan forma alguna de comprobarlo de otro modo, por ejemplo, un usuario realiza una operación ilegal en un sistema que no tiene la capacidad de realizar el seguimiento de las operaciones prohibidas. El no rechazo hace referencia a la capacidad de un sistema de contrarrestar las amenazas de rechazo. Por ejemplo, es posible que un usuario que adquiera un elemento deba firmar por el elemento al recibirlo. El proveedor puede utilizar el recibo firmado como prueba de que el usuario ha recibido el paquete. |
| Divulgación de información | Implica la exposición de información a personas que no deberían tener acceso a ella, por ejemplo, la capacidad de los usuarios de leer un archivo al que no se les ha concedido acceso o la capacidad de un intruso de leer datos en tránsito entre dos equipos. |
| Denegación de servicio | Los ataques por denegación de servicio (DoS) deniegan el servicio a usuarios válidos, por ejemplo, al hacer que un servidor web esté temporalmente inutilizable o no disponible. Debe protegerse contra determinado tipo de amenazas de denegación de servicio simplemente para mejorar la confiabilidad y disponibilidad del sistema. |
| Elevación de privilegios | Un usuario sin privilegios obtiene acceso con privilegios y, por tanto, tiene acceso suficiente para poner en peligro o destruir todo el sistema. Entre las amenazas de elevación de privilegios se incluyen aquellas situaciones en las que un atacante ha superado de manera eficaz todas las defensas del sistema y se ha convertido en parte del sistema de confianza en sí, una situación verdaderamente peligrosa. |

## <a name="mitigations"></a>Mitigaciones

Las mitigaciones de la Herramienta de modelado de amenazas se clasifican según el marco de seguridad de aplicación web, que consta de las siguientes acciones:

| Categoría | Descripción |
| -------- | ----------- |
| [Auditoría y registro](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-auditing-and-logging) | ¿Quién hizo qué y cuándo? La auditoría y el registro hacen referencia a cómo registra la aplicación los eventos relacionados con la seguridad. |
| [Autenticación](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authentication) | ¿Quién es usted? La autenticación es el proceso en el que una entidad demuestra la identidad de otra entidad, normalmente por medio de credenciales, como un nombre de usuario y una contraseña. |
| [Autorización](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-authorization) | ¿Qué puede hacer? La autorización es la forma en que la aplicación proporciona controles de acceso para los recursos y operaciones. | 
| [Seguridad en las comunicaciones](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-communication-security) | ¿Con quién está hablando? La seguridad de comunicaciones garantiza que todas las comunicaciones realizadas sean lo más seguras posibles. |
| [Administración de configuración](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-configuration-management) | ¿Quién ejecuta la aplicación? ¿A qué bases de datos se conecta? ¿Cómo se administra la aplicación? ¿Cómo se protege esta configuración? La administración de configuración hace referencia a cómo controla la aplicación estos problemas de funcionamiento. | 
| [Criptografía](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-cryptography) | ¿Cómo mantiene los secretos (confidencialidad)? ¿Cómo altera y corrige los datos o las bibliotecas (integridad)? ¿Cómo proporciona valores de inicialización para valores aleatorios que deben ser de alta seguridad criptográfica? La criptografía hace referencia a cómo exige la aplicación confidencialidad e integridad. | 
| [Administración de excepciones](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-exception-management) | Cuando se produce un error en una llamada al método en la aplicación, ¿qué hace la aplicación? ¿Cuánta información revela? ¿Devuelve una información de error descriptiva a los usuarios finales? ¿Envía información valiosa sobre excepciones al llamador? ¿La aplicación da errores leves? |
| [Validación de entradas](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-input-validation) | ¿Cómo sabe que las entradas que recibe la aplicación son válidas y seguras? La validación de entradas hace referencia a cómo filtra, anula o rechaza la aplicación las entradas antes de seguir con el procesamiento. Considere la posibilidad de restringir las entradas a través de puntos de entrada y de codificar las salidas a través de puntos de salida. ¿Confía en datos de orígenes como bases de datos y recursos compartidos de archivos? |
| [Información confidencial](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-sensitive-data) | ¿Cómo maneja la aplicación la información confidencial? La información confidencial hace referencia a cómo trata la aplicación los datos que deben protegerse en la memoria, en la red o en almacenes permanentes. | 
| [Administración de sesiones](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-session-management) | ¿Cómo controla y protege la aplicación las sesiones de usuario? Una sesión hace referencia a una serie de interacciones relacionadas entre un usuario y la aplicación web. | 

Esto ayuda a identificar: 

* Dónde se realizan los errores más comunes
* Dónde son las mejoras más procesables

En consecuencia, utiliza estas categorías para centrarse y priorizar el trabajo de seguridad, por lo que si sabe que los problemas de seguridad más frecuentes se producen en las categorías de validación, autenticación y autorización de entradas, puede empezar por ahí. Haga clic [aquí](https://www.google.com/patents/US7818788) para más información.

## <a name="next-steps"></a>Pasos siguientes
Visite estos valiosos vínculos para comenzar hoy mismo:
* [Vínculo de descarga](https://www.microsoft.com/download/details.aspx?id=49168)
* [Introducción](https://msdn.microsoft.com/magazine/dd347831.aspx)
* [Formación básica](https://www.microsoft.com/download/details.aspx?id=16420)

Mientras está en ello, consulte lo que han hecho algunos expertos en la Herramienta de modelado de amenazas:
* [Threats Manager](https://simoneonsecurity.com/threatsmanagersetup-v1-5-10/)
* [Blog de seguridad de Simone Curzi](https://simoneonsecurity.com/)
