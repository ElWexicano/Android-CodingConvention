## Coding Style Guidlines

An Android Coding Convention which is based upon the Code Style Guidelines for Contributors by Google with extra information regarding Resource Style Rules.

**The first bit of this coding convention is taken directly from the Code Style Guidelines provided by Google and I do not claim it to be my own work!**

### Java Language Rules

We follow standard Java coding conventions. We add a few rules:

#### Don't Ignore Exceptions

Sometimes it is tempting to write code that completely ignores an exception like this:

    void setServerPort(String value) {
        try {
            serverPort = Integer.parseInt(value);
        } catch (NumberFormatException e) { }
    }

You must never do this. While you may think that your code will never encounter this error condition or that it is not important to handle it, ignoring exceptions like above creates mines in your code for someone else to trip over some day. You must handle every Exception in your code in some principled way. The specific handling varies depending on the case.

#### Don't Catch Generic Exception

#### Don't Use Finalizers

Finalizers are a way to have a chunk of code executed when an object is garbage collected.

Pros: can be handy for doing cleanup, particularly of external resources.

Cons: there are no guarantees as to when a finalizer will be called, or even that it will be called at all.

Decision: we don't use finalizers. In most cases, you can do what you need from a finalizer with good exception handling. If you absolutely need it, define a close() method (or the like) and document exactly when that method needs to be called. See InputStream for an example. In this case it is appropriate but not required to print a short log message from the finalizer, as long as it is not expected to flood the logs.

#### Fully Qualify Imports

### Java Library Rules

There are conventions for using Android's Java libraries and tools. In some cases, the convention has changed in important ways and older code might use a deprecated pattern or library. When working with such code, it's okay to continue the existing style (see Consistency). When creating new components never use deprecated libraries.

### Java Style Rules

#### Use Javadoc Standard Comments

Every file should have a copyright statement at the top. Then a package statement and import statements should follow, each block separated by a blank line. And then there is the class or interface declaration. In the Javadoc comments, describe what the class or interface does.

    /*
     * Copyright (C) 2010 The Android Open Source Project 
     *
     * Licensed under the Apache License, Version 2.0 (the "License");
     * you may not use this file except in compliance with the License.
     * You may obtain a copy of the License at 
     *
     *      http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software 
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and 
     * limitations under the License.
     */
    
    package com.android.internal.foo;
    
    import android.os.Blah;
    import android.view.Yada;
    
    import java.sql.ResultSet;
    import java.sql.SQLException;
    
    /**
     * Does X and Y and provides an abstraction for Z.
     */
    
    public class Foo {
        ...
    }

Every class and nontrivial public method you write must contain a Javadoc comment with at least one sentence describing what the class or method does. This sentence should start with a 3rd person descriptive verb.

Examples:

    /** Returns the correctly rounded positive square root of a double value. */
    static double sqrt(double a) {
        ...
    }

or

    /**
     * Constructs a new String by converting the specified array of 
     * bytes using the platform's default character encoding.
     */
    public String(byte[] bytes) {
        ...
    }

You do not need to write Javadoc for trivial get and set methods such as setFoo() if all your Javadoc would say is "sets Foo". If the method does something more complex (such as enforcing a constraint or having an important side effect), then you must document it. And if it's not obvious what the property "Foo" means, you should document it.

Every method you write, whether public or otherwise, would benefit from Javadoc. Public methods are part of an API and therefore require Javadoc.

