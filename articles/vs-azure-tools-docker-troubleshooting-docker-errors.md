<properties
   pageTitle="Solución de problemas de errores del cliente Docker en Windows con Visual Studio | Microsoft Azure"
   description="Solucione los problemas que encuentre al usar Visual Studio para crear e implementar aplicaciones web en Docker en Windows mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# Solución de problemas de desarrollo de Docker en Visual Studio

Cuando se trabaja con la versión preliminar de Visual Studio Tools para Docker, pueden surgir ciertos problemas debido a la propia naturaleza de la versión preliminar. Éstos son algunos problemas frecuentes y sus soluciones.

##No se pudo configurar la compatibilidad de Program.cs con Docker

Si se agrega compatibilidad con Docker, es preciso agregar `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS"))` a WebHostBuilder(). Si no se han encontrado la función Main() de Program.cs o una nueva clase de WebHostBuilder, se mostrará una advertencia. UseUrls() es necesario para habilitar Kestrel para que escuche el tráfico entrante, más allá de localhost, cuando se ejecuta en un contenedor de Docker. Al finalizar, el código típico tendrá un aspecto similar al siguiente:

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() configured the WebHost to listen to incoming URL traffic. [Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS) will configure the environment variable in the dockerfile.debug/release mode as follows:

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## La asignación de volúmenes no funciona
Para habilitar las funcionalidades de edición y actualización, la asignación de volúmenes está configurada para compartir el código fuente del proyecto con la carpeta .app dentro del contenedor. Como los archivos se cambian en el equipo host, el directorio de la aplicación o contenedores usa el mismo directorio. En docker-compose.debug.yml, la siguiente configuración habilita la asignación de volúmenes:

```
    volumes:
      - ..:/app
```

Para comprobar si funciona la asignación de volúmenes, pruebe el siguiente comando:

**En Windows**

```
docker run -it -v /c/Users/Public:/wormhole busybox
cd wormhole
/ # ls
```

Debería ver una lista de directorios de la carpeta Usuarios/Público. Si no se muestran archivos y la carpeta /c/Usuarios/Público no está vacía, significa que la asignación de volúmenes no está configurada correctamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Cambie al directorio wormhole para ver el contenido del directorio `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

**Nota:** *Al trabajar con máquinas virtuales de Linux, el sistema de archivos del contenedor distingue mayúsculas de minúsculas.*

Si no puede ver el contenido, pruebe lo siguiente:

**Versión beta de Docker para Windows**
- Compruebe que se ejecuta la aplicación de escritorio de Docker para Windows, para lo que debe buscar el icono de la ballena en la bandeja del sistema y asegurarse de que es de color blanco y de que funciona.
- Compruebe que está configurada la asignación de volúmenes, para lo que debe hacer clic con el botón derecho en el icono de la ballena en la bandeja del sistema, seleccionar la configuración y hacer clic en **Manage shared drives...** (Administrar unidades compartidas).

**Docker Toolbox con VirtualBox**

De manera predeterminada, VirtualBox comparte `C:\Users` como `c:/Users`. Si es posible, mueva el proyecto a este directorio. Otra posibilidad es agregarlo manualmente a las [carpetas compartidas](https://www.virtualbox.org/manual/ch04.html#sharedfolders) de VirtualBox.
	
##Compilación: no se ha podido compilar la imagen. Error al comprobar la conexión TLS: el host no se está ejecutando

- Compruebe que se ejecuta el host de Docker predeterminado. Consulte el artículo [Configuración de un host de Docker con VirtualBox](./vs-azure-tools-docker-setup.md).

##Uso de Microsoft Edge como explorador predeterminado

Si utiliza el Explorador de Microsoft Edge, puede que el sitio no se abra ya que Edge considera que la dirección IP no es segura. Para solucionarlo, realice los siguientes pasos:
1. En el cuadro Ejecutar de Windows, escriba `Internet Options`.
2. Seleccione **Opciones de Internet** cuando aparezca. 
2. Seleccione la pestaña **Seguridad**.
3. Seleccione la zona **Intranet local**.
4. Seleccione **Sitios**. 
5. Agregue la dirección IP de la máquina virtual (en este caso, el host de Docker) en la lista. 
6. Actualice la página en Edge; debería ver que el sitio está en funcionamiento. 
7. Para más información sobre este problema, consulte la publicación del blog de Scott Hanselman [Microsoft Edge can't see or open VirtualBox-hosted local web sites](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx) (Microsoft Edge no puede ver ni abrir los sitios web locales hospedados en VirtualBox). 

##Solución de problemas de la versión 0.15, o de las versiones anteriores


###La ejecución de la aplicación hace que PowerShell se abra, muestre un error y se cierre. La página del explorador no se abre.

Podría tratarse de un error durante `docker-compose-up`. Para ver el error, realice los pasos siguientes:

1. Abra el archivo `Properties\launchSettings.json`.
1. Busque la entrada Docker.
1. Busque la línea que comienza de la manera siguiente:

    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
	
1. Agregue el parámetro `-noexit` para que la línea sea como la siguiente. De esta forma PowerShell se mantendrá abierto y podrá ver el error.

	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”

<!---HONumber=AcomDC_0608_2016-->