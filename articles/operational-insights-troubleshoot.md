<properties 
   pageTitle="Solución de problemas de Operational Insights"
   description="Conozca los procedimientos de solución de problemas de Operational Insights"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

#Solución de problemas de Operational Insights
Puede usar la información de las siguientes secciones para ayudarle a resolver problemas. Si el problema que tiene no se describe en este artículo, puede intentar el [blog del equipo de Operational Insights](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx).

## Diagnóstico de problemas de conexión en Operational Insights

Como Microsoft Azure Operational Insights se basa en los datos que se mueven a y desde la nube, los problemas de conexión pueden ser enormes. Use la siguiente información para comprender y resolver los problemas de conexión.



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Mensaje de error</b></td>
		<td><b>Causas posibles</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>La conectividad de Internet se ha verificado pero no se ha podido establecer una conexión al servicio de Operational Insights. Inténtelo de nuevo más tarde.|</td>
		<td>El servicio de Operational Insights está en proceso de mantenimiento. Espere a que el mantenimiento de Operational Insights finalice.<p>Su red ha bloqueado a Operational Insights. Póngase en contacto con su administrador de red y solicite acceso a Operational Insights, o use otro servidor como puerta de enlace.</td>
    </tr>
    <tr align="left" valign="top">
		<td>No se ha podido establecer una conexión a Internet. Compruebe la configuración de proxy.</td>
		<td>Este servidor no está conectado a Internet. Compruebe el estado de conectividad de Internet y conecte el servidor a Internet.<p>La configuración de proxy no es correcta. Consulte <A HREF="operational-insights-proxy-filewall.md">Configuración del proxy y el firewall</A> para obtener información sobre cómo configurar o cambiar la configuración del proxy.<p>El servidor proxy requiere autenticación. Consulte <A HREF="operational-insights-proxy-filewall.md">Configuración del proxy y el firewall</A> para obtener información sobre cómo configurar Operations Manager para usar un servidor proxy.</td>
    </tr>
    </tbody>
    </table>

## Solución de problemas de detección de SQL Server

Si está ejecutando Microsoft SQL Server 2008 R2, y a pesar de implementar el agente de Operations Manager no ve alertas para este servidor, puede que tenga un problema de detección.

Para confirmar si este es el origen de su problema, compruebe los dos siguientes problemas:

- En el registro de eventos de Operations Manager, se muestra el Id. de evento 4001. Este evento indica que hay una clase no válida.

- En el Administrador de configuración de SQL Server, cuando ve los servicios de SQL Server, se muestra el mensaje de error: "Error en la llamada a procedimiento remoto. [0x0800706be]"

Si ambos problemas se dan, deberá instalar SQL Server 2008 R2 Service Pack 2. Para descargar este Service Pack, consulte [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) en el Centro de descarga de Microsoft.

Después de instalar el Service Pack, debería ver datos de Operational Insights para el servidor al cabo de 24 horas.


<!--HONumber=52-->