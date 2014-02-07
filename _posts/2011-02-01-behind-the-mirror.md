---
layout: post
category : articles
tags : [c++, c++ reflection]
---
{% include JB/setup %}

<em>This article was originally printed as a <a href="http://gdmag.com">Game Developer Magazine</a> article in the February 2011 issue. It's also available online at <a href="http://www.gamasutra.com/view/feature/6379/sponsored_feature_behind_the_.php">Gamautra</a> as a sponsored feature thanks to my ex-Insomniac friend and coworker Orion Granatir, who also moved on to <a href="http://intel.com">Intel</a>.</em>

## Adding Reflection to C++

Reflection is a programming language feature that adds the ability for a program to utilize its own structure to inform its behavior. Reflection has its costs, but those are often outweighed by ability to automate the following:
* Serializing objects into and out of a file
* Cloning, comparison, search indexing, and network replication
* Type conversion (copying base data between derived class instances)
* User interface generation and data binding

Of course all of these tasks can be accomplished without reflection capabilities, but you will likely pay higher costs having to write code that is very rote and prone to error. A good implementation of reflection can provide a platform on which each of these problems can be solved without glue code in every class that desires these features.

At the highest level reflection can encompass many different features:
* Runtime knowledge of class members (fields and methods)
* Dynamic generation and adaptation of code
* Dynamic dispatch of procedure calls
* Dynamic type creation

However, for the purposes of this article I will define C++ Reflection to mean: having access at runtime to information about the C++ classes in your program.

## RTTI

Before diving headlong into how to add reflection to C++, it’s worth noting what type information is already built-in. The C++ language specification provides minimal information about the classes compiled into a program. When enabled, C++ Run Time Type Information (RTTI) can provide only enough information to generate an id and name (the typeid operator), and handle identifying an instance’s class given any type of compatible pointer (dynamic_cast&lt;&gt;).

For the purpose of game programming, RTTI is often disabled entirely. This is because its implementation is more costly than a system built on top of C++. Even if a program only makes a handful of RTTI queries, the toolchain is typically forced to generate, link, and allocate memory at runtime for information about every class in the application (that has a vtable). This significantly increases the amount of memory required to load your program, leaving less memory available for face-melting graphics, physics, and AI. It's better to implement your own RTTI-like system that only adds cost to the classes that need to utilize it. There are plenty of practical situations where vtables make sense without needing to do runtime type checking.

Thus, the first step in implementation of a reflection system is typically a user implementation of RTTI features. This can be accomplished with only a couple of steps.
Type information can be associated by a static member pointer (which also makes a good unique identifier for any given type within the program). In addition, some virtual functions allow querying an object's exact type, as well as test for base class types:

    // Returns the type for this instance
    virtual const Type* GetType() const;

    // Deduces type membership for this instance
    virtual bool HasType( const Type* type ) const;

GetType returns a pointer to the static type data, and HasType compares the provided type against its static type pointer as well as every base class' type pointer. This gives us all the information needed to re-implement dynamic_cast&lt;&gt;, but it only adds overhead to classes that are worth paying the added cost of type identification and type checking.

## Visitor Pattern

The simplest technique for implementing reflection is to take a purely programmatic approach. Virtual functions can be a mechanism for the traversal of all fields in a class.
The visitor design pattern provides an abstraction for performing arbitrary operations on the fields:

    // a base class for any object that
    //  wants to reflect upon any class' fields
    class ObjectVisitor
    {
    public:
        virtual void VisitField( int32_t&amp;, const char* ) = 0;
    };

    // an example of a class that would write/read
    //  from/to each field to/from a file
    class SerializeVisitor : public Visitor
    {
    public:
        virtual void VisitField( int32_t&amp; value, const char* name )
        {
            // do serialization work
        }
    };

    // a base class for some of your reflection-aware objects
    class Object
    {
    public:
        virtual void Accept( ObjectVisitor&amp; visitor ) = 0;
    };

    // an example of a derived class that has a reflected field
    class Foo : public Object
    {
    public:
        virtual void Accept( ObjectVisitor&amp; visitor )
        {
            visitor.VisitField( m_Number, "Number" );
        }

    private:
        int32_t m_Number;
    };

