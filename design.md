# Software desing

## Dependencies

## Patterns
(Introduction)

## Builder pattern
Builder is a creational design pattern, which allows constructing complex objects step by step. It is used in the `PdePreprocessing.java` file, whose responsability is to convert the sketch tabs, which are written in the processing language, into a single .java file that will be used to be compiled. This class for handling correctly this process, has 7 parameters, this means that its constructor should accept 7 parameters as input, which most of them are always used with their default values. This situation is not good because who uses this class should inserted every time all the parameters, increasing the possibility of generating an error. For this reason, the *PdePreprocessorBuilder* class is introduced in the same file, whose constructor accept only the sketch name. The other attributes are initially set to empty using the *Optional* class and they can be customized by using the setters implemented in the builder class. It also implemented the fluent interface because each builder's setters return itself, allowing the method chaining. Once the object is set correctly, the method *build* is called, which defines the default values for those parameters that are empty and then calls the constructor of the *PdePreprocessing* class to create the final object.
    
A potential problem of this pattern is that until the *build* method is called, there is not an useble object from the client side. So, if it is called in this situation, an error will arises. In addition, if once that the object is built can be still editable, other setters are needed also in the *PdePreprocessorBuilder* class, increasing significantly the number of lines of code. As alternative, a parameter object can be used. This solves the inconsistency present before calling the *build* method but it simply shifts the problem of inserting seven parameters from the *PdePreprocessing* class to the new parameter class. So, since the attributes once they are defined in the final object are immutable, the builder pattern is accettable.
  
![Class diagram that shows the builder pattern](./img/Builder_pattern.png)