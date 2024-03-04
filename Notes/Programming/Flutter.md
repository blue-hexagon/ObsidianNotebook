# Flutter
## Commands
`flutter doctor`

`flutter clean ios -> flutter pub get`

`flutter clean android -> flutter pub get`

`flutter pub add <package_name>`

`flutter pub outdated` # To see which dependencies have newer versions available

## Configure Firebase
`flutter pub add <firebase_packages>`

`dart pub global activate flutterfire_cli` # Remember to add to OS env-path: C:\Users\$USERNAME\AppData\Local\Pub\Cache\bin

## Flutter Devtool
[scrcpy](https://github.com/Genymobile/scrcpy) # Remember to add to OS env-path

## Flutter pubspec.yaml
```yaml
 assets:
   - assets/graphics/

 fonts:
   - family: Poppins
     fonts:
       - asset: assets/fonts/Poppins-Medium.ttf
         weight: 400
```

## Flutter Routing
```dart
import 'package:flutter/material.dart';
import 'package:flutter_testing/world_time/pages/home.dart';
import 'package:flutter_testing/world_time/pages/choose_location.dart';
import 'package:flutter_testing/world_time/pages/loading.dart';

void main() => runApp(MaterialApp(
      initialRoute: '/', // Remove later on, for testing purposes
      routes: {
        '/': (context) => const Loading(),
        '/home': (context) => const Home(),
        '/location': (context) => const ChooseLocation()
      },
    ));
```

### Pushing Routes
```dart
Navigator.pushReplacementNamed(context, '/home');
Navigator.pushNamed(context, '/home');

```

#### Sending Arguments to a Route
```dart
class _LoadingState extends State<Loading> {
  void setupWorldTime() async {
    WorldTime berlin = WorldTime(
        locationName: "Berlin",
        flagIconPath: "germany.png",
        timezoneEndpoint: "Europe/Berlin");
    await berlin.getTime();
    Navigator.pushReplacementNamed(context, '/home', arguments: {
      'location': berlin.locationName,
      'flag': berlin.flagIconPath,
      'time': berlin.time
    });
  }

  @override
  void initState() {
    super.initState();
    setupWorldTime();
  }
....

class _HomeState extends State<Home> {
  Map data = {};

  @override
  Widget build(BuildContext context) {
    data = ModalRoute.of(context)!.settings.arguments as Map;
    ....
....
```
---------------------------------------------------------------------------------
# Dart
## String Interpolation
```dart
    _selectedIndex = 0;
    print('${_selectedIndex}'); // Automatically calls the toString() method on _selectedIndex
    print(_selectedIndex.toString()); // The same as above
```

## setState
*Only Stateful Widgets can use `setState((){})`*

Use setState to trigger the `build` function of a statefull widget:
```dart
setState(() {
  age += 1;
});
```

Stateful widgets have lifecycle methods we can tap into:
- `initState()`
   - The first method to be called once our state object has been created. is only called once, when the widget has first been created.

```dart
@override
void initState() {
  //TODO: Implement initstate
  super.initState();
}
```

- `Build()`
   - Builds the widget tree, is triggered everytime we use `setState`
- `Dispose()`
   - When the widget or state object is removed.


## Null Coalescing and More
`variableWhichMMayBeNull ?? variableWhichMayAlsoBeNull ?? variableWhichMayAgainBeNull`


`prop = objectPropDrillingWherePropertyMayBeNull?.stringPropertyThatMayBeNull`

```dart
String name;
name ??= "Adam"; // name is null and "Adam" is assigned to name
print(name); //-> Adam
name ??= "Adrian"; // name is not null and "Adrian" is hence not assigned to name
print(name); //-> Adam
```
## Factory Constructors
```dart
class Cat {
  final String name;
  Cat(this.name);
  factory Cat.fluffball() {
    return Cat('Fluff Ball');
  }
}

void test() {
  final fluffBall = Cat.fluffBall();
  print(fluffBall.name);
}
```
## Custom Operators
```dart
class Cat extends Object {
  final String name;
  Cat(this.name);
  
  @override
  bool operator ==(covariant Cat other) => other.name == name;
  
  @override
  int get hashCode => name.hashCode;
}
```
## Extensions
```dart
extension Run on Cat {
  void run() {
    print('Cat $name is running');
  }
}

void test() {
  final meow = Cat('Fluffers');
  meow.run();
}
```
## Getter Extension
```dart
class Person {
  final String firstName;
  final String lastName;
  Person(this.firstName, this.lastName);
}

extension FullName on Person {
  String get fullName => '$firstName $lastName';
}

void test() {
  final foo = Person('Foo', 'Bar');
  print(foo.fullName);
}
```
## Futures
```dart
Future<int> aFutureFunction(int a) {
  return Future.delayed(const Duration(seconds: 3), () => a * 2);
}

void test() async {
  final result = await aFutureFunction(8);
  print(result); // prints 16 after 3 seconds.
}
```
### Await Example
```dart
  void getData() async {
    late String username;
    await Future.delayed(Duration(seconds: 3), () {
      username = "Dirk";
    });
    Future.delayed(Duration(seconds: 3), () {
      print("$username is a developer and musician!");
    });
    print("End of function")
    //> End of function
    //> Dirk is a developer and musician!
  }
```
## Streams
```dart
Stream<String> getName() {
  return Stream.value('Foo');
}

void test() async {
  await for (final value in getName()) {
    print(value);
  }
  print('Stream finished working);
}
```
------------------------------
```dart
Stream<String> getName() {
  return Stream.periodic(const Duration(seconds: 1), (value) {
    return 'Foo';
  }
}

void test() async {
  await for (final value in getName()) {
    print(value);
  }
}
```
## Generators

### Async
```dart
Stream<Iterable<int>> getOneTwoThree() async* {
  ...
}
```
### Sync
```dart
Iterable<int> getOneTwoThree() sync* {
  yield 1;
  yield 2;
  yield 3;
}

void test() {
  for (final value in getOneTwoThree()) {
    print(value);
  }
  // Or: print(getOneTwoThree());
}
```
## Generics
```dart
class PairOfIntegers {
  final int value1;
  final int value2;
  PairOfIntegers(this.value1, this.value2);
}

class Pair<A, B> {
  final A value1;
  final B value2;
  Pair(this.value1, this.value2);
}

void test() {
  final names = Pair('Foo', 'Bar');
  final ages = Pair(22, 34);
}
```
