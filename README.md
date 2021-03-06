
<A name="toc1-3" title="zproject - Class Project Generator" />
# zproject - Class Project Generator

<A name="toc2-6" title="Contents" />
## Contents


**<a href="#toc2-11">Overview</a>**
&emsp;<a href="#toc3-18">Scope and Goals</a>

**<a href="#toc2-46">Demo on PLAYTerm</a>**

**<a href="#toc2-51">Installation</a>**
&emsp;<a href="#toc3-54">Requirements</a>
&emsp;<a href="#toc3-64">Getting started</a>

**<a href="#toc2-82">Setup your project environment</a>**

**<a href="#toc2-104">Configuration</a>**

**<a href="#toc2-256">Sample API model</a>**
&emsp;<a href="#toc3-365">Supported API Model Attributes</a>
&emsp;<a href="#toc3-381">Tips</a>

**<a href="#toc1-387">if the `method` variable is a <method/> entity:</a>**

**<a href="#toc2-393">Ownership and License</a>**

**<a href="#toc2-402">Removal</a>**
&emsp;<a href="#toc3-405">autotools</a>
&emsp;<a href="#toc3-410">Hints to Contributors</a>
&emsp;<a href="#toc3-419">Notes for Writing Language Bindings</a>
&emsp;<a href="#toc4-422">Schema/Architecture Overview</a>
&emsp;<a href="#toc4-441">Informal Summary</a>
&emsp;<a href="#toc4-446">Semantic Attributes</a>
&emsp;<a href="#toc4-481">Language-Specific Implementation Attributes</a>
&emsp;<a href="#toc3-502">This Document</a>

<A name="toc2-11" title="Overview" />
## Overview

