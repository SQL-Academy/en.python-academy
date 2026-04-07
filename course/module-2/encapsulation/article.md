# Encapsulation in Python

Imagine you're designing a safe - from the outside, there's just an elegant control panel, while all the complex mechanisms and valuables are securely hidden inside. 🔐 That's how encapsulation works in Python: it allows you to hide implementation details, leaving only a convenient and secure interface for interacting with objects. Let's figure out how to create such "software safes" and why it's so important!

## What is Encapsulation?

> Encapsulation is an OOP principle that involves bundling the data and methods that work with it into a single object and restricting access to the internal state of the object from the external environment.

In other words, encapsulation allows you to:

1. **Hide** implementation details
2. **Protect** data from uncontrolled changes
3. **Provide** a controlled interface for working with the object

Encapsulation is like using a TV remote control (interface) without knowing exactly how the TV processes your commands internally (implementation).

## Access Levels in Python

Unlike some strongly typed languages (Java, C++), Python doesn't have strict mechanisms for defining access levels to attributes and methods. Instead, Python follows the "we're all consenting adults here" philosophy and uses naming conventions:

| Prefix                 | Access Type | Description                                             |
| ---------------------- | ----------- | ------------------------------------------------------- |
| No prefix              | Public      | Accessible from anywhere                                |
| Single underscore `_`  | Protected   | Should not be used outside the class and its subclasses |
| Double underscore `__` | Private     | Should not be used outside the class                    |

### 1. Public Attributes and Methods

Attributes and methods without a prefix are accessible from anywhere in the program:

```python
class Person:
    def __init__(self, name, age):
        self.name = name  # Public attribute
        self.age = age    # Public attribute

    def greet(self):      # Public method
        return f"Hello, my name is {self.name}. I am {self.age} years old."

# Creating an object
person = Person("Anna", 25)

# Accessing public attributes and methods
print(person.name)
print(person.age)
print(person.greet())

# Changing public attributes
person.name = "Maria"
print(person.greet())
```

### 2. Protected Attributes and Methods (with a single underscore)

Attributes and methods with a single underscore are considered protected. This convention indicates that they should not be used outside the class or its subclasses:

```python
class BankAccount:
    def __init__(self, owner, balance):
        self.owner = owner          # Public attribute
        self._balance = balance     # Protected attribute

    def _calculate_interest(self):  # Protected method
        return self._balance * 0.05

    def add_interest(self):         # Public method
        interest = self._calculate_interest()
        self._balance += interest
        return f"New balance: {self._balance}"

# Creating an account
account = BankAccount("Ivan", 1000)

# Proper use through public methods
print(account.add_interest())

# Technically, we can access protected members, but it's not recommended
print(account._balance)
```

### 3. Private Attributes and Methods (with a double underscore)

Attributes and methods with a double underscore undergo "name mangling" and cannot be directly accessed outside the class:

```python
class SecretAgent:
    def __init__(self, name, code_name):
        self.name = name                  # Public attribute
        self.__code_name = code_name      # Private attribute

    def __secret_mission(self):           # Private method
        return f"Agent {self.__code_name} on a secret mission"

    def report(self):                     # Public method
        return f"Agent {self.name}'s report: {self.__secret_mission()}"

# Creating an agent
agent = SecretAgent("James Bond", "007")

# Access through a public method
print(agent.report())

# Attempt to directly access private members
try:
    print(agent.__code_name)
except AttributeError as e:
    print(f"Error: {e}")

# Python doesn't completely prohibit access, but makes it more complex
print(agent._SecretAgent__code_name)
```

## Getters and Setters

To provide controlled access to attributes, getter and setter methods are used:

```python
class Person:
    def __init__(self, name, age):
        self.__name = name    # Private attribute
        self.__age = age      # Private attribute

    # Getter for name
    def get_name(self):
        return self.__name

    # Setter for name with validation
    def set_name(self, name):
        if isinstance(name, str) and len(name) > 0:
            self.__name = name
        else:
            raise ValueError("Name must be a non-empty string")

    # Getter for age
    def get_age(self):
        return self.__age

    # Setter for age with validation
    def set_age(self, age):
        if isinstance(age, int) and 0 <= age <= 120:
            self.__age = age
        else:
            raise ValueError("Age must be an integer between 0 and 120")

# Creating an object
person = Person("Alex", 30)

# Using getters and setters
print(f"Name: {person.get_name()}, Age: {person.get_age()}")

# Changing values through setters
person.set_name("Michael")
person.set_age(35)
print(f"Name: {person.get_name()}, Age: {person.get_age()}")

# Validation check
try:
    person.set_age(150)
except ValueError as e:
    print(f"Error: {e}")
```

## Properties

Python provides an elegant way to implement getters and setters through the `@property` decorator:

```python
class Temperature:
    def __init__(self, celsius=0):
        self.__celsius = celsius

    # Getter
    @property
    def celsius(self):
        return self.__celsius

    # Setter
    @celsius.setter
    def celsius(self, value):
        if value < -273.15:
            raise ValueError("Temperature cannot be below absolute zero")
        self.__celsius = value

    # Computed property
    @property
    def fahrenheit(self):
        return self.__celsius * 9/5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        self.__celsius = (value - 32) * 5/9

# Creating an object
temp = Temperature(25)

# Using properties as regular attributes
print(f"Temperature: {temp.celsius}°C = {temp.fahrenheit}°F")

# Changing temperature in Celsius
temp.celsius = 30
print(f"Temperature: {temp.celsius}°C = {temp.fahrenheit}°F")

# Changing temperature in Fahrenheit
temp.fahrenheit = 68
print(f"Temperature: {temp.celsius}°C = {temp.fahrenheit}°F")

# Properties with validation protect against errors
try:
    temp.celsius = -300
except ValueError as e:
    print(f"Error: {e}")
```

### Read-Only Properties

Properties can be used to create read-only attributes:

```python
import math

class Circle:
    def __init__(self, radius):
        self.__radius = radius

    @property
    def radius(self):
        return self.__radius

    @radius.setter
    def radius(self, value):
        if value > 0:
            self.__radius = value
        else:
            raise ValueError("Radius must be positive")

    # Read-only properties (no setters)
    @property
    def area(self):
        return math.pi * self.__radius ** 2

    @property
    def circumference(self):
        return 2 * math.pi * self.__radius

# Creating a circle and using properties
circle = Circle(5)
print(f"Radius: {circle.radius}, Area: {circle.area:.2f}")

# Changing the radius, area recalculates automatically
circle.radius = 7
print(f"Radius: {circle.radius}, Area: {circle.area:.2f}")

# Cannot change a read-only property
try:
    circle.area = 100
except AttributeError as e:
    print(f"Error: {e}")
```

## Advantages of Encapsulation

Applying encapsulation provides the following advantages:

1. **Control of data access** — checking values before setting
2. **Implementation flexibility** — ability to change internal implementation without changing the interface
3. **Data security** — protection against accidental changes
4. **Interface simplification** — hiding complex logic behind a simple API

## Understanding Check

**Which access level is most appropriate for an attribute that should be accessible for reading but should not be changed outside the class?**


## Conclusion

Encapsulation in Python acts as a smart security system — it doesn't build impenetrable walls, but creates clear boundaries and indicates the right ways to interact with objects. Next time, we'll learn about polymorphism — a principle that allows objects to change form, like chameleons, while maintaining a unified interface. 🦎
