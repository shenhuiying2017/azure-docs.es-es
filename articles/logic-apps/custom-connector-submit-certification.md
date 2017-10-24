---
title: "Certificación de conectores personalizados: Azure Logic Apps | Microsoft Docs"
description: "Ponga los conectores a disposición de todos los usuarios de Azure Logic Apps, Microsoft Flow y Microsoft PowerApps"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Envío de conectores para que Microsoft los certifique

Para que los conectores personalizados estén a disposición pública de todos los usuarios de Azure Logic Apps, Microsoft Flow y Microsoft PowerApps, envíe cada uno de ellos a Microsoft para que se revise, se valide y se apruebe su publicación. 

## <a name="certification-criteria"></a>Criterios de certificación

| Capacidad | Detalles | Obligatorio o recomendado |
|------------|---------|-------------------------|
| Aplicación de software como servicio (SaaS) | Cumple un escenario de usuario que encaja bien con Logic Apps, Flow y PowerApps. | Obligatorio |
| Tipo de autenticación | La API debe admitir OAuth2, Clave de API o Autenticación básica. | Obligatorio | 
| Soporte técnico | Debe especificar un contacto de soporte técnico para que los clientes puedan obtener ayuda. | Obligatorio | 
| Disponibilidad y tiempo de actividad | La aplicación tiene un mínimo de un 99,9 % de tiempo de actividad. | Recomendado | 
|||| 

Si no es partner de Microsoft o un fabricante de software independiente (ISV) y desea certificar y liberar públicamente un conector, debe ser propietario del servicio subyacente o presentar derechos explícitos para usar la API.

## <a name="validation-phases"></a>Fases de validación

Microsoft utiliza estas fases de validación para evaluar el conector:

| Fase de validación | Descripción | 
| ----- | ----------- |
| Funcionalidad | Microsoft comprueba el conector con Logic Apps, Flow y PowerApps para ver si aparecen estos errores: <p>-OpenAPI (Swagger) no válido o errores de JSON que aparecen en la sección de definición del Asistente para conector personalizado <p>-Errores de tiempo de ejecución y de validación de esquema que aparecen en la sección de pruebas del Asistente para conector personalizado | 
| Contenido | Microsoft comprueba que el conector utiliza nombres descriptivos y descripciones en todas las entidades. Asegúrese de que todas las operaciones, parámetros de entrada y atributos de respuesta de Swagger de su conector tienen estos elementos: <p>- [Resumen](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [Descripción](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [Información de visibilidad](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>Designe y envíe el conector a Microsoft para que lo certifique

Para solicitar la certificación, siga estos pasos:

1. **Designar**

   1. [Envíe la designación](https://go.microsoft.com/fwlink/?linkid=848754).

      1. En la parte inferior de la página, elija **Contact us** (Póngase en contacto con nosotros).
      2. Rellene el formulario y seleccione **Questions on ISV connector program and co-marketing** (Preguntas acerca del programa de conector de ISV y de marketing conjunto).
      3. Elija **Submit** (Enviar).

   2. Firme el acuerdo de confidencialidad y el acuerdo de socio comercial mutuos que recibe. 

      Para continuar, Microsoft exige que se firmen estos contratos. 
      A partir de ahí se puede comprobar si su conector cumple los criterios de certificación. 
   
   3. Si se aprueba el conector, Microsoft le proporcionará instrucciones para su incorporación.
    
2. **Revisión**

   1. Envíe esta información a su contacto de designación para su revisión:

      * El archivo OpenAPI que describe la API
      * El archivo de icono (.png o .jpg) que representa el conector 
      
        El icono debe tener un logotipo de 160 píxeles dentro de un cuadrado de 230 píxeles. 
        Se prefiere un logotipo blanco sobre un fondo de color.
      
      * El color de la marca del icono en formato hexadecimal, que debe coincidir con el fondo de color del archivo de icono

      * Una cuenta de prueba para la validación
      * Un contacto de soporte técnico

   2. Si se necesita más información, nos pondremos en contacto con usted.

3. **Publicar**

    Después de validar la funcionalidad y el contenido del conector, se realiza una copia intermedia de él para su implementación en todos los productos y regiones.
    
    De forma predeterminada, todos los conectores se publican como conectores "premium". 
    Si ha compilado su aplicación con Azure, puede solicitar que el conector aparezca en las listas como conector "estándar" disponible para todos los usuarios con planes de Office 365 Enterprise. 
    Si desea más información, pídasela a su contacto de designación.

## <a name="next-steps"></a>Pasos siguientes

* [Preguntas más frecuentes sobre conectores personalizados](../logic-apps/custom-connector-faq.md)
* [Información general de los conectores personalizados](../logic-apps/custom-connector-overview.md)