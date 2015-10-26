<properties
   pageTitle="Probar su oferta de VM para Marketplace | Microsoft Azure"
   description="Entienda cómo probar su imagen de VM para Azure Marketplace."
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
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Probar su oferta de VM para Marketplace en un entorno de ensayo

En la etapa de ensayo, se implementa la SKU en un “espacio aislado” privado, donde puede probar y validar su funcionalidad antes de implementarla en Marketplace. La SKU aparecerá en un entorno de ensayo tal y como lo haría para un cliente que la ha implementado. Su imagen de VM debe estar certificada para trasladarse a un entorno de ensayo.

## Paso 1: trasladar su oferta a un entorno de ensayo

1. Haga clic en **Push to Staging** (Pasar a ensayo) en la pestaña **Publish** (Publicar).

  ![dibujo](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Corrija los errores o discrepancias que pueda notificarle el Portal de publicación en este momento.
3.	Proporcione la lista de suscripciones de Azure que usará para obtener una vista previa de su oferta en el [Portal de vista previa de Azure](https://portal.azure.com) en el cuadro emergente como se muestra en la captura de pantalla anterior.
4. Inicie sesión en el [Portal de vista previa de Azure](http://portal.azure.com) mediante una de las suscripciones de Azure anteriores descritas en el paso anterior.
5. Busque su oferta y valide los puntos de su imagen de VM:
  1. El contenido de marketing se muestra correctamente en la galería.

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. Implementación completa de imagen de VM

> [AZURE.IMPORTANT]Su oferta permanecerá en un entorno de ensayo hasta que notifique a Microsoft a través del Portal de publicación [**Publicar** > **"Solicitar aprobación para enviar a producción"**] que está listo para enviar a producción. Este es un momento ideal para que todos los miembros del equipo revisen todo antes de hacer efectiva la oferta.

> La replicación por los centros de datos tarda de 24 a 48 horas. Cuando se complete la replicación, la oferta se mostrará en [Azure Marketplace](http://azure.microsoft.com/marketplace).

## Pasos siguientes
Ahora que el estado de su oferta es "De ensayo" y que ha probado su funcionalidad y contenido de marketing, puede continuar con la fase de publicación de SKU o la oferta final, **Paso 4**, [Implementación de su oferta en Marketplace](marketplace-publishing-push-to-production.md)

## Otras referencias
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->