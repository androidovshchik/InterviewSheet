# Паттерны (Шаблоны)
> повторяемая архитектурная конструкция, представляющая собой решение проблемы проектирования, в рамках некоторого часто возникающего контекста.

## Порождающие 
> паттерны беспокоятся о гибком создании объектов без внесения в программу лишних зависимостей.

<details>
  <summary>Builder</summary>

> позволяет создавать сложные объекты пошагово. Строитель даёт возможность использовать один и тот же код строительства для получения разных представлений объектов.

```kotlin
class Person private constructor(
    val firstName: String?,
    val lastName: String?,
    val age: Int?
) {
    // Внутренний класс Builder
    class Builder {
        private var firstName: String? = null
        private var lastName: String? = null
        private var age: Int? = null

        fun setFirstName(firstName: String) = apply { this.firstName = firstName }
        fun setLastName(lastName: String) = apply { this.lastName = lastName }
        fun setAge(age: Int) = apply { this.age = age }

        fun build() = Person(firstName, lastName, age)
    }

    override fun toString(): String {
        return "Person(firstName=$firstName, lastName=$lastName, age=$age)"
    }
}

fun main() {
    val person = Person.Builder()
        .setFirstName("Иван")
        .setLastName("Иванов")
        .setAge(30)
        .build()

    println(person)
}
```
</details>

<details>
  <summary>Prototype</summary>

> используется для создания новых объектов путем копирования уже существующих объектов (прототипов). Это позволяет создать объект с уже существующими значениями, а затем модифицировать его по мере необходимости.

```kotlin
data class Person(
    val firstName: String,
    val lastName: String,
    val age: Int
)

fun main() {
    // Создаем исходный объект
    val originalPerson = Person("Иван", "Иванов", 30)

    // Создаем клон объекта с некоторыми изменениями
    val clonedPerson = originalPerson.copy(age = 35)

    println("Исходный объект: $originalPerson")
    println("Клонированный объект: $clonedPerson")
}
```
```kotlin
class Car(
    var make: String,
    var model: String,
    var year: Int
) : Cloneable {
    public override fun clone(): Car {
        return super.clone() as Car
    }

    override fun toString(): String {
        return "Car(make='$make', model='$model', year=$year)"
    }
}

fun main() {
    val originalCar = Car("Toyota", "Camry", 2020)
    val clonedCar = originalCar.clone()

    // Изменим модель клонированной машины
    clonedCar.model = "Corolla"

    println("Исходная машина: $originalCar")
    println("Клонированная машина: $clonedCar")
}
```
</details>

<details>
  <summary>Singleton</summary>

> гарантирует, что у класса есть только один экземпляр, и предоставляет к нему глобальную точку доступа.

```kotlin
object Singleton {
    var counter: Int = 0

    fun doSomething() {
        counter++
        println("Counter: $counter")
    }
}

fun main() {
    // Вызов метода Singleton
    Singleton.doSomething() // Counter: 1
    Singleton.doSomething() // Counter: 2

    // Обращение к свойству
    println(Singleton.counter) // 2
}
```
```kotlin
class ThreadSafeSingleton private constructor() {
    init {
        println("ThreadSafeSingleton инициализирован.")
    }

    companion object {
        @Volatile
        private var instance: ThreadSafeSingleton? = null

        @Synchronized
        fun getInstance(): ThreadSafeSingleton {
            if (instance == null) {
                instance = ThreadSafeSingleton()
            }
            return instance!!
        }
    }

    fun showMessage() {
        println("Hello from ThreadSafeSingleton!")
    }
}

fun main() {
    val singleton1 = ThreadSafeSingleton.getInstance()
    singleton1.showMessage()

    val singleton2 = ThreadSafeSingleton.getInstance()
    println(singleton1 === singleton2) // true
}
```
</details>

Фабрика отвечает за создание объектов одним методом, определяющим нужный класс на основе параметров. Фабричный метод использует наследование и позволяет подклассам определять тип создаваемого объекта. Абстрактная фабрика управляет целыми семействами связанных объектов, не указывая их конкретные классы.

- Фабрики скрывают детали создания объектов, упрощая инстанцирование.
- Абстрактные фабрики обеспечивают создание совместимых продуктов, способствуя консистентности.
- Фабричный метод передает ответственность за создание объектов подклассам, что увеличивает гибкость.
  
<details>
  <summary>Factory Method</summary>

> используется для создания объектов, предоставляя интерфейс для создания объектов в суперклассе, но позволяя подклассам изменять тип создаваемых объектов. Этот паттерн помогает при создании объектов, когда точный класс создаваемого объекта неизвестен до выполнения программы.