This is a textbook implementation of the visitor design pattern. Objects deliver the visitor to each one of its fields and the visitor gets an opportunity to transact with each field in series. It offers excellent encapsulation since the object does not know or care about any implementation details of what the visitor is trying to accomplish.

This technique does not require data from an external tool to do its job since it’s implemented entirely in the code compiled into the program. It’s simple to step through and debug, and extensible since many operations can be implemented as another class of Visitor.

With this approach, the development cost is small. A single line of code for each field in every class in your codebase is a fair price to pay to attain the benefits reflection can provide. However, there are some drawbacks with using a visitor function for reflecting upon your objects. There are a lot of virtual function calls happening to interact with each field in a class. This is a concern for performance critical code and on certain platforms. Also, this technique is best suited for operations that want to visit every single field of a class. There are many situations where this work is not required, and iterating over every field just to access a few is wasteful and time consuming (depending on the size of the object).

## Data Model

To really take reflection to the next level it's necessary to be able to address specific fields and read and write data without iterating over every field in the class. A data model that represents the classes and fields specified in the code is needed to accomplish this. At runtime your program can reflect upon this model to interface with objects and their field data.

This data model is owned by a central registry of type information. This singleton object owns all the type information in the program and can have support for finding type information by name. It's also a central point where a map of the entire inheritance hierarchy of classes can be built. The registry can be populated by employing a parser tool to analyze your source code, or by adopting a method similar to the visitor function approach to populate this data model at program startup.

## To Parse or Not To Parse...

Using a parsing tool to analyze your code introduces a lot of complexity. C++ has a very complex syntax. While there are some tools you can take off the shelf to do the parsing, there is still a lot of work to do to make that data usable at runtime. Typically, you want to extract just the necessary data from the abstract parse tree and write out a meaningful representation of only the data that is required for what you want to reflect upon. Templates, typedefs, functions and other language features are generally overkill for the purpose of reflecting upon fields in a class.

A parsing tool is probably going to do one of two things: write a data file to be loaded at runtime (or packed into the executable as a global variable or resource section), or it's going to generate some code that gets compiled into your program.

If you choose the data file route, you have the added task of computing member size and offset information. This information is compiler specific and target platform specific. By
choosing this approach you are committing to abide by the padding and alignment rules of whatever compiler you use to build any given version of your program. Another source of complexity comes from the existence of two independent pipelines processing information about your code: the compiler and the parsing tool. This necessitates synchronizing the data output by the tool with the specific version of the compiled program, which will make packaging and deploying your program harder. Synchronization is a very important problem to solve in this approach because not detecting out of sync reflection information can cause nasty bugs (and potentially mangled data).

If you choose to generate source code to be compiled into your program, you inherit the burden of the complexities that come with creating a code generator that is most likely specific to your particular needs. The code generation tool will probably need to make a bunch of decisions about how your code needs to be decorated and organized. These requirements will change as your codebase evolves, and it will require you to be diligent about releasing and configuring your own build tool. Also, maintaining a tool that governs the ability to compile your game is risky because it has a tendency to break at the worst possible time (during a milestone).

The reward for using these approaches is tangible. You don’t have any code that needs to be written by hand to reflect upon your classes. If you choose to generate code, then you will also probably get great performance since you can generate function bodies that do specific operations on every field of your classes, just like you would have done if you weren’t using reflection at all.

In reality, there are a ton of moving parts when using this approach. Things can break in hard to trace ways if any step of the pipeline doesn’t work as expected. Having implemented and maintained this technique for many years, I can tell you that there are days when it feels like the planets have to align for all the parts in this complex pipeline to actually work together in harmony.

## Hand Coding

Alternatively, code can be written to populate the reflection data model when our program starts up. This code creates class information structures, populates them with information about every field within the class, and adds them to the registry. Writing this code sounds arduous, but C++ template support provides some excellent tools to accomplish this with remarkably concise and manageable code. A good goal for this is to extract as much information as possible in a single function call per field, per class (just like our visitor function). This allows us to avoid any time spent at build time processing source, managing dependencies on build tools, dependency checking generated code, and synchronizing externally loaded data.

