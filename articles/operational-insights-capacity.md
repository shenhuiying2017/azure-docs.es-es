<properties 
   pageTitle="Administración de la capacidad de la infraestructura"
   description="Aprenda a usar el paquete de inteligencia de planeamiento de capacidad en Visión operativa para entender la capacidad de su infraestructura de servidores"
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

# Administración de la capacidad de la infraestructura

Puede usar el paquete de inteligencia de planeamiento de capacidad en Visión operativa de Microsoft Azure para entender la capacidad de su infraestructura de servidores. Instale el paquete de inteligencia para actualizar el agente Operations Manager y el módulo de configuración básica en Visión operativa. El paquete de inteligencia lee los contadores de rendimiento en el servidor supervisado y envía datos de uso al servicio de Visión operativa en la nube para su procesamiento. La lógica se aplica a los datos usados y el servicio en la nube registra los datos. Con el tiempo, se identifican los patrones de uso y se proyecta la capacidad según el consumo actual

Por ejemplo, una proyección puede identificar cuándo serán necesarios núcleos de procesador adicionales o una memoria adicional para un servidor individual. En este ejemplo, la proyección podría indicar que en 30 días, el servidor necesitará memoria adicional. Esto puede ayudarle a planear una actualización de memoria durante la próxima ventana de mantenimiento del servidor, algo que puede ocurrir una vez cada dos semanas.

## Panel Administración de capacidad

Para poder usar el panel Administración de capacidad en Visión operativa de Microsoft Azure, debe tener instalado el paquete de inteligencia. Para obtener más información sobre cómo instalar los paquetes de inteligencia, consulte [Uso de la Galería para agregar o eliminar paquetes de inteligencia](operational-insights-add-intelligence-packs.md). Después de instalar el paquete de inteligencia de planeamiento de capacidad, puede ver la capacidad de los servidores supervisados mediante el icono **Planeamiento de capacidad** de la página **Información general** de Visión operativa. 

![imagen del icono Planeamiento de capacidad](./media/operational-insights-capacity/overview-cap-plan.png)

El icono abre el panel **Administración de capacidad**, donde puede ver un resumen de la capacidad del servidor. La página muestra los iconos siguientes en los que puede hacer clic:

- *Recuento de máquinas virtuales*: muestra el número de días que quedan para la capacidad de las máquinas virtuales.

- *Proceso*: muestra los núcleos de procesador y la memoria disponibles.

- *Almacenamiento*: muestra el espacio en disco utilizado y el promedio de latencia del disco.

- *Search*: el explorador de datos que puede usar para buscar cualquier dato en el sistema de Visión operativa.

>[AZURE.NOTE] Para ver los datos de administración de capacidad, debe habilitar la conectividad de Operations Manager con Virtual Machine Manager (VMM). Para obtener información adicional acerca de cómo conectar los sistemas, consulte la información sobre cómo conectar VMM con Operations Manager.

![imagen del panel Administración de capacidad](./media/operational-insights-capacity/gallery-capacity-01.png)

### Visualización de una página de capacidad

- En la página **Información general**, haga clic en **Administración de capacidad** y, a continuación, haga clic en **Proceso** o **Almacenamiento**.

## Página Proceso

Puede utilizar el panel **Proceso** en Visión operativa de Microsoft Azure para ver la información de capacidad sobre la utilización, los días proyectados de capacidad y la eficacia relacionada con la infraestructura. El área **Uso** permite ver el uso de la memoria y los núcleos de CPU en los hosts de máquina virtual. Puede usar la herramienta de proyección para calcular cuánta capacidad se espera que esté disponible para un intervalo de fechas determinado. Puede usar el área **Eficacia** para ver lo eficaces que son sus hosts de máquina virtual. Puede ver detalles sobre los elementos vinculados haciendo clic en ellos.

Puede generar un libro de Excel para las siguientes categorías:

- Hosts principales con mayor uso de núcleo

- Hosts principales con mayor uso de memoria

- Hosts principales con máquinas virtuales ineficaces

- Hosts principales por uso

- Últimos hosts según la utilización

![imagen de la página de procesos de Administración de capacidad](./media/operational-insights-capacity/gallery-capacity-02.png)


Las áreas siguientes se muestran en el panel **Proceso**:

**Uso**: permite ver el uso de la memoria y los núcleos de CPU en los hosts de máquina virtual.

- *Núcleos usados*: suma de todos los hosts (% de CPU utilizado multiplicado por el número de núcleos físicos en el host).

- *Núcleos libres*: núcleos físicos totales menos los núcleos usados.

- *Porcentaje de núcleos disponibles*: número de núcleos físicos libres dividido entre el número total de núcleos físicos.

- *Núcleos virtuales por cada máquina virtual*: número total de núcleos virtuales en el sistema dividido entre el número total de máquinas virtuales del sistema.

- *Relación entre los núcleos virtuales y los físicos*: relación entre la cantidad total de núcleos físicos y los núcleos físicos que utilizan las máquinas virtuales en el sistema.

- *Número de núcleos virtuales disponibles*: relación entre el núcleo virtual y los núcleos físicos multiplicado por los núcleos físicos disponibles.

