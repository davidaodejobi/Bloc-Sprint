---
title: Bloc - Creating a Cubit
---

##Cubit
* Cubit is a class: In simple words, a Cubit is a special type of class in Flutter that helps manage the state of your application. It's designed to handle a specific part of your app's state.

- State changes through functions: Cubits allow you to define functions that can be called to trigger changes in the state. These functions represent actions or events that occur in your app and result in a different state.
  
* States represent app's state: The output of a Cubit is called a "state." States represent different snapshots or versions of your app's state. For example, if you have a counter app, the states could be different numbers: 0, 1, 2, and so on.
  

- UI reacts to state changes: The UI components in your app can be notified of state changes and update themselves accordingly. For example, if the counter Cubit emits a new state with the number 3, the UI component listening to that Cubit will receive the new state and can redraw itself to display the updated number

####Creating a Cubit

Here is an example of a Cubit that manages the state of an arithmetic operation:

```dart
class ArithmeticCubit extends Cubit<int> {
  ArithmeticCubit() : super(0);
}
```

The Cubit class is a generic class that takes a type parameter. This type parameter represents the type of state that the Cubit will manage. In this case, the Cubit will manage an integer value.

The Cubit class has a constructor that takes an initial state. In this case, the initial state is 0. The initial state is the state that the Cubit will emit when it is first created. In the example above, the initial state is 0 but we in instances where we need to pass in a different initial state, we can do so by passing it into the super constructor.

Here is an example of how to create an instance of the ArithmeticCubit:

```dart
class ArithmeticCubit extends Cubit<int> {
  ArithmeticCubit(int initialState) : super(initialState);
}
```

By creating our cubit this way, we can pass in any initial state we want. For example, we can pass in 10 as the initial state:

```dart
final cubit = ArithmeticCubit(10);
```

####State Changes
To change the state of a **Cubit**, we call the **emit** method and pass in the new state. For example, if we want to change the state to 1, we can do so by calling emit(1):

```dart
class ArithmeticCubit extends Cubit<int> {
  ArithmeticCubit() : super(0);

  void increment() => emit(state + 1);
}
```

In the example above, we have a function called increment that calls emit and passes in the current state + 1. This will result in the Cubit emitting a new state with the value of 1.

> Note: The emit method is protected and can only be called from within the Cubit class. This is to ensure that the state of a Cubit can only be changed by the Cubit itself.

####Using a Cubit
To use a Cubit, we need to create an instance of it and then call its functions to trigger state changes. For example, if we want to use the ArithmeticCubit from above, we can do so by creating an instance of it and then calling its increment function:

```dart
void main() {
  final cubit = ArithmeticCubit();
  print(cubit.state); // prints 0
  cubit.increment();
  print(cubit.state); // prints 1
  cubit.increment();
  print(cubit.state); // prints 2
  cubit.close();
}
```

In the example above, we create an instance of the ArithmeticCubit and then call its increment function twice. After each call to increment, we print out the current state of the Cubit. The output of the above code is:

```dart
0
1
2
```

> Note: The close method is called to close the Cubit when it is no longer needed. This is important because it allows the Cubit to clean up any resources that it might be using. For example, if the Cubit is listening to a stream, it will close the stream when it is closed.

> Note: You can no longer use a Cubit after it has been closed. If you try to call a function on a closed Cubit, it will throw an error. 

####Observing a Cubit
When a Cubit emits a new state, we can listen to it and react to the state change. For example, if we want to listen to the ArithmeticCubit from above, we can do so by overriding the onChange method:

```dart
class ArithmeticCubit extends Cubit<int> {
  ArithmeticCubit() : super(0);

  void increment() => emit(state + 1);

  @override
  void onChange(Change<int> change) {
    print(change);
    super.onChange(change);
  }
}
```

In the example above, we override the onChange method and print out the change. The onChange method is called whenever the Cubit emits a new state.

Here is an example of how to use the ArithmeticCubit from above:

```dart
void main() {
  final cubit = ArithmeticCubit();
  cubit.increment();
  cubit.increment();
  cubit.increment();
  cubit.close();

  /* 
  simplified version of same code
  ArithmeticCubit()
    ..increment()
    ..increment()
    ..increment()
    ..close();
  */
}
```

