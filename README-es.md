![](get.png)

*Idiomas: Español (este archivo), [Inglés](README.md), [Portugués de Brasil](README.pt-br.md).*

[![pub package](https://img.shields.io/pub/v/get.svg?label=get&color=blue)](https://pub.dev/packages/get)
![building](https://github.com/jonataslaw/get/workflows/build/badge.svg)
[![Gitter](https://badges.gitter.im/flutter_get/community.svg)](https://gitter.im/flutter_get/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
<a href="https://github.com/Solido/awesome-flutter">
   <img alt="Awesome Flutter" src="https://img.shields.io/badge/Awesome-Flutter-blue.svg?longCache=true&style=flat-square" />
</a>
<a href="https://www.buymeacoffee.com/jonataslaw" target="_blank"><img src="https://i.imgur.com/aV6DDA7.png" alt="Buy Me A Coffee" style="height: 41px !important;width: 174px !important; box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;-webkit-box-shadow: 0px 3px 2px 0px rgba(190, 190, 190, 0.5) !important;" > </a>
<!-- ALL-CONTRIBUTORS-BADGE:START - Do not remove or modify this section -->
[![All Contributors](https://img.shields.io/badge/all_contributors-4-orange.svg?style=flat-square)](#contributors-)
<!-- ALL-CONTRIBUTORS-BADGE:END -->

- [Sobre GetX](#sobre-getx)
- [Los tres pilares](#los-tres-pilares)
  - [Gestión del Estado](#gestión-del-estado)
    - [Explicación completa](#explicación-completa)
  - [Gestión de Rutas](#gestión-de-rutas)
    - [Explicación completa](#explicación-completa-1)
  - [Gestión de dependencias](#gestión-de-dependencias)
    - [Explicación completa](#explicación-completa-2)
- [Utils](#utils)
  - [Cambiar de tema](#cambiar-de-tema)
  - [Otras API avanzadas y configuraciones manuales](#otras-api-avanzadas-y-configuraciones-manuales)
    - [Configuraciones globales opcionales](#configuraciones-globales-opcionales)
- [Rompiendo cambios desde 2.0](#rompiendo-cambios-desde-20)

# Sobre GetX

- GetX es una solución extra ligera y potente para Flutter. Combina gestión de estádo de alto rendimiento, inyección de dependencia inteligente y gestión de rutas, de forma rápida y práctica.  

- GetX no es para todos, se enfoca en el consumo mínimo de recursos (rendimiento) ([Mira los puntos de referencia](https://github.com/jonataslaw/benchmarks)), usando una sintaxis fácil y agradable (productividad), que permite el desacoplamiento total de la vista y la lógica de negocio (organización).

- GetX permite ahorrar horas de desarrollo, extraer el máximo rendimiento de una aplicación, siendo tatno fácil de implementar para principiantes como precisa para expertos. Navegue sin contexto, abra dialogs, snackbars y bottomsheets desde cualquier parte de su código, gestione estados e inyecte dependencias de una manera fácil y práctica. GetX es seguro, estable, actualizado y ofrece una amplia gama de APIs que no están presentes en el framework por defecto.

- Tiene una multitud de funciones que le permiten comenzar a programar sin preocuparse por nada. Cada una de estas funciones se encuentra en contenedores separados y solo se inicia después de su uso. Si solo usa State Management, solo State Management se compilará. Si solo usa rutas, no se compilará nada de gestión de estado. Puede compilar el repositorio de referencia, y verá que usando solo la gestión de estado de GetX, la aplicación compilada se ha vuelto más pequeña que todas las demás aplicaciones de otros paquetes que solo tienen la gestión de estado, porque nada de lo que no se use se compilará en su código, y cada solución GetX fue diseñada para ser extra liviana. El mérito aquí también proviene del AOT de Flutter, que es increíble y logra eliminar los recursos no utilizados como ningún otro framework lo hace.

**GetX hace que su desarrollo sea productivo, pero ¿quiere hacerlo aún más productivo? [Agregue la extensión a su VSCode](https://marketplace.visualstudio.com/items?itemName=get-snippets.get-snippets)**

*¿Quieres contribuir al proyecto? Estaremos orgullosos de destacarte como uno de nuestros colaboradores. Aquí hay algunos puntos en los que puede contribuir y hacer que GetX (y Flutter) sea aún mejor.*

- Ayudando a traducir el archivo Léame a otros idiomas.

- Agregar documentación al archivo Léame (ni siquiera la mitad de las funciones de GetX han sido documentadas todavía).

- Escriba artículos o haga videos que enseñen cómo usar GetX (se insertarán en el archivo Léame y en el futuro en nuestro Wiki).

- Ofreciendo PRs para código/pruebas.

- Incluyendo nuevas funciones.

# Los tres pilares

## Gestión del Estado

Vea una explicación más detallada de la administración del estado [aquí](./docs/es_ES/state_management.md). Allí verá más ejemplos y también la diferencia entre el Gestión del Estado simple y el Gestión del Estado reactivo

El proyecto "contador" creado por defecto en un nuevo proyecto en Flutter tiene más de 100 líneas (con comentarios). Para mostrar el poder de GetX, demostraré cómo hacer un "contador" cambiando el estado con cada clic, cambiando de página y compartiendo el estado entre pantallas, todo de manera organizada, separando la vista de la lógica de negocio, SOLO 26 LÍNEAS DE CÓDIGO INCLUIDOS COMENTARIOS.

- Paso 1:  
  Agregue "Get" antes de su materialApp, convirtiéndolo en GetMaterialApp

```dart
void main() => runApp(GetMaterialApp(home: Home()));
```

Nota: esto no modifica el MaterialApp del Flutter, GetMaterialApp no es una MaterialApp modificado, es solo un Widget preconfigurado, que tiene como child un MaterialApp por defecto. Puede configurar esto manualmente, pero definitivamente no es necesario. GetMaterialApp creará rutas, las inyectará, inyectará traducciones, inyectará todo lo que necesita para la navegación de rutas. Si usa Get solo para la gestión de estado o dependencias, no es necesario usar GetMaterialApp. GetMaterialApp es necesario para rutas, snackbars, internacionalización, bottomSheets, diálogos y APIs de alto nivel relacionadas con rutas y ausencia de contexto.

- Paso 2:  
Cree su clase con la lógica de negocio colocando todas las variables, métodos y controladores dentro de ella. Puede hacer que cualquier variable sea observable usando un simple ".obs".

```dart
class Controller extends GetXController {
  var count = 0.obs;
  increment() => count.value++;
}
```

- Paso 3:  
Cree su vista, use StatelessWidget y ahorre algo de RAM, con GetX ya no necesitará usar StatefulWidget.  

```dart
class Home extends StatelessWidget {

  // Instantiate your class using Get.put() to make it available for all "child" routes there.
  final Controller c = Get.put(Controller());

  @override
  Widget build(context) => Scaffold(
      // Use Obx(()=> to update Text() whenever count is changed.
      appBar: AppBar(title: Obx(() => Text("Clicks: " + c.count.string))),

      // Replace the 8 lines Navigator.push by a simple Get.to(). You don't need context
      body: Center(child: RaisedButton(
              child: Text("Go to Other"), onPressed: () => Get.to(Other()))),
      floatingActionButton:
          FloatingActionButton(child: Icon(Icons.add), onPressed: c.increment));
}

class Other extends StatelessWidget {
  // You can ask Get to find a Controller that is being used by another page and redirect you to it.
  final Controller c = Get.find();

  @override
  Widget build(context){
     // Access the updated count variable
     return Scaffold(body: Center(child: Text(c.count.string)));
}

```

Este es un proyecto simple pero ya deja en claro cuán poderoso es GetX. A medida que su proyecto crezca, esta diferencia se volverá más significativa. GetX fue diseñado para trabajar con equipos, pero también simplifica el trabajo de un desarrollador individual. Mejore sus plazos, entregue todo a tiempo, sin perder rendimiento. GetX no es para todos, pero si te identificaste con esa frase, ¡GET es para ti!

### Explicación completa

**Vea una explicación más detallada de la administración del estado [aquí](./docs/es_ES/state_management.md). Allí verá más ejemplos y también la diferencia entre el Gestión del Estado simple y el Gestión del Estado reactivo**

## Gestión de Rutas

Vea una explicación más detallada de la administración del estado [aquí](./docs/es_ES/route_management.md).

Para navegar a una nueva pantalla:

```dart
Get.to(NextScreen());
```

Para cerrar snackbars, dialogs, bottomsheets o cualquier cosa que normalmente cierre con Navigator.pop(contexto);

```dart
Get.back();
```

Para ir a la siguiente pantalla, sin opción a volver (util por ejemplo en SplashScreens, LoginScreen, etc.)

```dart
Get.off(NextScreen());
```

Para ir a la siguiente pantalla y cancelar todas las rutas anteriores (útil en carritos de compras, encuestas y exámenes)

```dart
Get.offAll(NextScreen());
```

Para navegar a la siguiente ruta y recibir o actualizar datos tan pronto como se regrese de ella:

```dart
var data = await Get.to(Payment());
```

### Explicación completa

**Vea una explicación más detallada de la Gestión de Rutas [aquí](./docs/es_ES/route_management.md).**

## Gestión de dependencias

Vea una explicación más detallada de la Gestión de dependencias [aquí](./docs/es_ES/dependency_management.md).

- Nota: si está utilizando el gestor de estado de GetX, no tiene que preocuparse por esto, solo lea para obtener información, pero preste más atención a la API de bindings, que hará todo esto automáticamente por usted.

¿Ya estás utilizando GetX y quieres que tu proyecto sea lo más ágil posible? GetX tiene un gestor de dependencias simple y poderoso que le permite recuperar la misma clase que su BLoC o Controller con solo una líneas de código, sin contexto de Provider, sin inheritedWidget:

```dart
Controller controller = Get.put(Controller()); // Rather Controller controller = Controller();
```

En lugar de crear una instancia de su clase dentro de la clase que está utilizando, la está creando dentro de la instancia GetX, que la hará disponible en toda su aplicación. Entonces puede usar su Controller (o BLoC) normalmente.

```dart
controller.fetchApi();
```

Imagine que ha navegado a través de numerosas rutas y necesita datos que quedaron en su controlador, necesitaría un gestor de estado combinado con Providere o Get_it, ¿correcto? No con GetX. Solo necesita pedirle a GetX que "encuentre" su controlador, no necesita dependencias adicionales:

```dart
Controller controller = Get.find();
//Yes, it looks like Magic, Get will find your controller, and will deliver it to you. You can have 1 million controllers instantiated, Get will always give you the right controller.
```

Y luego podrá recuperar los datos de su controlador que se obtuvieron allí:

```dart
Text(controller.textFromApi);
```

¿Buscando lazy loading? Puede declarar todos sus controladores, y se llamará solo cuando alguien lo necesite. Puedes hacer esto con:

```dart
Get.lazyPut<Service>(()=> ApiMock());
/// ApiMock will only be called when someone uses Get.find<Service> for the first time
```

### Explicación completa

**Vea una explicación más detallada de la Gestión de dependencias [aquí](./docs/es_ES/dependency_management.md).**

# Utils

## Cambiar de tema

No utilice ningún widget de nivel superior que GetMaterialApp para actualizarlo. Esto puede activar claves duplicadas. Mucha gente está acostumbrada al enfoque prehistórico de crear un widget "ThemeProvider" solo para cambiar el tema de su aplicación, y esto definitivamente NO es necesario con GetX.

Puede crear su tema personalizado y simplemente agregarlo dentro de Get.changeTheme sin ningun boilerplate para eso:

```dart
Get.changeTheme(ThemeData.light());
```

Si desea crear algo así como un botón que cambia el tema con onTap, puede combinar dos APIs GetX para eso, la API que verifica si se está utilizando el tema oscuro y la API de cambio de tema, simplemente puede poner esto dentro de un onPressed:

```dart
Get.changeTheme(Get.isDarkMode? ThemeData.light(): ThemeData.dark());
```

Cuando el modo oscuro está activado, cambiará al tema claro, y cuando el tema claro esté activado, cambiará a oscuro.

Si quieres saber en profundidad cómo cambiar el tema, puedes seguir este tutorial en Medium que incluso enseña la persistencia del tema usando GetX:

- [Temas dinámicos en 3 líneas usando GetX](https://medium.com/swlh/flutter-dynamic-themes-in-3-lines-c3b375f292e3) - Tutorial de [Rod Brown](https://github.com/RodBr).

## Otras API avanzadas y configuraciones manuales

GetMaterialApp configura todo para usted, pero si desea configurar GetX manualmente utilizando APIs avanzadas.

```dart
MaterialApp(
  navigatorKey: Get.key,
  navigatorObservers: [GetObserver()],
);
```

También podrá usar su propio Middleware dentro de GetObserver, esto no influirá en nada.

```dart
MaterialApp(
  navigatorKey: Get.key,
  navigatorObservers: [GetObserver(MiddleWare.observer)], // Here
);
```

```dart
Get.arguments // give the current args from currentScreen

Get.previousArguments // give arguments of previous route

Get.previousRoute // give name of previous route

Get.rawRoute // give the raw route to access for example, rawRoute.isFirst()

Get.routing // give access to Rounting API from GetObserver

Get.isSnackbarOpen // check if snackbar is open

Get.isDialogOpen // check if dialog is open

Get.isBottomSheetOpen // check if bottomsheet is open

Get.removeRoute() // remove one route.

Get.until() // back repeatedly until the predicate returns true.

Get.offUntil() // go to next route and remove all the previous routes until the predicate returns true.

Get.offNamedUntil() // go to next named route and remove all the previous routes until the predicate returns true.

GetPlatform.isAndroid/isIOS/isWeb... //(This method is completely compatible with FlutterWeb, unlike the framework. "Platform.isAndroid")

Get.height / Get.width // Equivalent to the method: MediaQuery.of(context).size.height, but they are immutable. If you need a changeable height/width (like browser windows that can be scaled) you will need to use context.height and context.width

Get.context // Gives the context of the screen in the foreground anywhere in your code.

Get.contextOverlay // Gives the context of the snackbar/dialog/bottomsheet in the foreground anywhere in your code.

```

### Configuraciones globales opcionales

Puede crear configuraciones globales para GetX. Simplemente agregue Get.config a su código antes de insertar cualquier ruta o hágalo directamente en su GetMaterialApp

```dart
GetMaterialApp(
  enableLog: true,
  defaultTransition: Transition.fade,
  opaqueRoute: Get.isOpaqueRouteDefault,
  popGesture: Get.isPopGestureEnable,
  transitionDuration: Get.defaultDurationTransition,
  defaultGlobalState: Get.defaultGlobalState,
);

Get.config(
  enableLog = true,
  defaultPopGesture = true,
  defaultTransition = Transitions.cupertino
)
```

# Rompiendo cambios desde 2.0

1- Rx types:

Antes: StringX ahora: RxString

Antes: IntX ahora: RxInt

Antes: MapX ahora: RxMax

Antes: ListX ahora: RxList

Antes: NumX ahora: RxNum

Antes: RxDouble ahora: RxDouble

RxController y GetBuilder ahora se han fusionado, ya no necesita memorizar qué controlador desea usar, solo use GetXController, funcionará para gestión de estádo simple y también para reactivo.

2- Rutas Nombradas

Antes:

```dart
GetMaterialApp(
  namedRoutes: {
    '/': GetRoute(page: Home()),
  }
)
```

Ahora:

```dart
GetMaterialApp(
  getPages: [
    GetPage(name: '/', page:()=> Home()),
  ]
)
```

¿Por qué este cambio?

A menudo, puede ser necesario decidir qué página se mostrará desde un parámetro o un token de inicio de sesión, el enfoque anterior era inflexible, ya que no permitía esto.

Insertar la página en una función ha reducido significativamente el consumo de RAM, ya que las rutas no se asignarán en la memoria desde que se inició la aplicación, y también permitió hacer este tipo de enfoque:

```dart
GetStorage box = GetStorage();

GetMaterialApp(
  getPages: [
    GetPage(name: '/', page:(){  
      return box.hasData('token') ? Home() : Login();
    })
  ]
)
```

Esta librería siempre se actualizará e implementará nuevas características. Siéntase libre de ofrecer PRs y contribuir a ellas.
