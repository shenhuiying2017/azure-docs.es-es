<properties title="Load Balancing for Azure Infrastructure Services" pageTitle="Load Balancing for Azure Infrastructure Services" description="Describes the facilities to perform load balancing with Traffic Manager and load balancer." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="josephd" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="josephd"></tags>

# Equilibrio de carga para Servicios de infraestructura de Azure

Hay dos niveles de equilibrio de carga disponibles para Servicios de infraestructura de Azure:

-   **Nivel DNS**: equilibrio de carga para el tráfico a distintos servicios en la nube situados en centros de datos diferentes, a distintos sitios web de Azure situados en centros de datos diferentes o a extremos externos. Esto se realiza mediante el Administrador de tráfico y el método de equilibrio de carga Round Robin.
-   **Nivel de red**: equilibrio de carga del tráfico de Internet entrante a distintas máquinas virtuales de un servicio en la nube o equilibrio de carga del tráfico entre las máquinas virtuales de un servicio en la nube o una red virtual. Esto se realiza mediante el equilibrador de carga de Azure.

## Equilibrio de carga del Administrador de tráfico para servicios en la nube y sitios web

El Administrador de tráfico de Azure permite controlar la distribución del tráfico de usuarios en los extremos especificados, que pueden incluir servicios en la nube, sitios web, sitios externos y otros perfiles del Administrador de tráfico. El Administrador de tráfico aplica un motor de directivas inteligente a las consultas del Sistema de nombres de dominio (DNS) para los nombres de dominio de los recursos de Internet. Los servicios en la nube o los sitios web pueden ejecutarse en centros de datos diferentes de todo el mundo.

Debe usar REST o Windows PowerShell para configurar los extremos externos o los perfiles de Administrador de tráfico como extremos.

El Administrador de tráfico de Azure utiliza tres métodos de equilibrio de carga para distribuir el tráfico:

-   **Conmutación por error**: use este método cuando desee usar un extremo principal para todo el tráfico, pero proporcionar copias de seguridad en caso de que el extremo principal pierda disponibilidad.
-   **Rendimiento**: Utilice este método cuando tenga extremos en diferentes ubicaciones geográficas y desee solicitar a los clientes que usen el extremo “más cercano” en términos de la latencia más baja.
-   **Round robin:** use este método cuando desee distribuir la carga entre un conjunto de servicios en la nube en el mismo centro de datos o entre servicios en la nube o sitios en diferentes centros de datos.

Para obtener más información, consulte [Acerca de los métodos de equilibrio de carga del Administrador de tráfico][].

En la figura siguiente se muestra un ejemplo del método de equilibrio de carga Round Robin para la distribución del tráfico entre diferentes servicios en la nube.

![equilibrio de carga][]

El proceso básico es el siguiente:

1.  Un cliente de Internet requiere un nombre de dominio que corresponda a un servicio web.
2.  DNS reenvía la solicitud de consulta de nombre al Administrador de tráfico.
3.  El Administrador de tráfico devuelve el nombre DNS del servicio en la nube en la lista de Round Robin. El servidor DNS del cliente de Internet resuelve el nombre en una dirección IP y lo envía al cliente de Internet.
4.  El cliente de Internet se conecta con el servicio en la nube elegido.

Para obtener más información, consulte [Administrador de tráfico][].

## Equilibrio de carga de Azure para máquinas virtuales

Las máquinas virtuales que están en el mismo servicio en la nube o en la misma red virtual pueden comunicarse directamente entre sí mediante sus direcciones IP privadas. Los equipos y servicios fuera del servicio en la nube o la red virtual solo pueden comunicarse con las máquinas virtuales de un servicio en la nube o red virtual con un extremo configurado. Un extremo es una asignación de una dirección IP pública y un puerto a esa dirección IP privada y un puerto de una máquina virtual o rol web en un servicio en la nube de Azure.

El Equilibrador de carga de Azure distribuye de forma aleatoria un tipo específico de tráfico entrante entre varias máquinas virtuales o servicios en una configuración conocida como conjunto de carga equilibrada. Por ejemplo, puede extender la carga del tráfico de solicitudes web entre varios servidores web o roles web.

En la siguiente imagen se muestra un extremo con equilibrio de carga para tráfico web estándar (sin cifrar) compartido entre tres máquinas virtuales para el puerto TCP público y privado de 80. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![equilibrio de carga][1]

Para obtener más información, consulte [Equilibrador de carga de Azure][]. Para conocer los pasos para crear un conjunto de carga equilibrada, consulte [Configurar un conjunto de carga equilibrada][].

Azure también puede equilibrar la carga en un servicio en la nube o una red virtual. Esto se conoce como equilibrio de carga interno y se puede usar de las siguientes maneras:

-   Para equilibrar la carga entre servidores de distintos niveles de una aplicación multinivel (por ejemplo, entre los niveles web y de base de datos).
-   Equilibrio de carga para las aplicaciones de línea de negocio (LOB) hospedadas en Azure sin requerir hardware ni software adicional de equilibrador de carga.
-   Incluir servidores locales en el conjunto de equipos cuyo tráfico tiene equilibrio de carga.

Simular al equilibrio de carga de Azure, el equilibrio de carga interno se facilita mediante la configuración de un conjunto interno de carga equilibrada.

En la figura siguiente se muestra un ejemplo de un extremo con carga equilibrada de una aplicación de línea de negocios (LOB) que se comparte entre tres máquinas virtuales en una red virtual de varias instalaciones.

![equilibrio de carga][2]

Para obtener más información, consulte [Equilibrio de carga interno][]. Para conocer los pasos para crear un conjunto de carga equilibrada, consulte [Configurar un equilibrio de carga interno establecido][].

<!-- LINKS -->

  [Acerca de los métodos de equilibrio de carga del Administrador de tráfico]: http://msdn.microsoft.com/es-es/library/azure/dn339010.aspx
  [equilibrio de carga]: ./media/load-balancing-vms/TMSummary.png
  [Administrador de tráfico]: http://msdn.microsoft.com/es-es/library/azure/hh745750.aspx
  [1]: ./media/load-balancing-vms/LoadBalancing.png
  [Equilibrador de carga de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn655058.aspx
  [Configurar un conjunto de carga equilibrada]: http://msdn.microsoft.com/es-es/library/azure/dn655055.aspx
  [2]: ./media/load-balancing-vms/LOBServers.png
  [Equilibrio de carga interno]: http://msdn.microsoft.com/es-es/library/azure/dn690121.aspx
  [Configurar un equilibrio de carga interno establecido]: http://msdn.microsoft.com/es-es/library/azure/dn690125.aspx
