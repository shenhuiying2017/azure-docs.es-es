---
title: "Protección de datos personales con las características de Azure Network Security | Microsoft Docs"
description: "Protección de datos personales mediante las características de Azure Network Security"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: d61b29f1327f57bc32b2c53de3fe58e53fcf3cac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Protección de datos personales con las características de seguridad de red: Azure Application Gateway y los grupos de seguridad de red

Este artículo proporciona información y procedimientos que le ayudarán a usar Azure Application Gateway y los grupos de seguridad de red para proteger los datos personales.

Un elemento importante de una estrategia de seguridad de varias capas para proteger la privacidad de los datos personales es una defensa contra los ataques de vulnerabilidad comunes como la inyección de código SQL o el scripting entre sitios. Mantener el tráfico de red no deseado fuera de Azure Virtual Network ayuda a protegerse contra posibles riesgos que afectan a los datos confidenciales, y Microsoft Azure proporciona herramientas para ayudar a proteger los datos frente a los atacantes.

## <a name="scenario"></a>Escenario

Una gran empresa de cruceros, con sede en Estados Unidos, se encuentra en proceso de expansión de sus operaciones para ofrecer itinerarios en los mares Mediterráneo, Adriático y Báltico, así como en las Islas Británicas. Para apoyar esos esfuerzos, ha adquirido varias líneas de cruceros más pequeñas establecidas en Italia, Alemania, Dinamarca y Reino Unido.

La empresa usa Microsoft Azure para almacenar datos corporativos en la nube y ejecutar aplicaciones en máquinas virtuales que procesen y accedan a estos datos. Estos datos incluyen información de identificación personal como nombres, direcciones, números de teléfono e información de la tarjeta de crédito de su clientela global. También incluye información tradicional de recursos humanos como direcciones, números de teléfono, números de identificación fiscal y otra información sobre los empleados de la empresa de todas las ubicaciones. La línea de cruceros también conserva una gran base de datos de miembros del programa de recompensa y lealtad que incluye información personal para hacer un seguimiento de las relaciones con clientes actuales y antiguos.

Los empleados corporativos tienen acceso a la red desde las oficinas remotas de la empresa, mientras que los agentes de viajes repartidos por todo el mundo tienen acceso a algunos recursos de la empresa y usan las aplicaciones basadas en web hospedadas en las máquinas virtuales de Azure para interactuar con ella.

## <a name="problem-statement"></a>Declaración del problema

La empresa debe proteger la privacidad de los clientes y los datos personales de los empleados frente a atacantes que aprovechan las vulnerabilidades del software para ejecutar código malintencionado que podría poner en peligro los datos personales almacenados o los datos que utilizan las aplicaciones basadas en la nube de la compañía.

## <a name="company-goal"></a>Objetivo de la empresa

El objetivo de la compañía es asegurarse de que personas no autorizadas no puedan acceder a las instancias corporativas de Azure Virtual Network ni a las aplicaciones y datos que se encuentran allí aprovechando las vulnerabilidades más comunes. 

## <a name="solutions"></a>Soluciones

Microsoft Azure proporciona mecanismos de seguridad para ayudar a evitar la entrada de tráfico no deseado en las instancias de Azure Virtual Network. Los firewalls han realizado normalmente el control del tráfico entrante y saliente. En Azure, puede usar Application Gateway junto con el firewall de aplicaciones web y los grupos de seguridad de red (NSG), que actúan como un firewall distribuido simple. Estas herramientas le permiten detectar y bloquear el tráfico de red no deseado.

### <a name="application-gatewayweb-application-firewall"></a>Application Gateway y firewall de aplicaciones web

El componente [Firewall de aplicaciones web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF) de [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) permite proteger las aplicaciones web, que son cada vez más frecuentemente objeto de ataques malintencionados que aprovechan las vulnerabilidades conocidas más comunes. Un WAF centralizado protege frente a ataques web y simplifica la administración de seguridad sin necesidad de realizar ningún cambio en las aplicaciones.

El firewall de aplicaciones web de Azure permite hacer frente a distintas categorías de ataques tales como la inyección de código SQL, el scripting entre sitios, las infracciones de protocolos HTTP, y a las anomalías, bots, rastreadores, escáneres, errores de configuración de aplicaciones habituales, denegación del servicio HTTP y a otros ataques frecuentes como la inyección de comandos, contrabando de solicitudes HTTP, división de respuestas HTTP y ataques remotos de inclusión de archivos. 

Puede crear una puerta de enlace de aplicaciones con WAF o agregar WAF a una ya existente. En ambos casos, Azure Application Gateway requiere su propia subred.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Creación de una puerta de enlace de aplicaciones con WAF 

Para crear una nueva puerta de enlace de aplicaciones con WAF habilitado, haga lo siguiente:

1. Inicie sesión en Azure Portal y, en el panel **Favoritos** de este, haga clic en **Nuevo**

2. En la hoja **Nuevo**, haga clic en **Redes**.

3. Haga clic en **Application Gateway**.

4. Vaya a Azure Portal, **haga clic en Nuevo \> Redes \> Application Gateway**.

   ![creación de puertas de enlace de aplicaciones](media/protect-netsec/app-gateway-01.png)

5. En la hoja **Conceptos básicos** que aparece, escriba los valores de los campos siguientes: Nombre, Nivel (Estándar o WAF), Tamaño de SKU (pequeño, mediano o grande), Número de instancias (2 para lograr alta disponibilidad), Suscripción, Grupo de recursos y Ubicación.

6. En la hoja **Configuración** que aparece en **Red virtual**, haga clic en **Elegir una red virtual**. Este paso abre la hoja Elegir una red virtual.

7. Haga clic en **Crear nuevo** para abrir la hoja **Crear red virtual**.