In the example above, we create an instance of the ArithmeticCubit and then call its increment function three times. After each call to increment, we close the Cubit. The output of the above code is:

```dart
Change { currentState: 0, nextState: 1 }
Change { currentState: 1, nextState: 2 }
Change { currentState: 2, nextState: 3 }
```
> Note: A Change object is passed into the onChange method. The Change object contains the currentState and the nextState. This is because the onChange method is called before the state is changed. This means that the currentState is the state before the change and the nextState is the state after the change. 

The output above is presented in kinda of a weird way. The reason for this is because the onChange method is called before the state is changed. This means that the currentState is the state before the change and the nextState is the state after the change. This is useful because it allows us to see what the state was before the change and what the state will be after the change.

If we want to get the next state that the Cubit will emit, we can call the .nextState getter on the Change object:

```dart
class ArithmeticCubit extends Cubit<int> {
  ArithmeticCubit() : super(0);

  void increment() => emit(state + 1);

  @override
  void onChange(Change<int> change) {
    print(change.nextState);
    super.onChange(change);
  }
}
```

#### Bloc Observer

The BlocObserver comes in handy when you want to observe all the state changes in a place. It is a class that has a bunch of methods that are called whenever a Cubit emits a new state. For example, if we want to observe all the state changes in our app, we can do so by extending the BlocObserver class and overriding its methods:

```dart
class SimpleBlocObserver extends BlocObserver {
  @override
  void onChange(BlocBase bloc, Change change) {
    super.onChange(bloc, change);
    print('${bloc.runtimeType} $change');
  }
}
```

In the example above, we override the onChange method and print out the bloc type ( in this case, the ArithmeticCubit) and the change. The onChange method is called whenever a Cubit emits a new state.

>Note : The internal OnChange override is called before the BlocObserver's OnChange override.

Here is an example of how to use the SimpleBlocObserver from above:

```dart
void main() {
  Bloc.observer = SimpleBlocObserver();
  final cubit = ArithmeticCubit();
  cubit.increment();
  cubit.increment();
  cubit.increment();
  cubit.close();
}
```

In the example above, we create an instance of the ArithmeticCubit and then call its increment function three times. After each call to increment, we close the Cubit. The output of the above code is:

```dart
Change { currentState: 0, nextState: 1 }
Change { currentState: 1, nextState: 2 }
Change { currentState: 2, nextState: 3 }
ArithmeticCubit Change { currentState: 0, nextState: 1 }
ArithmeticCubit Change { currentState: 1, nextState: 2 }
ArithmeticCubit Change { currentState: 2, nextState: 3 }
```

#### Error Handling
In Bloc, errors are just states. This means that we can handle errors just like we handle any other state. For example, if we want to handle errors in our ArithmeticCubit, we can do so by overriding the onError method:

```dart
class ArithmeticCubit extends Cubit<int> {
  ArithmeticCubit() : super(0);

  void increment() {
    addError(Exception('increment error!'), StackTrace.current);
    emit(state + 1);
  }

  @override
  void onChange(Change<int> change) {
    print(change);
    super.onChange(change);
  }

  @override
  void onError(Object error, StackTrace stackTrace) {
    print('$error, $stackTrace');
    super.onError(error, stackTrace);
  }
}
```

In the example above, we override the onError method and print out the error and the stack trace. The onError method is called whenever the Cubit emits an error. 

>Note : The **On Error** can only be called within the **Cubit** class to handle errors for that particular Cubit.

Here is a sample output of the above code:

```dart
Exception: increment error!, Error
    at Object.StackTrace_current (<anonymous>:3200:40)
    at main (<anonymous>:3329:14)
    at <anonymous>:4729:7
    at <anonymous>:4712:7
    at dartProgram (<anonymous>:4723:5)
    at <anonymous>:4731:3
    at replaceJavaScript (https://dartpad.dev/scripts/frame.js:19:19)
    at messageHandler (https://dartpad.dev/scripts/frame.js:100:13)
Change { currentState: 0, nextState: 1 }
ArithmeticCubit Change { currentState: 0, nextState: 1 }
```

>Note : The **OnError** override is called before the BlocObserver's overrides.
