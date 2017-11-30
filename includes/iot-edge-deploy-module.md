Una de las funcionalidades clave de Azure IoT Edge es que puede implementar módulos en dispositivos de IoT Edge desde la nube. Un módulo de IoT Edge es un paquete ejecutable que se implementa como un contenedor. En esta sección se implementa un módulo que genera telemetría para un dispositivo simulado. 

1. En Azure Portal, navegue hasta el centro de IoT.
1. Vaya a **IoT Edge (versión preliminar)** y seleccione el dispositivo de IoT Edge.
1. Seleccione **Set modules** (Establecer módulos).
1. Seleccione **Add IoT Edge module** (Agregar módulo de IoT Edge).
1. En el campo **Nombre**, escriba `tempSensor`. 
1. En el campo **URI de la imagen**, escriba `microsoft/azureiotedge-simulated-temperature-sensor:1.0-preview`. 
1. Deje los restantes valores tal cual y seleccione **Guardar**.

   ![Guarde el módulo de IoT Edge después de escribir el nombre y el URI de la imagen](./media/iot-edge-deploy-module/name-image.png)

1. Vuelva al paso **Add modules** (Agregar módulos) y seleccione **Siguiente**.
1. En el paso **Specify routes** (Especificar rutas), seleccione **Siguiente**.
1. En el paso **Review template** (Revisar plantilla), seleccione **Enviar**.
1. Vuelva a la página de detalles del dispositivo y seleccione **Actualizar**. Debería ver que el nuevo módulo tempSensor se ejecuta junto con el runtime de IoT Edge. 

   ![Vea tempSensor en la lista de módulos implementados][1]

<!-- Images -->
[1]: ../articles/iot-edge/media/tutorial-simulate-device-windows/view-module.png