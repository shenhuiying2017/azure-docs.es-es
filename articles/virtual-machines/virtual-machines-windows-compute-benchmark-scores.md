<properties
 pageTitle="Puntuaciones de pruebas comparativas de proceso para VM Windows | Microsoft Azure"
 description="Comparación de puntuaciones de pruebas comparativas de proceso de SPECint para máquinas virtuales de Azure con Windows Server"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Puntuaciones de pruebas comparativas de proceso para máquinas virtuales Windows

Las siguientes puntuaciones de pruebas comparativas SPECInt muestran el rendimiento de proceso para la alineación de máquinas virtuales de alto rendimiento de Azure con Windows Server. Las puntuaciones de pruebas comparativas de proceso también están disponibles para las [máquinas virtuales Linux](virtual-machines-linux-compute-benchmark-scores.md).



## Serie A: de proceso intensivo


Tamaño | vCPU | Nodos NUMA | CPU | Ejecuciones | Tasa base promedio | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 a 2,6 GHz | 10 | 236\.1 | 1\.1
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 a 2,6 GHz | 10 | 450\.3 | 7\.0
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 a 2,6 GHz | 5 | 235\.6 | 0\.9
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 a 2,6 GHz |7 | 454\.7 | 4\.8

## Serie Dv2


Tamaño | vCPU | Nodos NUMA | CPU | Ejecuciones | Tasa base promedio | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 83 | 36\.6 | 2\.6
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 27 | 70\.0 | 3\.7
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 130\.5 | 4\.4.
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 238\.1 | 5\.2
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 14 | 460\.9 | 15\.4
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 19 | 70\.1 | 3\.7
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 2 | 132\.0 | 1\.4
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 a 2,4 GHz | 17 | 235\.8 | 3\.8
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 a 2,4 GHz | 15 | 460\.8 | 6\.5


## Serie G, serie GS


Tamaño | vCPU | Nodos NUMA | CPU | Ejecuciones | Tasa base promedio | StdDev
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1, Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 31 | 71\.8 | 6\.5
Standard\_G2, Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 5 | 133\.4 | 13\.0
Standard\_G3, Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 6 | 242\.3 | 6\.0
Standard\_G4, Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 a 2 GHz | 15 | 398\.9 | 6\.0
Standard\_G5, Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 a 2 GHz | 22 | 762\.8 | 3\.7

## Acerca de SPECint

Las cifras de Windows se calcularon mediante la ejecución de [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) en Windows Server. SPECint se ejecutó con la opción de tasa base (SPECint\_rate2006), con una copia por núcleo. SPECint consta de 12 pruebas independientes y cada una se ejecuta tres veces, tomando el valor medio de cada prueba y ponderándolo para formar una puntuación compuesta. Se muestran las que se ejecutaron en varias máquinas virtuales para proporcionar las puntuaciones promedio.


## Pasos siguientes

* Para más información sobre las capacidades de almacenamiento, los detalles del disco y consideraciones adicionales para seleccionar tamaños de máquinas virtuales, consulte [Tamaños de máquinas virtuales](virtual-machines-windows-sizes.md).

<!---HONumber=AcomDC_0727_2016-->