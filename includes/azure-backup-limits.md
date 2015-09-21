<properties
   pageTitle="Tabla de límites de Copia de seguridad de Azure"
   description="Describe los límites del sistema para Copia de seguridad de Azure."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags  ms.service="backup" ms.devlang="NA" ms.topic="article" ms.tgt_pltfrm="NA" ms.workload="TBD" ms.date="08/26/2015" ms.author="jimpark"; "aashishr" />


Los límites siguientes corresponden a Copia de seguridad de Azure.

| Identificador de límites | Límite predeterminado |
|---|---|
|Número de servidores o máquinas que se pueden registrar en cada almacén|50|
|Tamaño del origen de datos para los datos almacenados en un almacén de Azure|54 400 gigabytes como máximo<sup>1</sup>|
|Número de almacenes de copia de seguridad que se pueden crear en cada suscripción a Azure|25|
|Número de veces por día que se puede programar la copia de seguridad|3 por día para servidor o cliente Windows <br/> 2 por día para SCDPM|
|Número de puntos de recuperación que se pueden crear|366<sup>2</sup>|
|Discos de datos conectados a una máquina virtual de Azure para copia de seguridad|5|

- <sup>1</sup>El límite de 54 400 gigabytes no se aplica a la copia de seguridad de máquinas virtuales IaaS.
- <sup>2</sup>Puede usar cualquier permutación cuyo resultado sea un número inferior a 366.

<!---HONumber=Sept15_HO2-->