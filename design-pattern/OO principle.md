1. [Single-responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle), **SRP**

   *every module or class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class, module or function.* 

2. [Open–closed principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle), **OCP**
     *software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification*

3. [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle), **LSP**

       *if S is a subtype of T, then objects of type T may be replaced with objects of type S (i.e. an object of type T may be substituted with any object of a subtype S) without altering any of the desirable properties of the program (correctness, task performed, etc.)*

4. [Dependency inversion principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle), **DIP**

     - High-level modules should not depend on low-level modules. Both should depend on abstractions (e.g. interfaces).
     - Abstractions should not depend on details. Details (concrete implementations) should depend on abstractions.

5. [Interface segregation principle](https://en.wikipedia.org/wiki/Interface_segregation_principle), **ISP**

     *no client should be forced to depend on methods it does not use. ISP splits interfaces that are very large into smaller and more specific ones so that clients will only have to know about the methods that are of interest to them.*

6. [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter), **LoD**

     - Each unit should have only limited knowledge about other units: only units "closely" related to the current unit.
     - Each unit should only talk to its friends; don't talk to strangers.
     - Only talk to your immediate friends.


