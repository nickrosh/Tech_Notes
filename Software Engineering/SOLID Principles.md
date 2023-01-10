

These principles help you write better, more modular object oriented code that can be extended and used by a large team.



### Single Responsibility Principle
Every component of code, classes, or functions should have one and only one responsibility. This makes it much easier to test and localize errors.


### Open-Closed Principle
You should not need to modify the code you have already written to accommodate ne functionality, but simply add what you now need. Make it open to extension, closed to modification


### Liskov Substitution Principle
Functions that use pointers must be able to use objects of derived classes without knowing it. Aka: derived classes must be substitutable for their base class. We can do this in Python with Abstract Base Classes and composition.


### Interface Segregation Principle
Many client-specific interface are better than one general purpose interface. A class should only have the interface needed, and avoid unnecessary methods that aren't needed.


### Dependency Inversion Principle
Abstractions should not depend on abstractions. So rather than pushing info from **Object A** to **Object B**, we create an interface that takes the info as input and passes to the others, like an API. That way, changes in the info in **Object A**  won't break **Object B**.