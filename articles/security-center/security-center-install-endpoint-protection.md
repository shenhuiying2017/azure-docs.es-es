<properties
   pageTitle="Instalación de Endpoint Protection en Azure Security Center | Microsoft Azure"
   description="En este documento, mostramos cómo implementar la recomendación de instalar Endpoint Protection de Azure Security Center."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# Instalación de Endpoint Protection en Azure Security Center

Azure Security Center recomendará que aprovisione un programa antimalware en sus máquinas virtuales de Azure si todavía no se ha habilitado. Esta recomendación se aplica únicamente a las máquinas virtuales de Windows.

> [AZURE.NOTE] En este documento se presenta el servicio mediante una implementación de ejemplo. No se trata de una guía paso a paso.

## Implementación de la recomendación

1. En la hoja **Recomendaciones**, seleccione **Instalar Endpoint Protection**. ![Selección de instalar Endpoint Protection][1]

2. Se abre la hoja **Instalar Endpoint Protection** con una lista de máquinas virtuales sin antimalware habilitado. Seleccione en la lista las máquinas virtuales en las que desea instalar el antimalware y haga clic en **Install on VMs** (Instalar en máquinas virtuales). ![Selección de las máquinas virtuales en donde instalar antimalware][2]

3. Se abre la hoja **Select Endpoint Protection** (Seleccionar Enpoint Protection) que le permite seleccionar la solución antimalware que desea usar. En este ejemplo, vamos a seleccionar **Microsoft Antimalware**. ![Selección de Endpoint Protection][3]

4. Aparece información adicional sobre la solución antimalware. Seleccione **Crear**. ![Creación de solución antimalware][4]

5. Escriba las opciones de configuración requeridas en la hoja **Agregar extensión** y seleccione **Aceptar**. Para más información acerca de las opciones de configuración, consulte [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../azure-security-antimalware.md#default-and-custom-antimalware-configuration) (Microsoft Antimalware para Servicios en la nube y Máquinas virtuales).

[Microsoft Antimalware](../azure-security-antimalware.md) ahora está activo en las máquinas virtuales seleccionadas.

## Consulte también

En este documento, mostramos cómo implementar la recomendación "Instalar Endpoint Protection" de Security Center. Para más información sobre cómo habilitar un programa antimalware en Azure, vea lo siguiente:

- [Microsoft Antimalware for Azure Cloud Services and Virtual Machines](../azure-security-antimalware.md) (Microsoft Antimalware para Servicios en la nube y Máquinas virtuales): aprenda a implementar Microsoft Antimalware.

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

- [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md): aprenda a configurar directivas de seguridad.
- [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
- [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
- [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
- [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-install-endpoint-protection/select-install-endpoint-protection.png
[2]: ./media/security-center-install-endpoint-protection/install-endpoint-protection-blade.png
[3]: ./media/security-center-install-endpoint-protection/select-endpoint-protection.png
[4]: ./media/security-center-install-endpoint-protection/create-antimalware-solution.png

<!---HONumber=AcomDC_0727_2016-->