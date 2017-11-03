---
title: "Introducción a Administración de costos de Azure de Cloudyn | Microsoft Docs"
description: "Administración de costos de Azure de Cloudyn es una solución de administración de costos en varias nubes que le permitirá usar Azure y otros recursos en la nube."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 10/11/2017
ms.topic: overview
ms.service: cost-management
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 969340080bfe2b04704367c2225895728773119e
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2017
---
# <a name="what-is-azure-cost-management"></a>¿Qué es Administración de costos de Azure?

Administración de costos de Azure de Cloudyn permite realizar un seguimiento de los gastos y el uso de la nube para los recursos de Azure y otros proveedores de servicios en la nube, como AWS y Google. Los sencillos informes del panel proporcionan ayuda con la asignación de costos, los contracargos y la visibilidad de los gastos. Administración de costos le ayuda a optimizar los gastos en la nube mediante la identificación de los recursos que no está aprovechando para que pueda administrarlos y ajustarlos.

Para ver un vídeo de introducción, consulte [Introducción a Azure Cost Management de Cloudyn](https://youtu.be/NWIRny6Wpsk).

## <a name="monitor-usage-and-spending"></a>Supervisión del uso y del gasto

Supervisar el uso y el gasto es crucial para las infraestructuras en la nube porque las organizaciones pagan por los recursos que consumen a lo largo del tiempo. Cuando el uso supera los umbrales del contrato, pueden producirse gastos inesperados rápidamente. Algunos factores importantes pueden dificultar la supervisión ad hoc. En primer lugar, proyectar los costos según el uso medio da por supuesto que el consumo es coherente durante un determinado período de facturación. En segundo lugar, cuando los costos están a punto de superar el presupuesto, es importante recibir notificaciones de forma proactiva para ajustar los gastos. Además, es posible que los proveedores de servicios en la nube no puedan ofrecer informes de comparación entre proyección de costos y umbrales, o entre períodos.

Los informes ayudan a supervisar los gastos para analizar y realizar un seguimiento del uso de la nube, los costos y las tendencias. Los informes de uso a lo largo del tiempo permiten detectar anomalías que difieren de las tendencias normales. Los informes de optimización también permiten detectar las deficiencias en la implementación de la nube. También puede detectar deficiencias en informes de análisis de costos.

![Informe de costo a lo largo del tiempo](media\overview\cost-over-time-rpt.png)


## <a name="manage-costs"></a>Administrar costos

Los datos históricos pueden ayudar a administrar los costos al analizar el uso y los costos a lo largo del tiempo para identificar tendencias. Después, las tendencias se usan para hacer una previsión de los gastos futuros. Administración de costos también incluye informes útiles de costos previstos.

La asignación de costos administra los costos mediante el análisis basado en la directiva de etiquetado. Puede utilizar etiquetas en sus cuentas, recursos y entidades personalizadas para refinar la asignación de costos. El administrador de categorías organiza las etiquetas para proporcionarle control adicional. Así mismo, puede usar la asignación de costos para el proceso de visualización completa de los gastos y contracargo, y para mostrar el uso de recursos y los costos asociados para influir en los comportamientos de consumo o cobrar a los clientes del inquilino.

El control de acceso ayuda a administrar los costos al garantizar que los equipos y los usuarios solo tienen acceso a los datos de administración de costos que necesitan. Puede usar la estructura de la entidad, la administración de usuarios e informes programados con las listas de destinatarios para asignar acceso.

Las alertas ayudan a administrar los costos mediante la notificación automática cuando se producen gastos adicionales o inusuales. También pueden notificar automáticamente a otras partes interesadas sobre anomalías de gastos y riesgos de gastos adicionales. Existen varios informes que admiten alertas en función de los umbrales de costo y presupuesto. Sin embargo, las alertas no se admiten actualmente para las suscripciones o las cuentas de asociados de CSP.

## <a name="improve-efficiency"></a>Mejorar la eficiencia

Puede determinar el uso óptimo de las VM e identificar o eliminar VM inactivas, así como detectar discos sin conectar con Administración de costos. Con la información de los informes acerca de la optimización de tamaño y la falta de eficiencia, puede crear un plan para reducir o eliminar las VM inactivas. Sin embargo, los informes sobre optimización no se admiten actualmente para las suscripciones o las cuentas de asociados de CSP.

![recomendaciones de tamaño](.\media\overview\sizing.png)

Si aprovisionó instancias reservadas de AWS, puede mejorar el uso de las instancias reservadas con informes de optimización que le permiten consultar recomendaciones de compra, modificar reservas sin usar y planear el aprovisionamiento.

## <a name="next-steps"></a>Pasos siguientes

Ahora que está familiarizado con Administración de costos, el paso siguiente es registrar el entorno de nube y empezar a explorar los datos.

- [Registrar una suscripción individual de Azure](quick-register-azure-sub.md)