Android does not currently enforce a specific style for writing Javadoc comments, but you should follow the [Sun Javadoc conventions](http://www.oracle.com/technetwork/java/index.html).

#### Write Short Methods

To the extent that it is feasible, methods should be kept small and focused. It is, however, recognized that long methods are sometimes appropriate, so no hard limit is placed on method length. If a method exceeds 40 lines or so, think about whether it can be broken up without harming the structure of the program.

#### Define Fields in Standard Places

Fields should be defined either at the top of the file, or immediately before the methods that use them.

#### Limit Variable Scope

The scope of local variables should be kept to a minimum (Effective Java Item 29). By doing so, you increase the readability and maintainability of your code and reduce the likelihood of error. Each variable should be declared in the innermost block that encloses all uses of the variable.

Local variables should be declared at the point they are first used. Nearly every local variable declaration should contain an initializer. If you don't yet have enough information to initialize a variable sensibly, you should postpone the declaration until you do.

One exception to this rule concerns try-catch statements. If a variable is initialized with the return value of a method that throws a checked exception, it must be initialized inside a try block. If the value must be used outside of the try block, then it must be declared before the try block, where it cannot yet be sensibly initialized:

    // Instantiate class cl, which represents some sort of Set 
    Set s = null;
    try {
        s = (Set) cl.newInstance();
    } catch(IllegalAccessException e) {
        throw new IllegalArgumentException(cl + " not accessible");
    } catch(InstantiationException e) {
        throw new IllegalArgumentException(cl + " not instantiable");
    }
    
    // Exercise the set 
    s.addAll(Arrays.asList(args));

But even this case can be avoided by encapsulating the try-catch block in a method:

    Set createSet(Class cl) {
        // Instantiate class cl, which represents some sort of Set 
        try {
            return (Set) cl.newInstance();
        } catch(IllegalAccessException e) {
            throw new IllegalArgumentException(cl + " not accessible");
        } catch(InstantiationException e) {
            throw new IllegalArgumentException(cl + " not instantiable");
        }
    }
    
    ...
    
    // Exercise the set 
    Set s = createSet(cl);
    s.addAll(Arrays.asList(args));

Loop variables should be declared in the for statement itself unless there is a compelling reason to do otherwise:

    for (int i = 0; i n; i++) {
        doSomething(i);
    }

and

    for (Iterator i = c.iterator(); i.hasNext(); ) {
        doSomethingElse(i.next());
    }

#### Order Import Statements

The ordering of import statements is:

1. Android imports

2. Imports from third parties (com, junit, net, org)

3. java and javax

To exactly match the IDE settings, the imports should be:

* Alphabetical within each grouping, with capital letters before lower case letters (e.g. Z before a).

* There should be a blank line between each major grouping (android, com, junit, net, org, java, javax).

Originally there was no style requirement on the ordering. This meant that the IDE's were either always changing the ordering, or IDE developers had to disable the automatic import management features and maintain the imports by hand. This was deemed bad. When java-style was asked, the preferred styles were all over the map. It pretty much came down to our needing to "pick an ordering and be consistent." So we chose a style, updated the style guide, and made the IDEs obey it. We expect that as IDE users work on the code, the imports in all of the packages will end up matching this pattern without any extra engineering effort.

This style was chosen such that:

* The imports people want to look at first tend to be at the top (android)

* The imports people want to look at least tend to be at the bottom (java)

* Humans can easily follow the style

* IDEs can follow the style

The use and location of static imports have been mildly controversial issues. Some people would prefer static imports to be interspersed with the remaining imports, some would prefer them reside above or below all other imports. Additinally, we have not yet come up with a way to make all IDEs use the same ordering.

Since most people consider this a low priority issue, just use your judgement and please be consistent.

#### Use Spaces for Indentation

We use 4 space indents for blocks. We never use tabs. When in doubt, be consistent with code around you.

We use 8 space indents for line wraps, including function calls and assignments. For example, this is correct:

    Instrument i =
            someLongExpression(that, wouldNotFit, on, one, line);
        
and this is not correct:

    Instrument i =
        someLongExpression(that, wouldNotFit, on, one, line);

#### Follow Field Naming Conventions

* Non-public, non-static field names start with m.

* Static field names start with s.

* Other fields start with a lower case letter.

* Public static final fields (constants) are ALL_CAPS_WITH_UNDERSCORES.

For example:

    public class MyClass {
        public static final int SOME_CONSTANT = 42;
        public int publicField;
        private static MyClass sSingleton;
        int mPackagePrivate;
        private int mPrivate;
        protected int mProtected;
    }

#### Use Standard Brace Style

Braces do not go on their own line; they go on the same line as the code before them. So:

    class MyClass {
        int func() {
            if (something) {
                // ...
            } else if (somethingElse) {
                // ...
            } else {
                // ...
            }
        }
    }
    
We require braces around the statements for a conditional. Except, if the entire conditional (the condition and the body) fit on one line, you may (but are not obligated to) put it all on one line. That is, this is legal:

    if (condition) {
        body(); 
    }
    
and this is legal:

    if (condition) body();
    
but this is still illegal:

    if (condition)
        body();  // bad!

#### Limit Line Length

Each line of text in your code should be at most 100 characters long.

There has been lots of discussion about this rule and the decision remains that 100 characters is the maximum.

Exception: if a comment line contains an example command or a literal URL longer than 100 characters, that line may be longer than 100 characters for ease of cut and paste.

Exception: import lines can go over the limit because humans rarely see them. This also simplifies tool writing.

#### Use Standard Java Annotations

Annotations should precede other modifiers for the same language element. Simple marker annotations (e.g. @Override) can be listed on the same line with the language element. If there are multiple annotations, or parameterized annotations, they should each be listed one-per-line in alphabetical order.<

Android standard practices for the three predefined annotations in Java are:

* **@Deprecated**: The @Deprecated annotation must be used whenever the use of the annotated element is discouraged. If you use the @Deprecated annotation, you must also have a @deprecated Javadoc tag and it should name an alternate implementation. In addition, remember that a @Deprecated method is still supposed to work.

If you see old code that has a @deprecated Javadoc tag, please add the @Deprecated annotation.

* **@Override**: The @Override annotation must be used whenever a method overrides the declaration or implementation from a super-class.

  For example, if you use the @inheritdocs Javadoc tag, and derive from a class (not an interface), you must also annotate that the method @Overrides the parent class's method.

* **@SuppressWarnings**: The @SuppressWarnings annotation should only be used under circumstances where it is impossible to eliminate a warning. If a warning passes this "impossible to eliminate" test, the @SuppressWarnings annotation must be used, so as to ensure that all warnings reflect actual problems in the code.

  When a @SuppressWarnings annotation is necessary, it must be prefixed with a TODO comment that explains the "impossible to eliminate" condition. This will normally identify an offending class that has an awkward interface. For example:

    // TODO: The third-party class com.third.useful.Utility.rotate() needs generics 
    @SuppressWarnings("generic-cast")
    List<String> blix = Utility.rotate(blax);

  When a @SuppressWarnings annotation is required, the code should be refactored to isolate the software elements where the annotation applies.


#### Treat Accronyms as Words

#### Use TODO Comments

#### Log Sparingly

#### Be consistent

### Javatests Style Rules

#### Follow Test Method Naming Conventions

**The remainder of this coding convention is my opionion of the best Resource Style Rules.**

### Resource Style Rules

All resources should be categorised. This makes it easier to search for a resource and to understand what the resource is being used for.

#### Animation Resources

#### Color State List Resources

#### Drawable Resources

bg_<category_name>.xml
ic_<category_name>.xml
img_<category_name>.xml

#### Layout Resources

activity_<activity_name>.xml
fragment_<fragment_name>.xml
list_item_<category_name>.xml
dialog_<category_name>.xml
map_<category_name>.xml

#### Menu Resources

menu_<category_name>.xml

#### String Resources

strings_<category_name>.xml
string_arrays_<category_name>.xml

#### Style Resources

styles_<category_name>.xml

#### Other Resources

settings_<category_name>.xml