zproject is a community project, like most ZeroMQ projects, built using the C4.1 process, and licensed under MPL v2. It solves the Makefile problem really well. It is unashamedly for C, and more pointedly, for that modern C dialect we call CLASS. CLASS is the Minecraft of C: fun, easy, playful, mind-opening, and social. Read more about it [hintjens#79](http://hintjens.com/blog:79).

zproject grew out of the work that has been done to automatically generate the build environment in CZMQ. It allows to share these automations with other projects like [zyre](https://github.com/zeromq/zyre), [malamute](https://github.com/zeromq/malamute) or [hydra](https://github.com/edgenet/hydra) and at the same time keep everything in sync.

<A name="toc3-18" title="Scope and Goals" />
### Scope and Goals

zproject has these primary goals:

* generate files for cross-platform build environments.
* generate CLASS ([ZeroMQ RFC/21](http://rfc.zeromq.org/spec:21)) compliant header and source skeletons for new classes.
* generate a public header file for your library so it can be easily included by others.
* generate stubs for man page documentation which uses the comment based approach from CZMQ.

All you need is a project.xml file in the project's root directory which is your

    One file to rule them all

The following build environments are currently supported:

* android
* autotools
* cmake
* mingw32
* qt-android
* vs2008
* vs2010
* vs2012
* vs2013

Thanks to the amazing ZeroMQ community you can do all the heavy lifting in C and than easily generate bindings to Python, Ruby and QML to write a nice GUI on top of it.

<A name="toc2-46" title="Demo on PLAYTerm" />
## Demo on PLAYTerm

There is a short Demo on PLAYTerm that shows how easy it is to get started with zproject: [ZeroMQ - Create new zproject](http://www.playterm.org/r/zeromq---create-new-zproject-1424116766)

<A name="toc2-51" title="Installation" />
## Installation

<A name="toc3-54" title="Requirements" />
### Requirements

zproject uses the universal code generator called GSL to process its XML inputs and create its outputs. Before you start you'll need to install GSL (https://github.com/imatix/gsl) on your system.

	git clone https://github.com/imatix/gsl.git
	cd gsl/src
	make
	make install

<A name="toc3-64" title="Getting started" />
### Getting started

GSL must be able to find the zproject resources on your system. Therefore you'll need to install them.

The following will install the zproject files to `/usr/local/bin`.

	git clone https://github.com/zeromq/zproject.git
	cd zproject
    ./autogen.sh
    ./configure
    make
    make install

NB: You may need to use the `sudo` command when running `make install` to elevate your privileges, e.g.

	sudo make install

<A name="toc2-82" title="Setup your project environment" />
## Setup your project environment

The easiest way to start is by coping the `project.xml` and `generate.sh` to your project or an empty directory. Licensing your project is important thus you'll need a license file. To get started you can copy `license.xml` from zproject and change the license to whatever you like. Here's an overview that might help you decide to [choose a license](http://choosealicense.com/). The text in the `license.xml` will be placed on every generated header and source file. Thus make sure not to insert the hole license but an appropriate disclaimer.

	mkdir myproject
	cd myproject
	cp ~/zproject/project.xml .
	cp ~/zproject/license.xml .
	cp ~/zproject/generate.sh .

Next, edit `project.xml` to your liking, see [Configuration](#Configuration). Once you're done you can create your project's build environment:

	./generate.sh
	./autogen.sh
	./configure.sh
	make

The compilation will probably fail as the generated skeleton source files are containing empty structs. You'll need to run the `generate.sh` script only when changing the zproject configuration. Otherwise stick to your favorite build environment. To also build the tests (assuming you have added some), use:

	make check

<A name="toc2-104" title="Configuration" />
## Configuration

zproject's `project.xml` contains an extensive description of the available configuration: The following snippet is taken from the `project.xml`:

<!--
    The project.xml generates build environments for:

        * android
        * autotool
        * cmake
        * mingw32
        * cygwin
        * vs2008
        * vs2010
        * vs2012
        * vs2013

    Classes are automatically added to all build environments. Further as you
    add new classes to your project you can generate skeleton header and source
    files according to http://rfc.zeromq.org/spec:21.

    name := The name of your project
    description := A short description for your project
    script := The gsl script to generate all the stuff !!! DO NOT CHANGE !!!
    email := The email address where to reach you project e.g. mailinglist.
-->
<project
    name = "myproject"
    description = "My Project that will change the world."
    script = "zproject.gsl"
    email = "mail@myproject.org"
    >

    <!--
        Includes are processed first, so XML in included files will be
        part of the XML tree
    -->
    <include filename = "license.xml" />

    <!--
        Current version of your project.
        This will be used to package your distribution
    -->
    <version major = "1" minor = "0" patch = "0" />

    <!--
        Specify which other projects this depends on.
        These projects must be known by zproject, and the list of
        known projects is maintained in the zproject_known_projects.xml model.
        You need not specify subdependencies if they are implied.
    <use project = "zyre" min_major= "1" min_minor = "1" min_patch = "0" />
    -->

    <!-- Header Files
         name := The name the header file to include without file ending
    <header name = "myproject_prelude" />
    -->

    <!--
        Classes, if the class header or source file doesn't exist this will
        generate a skeletons for them.
        use private = "1" for internal classes
    <class name = "myclass">Public class description</class>
    <class name = "someother" private = "1">Private class description</class>
    -->

    <!--
        Actors, are build using the simple actor framework from czmq. If the
        actors class header or source file doesn't exist this will generate a
        skeleton for them. The generated test method of the actor will teach
        you how to use them. Also have a look at the czmq docs to learn more
        about actors.
    <actor name = "myactor">Public actor description</actor>
    <actor name = "someactor" private = "1">Private actor description</actor>
    -->

    <!--
        Main programs built by the project
                 use private = "1" for internal tools
    <main name = "progname">Exported public tool</main>
    <main name = "progname" private = "1">Internal tool</main>
    <main name = "progname" service = "1">Installed as system service</main>
    -->

    <!--
        Benchmark programs built by the project
    <bench name = "benchname">Benchmark for class/function...</main>
    -->

    <!--
        Models that we build using GSL.
        This will generate a 'make code' target to build the models.
    <model name = "sockopts" />
    <model name = "zgossip" />
    <model name = "zgossip_msg" />

        If a model should be generated using a specific gsl script,
        this can be set through the script attribute:
    <model name = "hydra_msg" script = "zproto_codec_java.gsl" />

        Additional parameters to the script can be set via nested
        param elements:
    <model name = "hydra_msg" script = "zproto_codec_java.gsl">
        <param name = "root_path" value = "../main" />
    </model>
    -->

    <!-- Other source files that we need to package
    <extra name = "some_resource" />
    -->

    <!--
        Stuff that needs to be installed:

        NOTICE: If you copied this file to get started you want to delete or
                at least comment out those bin tag as they distribute the
                zproject files.

        * Linux -> /usr/local/bin
    -->
    <bin name = "zproject.gsl" />
    <bin name = "zproject_projects.gsl" />
    <bin name = "zproject_known_projects.xml" />
    <bin name = "zproject_class_api.gsl" />
    <bin name = "zproject_mkman" />

    <bin name = "zproject_actor.gsl" />
    <bin name = "zproject_android.gsl" />
    <bin name = "zproject_autoconf.gsl" />
    <bin name = "zproject_automake.gsl" />
    <bin name = "zproject_bench.gsl" />
    <bin name = "zproject_bindings_python.gsl" />
    <bin name = "zproject_bindings_qml.gsl" />
    <bin name = "zproject_bindings_ruby.gsl" />
    <bin name = "zproject_bindings_jni.gsl" />
    <bin name = "zproject_ci.gsl" />
    <bin name = "zproject_class.gsl" />
    <bin name = "zproject_cmake.gsl" />
    <bin name = "zproject_docs.gsl" />
    <bin name = "zproject_git.gsl" />
    <bin name = "zproject_lib.gsl" />
    <bin name = "zproject_mingw32.gsl" />
    <bin name = "zproject_cygwin.gsl" />
    <bin name = "zproject_qt_android.gsl" />
    <bin name = "zproject_tools.gsl" />
    <bin name = "zproject_vs2008.gsl" />
    <bin name = "zproject_vs2010.gsl" />
    <bin name = "zproject_vs2012.gsl" />
    <bin name = "zproject_vs2013.gsl" />
</project>

<A name="toc2-256" title="Sample API model" />
## Sample API model

The zproject scripts can also optionally generate the `@interface` in your class headers from an API model, in addition to a host of language bindings.  To opt-in to this behavior, just make a model to the `api` directory of your project.  For example, if your `project.xml` contains `<class name = "myclass"/>`, you could create the following `api/myclass.xml` file:

<!--
    This model defines a public API for binding. 
-->
<class name = "myclass" >
    My Feature-Rich Class

    <include filename = "../license.xml" />

    <constant name = "default port" value = "8080">registered with IANA</constant>

    <!-- Constructor is optional; default one has no arguments -->
    <constructor>
        Create a new myclass with the given name.
        <argument name = "name" type = "string" />
    </constructor>

    <!-- Destructor is optional; default one follows standard style -->
    <destructor />

    <!-- This models a method with no return value -->
    <method name = "sleep">
        Put the myclass to sleep for the given number of milliseconds.
        No messages will be processed by the actor during this time.
        <argument name = "duration" type = "integer" />
    </method>

    <!-- This models an accessor method -->
    <method name = "has feature">
        Return true if the myclass has the given feature.
        <argument name = "feature" type = "string" />
        <return type = "boolean" />
    </method>

    <!-- Callback typedefs can be declared like methods -->
    <callback_type name = "handler_fn">
        <argument name = "self" type = "myclass" />
        <argument name = "action" type = "string" />
        <return type = "boolean" />
    </callback_type>

    <!-- Callback types can be used as method arguments -->
    <method name = "add handler">
        Store the given callback function for later
        <argument name = "handler" type = "myclass_handler_fn" callback = "1" />
    </method>

    <!-- If singleton = "1", no class struct pointer is required. -->
    <method name = "test" singleton = "1">
        Self test of this class
        <argument name = "verbose" type = "boolean" />
    </method>

    <!-- These are the types we support
         Not all of these are supported in all language bindings;
         see each language binding's file for supported types in that
         language, and add more types as needed where appropriate.
         -->
    <method name = "tutorial">
        <argument name = "void pointer" type = "anything" />
        <argument name = "standard int" type = "integer" />
        <argument name = "standard float" type = "real" />
        <argument name = "standard bool" type = "boolean" />
        <argument name = "char pointer" type = "string" />
        <argument name = "custom pointer" type = "myclass_t" />
        <return type = "nothing">void method</return>
    </method>
</class>

This would cause the following `@interface` to be generated inside of `include/myclass.h`.  Note that if `include/myclass.h` has other handwritten content outside of the `@interface` this content will be retained.

    //  @warning THE FOLLOWING @INTERFACE BLOCK IS AUTO-GENERATED BY ZPROJECT!
    //  @warning Please edit the model at "api/myclass.xml" to make changes.
    //  @interface
    //  Create a new myclass with the given name.
    MYPROJECT_EXPORT myclass_t *
        myclass_new (const char *name);

    //  Destroy the myclass.
    MYPROJECT_EXPORT void
        myclass_destroy (myclass_t **self_p);

    //  Return true if the myclass has the given feature.
    MYPROJECT_EXPORT bool
        myclass_has_feature (myclass_t *self, const char *feature);

    //  Put the myclass to sleep for the given number of milliseconds.
    //  No messages will be processed by the actor during this time.
    MYPROJECT_EXPORT void
        myclass_sleep (myclass_t *self, int duration);

    //  Self test of this class
    MYPROJECT_EXPORT void
        myclass_test (bool verbose);
    //  @end

Language bindings will also be generated in the following languages:

* Ruby
* QML
* Python
* JNI (experimental)

The language bindings are minimal, meant to be wrapped in a handwritten idiomatic layer later.

<A name="toc3-365" title="Supported API Model Attributes" />
### Supported API Model Attributes

The following attributes are supported for methods:
- `name` - the name of the method (mandatory).
- `singleton = "1"` - the method is not invoked within the context of a specific instance of an object. Use this if your method does not need to be passed a `self` pointer as the first argument as normal. Implicit for all `constructor`s and `destructor`s and for the implicit `test` method.

The following attributes are supported for arguments and return values:
- `type` - the conceptual type or class name of the argument or return value (default: `"nothing"`, which translates to `void` in C).
- `constant = "1"` - the argument will not be modified, or the return valu
e should not be modified (roughly translates to `const` in C).
- `by_reference = "1"` - ownership of the argument (and responsibility for freeing it) is transferred from the caller to the function - in practice, the implementation code should also nullify the caller's reference, though this is not enforced by the API model.
- `fresh = "1"` - the return value is freshly allocated, and the caller receives ownership of the object and the responsibility for destroying it.
- `variadic = "1"` - used for representing variadic arguments.
- `is_format = "1"` - used for `printf`-style format strings preceding variadic arguments (which are added automatically).

<A name="toc3-381" title="Tips" />
### Tips

At any time, you can examine a resolved model as an XML string with all of its children and attributes using the appropriate GSL functions:

```gsl
<A name="toc1-387" title="if the `method` variable is a <method/> entity:" />
# if the `method` variable is a <method/> entity:
echo method.string()  # will print the model as an XML string.
method.save(filename) # will save the model as an XML string to the given file.
```

<A name="toc2-393" title="Ownership and License" />
## Ownership and License

The contributors are listed in AUTHORS. This project uses the MPL v2 license, see LICENSE.

zproject uses the [C4.1 (Collective Code Construction Contract)](http://rfc.zeromq.org/spec:22) process for contributions.

To report an issue, use the [zproject issue tracker](https://github.com/zeromq/zproject/issues) at github.com.

<A name="toc2-402" title="Removal" />
## Removal

<A name="toc3-405" title="autotools" />
### autotools

    make uninstall

<A name="toc3-410" title="Hints to Contributors" />
### Hints to Contributors

Make sure that the project model hides all details of backend scripts. For example don't make a user enter a header file because autoconf needs it.

Do read your code after you write it and ask, "Can I make this simpler?" We do use a nice minimalist and yet readable style. Learn it, adopt it, use it.

Before opening a pull request read our [contribution guidelines](https://github.com/zeromq/zproject/blob/master/CONTRIBUTING.md). Thanks!

<A name="toc3-419" title="Notes for Writing Language Bindings" />
### Notes for Writing Language Bindings

<A name="toc4-422" title="Schema/Architecture Overview" />
#### Schema/Architecture Overview

* All `class`es SHALL be in the project model (`project.xml`).
* Each `class` MAY have a corresponding API model (`api/{class name}.xml`).
* A binding generator SHOULD consider only `class`es with an API model (`where defined (class.api)`).
* Each API model SHALL consist of both explicit information (written in the XML file) and implicit information (inferred by the [`zproject_class_api`](zproject_class_api.gsl) script). Both kinds of information will already be resolved (and indistinguishable) when each language binding generator is invoked.
* Each API model SHALL have exactly one `class` entity at the top level.
* Each `class` SHALL have a `name` attribute.
* Each `class` MAY have one or more `method` entities.
* Each `class` MAY have one or more `constructor` entities.
* Each `class` MAY have one or more `destructor` entities.
* Each `method`, `constructor`, and `destructor` MAY have one or more `argument` entities.
* Each `method`, `constructor`, and `destructor` SHALL at least one `return` entity, and if more than one `return` entity exist, only the first SHOULD be considered. The `return` entity MAY be ignored if it has `type = "nothing"` (the default when no `type` is given).
* Each entity SHALL have its semantic attributes fully resolved before reaching the language binding generators.
* Each language binding generator SHALL NOT modify values of semantic attributes of entities.
* Each language binding generator MAY assign values to language-specific implementation attributes of entities.
* Each language binding generator SHOULD use a unique prefix for names of language-specific implementation attributes of entities.

<A name="toc4-441" title="Informal Summary" />
#### Informal Summary

A `class` is always the top-level entity in an API model, and it will be merged with the corresponding `class` entity defined in the project model. A class contains `method`s, `constructor`s, and `destructor`s (collectively, "method"s), and methods contain `argument`s and `return`s (collectively, "container"s). Each entity will contain both *semantic attributes* and *language-specific implementation attributes*.

<A name="toc4-446" title="Semantic Attributes" />
#### Semantic Attributes

Semantic attributes describe something intrinsic about the container.

For example, arguments may be described as passed `by_reference` to indicate that ownership is transferred from the caller. Similarly, return values may be described as `fresh` to indicate that ownership is transferred to the caller, which must destroy the object when it is finished with it. It's important to remember that these attributes are primarily meant to be an abstraction that describes conceptual information, leaving the details of how code generators interpret (or ignore) each attribute up to the authors.

Semantic attributes may be implicit (not given a value in the written model). In this case, it is up to the [`zproject_class_api`](zproject_class_api.gsl) script to fully resolve default values for all attributes. Downstream code generators should *never* resolve or alter semantic attributes, as this could change the behavior of any code generator that is run after the errant code generator.

These are the semantic attributes for each kind of entity that will be resolved before language bindings generators are invoked:

```gsl
class.name        # string (as given in the API model)
class.description # string (comment in the API model, or empty string)
```
```gsl
method.name           # string (as given in the API model, or a default value)
method.description    # string (comment in the API model, or a default value)
method.singleton      # 0/1 (default: 0, but 1 for constructors/destructors)
method.is_constructor # 0/1 (default: 0, but 1 for constructors)
method.is_destructor  # 0/1 (default: 0, but 1 for destructors)
```
```gsl
container.name         # string (as given in the API model, or "_")
container.type         # string (as given, or "nothing")
container.constant     # 0/1 (default: 0)
container.by_reference # 0/1 (default: 0)
container.callback     # 0/1 (default: 0)
container.fresh        # 0/1 (default: 0)
container.is_format    # 0/1 (default: 0)
container.is_enum      # 0/1 (default: 0)
container.enum_name    # string if is_enum, otherwise undefined
container.enum_class   # string if is_enum, otherwise undefined
```

<A name="toc4-481" title="Language-Specific Implementation Attributes" />
#### Language-Specific Implementation Attributes

Language-specific implementation attributes hold information that is not
intrinsic to the concept of the container, but to the binding implementation.

In practice, language-specific attributes may contain any information that
is useful to store as part of the container model to facilitate generation, according to any schema that is useful, but it may be helpful to try to follow patterns observed in other code generator scripts.

However, because the container is shared between all generators, which are
run in an unspecified order, it's important that generators not rely on
information resolved in generators. The one exceptions is that many
generators will rely directly on information from the C implementation
to which they bind.

It is also important that language-specific implementation attributes
use a naming convention that avoids collisions. The easiest way to
avoid collisions is to prefix all language-specific attributes with the
name of the language, though in principle, any collision-free convention
would be acceptable.

<A name="toc3-502" title="This Document" />
### This Document

This document is originally at README.txt and is built using [gitdown](http://github.com/imatix/gitdown).
