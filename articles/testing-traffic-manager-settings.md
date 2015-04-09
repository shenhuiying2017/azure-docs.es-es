<properties
   pageTitle="Comprobación de la configuración de Administrador de tráfico"
   Descripción = "Este artículo le ayudará a probar la configuración de Administrador de tráfico."
   services="traffic-manager"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />

# Comprobación de la configuración de Administrador de tráfico

La mejor manera de probar la configuración del Administrador de tráfico consiste en configurar una serie de clientes y, a continuación, detener los extremos del perfil, compuestos de servicios en la nube y sitios web, de uno en uno. Las siguientes sugerencias le ayudarán a probar el perfil del Administrador de tráfico.

## Pasos básicos de las pruebas

-**Establezca el período de vida de DNS muy bajo**, de manera que los cambios se propaguen rápido; por ejemplo, 30 segundos.

-**Conozca las direcciones IP de los servicios en la nube y sitios web de Azure** del perfil que está probando.

-**Use herramientas que le permitan resolver un nombre de DNS en una dirección IP** y muestren esa dirección. Está comprobando si el nombre de dominio de la empresa se resuelve en direcciones IP de los extremos del perfil. Deben resolverse de manera coherente con el método de equilibrio de carga del perfil del Administrador de tráfico. Si está en un equipo que en el que se ejecuta Windows, puede usar la herramienta Nslookup.exe desde un símbolo del sistema o de Windows PowerShell. También dispone en Internet de otras herramientas disponibles públicamente que le permiten "profundizar" en la dirección IP.

### Para comprobar un perfil del Administrador de tráfico mediante nslookup

1-Abra un símbolo del sistema o de Windows PowerShell como administrador.

2-Escriba `ipconfig /flushdns` para vaciar la memoria caché de la resolución DNS.

3-Escriba `nslookup <your Traffic Manager domain name>`. Por ejemplo, el siguiente comando comprueba el nombre de dominio con el prefijo *myapp.contoso*
    nslookup myapp.contoso.trafficmanager.net
A continuación se muestra un resultado típico:
- El nombre de DNS y la dirección IP del servidor DNS al que se obtiene acceso para resolver este nombre de dominio del Administrador de tráfico.
- El nombre de dominio del Administrador de tráfico que escribió en la línea de comandos después de "nslookup" y la dirección IP en la que resuelve el dominio del Administrador de tráfico. La segunda dirección IP es la que es importante comprobar. Debe coincidir con una dirección IP virtual (VIP) para uno de los servicios en la nube o sitios web del perfil del Administrador de tráfico que está probando.

## Comprobación de los métodos de equilibrio de carga


### Para probar una método de equilibrio de carga de conmutación por error

1-Deje activos todos los extremos.
2-Utilice un único cliente.
3-Solicite la resolución DNS para el nombre de dominio de la empresa con la herramienta Nslookup.exe o una utilidad similar.
4-Asegúrese de que la dirección IP que obtiene es para el servicio principal hospedado.
5-Desactive el extremo principal o quite el archivo de supervisión, de forma que el Administrador de tráfico piense que está desactivado.
6-Espere a que transcurra el período de vida (TTL) de DNS del perfil del Administrador de tráfico más dos minutos adicionales. Por ejemplo, si el TTL de DNS es 300 segundos (5 minutos), debe esperar 7 minutos.
7-Vacíe la caché de cliente DNS y solicite una resolución DNS. En Windows, puede vaciar la caché DNS con el comando ipconfig /flushdns emitido en un símbolo del sistema o de Windows PowerShell.
8-Asegúrese de que la dirección IP que obtenga corresponda al extremo secundario.
9-Repita el proceso; para ello, detenga el extremo secundario, después el terciario y así sucesivamente. Asegúrese siempre de que la resolución DNS devuelve la dirección IP del siguiente extremo en la lista. Cuando todos los extremos estén detenidos, debe volver a obtener la dirección IP del extremo principal.

### Para probar un método de equilibrio de carga Round Robin

1-Deje activos todos los extremos.
2-Utilice un único cliente.
3-Solicite la resolución DNS para el dominio de la empresa con la herramienta Nslookup.exe o una utilidad similar.
4-Asegúrese de que la dirección IP que obtiene sea una de las que aparecen en la lista.
5-Vacíe la caché de cliente DNS y repita los pasos 3 y 4 sucesivamente. Debe comprobar que se devuelven direcciones IP distintas para cada uno de los extremos. A continuación, se repetirá el proceso.

### Para probar una método de equilibrio de carga de rendimiento

Para comprobar eficazmente un método de equilibrio de carga de rendimiento, deberá tener clientes situados en diversas partes del mundo. Puede crear clientes en Azure que intentarán llamar a los servicios a través del nombre de dominio de la empresa. Si su empresa es internacional, también puede iniciar sesión de forma remota en los clientes en otros lugares del mundo y realizar la prueba desde esos clientes.

Hay disponibles de manera gratuita servicios de inspección y búsqueda DNS basada en web. Algunos de estos proporcionan la capacidad de comprobar la resolución de nombres DNS desde varias ubicaciones. Realice una búsqueda de "Búsqueda DNS" para obtener ejemplos. Otra opción es usar una solución de terceros como Gomez o Keynote para confirmar que los perfiles distribuyen el tráfico según lo esperado.

## Otras referencias

[Métodos de equilibrio de carga del Administrador de tráfico](about-traffic-manager-balancing-methods.md)
[Tareas de configuración del Administrador de tráfico](https://msdn.microsoft.com/library/azure/hh744830.aspx)
[Administrador de tráfico](traffic-manager.md)

<!--HONumber=49-->