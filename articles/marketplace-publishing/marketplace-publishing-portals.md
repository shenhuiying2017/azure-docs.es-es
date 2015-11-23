<properties
   pageTitle="Información general de los diversos portales necesarios para crear una oferta en Marketplace | Microsoft Azure"
   description="Información general de los diversos portales necesarios para crear una oferta en Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2015"
   ms.author="hascipio" />


# Portales que necesitará
Antes de que empiece con el proceso, vamos a presentarle los diversos portales que necesitará para publicar la oferta. A continuación se muestra el resumen breve sobre los portales: **Panel de vendedores, Portal de publicación** y **Portal de vista previa de Azure** en el orden en que se interactúa con ellos.
## Panel de vendedores
[https://sellerdashboard.microsoft.com](https://sellerdashboard.microsoft.com)
### Descripción
La cuenta del panel de vendedores solo se crea una vez. El socio debe asegurarse de que su compañía no tenga ya una cuenta en el Panel de vendedores antes de intentar crear una. Durante el proceso, recopilamos los datos de su cuenta bancaria, su información fiscal y la dirección de la compañía.

> [AZURE.NOTE]Si solo va a publicar ofertas gratuitas (o de tipo traiga su propia licencia) no necesitamos información bancaria ni fiscal.

### Identidad y cuenta usadas
Lo ideal es una lista de distribución o un grupo de seguridad (por ejemplo, azurepublishing@partnercompany.com). La lista de distribución o el grupo de seguridad **debe** registrarse como cuenta de Microsoft.

> [AZURE.TIP]Se recomienda usar una lista de distribución o un grupo de seguridad, puesto que eliminan la dependencia de cualquier persona; aunque también se puede usar una cuenta individual.

## Portal de publicación
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Descripción
Portal para que el socio trabaje en su oferta y la publique (marketing, precios, publicación, certificación si es aplicable, etc.).

### Identidad y cuenta usadas
Anteriormente había que usar una **lista de distribución** o un **grupo de seguridad** al iniciar sesión por primera vez en el Portal de publicación y posteriormente podían agregarse otros usuarios como coadministradores. Así es cómo se asigna a los datos de registro del Panel de vendedores.

## Portal de vista previa de Azure
[https://ms.portal.azure.com](https://ms.portal.azure.com)
### Descripción
Este es el portal donde los socios pueden ver sus ofertas publicadas y de ensayo en Marketplace (aplicable a máquinas virtuales, plantillas de solución y servicios de desarrolladores basados en ARM)
### Identidad y cuenta usadas
Mientras la oferta del portal de publicación esté en ensayo, es preciso que se incluya un identificador de suscripción en la lista blanca. Para probar la oferta de ensayo, hay que usar la misma suscripción [no hay nombre de usuario y contraseña asociados] al iniciar sesión en este portal.

## Otras referencias
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->