<properties 
    pageTitle="Límites del servicio de RemoteApp"
    description="Obtenga información acerca de los límites y los valores predeterminados para Azure RemoteApp" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/07/2015" 
    ms.author="elizapo" />


# Límites de servicio de RemoteApp y valores predeterminados

En este tema se describen los límites de servicio para RemoteApp de Azure.

  
## Límites de colección
Todas las colecciones:

- Colecciones por usuario: 1
- Aplicaciones publicadas por colección: 5000 

Colecciones de prueba:

- Duración de la prueba: 30 días
- Colecciones de prueba: 2 por suscripción
- Usuarios de prueba por colección: 10 
- Imágenes de plantilla de prueba: 25
 
Colecciones (pagadas) activadas:

- Colecciones pagadas: 3 (puede solicitar un aumento de este límite)
- Imágenes de plantillas pagadas: 25

 
## Límites de usuarios

Los límites de colección documentados son límites suaves (lo que significa que se pueden cambiar). El número máximo de usuarios por colección es un límite máximo, no se puede cambiar. Sin embargo, el número de usuarios que puede tener se determina por el nivel de la colección, de la siguiente manera:


- Básico: 800 usuarios
- Estándar: 500 usuarios

(El número de usuarios que puede admitir viene determinado por el número de máquinas virtuales que se usan para su colección. Para el nivel básico, hay 16 usuarios por máquina virtual, mientras que el nivel estándar tiene 10 usuarios por máquina virtual).

Para calcular el número máximo de usuarios asignados que puede admitir, multiplique el número de usuarios pagados por colección por el número de colecciones que tiene.
  
Puede crear varias colecciones en su cuenta con estos límites y subir hasta 5.000 conexiones de usuarios simultáneas en todas las colecciones. Si necesita un número más alto, puede solicitar un aumento.

## Otros valores y límites:

- Almacenamiento de datos de usuario por usuario y colección: 50 GB
- Tiempo de espera de inactividad: 4 horas
- Tiempo de espera de desconexión: 4 horas

## Cómo solicitar un aumento del límite
Puede solicitar un aumento de los límites de colección y usuarios simultáneos. Para ello, abra una incidencia de soporte técnico en la que se explique lo que necesita.


Para abrir una incidencia de soporte técnico, haga lo siguiente:

1.	En el portal de administración, haga clic en [Obtener soporte](https://manage.windowsazure.com/?getsupport=true). Si no ha iniciado sesión, se le solicitará que especifique sus credenciales.
2.	Seleccione su suscripción.
3.	En el tipo de soporte técnico, seleccione **Técnico**.
4.	Haga clic en **Crear incidencia**. 
5.	Seleccione **Azure RemoteApp** en la lista de productos que se presentan en la siguiente página.
6.	Seleccione un **Tipo de problema** adecuado para lo que pasa.
7.	Haga clic en **Continue**.
8.	Siga las instrucciones de la página siguiente y especifique luego detalles sobre el aumento del límite que necesita. Por ejemplo, si desea ampliar el período de prueba, escriba "Amplíe mi período de prueba X días". 
9.	Haga clic en **Enviar** para abrir la incidencia.

<!---HONumber=58-->