---
title: Lenguajes para Data Science Virtual Machine en Azure | Microsoft Docs
description: Lenguajes para Data Science Virtual Machine en Azure
keywords: "herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 2f2125e739b738847e03ce429d65801969611685
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Lenguajes admitidos en Data Science Virtual Machine 

Data Science Virtual Machine (DSVM) incorpora varios lenguajes predefinidos y herramientas de desarrollo para compilar sus aplicaciones de AI. Estos son algunos de los más destacados. 

## <a name="python"></a>Python

|    |           |
| ------------- | ------------- |
| Versiones de lenguajes admitidas | 2.7 y 3.5 |
| Ediciones compatibles de DSVM      | Linux y Windows     |
| ¿Cómo se configura/instala en DSVM?  | Se crean dos entornos `conda` globales. <br /> El entorno * `root` ubicado en `/anaconda/` es Python 2.7. <br/> El entorno * `py35` ubicado en `/anaconda/envs/py35` es Python 3.5       |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo para Python     |
| Herramientas relacionadas en DSVM      | PySpark, R y Julia      |
### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?    

**Windows**:

* Ejecución en el símbolo del sistema

Abra el símbolo del sistema y realice lo siguiente según la versión de Python que quiera ejecutar. 

```
# To run Python 2.7
activate 
python --version

# To run Python 3.5
activate py35
python --version

```
* Uso en un IDE

Use Herramientas de Python para Visual Studio (PTVS), que viene instalado en la edición de Visual Studio Community. El único entorno configurado automáticamente en PTVS es Python 2.7. 
> [!NOTE]
> Para señalar PTVS en Python 3.5, debe crear un entorno personalizado en PTVS. Para establecer estas rutas de entorno en Visual Studio Community Edition, vaya a **Herramientas** -> **Herramientas de Python** -> **Entornos de Python** y haga clic en **+ Personalizar**. Luego, establezca la ubicación en `c:\anaconda\envs\py35` y haga clic en _Detección automática_. 

* Uso en Jupyter

Abra Jupyter y haga clic en el botón `New` para crear un bloc de notas. En este momento puede elegir el tipo de kernel como _Python [Conda Root]_ para el entorno Python 2.7 y _Python [Conda env:py35]_ para el entorno Python 3.5. 

* Instalación de paquetes de Python

Los entornos predeterminados de Python en DSVM son entornos globales que pueden leer todos los usuarios, pero solo los administradores pueden escribir/instalar paquetes globales. Para instalar el paquete en el entorno global, actívelo en el entorno raíz o en py35 mediante el comando `activate` como administrador. Después podrá usar un administrador de paquetes como `conda` o `pip` para instalar o actualizar paquetes. 


**Linux**:

* Ejecución en el terminal

Abra un terminal y realice lo siguiente según la versión de Python que quiera ejecutar. 

```
# To run Python 2.7
source activate 
python --version

# To run Python 3.5
source activate py35
python --version

```
* Uso en un IDE

Use PyCharm, que está instalado en la edición Visual Studio Community. 

* Uso en Jupyter

Abra Jupyter y haga clic en el botón `New` para crear un bloc de notas. En este momento puede elegir el tipo de kernel como _Python [Conda Root]_ para el entorno Python 2.7 y _Python [Conda env:py35]_ para el entorno Python 3.5. 

* Instalación de paquetes de Python

Los entornos predeterminados de Python en DSVM son entornos globales que pueden leer todos los usuarios, pero solo los administradores pueden escribir/instalar paquetes globales. Para instalar el paquete en el entorno global, actívelo en el entorno raíz o en py35 mediante el comando `source activate` como administrador o como un usuario que tenga el permiso sudo. Después podrá usar un administrador de paquetes como `conda` o `pip` para instalar o actualizar paquetes. 

## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Versiones de lenguajes admitidas | Microsoft R Open 3.x (100 % compatible con CRAN-R)<br /> Microsoft R Server 9.x Developer (una plataforma de R escalable y preparada para empresas)|
| Ediciones compatibles de DSVM      | Linux y Windows     |
| ¿Cómo se configura/instala en DSVM?  | Windows: `C:\Program Files\Microsoft\R Server\R_SERVER` <br />Linux: ` /usr/lib64/microsoft-r/3.3/lib64/R`    |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo para R     |
| Herramientas relacionadas en DSVM      | SparkR, Python y Julia      |
### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?    

**Windows**:

* Ejecución en el símbolo del sistema

Abra el símbolo del sistema y escriba `R`.

* Uso en un IDE

Use Herramientas de R para Visual Studio (RTVS), que viene instalado en la edición de Visual Studio Community o RStudio. Están disponibles en el menú Inicio o en forma de icono del escritorio. 