- *Memoria usada*: suma de la memoria que utilizan todos los hosts.

- *Memoria libre*: memoria física total menos la memoria usada.

- *Porcentaje de memoria disponible*: memoria física libre dividida entre la memoria física total.

- *Memoria virtual por cada máquina virtual*: memoria virtual total en el sistema dividida entre el número total de máquinas virtuales del sistema.

- *Relación entre la memoria virtual y la memoria física*: total de memoria virtual en el sistema dividido entre la memoria física total del sistema.

- *Memoria virtual disponible*: relación entre la memoria virtual y la memoria física multiplicado por la memoria física disponible.

**Herramienta de proyección**

Mediante la herramienta de proyección, puede ver las tendencias históricas del uso de los recursos. Se incluyen las tendencias de uso de las máquinas virtuales, la memoria, los núcleos y el almacenamiento. La capacidad de proyección utiliza un algoritmo de proyección que permite saber cuándo se están agotando los distintos recursos. Esto permite calcular el planeamiento de capacidad correcto para que pueda saber si debe adquirir más capacidad (por ejemplo, memoria, núcleos o almacenamiento).

**Eficacia**

- *Máquinas virtuales inactivas*: aquellas que usan menos del 10 % de la CPU y de la memoria durante el período de tiempo especificado.

- *VM sobreutilizadas*: aquellas que usan más del 90 % de la CPU y de la memoria durante el período de tiempo especificado.

- *Hosts inactivos*: aquellas que usan menos del 10 % de la CPU y de la memoria durante el período de tiempo especificado.

- *Hosts sobreutilizados*: aquellas que usan más del 90 % de la CPU y de la memoria durante el período de tiempo especificado.

### Uso de los elementos de la página Proceso

1. En el panel **Proceso**, en el área **Uso**, puede ver información acerca de los núcleos de CPU y la memoria en uso.

2. Haga clic en un elemento para abrirlo en la página **Buscar** y ver información detallada al respecto.

3. En la herramienta **Proyección**, mueva el control deslizante de fecha para mostrar una proyección de la capacidad que se usará en la fecha seleccionada.

3. En el área **Eficacia**, puede ver la información sobre la eficacia de la capacidad acerca de las máquinas virtuales y los hosts de máquina virtual.

##Página Almacenamiento con conexión directa

Puede utilizar el panel **Almacenamiento con conexión directa** de Visión operativa de Microsoft Azure para ver información de capacidad sobre la utilización del almacenamiento, el rendimiento del disco y los días proyectados de capacidad de disco. El área **Uso** permite ver el uso del espacio de disco en los hosts de máquina virtual. Puede utilizar el área **Rendimiento del disco** para ver el rendimiento del disco y la latencia de los hosts de máquina virtual. También puede usar la herramienta de proyección para calcular cuánta capacidad se espera que esté disponible para un intervalo de fechas determinado. Puede ver detalles sobre los elementos vinculados haciendo clic en ellos.

Puede generar un libro de Excel a partir de esta información de capacidad para las siguientes categorías:

- Mayor uso de espacio de disco por host

- Mayor promedio de latencia por host

Las áreas siguientes se muestran en la página **Almacenamiento**:

- *Uso*: conozca el uso del espacio de disco en los hosts de máquina virtual.

- *Espacio total en disco*: suma (espacio de disco lógico) para todos los hosts.

- *Espacio de disco usado*: suma (espacio de disco lógico usado) para todos los hosts.

- *Espacio disponible en disco*: espacio en disco total menos el espacio en disco utilizado.

- *Porcentaje de disco utilizado*: espacio en disco utilizado dividido entre el espacio total en disco.

- *Porcentaje de disco disponible*: espacio en disco disponible dividido entre el espacio total en disco.

![imagen de la página Administración de capacidad del almacenamiento con conexión directa](./media/operational-insights-capacity/gallery-capacity-03.png)

**Rendimiento de disco**

Mediante el uso de Visión operativa, puede ver las tendencias históricas de uso del espacio del disco. La capacidad de proyección utiliza un algoritmo para proyectar el uso futuro. Sobre el uso del espacio en particular, la capacidad de proyección permite proyectar cuándo puede quedarse sin espacio de disco. Esto permite planear el almacenamiento adecuado y saber cuándo necesita adquirir más almacenamiento.

**Herramienta de proyección**

Mediante la herramienta de proyección, puede ver las tendencias históricas del uso del espacio de disco. También permite proyectar cuándo se quedará sin espacio de disco. Esto permite planear la capacidad adecuada y saber cuándo necesita adquirir más capacidad de almacenamiento.

### Uso de los elementos de la página Almacenamiento con conexión directa

1. En el panel **Almacenamiento con conexión directa**, en el área **Uso**, puede ver la información sobre el uso del disco.

2. Haga clic en un elemento vinculado para abrirlo en la página **Buscar** con objeto de ver información detallada al respecto.

3. En el área **Rendimiento del disco**, puede ver la información sobre el rendimiento y la latencia del disco.

4. En la herramienta **Proyección**, mueva el control deslizante de fecha para mostrar una proyección de la capacidad que se usará en la fecha seleccionada.


<!--HONumber=52-->