## Polymorphic Data

Because containers in C++ are template types instead of concrete types, function overloading can only take us so far. Since each template instantiation is a completely different type, trying to support containers using a visitor pattern could lead to a combinatorial explosion in the number of overridden functions. Enumerated data types present the same challenges. It's not easy to support them via overloading since every enum in the entire game would need a different overload.

A solution to this shortcoming is to delegate the handling of any piece of data to a separate class of object that can interface with individual fields using a pointer. This will give us the ability to operate on any data in a polymorphic manner, including integer, floating point, and enumerated data types. Many languages that require derivation from a canonical Object class do this already. Adding support for treating simple types with polymorphism doesn't mean that it's necessary to use these polymorphic versions of these types everywhere in your code. They will only be used to abstract away the implementation details of dealing with serializing, comparing, and converting data to and from human readable strings (which is very handy for generating property UIs).

Truly polymorphic data can solve many edge cases and provide extensibility for user types like enums and exotic containers. It can also support user data types that need custom processing during serialization. If these data classes store a value in addition to working through a pointer, they can be used to interface with fields and store standalone data. This allows for interoperability between versions of the program that have slightly different fields without discarding this "unknown" information. This is a major coup for game development tools that revise sets of properties frequently between releases. You can publish a test release with a very different set of properties and know that if content creators check in some of those files they probably won't break folks still using the stable production tools (since the stable tools data is still there in the files).

Every field in the reflection information will specify a class of object that will handle the details of reading and writing the necessary data to a persistence interface or other objects of the same type. With this in mind, it's time to declare some data structures to store Class and Field information:

## Class

Class stores all of the information for a class of object in your code (client object or data object):

    struct Class
    {
        const Class*            m_Base;     // our base class
        Array&lt; const Class* &gt;   m_Derived;  // our derived classes
        const char*             m_Name;     // our name (user-friendly)
        Array&lt; const Field* &gt;   m_Fields;   // fields of this class
        Class( const char* name )
            : m_Base( NULL )
            , m_Name( name )
        {
        }
    };

## Field

Field stores information about a particular member variable in a class. Fields are stored in an array in the Class object that owns them.

    struct Field
    {
        const Class*      m_OwnerClass;  // the class this is within
    ￼    const Class*      m_DataClass;   // the class of data
                                         //  that serializes the field
        const char*       m_Name;        // name of the field
        size_t            m_Size;        // the size of the field
        uintptr_t         m_Offset;      // the offset to the field

        Field( const Class* owner, const Class* data,
               const char* name, size_t size, uintptr_t offset )
            : m_OwnerClass( owner )
            , m_DataClass( data )
            , m_Name( name )
            , m_Size( size )
            , m_Offset( offset )
        {
        }
    };

## Populating the Data Model

To help populate the data model, some template functions can help extract useful data via template parameters:

    template&lt; class ObjectT, class DataT &gt;
    Field* AddField( Class* owner, DataT T::* field, const char* name,
                     const Class* data = NULL )
    {
        // call out to a template function that is specialized
        //  to return the appropriate data class for this type of field
        // also compute the offset from the base pointer
        //  using the pointer to the member variable
        Field* field;
        field = new Field( owner,
                           data ? data : DeduceDataClass&lt; DataT &gt;(),
                           name,
                           sizeof( DataT ),
                           GetFieldOffset( field ) );
        owner-&gt;m_Fields.Push( field );
        return field;
    }

A template function with parameters for the object type and variable type provides an easy way to extract the size of the variable, its offset from the base instance pointer (using a pointer to member variable), and it supports the use of template specialization to deduce which type of data object is applicable to this field. Three important things are happening in this function to extract data for our reflection data model: pointer to member variable C++ syntax, translation of this syntax into an offset from a base object address, and the use of deduction using explicit specialization.

## Pointer to Member Variables

