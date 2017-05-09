---
title:   Coding Guidelines
author:    chrisbasoglu
date:    11/14/2016
ms.author:   cbasoglu
ms.date:   11/14/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   csharp, cpp
---

#Coding Guidelines

## CNTK Coding Style

This page documents the conventions used in the source code of CNTK. Please adhere to these conventions 
when writing new code. Follow common sense and break up functions exceeding a reasonable limit 
(a couple of screen pages), use meaningful names, comment well and keep comments and code in sync, etc.

### Basics: indentation, spacing and braces

Code is consistently indented using four spaces. Tab characters are not allowed anywhere in the code. 
The only exceptions are Makefiles, other build system, or data files where tab characters are syntactically 
required.

The following code blocks are indented:

 * Bodies of control statements: for, if, while, switch, etc.
 * Free statement blocks, i.e. opening and closing braces which do not follow any control statement. These 
 are sometimes used to limit the lifetime of objects.
 * Bodies of classes and functions. 
 * Statements continued from the previous line.
 * Code in case statements starts on the line following the case statement and is indented.

The following things are not indented:
* Contents of namespaces.
* Case labels
* Access control specifiers.

Function declarations with long parameter lists may be split over multiple lines. The paramerter declaration
on the split lines should be indented to the opening parenthesis of the function declaration. Calls to functions 
with long parameter lists may be split over multiple lines, the split lines should be indented to the opening
parenthesis of the associated function statement.

Code is written using [Allman or BSD Unix style braces](https://en.wikipedia.org/wiki/Indent_style#Allman_style). This style puts the brace associated with a 
control statement on the next line, indented to the same level as the control statement. 
Statements within the braces are indented to the next level, it is recommended to never omit braces, even for small blocks.

Spaces are present in the following places:
* Around all binary operators, including assignments
* Between a keyword and parentheses
* Between an identifier or keyword and a brace
* After commas and semicolons that do not end a line

Spaces are absent in the following places:
* Before semicolons and commas
* On the inner side of parentheses
* Between a function name and its argument list
* Between unary operators and their operands
* Inside an empty argument list
* Between a label and a colon
* Around the scope operator ::

Member initializer lists and base class lists that contain more than one class should be written on a separate 
line. This makes it very easy to spot errors.

```
namespace Microsoft { namespace MSR { namespace CNTK {

Matrix ImplodeSelf(int x);
int ConfuseUs(float y);

class MainPart:
    public Head,
    protected Heart
{
public:
    MainPart():
        m_weight(99),
        m_height(180)
    {}
private:
    void EatMore();
    int m_consume, m_repeater;
};

template <typename Box>
void Inspect(Box & container)
{
    switch (container)
    {
    case 1:
        PrepareIt();
        break;

    case 2:
        Finish();
        break;

    default:
        break;
    }

    for (int i = 0; i < 30; ++i)
    {
        container << EatMore();
    }
    return container;
}

} } }
```

### Naming conventions

* Class and namespace names use [UpperCamelCase aka PascalCase](https://en.wikipedia.org/wiki/CamelCase). 
* Names commonly spelled in all-caps (SQL, CNTK, ...) can stay in all upper cases. 
* Global and public static functions, stack variables, and class members (class variables) use lowerCamelCase. 
* Class member functions (methods) use UpperCamelCase. 
* Macros and constants use UPPER_SNAKE_CASE. 
* Template parameters which are types use UpperCamelCase. 
* Type prefixes, Hungarian notation, etc. are disallowed. Use meaningful suffixes if you need to disambiguate, 
e.g. floatMatrix and normalizedDoubleMatrix.

Name prefixes
* ```m_``` for member variables
* ```s_``` for static varibales in any context
* ```g_``` for global variables, which should be avoided in the first place (as much as possible)

Variable names should be nouns. Function names should be verbs, with the exception of getters, which can be 
nouns. For instance, a class property called position would have the setter SetPosition() and the getter Position(). 


### Filename conventions

C++ files should have the .cpp extension, while header files should have the .h extension. Spaces and underscores are not allowed. Using numbers in filenames is discouraged.
```
#define GOOD_MACRO(x) x
void CallOut();
unsigned const g_theAnswer = 42;

class SolveAllProblems 
{
public:
    void DoWhatWeNeed();
    static void SetBugsOff();
    int m_countReasons;
protected:
    void NeverWorking();
    static void GetReason();
    int internalCounter;
private:
    void InternalNeeds();
    static void ShowReason();
    int m_countShows;
};

template <typename TypeParam, int numberOfReasons>
void CallGlobal(boost::array<TypeParam, numberOfReasons> const &array);
```

### Preprocessor

Conditional compilation using the preprocessor is strongly discouraged, since it leads to code rot. 
Use it only when it is unavoidable, for instance when an optional dependency is used. 
A special case is using conditional compilation to exclude an entire file based on the platform, which is allowed.

In preference to conditionally compiled, platform-specific code, you should aim to write portable code 
which works the same regardless of platform. Using the Boost libraries can help a lot in this respect. 
If you must use different code depending on the platform, try to encapsulate it in helper functions, 
so that the amount of code that differs between platforms is kept to a minimum.

