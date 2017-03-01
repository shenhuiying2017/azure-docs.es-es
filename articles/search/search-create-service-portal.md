---
title: "Creación de un servicio Azure Search en Azure Portal | Microsoft Docs"
description: Aprovisione un servicio Azure Search en Azure Portal.
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/16/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 4d7c0afe5e43e0c119f534cdedaaa03a16ed5303
ms.openlocfilehash: 0d5198c74fa81358dcdfec514e1fe422d17f368f
ms.lasthandoff: 02/17/2017


---
# <a name="create-an-azure-search-service-in-the-portal"></a>Creación de un servicio de Búsqueda de Azure en el portal

En este artículo se explica cómo crear o proporcionar un servicio Azure Search en Azure Portal. Para obtener instrucciones de PowerShell, consulte [Administración del servicio Azure Search con PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Suscripción (gratuita o de pago)

[Abra una cuenta gratuita de Azure ](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) y use créditos gratuitos para probar servicios de pago de Azure. Cuando se consuman los créditos, mantenga la cuenta y siga usando servicios de Azure gratuitos, como Websites. No se le realizará ningún cargo en su tarjeta de crédito a menos que cambie explícitamente la configuración y lo solicite.

Como alternativa, [active las ventajas de suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Una suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago. 

## <a name="find-azure-search"></a>Búsqueda de Azure Search
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Haga clic en el signo más ("+") en la esquina superior izquierda.
3. Seleccione **Web y móvil** > **Azure Search**.

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>Asignación de un nombre al servicio y al punto de conexión de dirección URL

Un nombre de servicio es parte del punto de conexión de dirección URL con la que se emiten llamadas a la API. Escriba el nombre del servicio en el campo **Dirección URL** . 

Requisitos de nombre de servicio:
   * Entre 2 y 60 caracteres de longitud.
   * Letras minúsculas, números o guiones ("-").
   * Sin guiones ("-") en los dos primeros caracteres o en el último carácter.
   * Sin guiones consecutivos ("--").

## <a name="select-a-subscription"></a>Selección de una suscripción
Si tiene más de una suscripción, elija una que también tenga servicios de almacenamiento de datos o archivos. Azure Search puede detectar automáticamente Azure Table y Blob Storage, SQL Database y DocumentDB para indexarlos mediante *indexadores*, pero solo para servicios de la misma suscripción.

## <a name="select-a-resource-group"></a>Selección de un grupo de recursos
Un grupo de recursos es una colección de servicios y recursos de Azure que se usan juntos. Por ejemplo, si usa Azure Search para indexar una base de datos SQL, ambos servicios deben formar parte del mismo grupo de recursos.

> [!TIP]
> Al eliminar un grupo de recursos también se eliminan los servicios que contiene. En el caso de proyectos de prototipo que usan muchos servicios, si se ponen todos ellos en el mismo grupo de recursos, la limpieza resulta más fácil después de que el proyecto ha finalizado. 

## <a name="select-a-hosting-location"></a>Selección de una ubicación de hospedaje 
En cuanto servicio de Azure, Azure Search se puede hospedar en centros de datos de todo el mundo. Tenga en cuenta que los [precios pueden variar](https://azure.microsoft.com/pricing/details/search/) según la región geográfica.

## <a name="select-a-pricing-tier-sku"></a>Selección de un plan de tarifa (SKU)
[Búsqueda de Azure se ofrece actualmente en varios planes de tarifa](https://azure.microsoft.com/pricing/details/search/): Gratis, Básico y Estándar. Cada plan tiene su propia [capacidad y sus propios límites](search-limits-quotas-capacity.md). Consulte [Selección SKU o plan de tarifa](search-sku-tier.md) para obtener instrucciones.

En este tutorial, hemos elegido el nivel Estándar para nuestro servicio.

## <a name="create-your-service"></a>Creación del servicio

No olvide anclar su servicio en el panel para facilitar el acceso siempre que inicie sesión.

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>Escalar el servicio
La creación de un servicio puede llevarle unos minutos (15 o más, en función del nivel). Después de aprovisionado el servicio, se puede escalar para satisfacer sus necesidades. Dado que ha elegido el nivel Estándar para el servicio Azure Search, puede escalar el servicio en dos dimensiones: réplicas y particiones. Si ha elegido el nivel Básico, solo puede agregar réplicas. Si ha aprovisionado el servicio gratuito, el escalado no está disponible.

Las ***particiones*** permiten que el servicio almacene y busque en más documentos.

Las ***réplicas*** permiten al servicio administrar una carga más elevada de consultas de búsqueda.

> [!Important]
> Un servicio debe tener [2 réplicas para SLA de solo lectura y 3 réplicas para SLA de lectura y escritura](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Vaya a la hoja del servicio de búsqueda en Azure Portal.
2. En el panel de navegación de la izquierda, seleccione **Configuración** > **Escala**.
3. Use la barra deslizante para agregar réplicas o particiones.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Cada nivel tiene distintos [límites](search-limits-quotas-capacity.md) en el número total de unidades de búsqueda que se permiten en un único servicio (réplicas * particiones = total de unidades de búsqueda).

## <a name="next-steps"></a>Pasos siguientes
Después de aprovisionar un servicio Azure Search, está listo para [definir un índice](search-what-is-an-index.md) para que pueda cargar y buscar los datos.

Para acceder al servicio mediante código o script, proporcione la dirección URL (*nombre_servicio*.search.windows.net) y una clave. Las claves de administrador conceden acceso completo; las claves de consulta conceden acceso de solo lectura. Consulte [Cómo usar Azure Search en .NET](search-howto-dotnet-sdk.md) para comenzar.

Consulte [Compilación y consulta del primer índice](search-get-started-portal.md) para obtener un tutorial rápido basado en Azure Portal.


