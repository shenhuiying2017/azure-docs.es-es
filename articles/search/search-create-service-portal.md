---
title: "Creación de un servicio Azure Search mediante Azure Portal | Microsoft Docs"
description: "Más información sobre cómo aprovisionar un servicio Búsqueda de Azure mediante el Portal de Azure."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 702ea254c19d1f9782f83d8445b7f440f11963b9
ms.openlocfilehash: 5046b3fc1444e37388505f2662a28486342c8822


---
# <a name="create-an-azure-search-service-using-the-azure-portal"></a>Creación de un servicio Búsqueda de Azure mediante el Portal de Azure
Este artículo le llevará por el proceso de crear (o aprovisionar) un servicio Azure Search con [Azure Portal](https://portal.azure.com/).

En esta artículo se supone que ya tiene una suscripción de Azure y puede iniciar sesión en Azure Portal.

## <a name="find-azure-search-in-the-azure-portal"></a>Encontrar Búsqueda de Azure en el Portal de Azure
1. Abra [Azure Portal](https://portal.azure.com/) e inicie sesión.
2. Haga clic en el signo más ("+") en la esquina superior izquierda.
3. Seleccione **Web + Móvil**.
4. Seleccione **Búsqueda de Azure**.

![](./media/search-create-service-portal/find-search.png)

## <a name="pick-a-service-name-and-url-endpoint-for-your-service"></a>Elegir un nombre de servicio y el punto de conexión de la dirección URL del servicio
1. El nombre del servicio será parte de la dirección URL del punto de conexión del servicio Búsqueda de Azure contra el que hará las llamadas de API para administrar y usar el servicio de búsqueda.
2. Escriba el nombre del servicio en el campo **Dirección URL** . El nombre del servicio debe cumplir los siguientes requisitos:
   * Solo debe contener letras minúsculas, números o guiones ("-").
   * No puede utilizar guiones ("-") en los dos primeros caracteres o en el último carácter.
   * No puede contener guiones consecutivos ("--").
   * Está limitada a una longitud de entre 2 y 60 caracteres.

## <a name="select-a-subscription-where-you-will-keep-your-service"></a>Seleccionar una suscripción donde mantendrá el servicio
Si tiene más de una suscripción, puede seleccionar cuál incluirá este servicio Búsqueda de Azure.

## <a name="select-a-resource-group-for-your-service"></a>Seleccionar un grupo de recursos para el servicio
Cree un nuevo grupo de recursos o seleccione uno existente. Un grupo de recursos es una colección de servicios y recursos de Azure que se usan juntos. Por ejemplo, si utiliza Búsqueda de Azure para indexar una base de datos SQL, ambos servicios deberían formar parte del mismo grupo de recursos.

## <a name="select-the-location-where-your-service-will-be-hosted"></a>Seleccionar la ubicación donde se hospedará el servicio
En cuanto servicio de Azure, Búsqueda de Azure está disponible para hospedarse en centros de datos de todo el mundo. Tenga en cuenta que los [precios pueden variar](https://azure.microsoft.com/pricing/details/search/) según la región geográfica.

## <a name="select-your-pricing-tier"></a>Seleccionar el plan de tarifa
[Búsqueda de Azure se ofrece actualmente en varios planes de tarifa](https://azure.microsoft.com/pricing/details/search/): Gratis, Básico y Estándar. Cada plan tiene su propia [capacidad y sus propios límites](search-limits-quotas-capacity.md). Consulte [Selección SKU o plan de tarifa](search-sku-tier.md) para obtener instrucciones.

En este caso, hemos elegido el nivel Estándar para nuestro servicio.

## <a name="select-the-create-button-to-provision-your-service"></a>Seleccionar el botón "Crear" para aprovisionar el servicio
![](./media/search-create-service-portal/create-service.png)

## <a name="scale-your-service"></a>Escalar el servicio
Después de aprovisionado el servicio, se puede escalar para satisfacer sus necesidades. Dado que ha elegido el nivel Estándar para el servicio Azure Search, puede escalar el servicio en dos dimensiones: réplicas y particiones. Si ha elegido el nivel Básico, solo puede agregar réplicas. Si ha aprovisionado el servicio gratuito, el escalado no está disponible.

Las ***particiones*** permiten que el servicio almacene y busque en más documentos.

Las ***réplicas*** permiten que el servicio gestione una carga más elevada de consultas de búsqueda: [un servicio requiere dos réplicas para lograr un SLA de solo lectura y tres réplicas para lograr un SLA de lectura y escritura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Vaya a la hoja de administración de su servicio Azure Search en Azure Portal.
2. En la hoja **Configuración**, seleccione **Escala**.
3. Puede escalar el servicio mediante la adición de particiones o réplicas.
   * Cada nivel de servicio tiene distintos [límites](search-limits-quotas-capacity.md) en el número total de unidades de búsqueda que se permiten en un único servicio (réplicas * particiones = total de unidades de búsqueda).

![](./media/search-create-service-portal/scale-service.png)

## <a name="next-steps"></a>Pasos siguientes
Después de aprovisionar un servicio Búsqueda de Azure, estará listo para [definir un índice de Búsqueda de Azure](search-what-is-an-index.md) para que pueda cargar y buscar los datos.

Consulte [Introducción a Búsqueda de Azure en el Portal](search-get-started-portal.md) para un tutorial rápido.




<!--HONumber=Jan17_HO2-->


