<properties
   pageTitle="Prueba de la oferta de plantilla de solución en Marketplace | Microsoft Azure"
   description="Conozca cómo probar la oferta de plantilla de solución en Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="hascipio; v-divte" />

# Prueba de la oferta de plantilla de solución en ensayo
En la etapa de ensayo se implementa la oferta en un “espacio aislado” privado, donde puede probar y comprobar su funcionalidad antes pasarla a producción. La oferta aparecerá en un entorno de ensayo tal y como lo haría para un cliente que la implementase. La oferta **debe estar certificada para pasar a un entorno de ensayo**.

Después de preconfigurar la oferta, puede verla y probarla en el [Portal de vista previa de Azure](https://ms.portal.azure.com/).

Siga los pasos indicados a continuación para insertar la oferta en el entorno de ensayo y llevar a cabo las pruebas en el [Portal de vista previa de Azure](https://ms.portal.azure.com/).

1.	Navegue al [Portal de publicación](https://publish.windowsazure.com)-> seleccione la pestaña **Plantillas de solución** -> su oferta -> **Publicar** -> haga clic en **"Insertar en ensayo"**.
2.	Proporcione la lista de suscripciones de Azure que va a usar para obtener la vista previa de la oferta o probarla.
3.	Inicie sesión en el Portal de vista previa de Azure con el mismo identificador de suscripción con el que preconfiguró la oferta en el paso anterior.
4.	Realice al menos una ronda de pruebas en el Portal de vista previa de Azure sobre los puntos que se mencionan a continuación.
  -	El contenido de marketing se muestra correctamente en la galería.
  -	Compruebe la implementación completa de la topología.
  -	Realice pruebas de rendimiento y pruebas de esfuerzo.
  -	Asegúrese de que la topología se ajusta a las prácticas recomendadas.

## Paso siguiente
Si está satisfecho con los resultados, puede continuar en la fase de publicación de la oferta final, **paso 4**, [Implementación de la oferta en Marketplace](marketplace-publishing-push-to-production.md). Si no, realice cambios en la oferta y vuelva a solicitar la certificación.

> [AZURE.NOTE]Para los cambios de contenido de marketing, no se requiere certificación.

## Otras referencias
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->