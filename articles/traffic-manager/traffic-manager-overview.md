<properties 
   pageTitle="¿Qué es el Administrador de tráfico? | Microsoft Azure"
   description="Este artículo lo ayudará a entender qué es el Administrador de tráfico y si se trata de la opción de enrutamiento de tráfico adecuada para su aplicación."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/09/2016"
   ms.author="sewhee" />

# ¿Qué es el Administrador de tráfico?

El Administrador de tráfico de Microsoft Azure permite controlar la distribución del tráfico de los usuarios a los puntos de conexión de servicio que se ejecutan en distintos centros de datos de todo el mundo.

Entre los puntos de conexión de servicio compatibles con el Administrador de tráfico se incluyen aplicaciones web, servicios en la nube y máquinas virtuales de Azure. También puede utilizar el Administrador de tráfico con puntos de conexión externos, que no forman parte de Azure.

El Administrador de tráfico funciona utilizando el Sistema de nombres de dominio (DNS) para dirigir las solicitudes de los usuarios finales al punto de conexión más adecuado. Para ello, se basa en el método de enrutamiento de tráfico configurado y la vista actual del estado de los puntos de conexión. Posteriormente, los clientes se conectan directamente al punto de conexión de servicio apropiado.

El Administrador de tráfico admite diferentes [métodos de enrutamiento de tráfico de](traffic-manager-routing-methods.md) con el fin de adaptarse a los distintos requisitos de las aplicaciones. Además, este servicio proporciona [comprobaciones de estado y funcionalidades de conmutación por error automática de los puntos de conexión](traffic-manager-monitoring.md), de modo que podrá crear aplicaciones de alta disponibilidad que sean resistentes a errores, aunque afecten a toda una región de Azure.

## Ventajas del Administrador de tráfico

El Administrador de tráfico puede ayudarle a:

- **Mejorar la disponibilidad de las aplicaciones imprescindibles**: el Administrador de tráfico permite brindar una alta disponibilidad a las aplicaciones imprescindibles supervisando los puntos de conexión de Azure y proporcionado funcionalidades de conmutación por error automática cuando un punto de conexión deje de funcionar.
- **Mejorar la capacidad de respuesta de las aplicaciones de alto rendimiento**: Azure permite ejecutar servicios en la nube o sitios web en centros de datos de todo el mundo. El Administrador de tráfico puede mejorar la capacidad de respuesta de las aplicaciones dirigiendo a los usuarios finales al punto de conexión con la menor latencia de red del cliente.
- **Actualizar y realizar el mantenimiento del servicio sin que se produzcan tiempos de inactividad** : puede llevar a cabo perfectamente actualizaciones y otras operaciones de mantenimiento planeadas en las aplicaciones sin que los usuarios tengan que sufrir tiempos de inactividad. Puede lograr este objetivo utilizando el Administrador de tráfico para dirigir tráfico a puntos de conexión alternativos cuando haya en curso tareas de mantenimiento.
- **Combinar aplicaciones basadas en nube y locales** : el Administrador de tráfico admite puntos de conexión externos (que no forman parte de Azure) que pueden emplearse en implementaciones locales y de nube híbrida, entre ellos los escenarios de migración y conmutación por error a la nube.
- **Distribuir el tráfico en implementaciones complejas y de gran envergadura** : los métodos de enrutamiento de tráfico pueden combinarse mediante [perfiles anidados del Administrador de tráfico](traffic-manager-nested-profiles.md) para crear configuraciones de enrutamiento de tráfico flexibles y sofisticadas con el fin de satisfacer los requisitos de implementaciones más complejas y de mayor envergadura.

[AZURE.INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## Pasos siguientes

- Obtenga más información sobre [cómo funciona el Administrador de tráfico](traffic-manager-how-traffic-manager-works.md).

- Aprenda a desarrollar aplicaciones de alta disponibilidad mediante la [supervisión de puntos de conexión del Administrador de tráfico](traffic-manager-monitoring.md).

- Encuentre más información sobre los [métodos de enrutamiento de tráfico](traffic-manager-routing-methods.md) que admite el Administrador de tráfico.

- [Creación de un perfil del Administrador de tráfico](traffic-manager-manage-profiles.md).
 

<!---HONumber=AcomDC_0824_2016-->