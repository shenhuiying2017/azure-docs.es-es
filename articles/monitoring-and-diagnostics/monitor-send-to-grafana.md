---
title: Supervisar los servicios y aplicaciones de Azure con Grafana | Microsoft Docs
description: Enrute los datos de Azure Monitor y Application Insights para que puedan verse en Grafana.
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: c189e67c481239a8a68f2e2b30d05bb615cfa24e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>Supervisar los servicios de Azure en Grafana
Ahora, los servicios y las aplicaciones de Azure también se pueden supervisar desde [Grafana](https://grafana.com/) gracias al [complemento de origen de datos de Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). Este complemento recopila los datos de rendimiento de la aplicación recabados por el SDK de Application Insights, así como los datos de infraestructura proporcionados por Azure Monitor. De este modo, podrá ver estos datos en el panel de Grafana.

Actualmente, el complemento se encuentra en versión preliminar.

Haga lo siguiente para configurar un servidor de Grafana desde Azure Marketplace y confeccionar paneles para las métricas de Azure Monitor y Application Insights.

## <a name="set-up-a-grafana-instance"></a>Configurar una instancia de Grafana
1. Vaya a Azure Marketplace y seleccione Grafana de Grafana Labs.

2. Rellene los nombres y los detalles pertinentes. Cree un nuevo grupo de recursos. Lleve un seguimiento de los valores de nombre de usuario de máquina virtual, contraseña de máquina virtual y contraseña de administrador del servidor de Grafana que haya elegido.  

3. Elija un tamaño de máquina virtual y una cuenta de almacenamiento.

4. Configure la red.

5. Vea el resumen y seleccione **Crear** después de aceptar los términos de uso.

## <a name="log-in-to-grafana"></a>Iniciar sesión en Grafana
1. Una vez finalizada la implementación, seleccione **Ir al grupo de recursos**. Verá una lista de los recursos recién creados. 

    ![Objetos del grupo de recursos de Grafana](.\media\monitor-how-to-grafana\grafana1.png) 

    Si selecciona el grupo de seguridad de red (*grafana-nsg* en este caso), verá que se usa el puerto 3000 para tener acceso al servidor Grafana. 

2. Vuelva a la lista de recursos y seleccione **Dirección IP pública**. Usando los valores que aparecen en esta pantalla, escriba *http://<IP address>: 3000* o *<DNSName>:3000* en el explorador. Debería aparecer una página de inicio de sesión del servidor de Grafana que acaba de crear.
    
    ![Pantalla de inicio de sesión de Grafana](.\media\monitor-how-to-grafana\grafana2.png) 

3. Inicie sesión con el nombre de usuario *admin* y la contraseña de administrador del servidor de Grafana que creó anteriormente. 

## <a name="configure-data-source-plugin"></a>Configurar el complemento de origen de datos

Tras haber iniciado sesión correctamente, deberá ver que el complemento de origen de datos de Azure Monitor ya aparece incluido.

![Grafana muestra el complemento de Azure Monitor](.\media\monitor-how-to-grafana\grafana3.png) 

1. Seleccione **Agregar origen de datos** para configurar Azure Monitor y Application Insights. 
    
2. Elija un nombre para el origen de datos y seleccione **Azure Monitor** como origen de datos en la lista desplegable.
    
    
## <a name="create-a-service-principal"></a>Creación de una entidad de servicio 

Grafana usa una entidad de servicio de Azure Active Directory para conectarse a las API de Azure Monitor y recopilar datos de métricas. Debe crear una entidad de servicio para administrar el acceso a los recursos de Azure.

1. Vea [estas instrucciones](../azure-resource-manager/resource-group-create-service-principal-portal.md) para crear una entidad de servicio. Copie y guarde el identificador de inquilino, el identificador de cliente y un secreto de cliente.

2. Vea [Asignación de aplicación a un rol](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) para asignar el rol de lector a la aplicación Azure Active Directory.     

3. Si usa Application Insights, también puede incluir la API de Application Insights y el identificador de la aplicación para recopilar métricas basadas en Application Insights. Para más información, vea [Getting your API key and Application ID](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID) (Obtener la clave de API y el identificador de aplicación).

4. Tras haber especificado toda de esta información, seleccione **Guardar**. Grafana comprobará la API. Debería aparecer un mensaje similar al siguiente.  

    ![Grafana muestra el complemento de Azure Monitor](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>Crear un panel de Grafana

1. Vaya a la página principal y seleccione **Nuevo panel**.

2. En el nuevo panel, seleccione el **gráfico**. Puede probar con otras opciones de gráfico, pero en este artículo usaremos *Gráfico* como ejemplo. 

    ![Nuevo panel de Grafana](.\media\monitor-how-to-grafana\grafana5.png) 

3. Se abre un gráfico en blanco en el panel. 

4. Haga clic en el título del panel y seleccione **Editar** para especificar los detalles de los datos que quiere trazar en este gráfico.
    
5. Cuando haya seleccionado todas las máquinas virtuales que proceda, puede empezar a ver las métricas en el panel. 

Este es un sencillo panel con dos gráficos. El de la izquierda muestra el porcentaje de CPU de dos máquinas virtuales. El de la derecha muestra las transacciones de una cuenta de almacenamiento de Azure desglosadas por el tipo de la API de transacción.
    
![Ejemplo de dos gráficos en Grafana](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>Opcional: Crear listas de reproducción de panel

Una de las muchas características útiles de Grafana es la lista de reproducción de panel. Puede crear varios paneles y agregarlos a una lista de reproducción, configurando un intervalo para que cada panel se muestre. Seleccione **Reproducir** para ir viendo los paneles uno a uno. Puede que quiera que se muestren en un monitor de pared de grandes dimensiones a modo de "panel de estado" del grupo. 
    
![Ejemplo de lista de reproducción de Grafana](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Supervisar las métricas personalizadas en el mismo servidor de Grafana

También puede instalar Telegraf e InfluxDB para recopilar y trazar métricas tanto basadas en agente como personalizadas en la misma instancia de Grafana. Existe un amplio abanico de complementos de origen de datos que se pueden usar para reunir estas métricas en un panel. 
    
También puede reutilizar esta configuración para incluir métricas desde su servidor de Prometheus. Use el complemento de origen de datos de Prometheus de la galería de complementos de Grafana.
    
Aquí tiene algunos artículos de referencia interesantes sobre cómo usar Telegraf, InfluxDB, Prometheus y Docker:
 - [How To Monitor System Metrics with the TICK Stack on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04) (Cómo supervisar métricas de sistema con la pila TICK en Ubuntu 16.04)

 - [Monitor Docker resource metrics with Grafana, InfluxDB, and Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/) (Supervisar métricas de recursos de Docker con Grafana, InfluxDB y Telegraf)

 - [A monitoring solution for Docker hosts, containers, and containerized services](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/) (Solución de supervisión para hosts, contenedores y servicios en contenedores de Docker)

Esta es una imagen de un panel de Grafana completo con métricas de Azure Monitor y Application Insights.
![Métricas de ejemplo de Grafana](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>Limpieza de recursos

Incurrirá en cargos cuando las máquinas virtuales se ejecuten, independientemente de si las usa o no. Para evitar estos cargos extra, limpie el grupo de recursos creado en este artículo. 

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en **Grafana**. 
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba **Grafana** en el cuadro de texto y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes
* [Información general sobre las métricas en Microsoft Azure](monitoring-overview-metrics.md)


