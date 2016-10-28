<properties
   pageTitle="Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal | Microsoft Azure"
   description="Más información acerca de la creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace" />

# Creación de un sondeo personalizado para Puerta de enlace de aplicaciones mediante el portal

> [AZURE.SELECTOR]
- [Portal de Azure](application-gateway-create-probe-portal.md)
- [PowerShell del Administrador de recursos de Azure](application-gateway-create-probe-ps.md)
- [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

<BR>

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## Escenario

El siguiente escenario pasa por la creación de un sondeo del estado personalizado en una puerta de enlace de aplicaciones existente. En este escenario se asume que ya ha seguido los pasos para [crear una puerta de enlace de aplicaciones](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Creación del sondeo

Los sondeos se configuran en un proceso de dos pasos a través del portal. El primer paso es crear el sondeo y luego hay que agregar dicho sondeo a la configuración de http del back-end de la puerta de enlace de aplicaciones.

### Paso 1

Navegue al http://portal.azure.com y seleccione una puerta de enlace de aplicaciones existente.

![Introducción a Puerta de enlace de aplicaciones][1]

### Paso 2

Haga clic en **Sondeos** y en el botón **Agregar** para agregar un sondeo nuevo.

![Agregar hoja Sondeo con la información rellena][2]

### Paso 3

Rellene la información necesaria para el sondeo y cuando finalice, haga clic en **Aceptar**.

- **Nombre**: es un nombre descriptivo para el sondeo al que se puede acceder en el portal.
- **Host**: es el nombre de host que se utiliza para el sondeo.
- **Ruta**: el resto de la dirección URL completa del sondeo personalizado.
- **Intervalo (s)**: la frecuencia con que se ejecuta el sondeo para comprobar el estado.
- **Tiempo de espera (s)**: el periodo que espera el sondeo antes de agotarse el tiempo de espera.
- **Umbral incorrecto**: número de intentos con error para que se considere incorrecto.

> [AZURE.IMPORTANT] El nombre de host no es el nombre del servidor. Es el nombre del host virtual que se ejecuta en el servidor de aplicaciones. El sondeo se envía a http://(nombre de host):(puerto de configuración http)/urlPath

![valores de configuración de sondeo][3]

## Adición de un sondeo a la puerta de enlace

Una vez creado el sondeo, es el momento de agregarlo a la puerta de enlace. La configuración del sondeo se establece en la configuración de http del back-end de la puerta de enlace de aplicaciones.

### Paso 1

Haga clic en la **configuración de HTTP** de la puerta de enlace de la aplicación y, luego, en la configuración de http del back-end actual en la ventana para abrir la hoja de configuración.

![ventana de configuración de https][4]

### Paso 2

En la hoja de configuración de **appGatewayBackEndHttp**, haga clic en **Usar sondeo personalizado** y elija el sondeo creado en la sección [Creación del sondeo](#createprobe). Cuando haya terminado, haga clic en **Aceptar** y se aplicará la configuración.

![hoja de configuración de appgatewaybackend][5]

El sondeo predeterminado comprueba el acceso predeterminado a la aplicación web. Una vez que se ha creado el sondeo personalizado, la puerta de enlace de aplicaciones usa la ruta de acceso personalizada definida para supervisar el estado del backend seleccionado. En función de los criterios definidos, la puerta de enlace de aplicaciones comprueba el archivo especificado en el sondeo. Si la llamada a host:Port/path no devuelve una respuesta de estado Http 200 OK, el servidor se saca de la rotación cuando se alcanza el umbral de estado incorrecto. El sondeo continúa en la instancia incorrecta para determinar cuándo vuelve a ser correcta. Una vez que la instancia se vuelve a agregar al grupo de servidores correcto, el tráfico comienza a fluir hacia ella de nuevo y el sondeo de la instancia continúa en el intervalo especificado por el usuario de forma normal.


## Pasos siguientes

Para aprender a configurar la descarga de SSL con Puerta de enlace de aplicaciones de Azure, consulte [Configure an application gateway for SSL offload by using the portal](application-gateway-ssl-portal.md) (Configuración de una puerta de enlace de aplicaciones para la descarga de SSL mediante el portal)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016-->