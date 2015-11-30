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
Antes de que empiece el proceso de la publicación de una oferta, vamos a presentarle los diversos portales que necesitará. A continuación se muestra el resumen breve sobre los portales -Panel de vendedores, portal de publicación y el portal de vista previa de Azure- en el orden en que se interactúa con ellos.
## Panel de vendedores
[https://sellerdashboard.microsoft.com](https://sellerdashboard.microsoft.com)
### Descripción
La cuenta del panel de vendedores solo se crea una vez. Asegúrese de que la compañía no tenga ya una cuenta en el Panel de vendedores antes de intentar crear una nueva. Durante el proceso, recopilamos los datos de su cuenta bancaria, su información fiscal y la dirección de la compañía.

> [AZURE.NOTE]Si solo va a publicar ofertas gratuitas (u ofertas de tipo traiga su propia licencia), no necesitamos información bancaria ni fiscal.

### Identidad y cuenta usadas
Lo ideal es una lista de distribución o un grupo de seguridad (por ejemplo, azurepublishing@*partnercompany*.com). La lista de distribución o el grupo de seguridad **debe** registrarse como cuenta de Microsoft.

> [AZURE.TIP]Se recomienda usar una lista de distribución o un grupo de seguridad porque que elimina la dependencia de cualquier persona aunque también se puede usar una cuenta individual.

## Portal de publicación
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### Descripción
Este es el portal para que usa para trabajar en la ofertas y publicarla (marketing, precios, publicación, certificación si es aplicable, etc.).

### Identidad y cuenta usadas
Se debe usar la lista de distribución o el grupo de seguridad por primera vez para iniciar sesión en el portal de publicación. Más adelante, se pueden agregar otros usuarios como coadministradores. Así es cómo se asigna a los datos de registro del Panel de vendedores.

## Portal de vista previa de Azure
[https://ms.portal.azure.com](https://ms.portal.azure.com)
### Descripción
Este es el portal donde puede ver sus ofertas publicadas y de ensayo en Azure Marketplace (aplicable a máquinas virtuales, plantillas de solución y servicios de desarrolladores basados en el Administrador de recursos de Azure).
### Identidad y cuenta usadas
Mientras pone en ensayo una oferta del portal de publicación, es preciso que se incluya un identificador de suscripción en la lista blanca. Para probar la oferta de ensayo, hay que usar la misma suscripción (no hay nombre de usuario ni contraseña asociados) al iniciar sesión en este portal.

## Consulte también
- [Introducción: Publicación de una oferta en Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO4-->