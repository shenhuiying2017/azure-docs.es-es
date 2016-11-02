<properties
  pageTitle="Uso de Azure DNS con otros servicios de Azure | Microsoft Azure"
  description="Descripción de cómo usar Azure DNS para resolver el nombre de otros servicios de Azure"
  services="dns"
  documentationCenter="na"
  authors="sdwheeler"
  manager="carmonm"
  editor=""
  tags="azure dns"
/>
<tags
  ms.service="dns"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="infrastructure-services"
  ms.date="09/21/2016"
  ms.author="sewhee"
/>


# <a name="using-azure-dns-with-other-azure-services"></a>Uso de Azure DNS con otros servicios de Azure

Azure DNS es un servicio de resolución de nombres y administración de DNS hospedado. Esto le permite crear nombres DNS públicos para las otras aplicaciones y servicios que tiene implementados en Azure. Crear el nombre para un servicio de Azure en el dominio personalizado es tan simple como agregar un registro del tipo correcto del servicio.

* Para las direcciones IP asignadas dinámicamente, debe crear un registro CNAME de DNS que se asigna al nombre DNS que Azure creó para el servicio. Los estándares DNS evitan que use un registro CNAME para el vértice de la zona.
* Para las direcciones IP asignadas de forma estática, puede crear un registro D de DNS con cualquier nombre, incluido un nombre de _dominio simple_ en el vértice de la zona.

En la tabla siguiente se describen los tipos de registro compatibles que se pueden usar para diversos servicios de Azure. Como puede ver en esta tabla, Azure DNS solo admite registros DNS para recursos de red orientados a Internet. Azure DNS no se puede usar para la resolución de nombres de direcciones privadas internas.

| Servicio de Azure | Interfaz de red | Description |
|---------------|-------------------|-------------|
| Puerta de enlace de aplicaciones | Dirección IP pública de front-end | Puede crear un registro D o CNAME de DNS. |
| Equilibrador de carga | Dirección IP pública de front-end | Puede crear un registro D o CNAME de DNS. Load Balancer puede tener una dirección IP pública IPv6 que se asigna dinámicamente. Por lo tanto, debe crear un registro CNAME para una dirección IPv6. |
| Administrador de tráfico | Nombre público | Solo puede crear un registro CNAME que se asigne al nombre de trafficmanager.net que está asignado a su perfil de Traffic Manager. Para más información, consulte [Cómo funciona Traffic Manager](../traffic-manager/traffic-manager-how-traffic-manager-works.md#traffic-manager-example). |
| Servicio en la nube | Dirección IP pública | Para las direcciones IP asignadas de forma estática, puede crear un registro D de DNS. Para las direcciones IP asignadas dinámicamente, debe crear un registro CNAME que se asigne al nombre de _cloudapp.net_ . Esta regla se aplica a las máquinas virtuales creadas en el portal clásico, porque están implementadas como servicio en la nube. Para más información, consulte [Configurar un nombre de dominio personalizado en Cloud Services](../cloud-services/cloud-services-custom-domain-name-portal.md). |
| Servicio de aplicaciones | Dirección IP externa | Para las direcciones IP externas, puede crear un registro D de DNS. De lo contrario, deberá crear un registro CNAME que se asigne al nombre de azurewebsites.net. Para más información, consulte [Asignación de un nombre de dominio personalizado a una aplicación de Azure](../app-service-web/web-sites-custom-domain-name.md) |
| Máquinas virtuales de Resource Manager | Dirección IP pública | Las máquinas virtuales de Resource Manager pueden tener direcciones IP públicas. Una máquina virtual con una dirección IP pública también puede encontrarse detrás de un equilibrador de carga. Puede crear un registro CNAME o D de DNS para la dirección pública. Este nombre personalizado se puede usar para omitir el VIP en el equilibrador de carga. |
| Máquinas virtuales clásicas | Dirección IP pública | Las máquinas virtuales clásicas creadas con PowerShell o la CLI se pueden configurar con una dirección virtual dinámica o estática (reservada). Puede crear un registro CNAME o D de DNS, respectivamente. |



<!--HONumber=Oct16_HO2-->


