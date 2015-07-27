<properties 
 pageTitle="Tamaños de los servicios en la nube" 
 description="Enumera los diferentes tamaños para los roles web y de trabajo del servicio en la nube de Azure." 
 services="cloud-services" 
 documentationCenter="" 
 authors="Thraka" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="cloud-services" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="na" 
 ms.workload="tbd"
 ms.date="06/04/2015" 
 ms.author="adegeo"/>
 
# Tamaños de los servicios en la nube

En este tema se describen las opciones y los tamaños disponibles para las instancias de rol de servicio en la nube (roles web y roles de trabajo). También ofrece consideraciones de implementación que hay que tener en cuenta a la hora de planear usar estos recursos.

Las máquinas virtuales y servicios en la nube de Azure son dos de los distintos tipos de recursos de proceso que ofrece Azure. Para obtener más explicaciones, consulte [Cálculo de las opciones de hospedaje proporcionadas por Azure](fundamentals-application-models.md).

> [AZURE.NOTE]Para ver límites relacionados de Azure, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md)

## Tamaños de instancias de roles web y de trabajo

Las consideraciones siguientes pueden ayudarle a decidirse por un tamaño:

* Las instancias se pueden configurar ahora para usar una máquina virtual de la serie D. Están diseñadas para ejecutar aplicaciones que exigen una mayor capacidad de proceso y un rendimiento de disco temporal. Las máquinas virtuales de la serie D ofrecen procesadores más rápidos, una mayor proporción de memoria al núcleo y una unidad de estado sólido (SSD) para el disco temporal. Para obtener más información, vea el anuncio en el blog de Azure, [Nuevos tamaños de máquina virtual de la serie D](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).  

* Los roles web y los roles de trabajo requieren más espacio en el disco temporal que las máquinas virtuales de Azure debido a los requisitos del sistema. Los archivos del sistema reservan 4 GB de espacio para el archivo de paginación de Windows y 2 GB de espacio para el archivo de volcado de memoria de Windows.

* El disco del sistema operativo contiene el sistema operativo invitado de Windows e incluye la carpeta Archivos de programa (incluidas las instalaciones realizadas a través de tareas de inicio a menos que especifique otro disco), cambios del Registro, la carpeta System32 y .NET Framework.

* El disco de recursos local contiene registros de Azure y archivos de configuración, Diagnósticos de Azure (que incluye sus registros de IIS) y cualquier recurso de almacenamiento local que defina.

* El disco de aplicaciones es donde se extrae su .cspkg e incluye su sitio web, los archivos binarios, el proceso de host de rol, las tareas de inicio, web.config, etc.

* Los tamaños de máquina virtual A8/A10 y A9/A11 tienen las mismas capacidades. Las instancias de máquina virtual A8 y A9 incluyen otro adaptador de red que se conecta a una red de acceso de memoria directa remota (RDMA) para una rápida comunicación entre máquinas virtuales. Las instancias A8 y A9 están diseñadas para aplicaciones informáticas de alto rendimiento que requieren una comunicación constante y de baja latencia entre nodos durante la ejecución, por ejemplo, aplicaciones que utilizan la interfaz de paso de mensajes (MPI). Las instancias de máquina virtual A10 y A11 no incluyen el adaptador de red adicional. Las instancias A10 y A11 están diseñadas para aplicaciones informáticas de alto rendimiento que no requieren la comunicación constante y de baja latencia entre nodos, también conocidas como aplicaciones paramétricas o embarazosamente paralelas.

|Tamaño|Núcleos<br>de CPU|Memoria|Tamaños de disco|
|---|---|---|---|
|ExtraSmall|1|768 MB|SO = tamaño del SO invitado<br/>Recurso local = 19 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Pequeña|1|1,75 GB|SO = tamaño del SO invitado<br/>Recurso local = 224 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Mediano|2|3,5 GB|SO = tamaño del SO invitado<br/>Recurso local = 489 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Grande|4|7 GB|SO = tamaño del SO invitado<br/>Recurso local = 999 GB<br/>Aplicaciones = aprox. 1,5 GB|
|ExtraLarge|8|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 2.039 GB<br/>Aplicaciones = aprox. 1,5 GB|
|A5|2|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 489 GB<br/>Aplicaciones = aprox. 1,5 GB|
|A6|4|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 999 GB<br/>Aplicaciones = aprox. 1,5 GB|
|A7|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 2.039 GB<br/>Aplicaciones = aprox. 1,5 GB
|A8|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 1,77 TB<br/>Aplicaciones = aprox. 1.5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|A9|16|112 GB|SO = tamaño del SO invitado<br/>Recurso local = 1,77 TB<br/>Aplicaciones = aprox. 1.5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|A10|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 1,77 TB<br/>Aplicaciones = aprox. 1.5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|A11|16|112 GB|SO = tamaño del SO invitado<br/>Recurso local = 1,77 TB<br/>Aplicaciones = aprox. 1.5 GB<blockquote> Nota: para obtener información y algunas consideraciones sobre el uso de este tamaño, vea <a href="http://go.microsoft.com/fwlink/p/?linkid=328042">Acerca de las instancias intensivas de proceso A8, A9, A10 y A11</a>.</blockquote>|
|Standard_D1|1|3,5 GB|SO = tamaño del SO invitado<br/>Recurso local = 50 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard_D2|2|7 GB|SO = tamaño del SO invitado<br/>Recurso local = 100 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard_D3|4|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 200 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard_D4|8|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 400 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard_D11|2|14 GB|SO = tamaño del SO invitado<br/>Recurso local = 100 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard_D12|4|28 GB|SO = tamaño del SO invitado<br/>Recurso local = 200 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard_D13|8|56 GB|SO = tamaño del SO invitado<br/>Recurso local = 400 GB<br/>Aplicaciones = aprox. 1,5 GB|
|Standard_D14|16|112 GB|SO = tamaño del SO invitado<br/>Recurso local = 800 GB<br/>Aplicaciones = aprox. 1,5 GB|

## Pasos siguientes

[Configurar un servicio en la nube para Azure](https://msdn.microsoft.com/library/hh124108) [Configurar los tamaños de los servicios en la nube](https://msdn.microsoft.com/library/ee814754)

<!---HONumber=July15_HO3-->