```kotlin
// Интерфейс Animal
interface Animal {
    fun speak(): String
}

// Реализация Cat
class Cat : Animal {
    override fun speak(): String {
        return "Meow"
    }
}

// Реализация Dog
class Dog : Animal {
    override fun speak(): String {
        return "Woof"
    }
}

// Фабрика AnimalFactory
abstract class AnimalFactory {
    abstract fun createAnimal(): Animal
}

// Фабрика CatFactory для создания Cat
class CatFactory : AnimalFactory() {
    override fun createAnimal(): Animal {
        return Cat()
    }
}

// Фабрика DogFactory для создания Dog
class DogFactory : AnimalFactory() {
    override fun createAnimal(): Animal {
        return Dog()
    }
}

// Использование фабричных методов
fun main() {
    // Создание фабрики для котов
    val catFactory: AnimalFactory = CatFactory()
    val cat: Animal = catFactory.createAnimal()
    println("Cat says: ${cat.speak()}") // Output: Cat says: Meow

    // Создание фабрики для собак
    val dogFactory: AnimalFactory = DogFactory()
    val dog: Animal = dogFactory.createAnimal()
    println("Dog says: ${dog.speak()}") // Output: Dog says: Woof
}
```
</details>

<details>
  <summary>Abstract Factory</summary>

> предоставляет интерфейс для создания семейств связанных или зависимых объектов, не специфицируя их конкретных классов. Это паттерн "фабрика фабрик", который позволяет создавать группы связанных объектов без привязки к конкретным классам.

```kotlin
// Интерфейс Button
interface Button {
    fun paint()
}

// Реализация кнопки для Windows
class WindowsButton : Button {
    override fun paint() {
        println("Рисуем кнопку в стиле Windows")
    }
}

// Реализация кнопки для MacOS
class MacOSButton : Button {
    override fun paint() {
        println("Рисуем кнопку в стиле MacOS")
    }
}

// Интерфейс TextField
interface TextField {
    fun render()
}

// Реализация текстового поля для Windows
class WindowsTextField : TextField {
    override fun render() {
        println("Отображаем текстовое поле в стиле Windows")
    }
}

// Реализация текстового поля для MacOS
class MacOSTextField : TextField {
    override fun render() {
        println("Отображаем текстовое поле в стиле MacOS")
    }
}

// Абстрактная фабрика GUIFactory
interface GUIFactory {
    fun createButton(): Button
    fun createTextField(): TextField
}

// Фабрика WindowsFactory для создания элементов Windows
class WindowsFactory : GUIFactory {
    override fun createButton(): Button {
        return WindowsButton()
    }

    override fun createTextField(): TextField {
        return WindowsTextField()
    }
}

// Фабрика MacOSFactory для создания элементов MacOS
class MacOSFactory : GUIFactory {
    override fun createButton(): Button {
        return MacOSButton()
    }

    override fun createTextField(): TextField {
        return MacOSTextField()
    }
}

// Клиентский код
fun main() {
    // Фабрика для Windows
    val windowsFactory: GUIFactory = WindowsFactory()
    val windowsButton: Button = windowsFactory.createButton()
    val windowsTextField: TextField = windowsFactory.createTextField()

    windowsButton.paint()           // Output: Рисуем кнопку в стиле Windows
    windowsTextField.render()       // Output: Отображаем текстовое поле в стиле Windows

    // Фабрика для MacOS
    val macFactory: GUIFactory = MacOSFactory()
    val macButton: Button = macFactory.createButton()
    val macTextField: TextField = macFactory.createTextField()

    macButton.paint()               // Output: Рисуем кнопку в стиле MacOS
    macTextField.render()           // Output: Отображаем текстовое поле в стиле MacOS
}
```
</details>

<details>
  <summary>Simple Factory</summary>

> это объект для создания других объектов. Формально фабрика — это функция или метод, который возвращает объекты изменяющегося прототипа.

```kotlin
// Интерфейс Vehicle
interface Vehicle {
    fun drive()
}

// Реализация Car
class Car : Vehicle {
    override fun drive() {
        println("Вождение автомобиля")
    }
}

// Реализация Bike
class Bike : Vehicle {
    override fun drive() {
        println("Вождение велосипеда")
    }
}

// Фабрика VehicleFactory для создания транспортных средств
class VehicleFactory {
    companion object {
        fun createVehicle(type: String): Vehicle {
            return when (type) {
                "Car" -> Car()
                "Bike" -> Bike()
                else -> throw IllegalArgumentException("Неизвестный тип транспортного средства")
            }
        }
    }
}

// Использование фабрики
fun main() {
    val car: Vehicle = VehicleFactory.createVehicle("Car")
    car.drive()  // Output: Вождение автомобиля

    val bike: Vehicle = VehicleFactory.createVehicle("Bike")
    bike.drive()  // Output: Вождение велосипеда

    // Если передан неизвестный тип
    try {
        val unknown: Vehicle = VehicleFactory.createVehicle("Plane")
    } catch (e: IllegalArgumentException) {
        println(e.message)  // Output: Неизвестный тип транспортного средства
    }
}
```
</details>

## Структурные
> паттерны

## Поведенческие
> паттерны