* Uso en Jupyter

Abra Jupyter y haga clic en el botón `New` para crear un bloc de notas. En este momento puede elegir el tipo de kernel como _R_ para usar el kernel de Jupyter R (IRKernel). 

* Instalación de paquetes de R

R se instala en DSVM en un entorno global que todos los usuarios pueden leer, pero solo los administradores pueden escribir/instalar paquetes globales. Para poder instalar el paquete en el entorno global, ejecute R con uno de los métodos anteriores. Después podrá ejecutar el administrador de paquetes de R `install.packages()` para instalar o actualizar paquetes. 

**Linux**:

* Ejecución en el terminal

Abra el terminal y ejecute `R`.  

* Uso en un IDE

Use RStudio, que viene instalado en DSVM para Linux.  

* Uso en Jupyter

Abra Jupyter y haga clic en el botón `New` para crear un bloc de notas. En este momento puede elegir el tipo de kernel como _R_ para usar el kernel de Jupyter R (IRKernel). 

* Instalación de paquetes de R

R se instala en DSVM en un entorno global que todos los usuarios pueden leer, pero solo los administradores pueden escribir/instalar paquetes globales. Para poder instalar el paquete en el entorno global, ejecute R con uno de los métodos anteriores. Después podrá ejecutar el administrador de paquetes de R `install.packages()` para instalar o actualizar paquetes. 


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Versiones de lenguajes admitidas | 0,5 |
| Ediciones compatibles de DSVM      | Linux y Windows     |
| ¿Cómo se configura/instala en DSVM?  | Windows: Instalado en `C:\JuliaPro-VERSION`<br /> Linux: Instalado en `/opt/JuliaPro-VERSION`    |
| Vínculos a ejemplos      | Se incluyen blocs de notas de Jupyter de ejemplo para Julia     |
| Herramientas relacionadas en DSVM      | Python y R      |
### <a name="how-to-use--run-it"></a>¿Cómo se usa/ejecuta?    

**Windows**:

* Ejecución en el símbolo del sistema

Abra el símbolo del sistema y ejecute `julia`. 
* Uso en un IDE

Use `Juno`, el IDE de Julia instalado en DSVM y disponible como acceso directo del escritorio.

* Uso en Jupyter

Abra Jupyter y haga clic en el botón `New` para crear un bloc de notas. En este momento puede elegir el tipo de kernel como `Julia VERSION` 

* Instalación de paquetes de Julia

La ubicación predeterminada de Julia es un entorno global que todos los usuarios pueden leer, pero solo los administradores pueden escribir/instalar paquetes globales. Para poder instalar el paquete en el entorno global, ejecute Julia con uno de los métodos anteriores. Después podrá ejecutar los comandos del administrador de paquetes de Julia, como `Pkg.add()`, para instalar o actualizar paquetes. 


**Linux**:
* Ejecución en el terminal

Abra el terminal y ejecute `julia`. 
* Uso en un IDE

Use `Juno`, el IDE de Julia instalado en DSVM y disponible como acceso directo del menú Aplicación.

* Uso en Jupyter

Abra Jupyter y haga clic en el botón `New` para crear un bloc de notas. En este momento puede elegir el tipo de kernel como `Julia VERSION` 

* Instalación de paquetes de Julia

La ubicación predeterminada de Julia es un entorno global que todos los usuarios pueden leer, pero solo los administradores pueden escribir/instalar paquetes globales. Para poder instalar el paquete en el entorno global, ejecute Julia con uno de los métodos anteriores. Después podrá ejecutar los comandos del administrador de paquetes de Julia, como `Pkg.add()`, para instalar o actualizar paquetes. 

## <a name="other-languages"></a>Otros lenguajes

**C#**: Disponible en Windows y accesible a través de la edición Visual Studio Community o en un `Developer Command Prompt for Visual Studio`, donde solo puede ejecutar el comando `csc`. 

**Java**: OpenJDK está disponible en la edición para Windows y Linux de DSVM y establecido en la ruta de acceso. Puede escribir el comando `javac` o `java` en el símbolo del sistema (Windows) o en el shell Bash (Linux) para usar Java. 

**node.js**: node.js está disponible en la edición para Windows y Linux de DSVM y establecido en la ruta de acceso. Puede escribir el comando `node` o `npm` en el símbolo del sistema (Windows) o en el shell Bash (Linux) para obtener acceso a node.js. En Windows, la extensión de las Herramientas de Node.js para Visual Studio se instala para proporcionar un IDE gráfico para desarrollar su aplicación node.js. 

**F#**: Disponible en Windows y accesible a través de la edición Visual Studio Community o en un `Developer Command Prompt for Visual Studio`, donde solo puede ejecutar el comando `fsc`. 


