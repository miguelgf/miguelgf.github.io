---
layout: post
title: 'Migrando aplicación Android a React native: Primeros problemas'
date: '2023-03-26'
author: Miguel G. Flores
tags:
- android
- react native
---

En el tutorial de como añadir [React Native a una aplicación nativa](https://reactnative.dev/docs/integration-with-existing-apps) 
para Android, están muy claro los pasos a seguir, pero encontrarse con problemas es extremadamente fáciles, en la
primera hora encontré tres problemas solo para poder ejecutar una sincronización de Gradle por primera vez.

### Versión de Gradle
React Native, al menos en la versión 0.71 (la última a fecha de escribir este artículo), necesita funcionar con Gradle 6+
en Android para poder funcionar, pero no está especificado en ningún sitio, el error en cuestión es:

    e: node_modules/react-native-gradle-plugin/build.gradle.kts:10:19: Unresolved reference: configurationcache

Solución: Actualizar Gradle de la versión 4.2 en la que estaba el proyecto hasta 7.5, para estar en la última disponible
y evitar problemas a futuro.

### Archivos antiguos impidiendo Gradle Build
Una vez pasado el primer error, el segundo es también bastante críptico, al hacer build de Gradle:

    The current character read is 'i' with an int value of 105

Solución: Eliminar las carpetas de `build` de build anteriores de Android dentro de `app/build/`


### Memoria de Android Studio
Tercer error, como si de una aventura se tratase, encontramos el siguiente al hacer build:

    Null extracted folder for artifact: ResolvedArtifact(componentIdentifier=com.facebook.react:react-android:0.71.3, 
    variant=com.facebook.react:react-android:0.71.3 variant debugVariantDefaultApiPublication, variantName=null, 
    artifactFile=.gradle/caches/modules-2/files-2.1/com.facebook.react/react-android/0.71.3/23d18991c93161fab7f522d07bb77eb76287b8a/react-android-0.71.3-debug.aar, 
    isTestFixturesArtifact=false, extract edFolder=null, publishedLintJar=null, dependencyType=ANDROID, isWrappedModule=false, buildMapping={__current_build__=/android, react-native-gradle-plugin=/node_modules/react-native-gradle-plugin})

Solución: Aumentar la memoria de Android Studio, ya qua que se queda sin memoria al realizar el build en esta ocasión.
Para ello hay que modificar el archivo `gradle.properties` en la raiz del proyecto y añadir: `org.gradle.jvmargs=-Xmx4096M`

### PackageList missing
Cuarto error encontrado, al no poder encontrar el paquete PackageList dentro de React. Este problema es derivado de
haber instalado (o intentando instalar) el paquete con la versión anterior de Gradle (punto 1), en el que la instalación
quedó incompleta.

    error: cannot find symbol
    List<ReactPackage> packages = new PackageList(getApplication()).getPackages();
    ^
    symbol:   class PackageList
    location: class MyReactActivity

Solución: Borrar la carpeta `node_modules` y volver a ejecutar `yarn install` será suficiente.

### Arrancar app android desde yarn
Al terminar de configurar todo e intentar arrancar la app usando `yarn start` + `a`, tenemos el siguiente error en la
consola:

    Error: spawn ./gradlew EACCES
        at ChildProcess._handle.onexit (node:internal/child_process:285:19)
        at onErrorNT (node:internal/child_process:483:16)
        at process.processTicksAndRejections (node:internal/process/task_queues:82:21)
    info Run CLI with --verbose flag for more details.

Solución: Modificar los permisos para ejecutar `gradlew` fuera de Android Studio, solucionado con un simple chmod:
`chmod 755 android/gradlew

### Ruby 2.7.6 en Mac M1
Para instalar las dependencias de iOS con `bundler` es necesario tener Ruby 2.7.6 (que está deprecado y solo recibe
actualizaciones de seguridad críticas, no bugfixes), e instalarlo en un Mac M1 en 2023 no es tan fácil como podría 
parecer.

    ✖ Installing Bundler
    error Your Ruby version is 3.2.1, but your Gemfile specified 2.7.6

Solución: 
- Instalar [rbenv](https://github.com/rbenv/rbenv)
- Añadir rbenv en el path (.zshrc en mi caso)

  ```
  export PATH=$HOME/.rbenv/bin:/usr/local/bin:$HOME/.bin:$PATH
  ```

- Forzar la instalación de 2.7.6 con el siguiente comando, ya que una instalación normal fallará con el error `BUILD FAILED (macOS 13.1 using ruby-build 20230222)`

    ```shell
  CFLAGS="-Wno-error=implicit-function-declaration" RUBY_CONFIGURE_OPTS='--with-readline-dir=/usr/local/opt/readline/' arch -x86_64 rbenv install 2.7.6
  ```

- Establecer ruby 2.7.6 globalmente para la instalación de React Native / iOS `rbenv global 2.7.6`

### Añadir soporte para iOS en el proyecto
La documentación de React tiene toda la información para añadir React Native a una aplicación existente, pero no cuenta
con documentación oficial de como añadir soporte para otra plataforma. La manera de conseguirlo es en base a realizar
distintos trucos.

Solución:
- Crear un archivo `app.json` con el contenido `{"name": "empirestrikeapp", "displayName": "Empire Strike"}`
- Instalar react-native-eject `yarn add react-native-eject`
- Ejecutar eject `npx react-native eject`

Esto generará la carpeta de ios, donde ya se puede acceder y ejecutar `pod install`


### ¡Hola Mundo!
Finalmente, conseguimos tener la primera vista de React Native con uno ¡Hola mundo!

