## <a name="setting-up-your-ios-application"></a>Configuración de la aplicación de iOS

En esta sección se proporcionan instrucciones paso a paso sobre cómo crear un nuevo proyecto a fin de mostrar cómo integrar una aplicación de iOS (Swift) con *Iniciar sesión en Microsoft*, de manera que pueda consultar las API web que requieran un token.

> ¿Prefiere descargar este proyecto de XCode de ejemplo en su lugar? [Descargue un proyecto](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) y vaya al [paso de configuración](#create-an-application-express) para configurar el código de ejemplo antes de ejecutarlo.


## <a name="install-carthage-to-download-and-build-msal"></a>Instalación de Carthage para descargar y compilar MSAL
Se utiliza el administrador de paquetes Carthage durante el período de versión preliminar de MSAL: se integra con XCode manteniendo la capacidad de Microsoft para realizar cambios en la biblioteca.

- Descargue e instale la versión más reciente de Carthage [aquí](https://github.com/Carthage/Carthage/releases "Dirección URL de descarga de Carthage")

## <a name="creating-your-application"></a>Creación de la aplicación

1.  Abra Xcode y seleccione `Create a new Xcode project`.
2.  Seleccione `iOS` > `Single view Application` y haga clic en *Siguiente*
3.  Asigne un nombre al producto y haga clic en *Siguiente*
4.  Seleccione una carpeta para crear la aplicación y haga clic en *Crear*.

## <a name="build-the-msal-framework"></a>Compilación del marco MSAL

Siga las instrucciones siguientes para extraer y, después, generar la versión más reciente de las bibliotecas MSAL mediante Carthage:

1.  Abra el terminal de Bash y vaya a la carpeta raíz de la aplicación.
2.  Copie lo siguiente y péguelo en el terminal de Bash para crear un archivo 'Cartfile':

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Copie y pegue lo siguiente. Este comando recupera las dependencias en una carpeta Carthage/Checkouts y, después, compila la biblioteca MSAL:
</li>
</ol>

```bash
carthage update
```

> El proceso descrito anteriormente se utiliza para descargar y compilar la Biblioteca de autenticación de Microsoft (MSAL). MSAL controla la adquisición, el almacenamiento en caché y la actualización de los tokens de usuario que se utilizan para tener acceso a las API protegidas por el Azure Active Directory v2.

## <a name="add-the-msal-framework-to-your-application"></a>Adición del marco MSAL a la aplicación
1.  En Xcode, abra la pestaña `General`.
2.  Vaya a la sección `Linked Frameworks and Libraries` y haga clic en `+`.
3.  Seleccionar `Add other…`
4.  Seleccione: `Carthage` > `Build` > `iOS` > `MSAL.framework` y haga clic en *Abrir*. Debería ver `MSAL.framework` agregado a la lista.
5.  Vaya a la pestaña `Build Phases`, haga clic en el icono `+` y elija `New Run Script Phase`.
6.  Agregue el contenido siguiente al *área del script*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Agregue lo siguiente a <code>Input Files</code> haciendo clic en <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Creación de la interfaz de usuario de la aplicación
Debe crearse un archivo Main.storyboard automáticamente como parte de la plantilla de proyecto. Siga las instrucciones siguientes para crear la interfaz de usuario de la aplicación:

1.  Presione Control y haga clic en `Main.storyboard` para mostrar el menú contextual y, a continuación, haga clic en: `Open As` > `Source Code`
2.  Reemplace el nodo `<scenes>` por el código siguiente:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign-Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
