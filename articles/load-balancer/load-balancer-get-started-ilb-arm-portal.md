<properties
   pageTitle="Primeros pasos en la creación de un equilibrador de carga interno en el Resource Manager mediante el Portal de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo crear un equilibrador de carga interno en Resource Manager mediante el Portal de Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# Primeros pasos en la creación de un equilibrador de carga interno en el Portal de Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


## Primeros pasos en la creación de un equilibrador de carga interno mediante el Portal de Azure

Para crear un equilibrador de carga interno desde el Portal de Azure, siga estos pasos.

1. Desde un explorador, vaya al [Portal de Azure](http://portal.azure.com) y, si fuera necesario, inicie sesión con su cuenta de Azure.
2. En la parte superior izquierda de la pantalla, haga clic en **Nuevo** > **Red** > **Equilibrador de carga**.
3. En la hoja **Cree un equilibrador de carga**, escriba un **nombre** para el equilibrador de carga.
4. En **Esquema**, haga clic en **Interno**.
5. Haga clic en **Red Virtual** y, a continuación, seleccione la red virtual donde desee crear el equilibrador de carga.

    >[AZURE.NOTE] Si no ve la red virtual que desea utilizar, compruebe la **ubicación** que utiliza para el equilibrador de carga y cámbiela según corresponda.

6. Haga clic en **Subred** y, a continuación, seleccione la subred en la que desee crear el equilibrador de carga.
7. En **Asignación de direcciones IP**, haga clic en **Dinámica** o **Estática**, dependiendo de si desea que la dirección IP del equilibrador de carga sea fija (estática) o no.

    >[AZURE.NOTE] Si selecciona usar una dirección IP estática, tendrá que proporcionar una dirección para el equilibrador de carga.

8. En **Grupo de recursos**, especifique el nombre de un grupo de recursos nuevo para el equilibrador de carga, o haga clic en **Seleccionar existente** y seleccione un grupo de recursos existente.
9. Haga clic en **Crear**.

## Configuración de las reglas de equilibrio de carga

Después de la creación del equilibrador de carga, vaya al recurso del equilibrador de carga para configurarlo. Tiene que configurar primero un grupo de direcciones back-end y un sondeo antes de configurar una regla de equilibrio de carga.

### Paso 1: Configuración de un grupo back-end

1. En el Portal de Azure, haga clic en **Examinar** > **Equilibradores de carga**, y, a continuación, haga clic en el equilibrador de carga que creó anteriormente.
2. En la hoja **Configuración**, haga clic en **Grupos back-end**.
3. En la hoja **Grupos de direcciones de back-end**, haga clic en **Agregar**.
4. En la hoja **Agregar grupo back-end**, escriba un **nombre** para el grupo back-end y, a continuación, haga clic en **Aceptar**.

### Paso 2: Configure un sondeo

1. En el Portal de Azure, haga clic en **Examinar** > **Equilibradores de carga**, y, a continuación, haga clic en el equilibrador de carga que creó anteriormente.
2. En la hoja **Configuración**, haga clic en **Sondeos**.
3. En la hoja **Sondeos**, haga clic en **Agregar**.
4. En la hoja **Agregar sondeo**, escriba un **nombre** para el sondeo.
5. En **Protocolo**, seleccione **HTTP** (para sitios web) o **TCP** (para otras aplicaciones basadas en TCP).
6. En **Puerto**, especifique el puerto que se utilizará al obtener acceso el sondeo.
7. En **Ruta** (solo para sondeos HTTP), especifique la ruta de acceso que usar como un sondeo.
8. En **Intervalo**, especifique la frecuencia de sondeo de la aplicación.
9. En **Umbral incorrecto**, especifique cuántos intentos fallidos deben producirse antes de que la VM back-end se marque como incorrecta.
10. Haga clic en **Aceptar** para crear el sondeo.

### Paso 3: Configuración de las reglas de equilibrio de carga

1. En el Portal de Azure, haga clic en **Examinar** > **Equilibradores de carga**, y, a continuación, haga clic en el equilibrador de carga que creó anteriormente.
2. En la hoja **Configuración**, haga clic en **Reglas de equilibrio de carga**.
3. En la hoja **Reglas de equilibrio de carga**, haga clic en **Agregar**.
4. En la hoja **Agregar regla de equilibrio de carga**, escriba un **nombre** para la regla.
5. En **Protocolo**, seleccione **HTTP** (para sitios web) o **TCP** (para otras aplicaciones basadas en TCP).
6. En **Puerto**, especifique los clientes de puertos que se conectan en el equilibrador de carga.
7. En **Puerto back-end**, especifique el puerto que se utilizará para el grupo back-end (normalmente, el puerto del equilibrador de carga y el puerto back-end son iguales).
8. En **Grupo back-end**, seleccione el grupo back-end que creó anteriormente.
9. En **Persistencia de la sesión**, seleccione cómo desea que se conserven las sesiones.
10. En **Tiempo de espera de inactividad (minutos)**, especifique el tiempo de espera de inactividad.
11. En **IP flotante (Direct Server Return)**, haga clic en **Deshabilitado** o **Habilitado**.
12. Haga clic en **Aceptar**.

## Pasos siguientes

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0914_2016-->