Pointers to member variables are a pretty infrequently used aspect of C++. It does what you might expect, but its syntax is strange if you haven't seen it before:
int32_t Object::* pointer_to_member_variable = &amp;Object::m_Member;
￼These are typically dereferenced with an instance of the object type (just like member function pointers):

    Object object, *pointer = new Object;
    int32_t value1 = object.*pointer_to_member_variable;
    int32_t value2 = pointer-&gt;*pointer_to_member_variable;

## Translation Into Offset

To compute the offset from a pointer to a member variable:

    template&lt; class ObjectT, class DataT &gt;
    uint32_t GetFieldOffset( ObjectT DataT::* field )
    {
        // a pointer-to-member is really just an
        //  offset value disguised by the compiler
        return (uint32_t) (uintptr_t) &amp;( ((ObjectT*)NULL)-&gt;*field );
    }

This function doesn't bother with allocating an instance to dereference the pointer to member variable. It substitutes a NULL pointer, dereferences the pointer to member variable, and uses the address operator to yield the offset (from NULL) at which the pointed member exists. Some of this syntax may seem strange if you haven't used it before; but it's a perfect fit for maximizing what information is needed to describe a field in a single function parameter.

## Explicit Specialization

DeduceDataClass is a good example of template deduction using explicit template specialization. This deduction technique is a way of using the C++ template mechanism to allow for the automatic selection of some information by the template compiler based only on a template parameter. The default template function's implementation returns NULL, indicating that the deduction failed since no specialization was found to find the associated data:

    template&lt; class DataT &gt;
    Class* DeduceDataClass()
    {
        // unknown data!
        return NULL;
    }

Then create an explicit specialization for every type that can be deduced:

    template&lt;&gt;
    Class* DeduceDataClass()
    {
        // this specialization associates the uint32_t
        //  built in type with an object class that can
    ￼    //  process data of type uint32_t with respect
        //  to other persistence / cloning / mining code
        return SimpleData&lt; uint32_t &gt;::s_Class;
    }

In this case, a pointer is returned to the class reflection information for the type of data object to be used when dealing with the built-in type passed into the template argument.

## Putting It All Together

One more template will help keep the code that registers classes at startup concise:

    template&lt; class ObjectT &gt;
    static Class* CreateClass( const char* name )
    {
        Class* result = new Class( name );

        // populate the field information for this class
        ObjectT::Populate( *result );

        return result;
    }

Finally, an example class and main that will put all of this code to work:

    class Foo
    {
    private:
        uint32_t m_Number;
    public:
        static Populate( Class&amp; c )
        {
            // AddField is a template function that will deduce
            //  everything but what the desired name is.
            Field* f = c.AddField( &amp;Object::m_Number, "Number" );
            // Its easy to imagine Field having extra information
            //  to inform all sorts of program behavior
            //  f-&gt;SetRange( 0, 10 );
            //  f-&gt;SetCategory( "Advanced Settings" );
        }
    };

    void main()
    {
        Registry::RegisterClass( CreateClass&lt; Foo &gt;( "Foo" ) );

        // program goes here

    ￼    Registry::UnregisterClass( Registry::GetClass&lt; Foo &gt;() );
    }

## Conclusion

Reflection can imbue an enormous amount of flexibility to your game engine, but this flexibility doesn't come without cost. However, the extra memory reflection data consumes is balanced by the time saved implementing features more rapidly. The ability to deliver changes to your users quickly, and with minimal engineering overhead will pay dividends as your user base grows and your production time stretches across multiple titles.

## Open Source Implementation

Helium is an open source game engine toolkit that contains an implementation of C++ Reflection. Much of the code in this article was derived from it. It uses a BSD-style license, and is available at <a href="http://heliumproject.org">heliumproject.org</a>. The reflection system itself is located in the Foundation/Reflect folder within the source repository.

## Bio

At the time of this writing, Geoff Evans was a Senior Engineer at WhiteMoon Dreams, Inc. in Los Angeles, CA. He was a founder of the Nocturnal Initiative open source project at Insomniac Games, and is a founder of the Helium Project (started at Whitemoon Dreams), which aims to build an open source commercial quality game engine.
￼