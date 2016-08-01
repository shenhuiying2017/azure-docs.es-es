<properties
   pageTitle="Adición de un firewall de próxima generación en Azure Security Center | Microsoft Azure"
   description="En este documento se muestra cómo implementar la recomendación de Azure Security Center de agregar un firewall de próxima generación."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/15/2016"
   ms.author="terrylan"/>

# Adición de un firewall de próxima generación en Azure Security Center

Azure Security Center puede recomendarle agregar un firewall de próxima generación (NGFW) de un asociado de Microsoft para aumentar la protección. Este documento le ofrece un ejemplo de cómo hacerlo:

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure. En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Add a Next Generation Firewall** (Agregar un firewall de próxima generación). ![Adición de firewall de próxima generación][1]

2. En la hoja **Add a Next Generation Firewall** (Agregar un firewall de próxima generación), seleccione un punto de conexión. ![Selección de un punto de conexión][2]

3. Se abrirá una segunda hoja **Add a Next Generation Firewall** (Agregar un firewall de próxima generación). Puede elegir usar una solución existente, si está disponible, o puede crear una nueva. En este ejemplo no hay ninguna solución existente, por lo que vamos a crear un nuevo NGFW. ![Creación de un firewall de próxima generación][3]

4. Para crear un nuevo NGFW, seleccione una solución de la lista de asociados integrados. En este ejemplo se seleccionará **Punto de comprobación**. ![Selección de una solución de firewall de próxima generación][4]

5. Se abrirá la hoja **Punto de comprobación**, que le proporcionará información sobre la solución del asociado. Seleccione **Crear** en la hoja de información. ![Hoja de información del firewall][5]

6. Se abrirá la hoja **Crear máquina virtual**. Aquí puede escribir la información que se requiere para poner en marcha una máquina virtual que ejecutará el NGFW. Siga los pasos y proporcione la información de NGFW que sea necesaria. Seleccione Aceptar para que se aplique. ![Creación de una máquina virtual para ejecutar un NGFW][6]

## Pasos siguientes

En este documento se muestra cómo implementar la recomendación de Azure Security Center de agregar un firewall de próxima generación. Para obtener más información sobre los NGF y las soluciones de punto de comprobación de asociados, consulte los siguientes recursos:

- [Firewall de próxima generación](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que lo ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png

<!---HONumber=AcomDC_0720_2016-->