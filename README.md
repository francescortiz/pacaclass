
PacaClass
=========

Javascript pythonish multiple inheritance class system.


About
----------------

Framework to work with class inheritance. It is pythonish, so remember:

  - You have multiple inheritance
  - Always use this ot access methods or atributes
  - Everything is public... well, we are on javascript, do your tricks if you need to.
  
 
How does pacaclass compare to Twitter Flight
---------------
  
Now that twitter has released [Flight http://twitter.github.com/flight/], a javascript library that ,among other things, follows a similar principle, it is good to see what differences you will find.
Instead of multiple inheritance they use mixins, a way of adding functionality to classes. Here is a list of functionalities you miss with twitter flight:

- real multiple inheritance (mixins overwrite your classes, instead of your class overwriting the superclasses).
- the "isInstance" method supports multiple inheritance.
- When you declare the classes you define what you want to extend, you don't have to look anywhere else to see what functionalities you added to the class.
- pacaclass gives you access to the prototype of superclasses. Twitter's Flight mixins overwrite your class.

Then, there are other features that you don't get with pacaclass, because it is only fucused in multiple inheritance, but that you will get with pacaclass-lib:

- Event system (based on ActionScript 3 event system).
- Dom integration (jquery or native).
- Template system (implemented with pure templates), but easily extendable.
- Gaming library (alpha state - usable for complex animations).


USAGE
-----------

- PacaClass([className], [main superclass], [superclass], [superclass], ...);
  * Returns a subclass of the given classes
  * it popuplates [class].prototype.__class__ with a reference to the created class
  * if first argument is a string, it is used to populate [class].prototype.__class__.name
  * it popuplates [class].supers with an array af all superclassses.
    TODO: prevent repetitions in [class].supers array
- PacaClass.include("path/to/javascript/file.js", [async=false], [async_callback]);
  * Behaves like import
  
Then, for the instances you have:

- [instance].isInstance([class]);
  * like instanceof, but with support for multiple inheritance
- [instance].getSuper([superclass])
  * returns the specified super prototype.

  
EXAMPLE
----------------

Sample class

    var C = PacaClass(A, B); (function() { var public = C.prototype;

        public.someVar = "somveValue";
        public.c = "99";
        C.public_static_var = 'something';
        
        public.constructor = function(){
            this.getSuper(A).constructor.call(this, 'argument1', 'argument2');
            log("C constructor");
        }

        public.doC = function() {
            log(this.c);
        }

        public.whoAmI = function() {
            log("i am C and my supers say: ");
            this.getSuper(A).whoAmI.call(this); // calls whoAmI in A
            this.getSuper(B).whoAmI.call(this);  // calls whoAmI in B
        }
        
        public.customeEvent = function(event) {
            log("C.customEvent", "event.data = ", event.data, "this.someVar = ",this.someVar);
        }

        C.public_static_method = function() {
            log('This is a static method');
        };

    })();


Tests:

    var c = new C();

    c.isInstance(A); // true
    c.isInstance(B); // true

    c.doC(); // outputs: "[Object object]"
    c.whoAmI(); // outputs: "i am C and my supers say:\nI amb A\nI am B"
    C.static_method(); // outputs: "This is a static method"


Extras
-----------

### PacaClass.include
Loads javascript dynamically.

    // Makes code portable
    PacaClass.settings.JS_PATH = '/path/';

    // Code execution stops here until the script is available
    PacaClass.include("file.js");

    // Don't lock code execution.
    PacaClass.include("file.js", false); 

    // When file.js is loaded, execute file_loaded("file.js");
    PacaClass.include("file.js", false, file_loaded); 
    
### log
Simple logger.

    log('something');
    

Debugging tips
-------------------

- All pacaclass prototypes come with a __name__ attribute, and all instances come with __class__.__name__, if you defined a name for the class.
- In crome classes appear encapsulated under a variable that is named after the class.


LIMITATIONS
-------------

- Forget about private. But since the implementation is pythonish, and python doesn't
  have the concept of private, it is fine.
- Always provide a constructor, unless you are sure that no superclass has a
  constructor, to prevent unwanted repeated constructor calls.


CONSIDERATIONS
-----------------------------

- super and import are reserved words, so getSuper and include have been chosen instead.
- Declare public methods and attributes outside the constructor. Easiear to read. Allows
  access to them before class initialization.
- the proposed class declaration structure looks for being clear comfortable coming from
  other OOP languages.
