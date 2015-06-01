
<properties 
    pageTitle="Información de tamaño para una red virtual de RemoteApp"
    description="Obtenga información acerca de los requisitos de direcciones IP de RemoteApp cuando se ejecuta con una red virtual." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/29/2015" 
    ms.author="elizapo" />



# Información de tamaño para una red virtual de RemoteApp

Al usar RemoteApp con una red virtual (VNET), RemoteApp utiliza direcciones IP dentro de la subred. En función de la escala de su servicio de RemoteApp, deberá asegurarse de que la subred tiene suficientes direcciones IP disponibles para las máquinas virtuales de RemoteApp. Aunque esta guía sobre tamaños no es perfecta dado el modo en que RemoteApp aumenta y disminuye dinámicamente la capacidad de las máquinas virtuales dentro de una colección, le permitirá calcular el intervalo de subredes. Esto es especialmente importante ya que, una vez esté vigente el servicio de RemoteApp en una red virtual, no se puede aumentar el tamaño de la subred sin quitar RemoteApp.

Para cada colección de RemoteApp que desee ejecutar a su capacidad máxima, debe tener 100 direcciones IP disponibles. Por ejemplo, si tiene una colección de RemoteApp en el plan Estándar y desea tener como máximo 500 usuarios, debe tener 100 direcciones IP para esa colección. Del mismo modo, necesita 100 direcciones IP para una colección de RemoteApp en el plan Básico que tenga 800 usuarios. Si planea tener una cantidad inferior de usuarios (por debajo del valor máximo), puede reducir las direcciones IP que se necesitan por colección. El requisito de tamaño mínimo de subred es de 30 direcciones IP (/27).

<!--HONumber=52-->