8. Escriba los siguientes valores: Nombre, Espacio de direcciones, Nombre de subred, Intervalo de direcciones de subred. Haga clic en **Aceptar**.

9. En la hoja **Configuración** de **Configuración de IP de front-end**, seleccione el tipo de dirección IP.

10. Haga clic en **Elegir una dirección IP pública** y, a continuación, en **Crear nueva**.

11. Acepte el valor predeterminado y haga clic en **Aceptar**.

12. En la hoja **Configuración**, en **Configuración de agente de escucha**, elija utilizar HTTP o HTTPS en **Protocolo**. Para usar HTTPS, se requiere un certificado.

13. Configure los valores específicos de WAF: **Estado de firewall** (**Habilitado**) y **Modo de firewall** (**Prevención**). Si elige **Detección** como el modo, el tráfico solo se registra.

14. Revise la página **Resumen** y haga clic en **Aceptar**. La puerta de enlace de aplicaciones se pone en cola y se crea.

Una vez creada la puerta de enlace de aplicaciones, puede ir hasta ella en el portal para continuar con su configuración.

![puerta de enlace de aplicaciones creada](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>¿Cómo se agrega WAF a una aplicación existente?

Para actualizar una puerta de enlace de aplicaciones existente para que admita un WAF en modo de prevención, haga lo siguiente:

1. En el panel **Favoritos** de Azure Portal, haga clic en **Todos los recursos**.

2. Haga clic en la puerta de enlace de aplicaciones existente en la hoja **Todos los recursos**. 
>[!NOTE]
Nota: Si la suscripción que seleccionó ya tiene varios recursos en ella, puede escribir el nombre en Filtrar por nombre... para acceder fácilmente a la zona DNS.
3. Haga clic en **Firewall de aplicaciones web** y actualice la configuración de esta: **Actualizar al nivel WAF** (activada), **Estado de firewall** (habilitado), **Modo de firewall** (Prevención). También debe configurar el conjunto de reglas y las reglas deshabilitadas.

Para más información sobre cómo crear una nueva puerta de enlace de aplicaciones con WAF y cómo agregar WAF a una puerta de enlace de aplicaciones existente, consulte [Creación de una puerta de enlace de aplicaciones con el firewall de aplicaciones web mediante el portal](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal).

### <a name="network-security-groups"></a>Grupos de seguridad de red

Un [grupo de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) contiene una lista de reglas de seguridad que permiten o deniegan el tráfico de red a recursos conectados a instancias de [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/) (VNet). Los grupos de seguridad de red se pueden asociar a subredes o a máquinas virtuales individuales. Cuando un grupo de seguridad de red está asociado a una subred, las reglas se aplican a todos los recursos conectados a la subred. El tráfico se puede restringir aún más si se asocia también un grupo de seguridad de red a una máquina virtual o interfaz de red.

Los grupos de seguridad de red contienen cuatro propiedades: Nombre, Región, Grupo de recursos y Reglas.
>[!Note]
Aunque un grupo de seguridad de red existe en un grupo de recursos, puede estar asociado a recursos de cualquier grupo de recursos, siempre y cuando el recurso forme parte de la misma región de Azure que el grupo de seguridad de red.

Las reglas de los grupos de seguridad de red contienen nueve propiedades: Nombre, Protocolo (TCP, UDP o \*, que incluye ICMP así como UDP y TCP), Intervalo de puertos de origen, Intervalo de puertos de destino, Prefijo de dirección de origen, Prefijo de dirección de destino, Dirección (entrante o saliente), Prioridad (entre 100 y 4096) y Tipo de acceso (permitir o denegar). Todos los grupos de seguridad de red contienen un conjunto de reglas predeterminadas que se pueden eliminar, o reemplazar por las reglas que cree.

#### <a name="how-do-i-implement-nsgs"></a>¿Cómo se implementan los grupos de seguridad de red?

La implementación de los grupos de seguridad de red requiere planeación y hay varias consideraciones de diseño que debe tener en cuenta. Puede tratarse de límites en el número de grupos de seguridad de red por suscripción o de reglas por cada grupo, límites de diseño de red y subred virtual, de reglas especiales, de tráfico ICMP, de aislamiento de niveles con subredes, de equilibradores de carga y muchos más.

Para más instrucciones sobre cómo planear e implementar grupos de seguridad de red y un escenario de implementación de ejemplo, consulte [Filtrado del tráfico de red con grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Creación de reglas en un grupo de seguridad de red

Para crear reglas de entrada en un grupo de seguridad de red existente, haga lo siguiente:

1. Haga clic en **Examinar** y, a continuación, en **Grupos de seguridad de red**.

2. En la lista de grupos de seguridad de red, haga clic en **NSG-FrontEnd** y, a continuación, en **Reglas de seguridad de entrada**.

3. En la lista de reglas de seguridad de entrada, haga clic en **Agregar**.

4. Escriba los valores en los siguientes campos: Nombre, Prioridad, Origen, Protocolo, Intervalo de origen, Destino, Intervalo de puertos de destino y Acción.

Después de unos segundos, verá la nueva regla en el grupo de seguridad de red.

![reglas de seguridad de red](media/protect-netsec/inbound-security.png)

Para más instrucciones sobre cómo crear grupos de seguridad de red en subredes, crear reglas y asociar un grupo de seguridad de red con una subred de front-end y back-end, consulte [Creación de grupos de seguridad de red con Azure Portal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="next-steps"></a>Pasos siguientes

[Azure Network Security](https://azure.microsoft.com/blog/azure-network-security/)

[Procedimientos recomendados de Azure Network Security](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[Obtención de información acerca de un grupo de seguridad de red](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Firewall de aplicaciones web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)
