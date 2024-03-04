# Class Diagrams
## Perspectives
We can use class diagrams at different stages of the software development lifecycle.
Therefore there are three different levels of perspectives - theese are:
* Conceptual
  * Language independent
  * Describes entities of the real world
  * Construct a diagram that represents the concepts in the domain
* Specification
  * Describing abstractions of software or components with specification and interfaces but without any reference to a specfic implementation
* Implementation
  * Diagrams are interpreted as a description of a software implementations in a particular technology or language

## Access Modifiers
```
+ public
- private
# protected
~ package local
```

## Relationships between classes
The types of relationships that are possible are:
* When one class is a "kind of" another class: the _is-a_ relationship.
* When there are associations between two classes.
  - One class "contains" another class: the _has-a_ relationship.
    - Composition: The contained item is part of the containing item (like an engine in a car).
    - Aggregation: I have a collection of things that can exists on their own (like airplanes at an airport).
  - One class "uses" another class.

### Arrows
![billede](https://user-images.githubusercontent.com/26361520/186847710-a4f2234f-e375-4660-9900-7a86a19d83bd.png)

### Association
Connects classes that are associated and interact with each other

![billede](https://user-images.githubusercontent.com/26361520/186848706-fc6d3f83-5fdf-484a-8d93-544180d98407.png)

### Inheritance
The hollow arrow is always directed towards the super/base/parent-class
If the parent class is abstract it's name is italicized

![billede](https://user-images.githubusercontent.com/26361520/186848994-ef6bb3bc-75cf-4d0c-b3a7-6ba81cf2832d.png)

### Realisation
This refers to the relationships between an interface and objects that implement this interface

![billede](https://user-images.githubusercontent.com/26361520/186849665-1c0ff223-5e33-4c1b-bf52-672efacd6818.png)


### Dependency
When an object of one class uses an object of another class in its method and this object is not stored in any field, 
this is modelled as an dependency. Dependency is a special case of association of two classes. Changes in one class will inexorably entail changes in the other.

![billede](https://user-images.githubusercontent.com/26361520/186850392-d16d253d-c525-4866-9ef3-9f2f5eac5201.png)


### Aggregation
A special type of relationship between classes when one class is a composite part of another class.
The child components can exist on their own even without being a part of the class-aggregator.

![billede](https://user-images.githubusercontent.com/26361520/186850659-65c9bec6-84ef-4d71-8286-dad4d550bec3.png)



### Composition
A type of aggregation that when the aggregate class instance is destroyed, it's constituents are also destroyed.

![billede](https://user-images.githubusercontent.com/26361520/186851126-e5ebbde6-b961-49be-8f2e-58602ebf836f.png)



