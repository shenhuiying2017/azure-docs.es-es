La etapa final de este tutorial consiste en crear y ejecutar la aplicación nueva.

1.  Vaya a la ubicación donde guardó los archivos comprimidos del proyecto, expándalos en su equipo y abra el archivo de proyecto utilizando Xcode.

    ![][0]

2.  Presione el botón **Run** para crear el proyecto e iniciar la aplicación en el emulador de iPhone, que es la solución predeterminada para este proyecto.

3.  En la aplicación, escriba un texto significativo, como *Realice el tutorial*. A continuación, haga clic en el icono de suma (**+**).

    ![][1]

    Esta acción envía una solicitud POST al nuevo servicio móvil hospedado en Azure. Los datos de la solicitud se insertan en la tabla TodoItem. El servicio móvil devuelve los elementos almacenados en la tabla y los datos se muestran en la lista.

    > [WACOM.NOTE] Puede revisar el código que tiene acceso a su servicio móvil para consultar e insertar datos, que se encuentra en el archivo TodoService.m.
    >
    > </div>
    > </p>

  [0]: ./media/mobile-services-ios-run-app/mobile-xcode-project.png
  [1]: ./media/mobile-services-ios-run-app/mobile-quickstart-startup-ios.png
