
ProtoSum
=========

- Javascript pythonish multiple inheritance class system.
- DOMReady: Lightweight DOMReady implementation.


About ProtoSum
----------------

Framework to work with class inheritance. It is pythonish, so remember:

  - You have multiple inheritance
  - Always use this to access methods or atributes
  - Everything is public... well, we are on javascript, do your tricks if you need to.
  

Why
-------------

In short:

- **Readability:** define classes with their members. From methods or static functions call other class members or trigger events. No need for nesting.
- **Easier debugging:** when you call console.log and it hits a ProtoSum instance it is named after the class in Chrome. Also, string representations follows the class name (no more "[Object object]")
- **Performance:** class inheritance (AKA mixin) is applied on class definition instead of on instance initialization.
- **Short learning curve coming from OOP**: The way you define classes looks very familiar to people who come from other OOP languages like Java or ActionScript3.

In other words, I've been using ProtoSum for one year now and it allowed me to solve complex algorithms in a very clear
way with everything defined in the right place. And when something is complex, clearness and code organization are your best friends.


### What you need to know ###

ProtoSum assumes that you are an experienced OOP coder and that your JavaScript knowledge is far beyond jQuery.


### Related projects ###

- **[protosum-lib](http://github.com/francescortiz/protosum-lib)**: *This is what I use on my projects.* Event handling, DOM handling, Automapping\*, some more...
- **protosum-components**: (nothing published yet) Components that help you with common tasks, like AJAX popups or photo galleries.

\*Automapping deserves an explanation: usually you have

    <div class="some-behaviour"></div>

and then you map "some-behaivour" to the desired behaviour with something like '$(".some-behaviour").myBehaviour();'. Automapping saves you the need for '$(".some-behaviour").myBehaviour();'.
Instead, define the div as

    <div protosum="MyBehaviour"></div>

That's all. You get complete separation between HTML/CSS and Javascript.


USAGE
-----------

- ProtoSum([className], [main superclass], [superclass], [superclass], ...);
  * Returns a subclass of the given classes
  * it popuplates [class].prototype.__class__ with a reference to the created class
  * if first argument is a string, it is used to populate [class].prototype.__class__.name
  * it popuplates [class].supers with an array af all superclassses.
    TODO: prevent repetitions in [class].supers array
- ProtoSum.include("path/to/javascript/file.js", [async=false], [async_callback]);
  * Behaves like import
  
Then, for the instances you have:

- [instance].isInstance([class]);
  * like instanceof, but with support for multiple inheritance
- [instance].getSuper([superclass])
  * returns the specified super prototype.

  
EXAMPLE
----------------

Sample class

    var C = ProtoSum(A, B); (function() {
        var proto = C.prototype;

        proto.someVar = "somveValue";
        proto.c = "99";

        // Objects and Arrays are assigned by reference, so this creates a shared object. Use static access instead to make code more readable.
        proto.d = {};

        C.public_static_var = 'something';
        
        proto.__init__ = function(){
            this.getSuper(A).__init__.call(this, 'argument1', 'argument2');
            log("C __init__");
        }

        proto.doC = function() {
            log(this.c);
        }

        proto.whoAmI = function() {
            log("i am C and my supers say: ");
            this.getSuper(A).whoAmI.call(this); // calls whoAmI in A
            this.getSuper(B).whoAmI.call(this);  // calls whoAmI in B
        }
        
        proto.customeEvent = function(event) {
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

### ProtoSum.include
Loads javascript dynamically.

    // Makes code portable
    ProtoSum.settings.JS_PATH = '/path/';

    // Code execution stops here until the script is available
    ProtoSum.include("file.js");

    // Don't lock code execution.
    ProtoSum.include("file.js", false); 

    // When file.js is loaded, execute file_loaded("file.js");
    ProtoSum.include("file.js", false, file_loaded); 
    
### log
Simple logger.

    log('something');
    

Debugging tips
-------------------

- All protosum prototypes come with a __name__ attribute, and all instances come with __class__.__name__, if you defined a name for the class.
- In crome classes appear encapsulated under a variable that is named after the class.


LIMITATIONS
-------------

- Forget about private. But since the implementation is pythonish, and python doesn't
  have the concept of private, it is fine.


CONSIDERATIONS
-----------------------------

- super and import are reserved words, so getSuper and include have been chosen instead.
- Declare public methods and attributes outside the __init__. Easiear to read. Allows
  access to them before class initialization.
- the proposed class declaration structure looks for being clear comfortable coming from
  other OOP languages.
- variable initialization of referenced values (objects, arrays) must be done inside the __init__, otherwise you get the same
  reference passed to all the instances.


DOMReady
=============

Lightweight onready implementation. DOMReady is an array with a run method that executes all functions that it contains.
Add functions to this array via DOMReady.push([function]) and put

    <script>DOMReady.run()</script>

to the end of your HTML.


