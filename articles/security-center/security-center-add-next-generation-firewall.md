<properties
   pageTitle="Adición de un firewall de próxima generación en Azure Security Center | Microsoft Azure"
   description="En este documento se muestra cómo implementar la recomendación de Azure Security Center de agregar un firewall de próxima generación y enrutar el tráfico solo a través de NGFW."
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
   ms.date="07/26/2016"
   ms.author="terrylan"/>

# Adición de un firewall de próxima generación en Azure Security Center

Azure Security Center puede recomendarle agregar un firewall de próxima generación (NGFW) de un asociado de Microsoft para aumentar la protección. Este documento le ofrece un ejemplo de cómo hacerlo:

> [AZURE.NOTE] En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Add a Next Generation Firewall** (Agregar un firewall de próxima generación). ![Adición de firewall de próxima generación][1]

2. En la hoja **Add a Next Generation Firewall** (Agregar un firewall de próxima generación), seleccione un punto de conexión. ![Selección de un punto de conexión][2]

3. Se abrirá una segunda hoja **Add a Next Generation Firewall** (Agregar un firewall de próxima generación). Puede elegir usar una solución existente, si está disponible, o puede crear una nueva. En este ejemplo no hay ninguna solución existente, por lo que vamos a crear un nuevo NGFW. ![Creación de un firewall de próxima generación][3]

4. Para crear un nuevo NGFW, seleccione una solución de la lista de asociados integrados. En este ejemplo se seleccionará **Punto de comprobación**. ![Selección de una solución de firewall de próxima generación][4]

5. Se abrirá la hoja **Punto de comprobación**, que le proporcionará información sobre la solución del asociado. Seleccione **Crear** en la hoja de información. ![Hoja de información del firewall][5]

6. Se abrirá la hoja **Crear máquina virtual**. Aquí puede escribir la información que se requiere para poner en marcha una máquina virtual que ejecutará el NGFW. Siga los pasos y proporcione la información de NGFW que sea necesaria. Seleccione Aceptar para que se aplique. ![Creación de una máquina virtual para ejecutar un NGFW][6]

## Enrutar el tráfico solo a través de NGFW

Vuelva a la hoja **Recomendaciones**. Después de agregar un NGFW a través de Security Center, se genera una nueva entrada llamada **Enrutar el tráfico solo a través de NGFW**. Esta recomendación solo se crea si ha instalado el NGFW a través de Security Center. Si tiene puntos de conexión accesibles desde Internet, Security Center recomendará configurar reglas de grupos de seguridad de red que fuercen a que el tráfico entrante pase a la máquina virtual a través del NGFW.

1. En la hoja **Recomendaciones**, seleccione **Enrutar el tráfico solo a través de NGFW**. ![Enrutar el tráfico solo a través de NGFW][7]

2. Se abrirá la hoja **Enrutar el tráfico solo a través de NGFW**, que enumera las máquinas virtuales que puede enrutar el tráfico. Seleccione una máquina virtual de la lista. ![Seleccionar una máquina virtual][8]

3. Se abre una hoja de la máquina virtual seleccionada, que muestra las reglas de entrada relacionadas. Una descripción proporciona más información sobre los posibles pasos posteriores. Seleccione **Editar reglas de entrada** para continuar con la edición de una regla de entrada. El **origen** no debería estar establecido en **Cualquiera** para los puntos de conexión accesibles desde Internet vinculados al NGFW. Para obtener más información sobre las propiedades de la regla de entrada, consulte [Reglas de grupo de seguridad de red](../virtual-network/virtual-networks-nsg.md#nsg-rules). ![Configuración de reglas para limitar el acceso][9] ![Editar regla de entrada][10]

## Consulte también

En este documento se muestra cómo implementar la recomendación de Azure Security Center de agregar un firewall de próxima generación. Para obtener más información sobre los NGF y las soluciones de punto de comprobación de asociados, consulte los siguientes recursos:

- [Firewall de próxima generación](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
- [Check Point vSEC](https://azure.microsoft.com/marketplace/partners/checkpoint/check-point-r77-10/)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad.
- [Administración de recomendaciones de seguridad en el Centro de seguridad de Azure](security-center-recommendations.md): obtenga información sobre cómo las recomendaciones lo ayudan a proteger sus recursos de Azure.
- [Supervisión del estado de seguridad en Centro de seguridad de Azure](security-center-monitoring.md): obtenga información sobre cómo supervisar el estado de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes acerca del Centro de seguridad de Azure](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): Encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png

<!---HONumber=AcomDC_0727_2016-->