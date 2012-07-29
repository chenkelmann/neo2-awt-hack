*Important Notice*
While this version compiles with Java 7, it is absolutely useless :)

In Java 7, the NEO2 Keyboard layout works fine out of the box, so no need to bother with this hack.

If you use Java 6, the hack in the "Java 6" branch should work for you.

For Java 5, use version 0.2, you can find a binary download here: 
http://maven.henkelmann.eu/eu/henkelmann/neo2-awt-hack/0.2/neo2-awt-hack-0.2.jar

# NEO2 AWT Hack #

This is the version for Java 7.

It is known to work for OpenJDK. If you successfully use it for the Oracle JDK, 
please let me know.

This is a nasty hack to enable the neo 2 keyboard layout in AWT/Swing based 
programs under X11 (e.g. Linux). 

For information on the neo 2 keyboard layout see: http://www.neo-layout.org/
Usage of this hack is a workaround for the following issue: http://wiki.neo-layout.org/ticket/129

## Why? ##
The neo 2 keyboard layout uses additional modifiers that enable additional 
keyboard layers. These layers are not properly mapped to java keycodes under
X11, which causes notably the 4th layer (cursor keys and num block) not to work.

## How? ## 
This hack contains a modified version of the java runtime internal class 
`sun.awt.X11.XKeysym` in which the mapping from X11 keycodes to java keycodes
takes place. This modified version contains an additional lookup into a hardcoded
hash map that maps to the appropriate key code if the level 4 modifier key is pressed.

By adding the patched class to the bootclasspath it gets loaded before the "proper"
class shipped with the JRE, which causes the patch to be used when mapping X11 keycodes.

## Compiling ##
As we are recompiling against the internal classes in the java runtime (found 
in `rt.jar` in your java installation), compilation is somewhat of an issue.

Manual compilation as well as compilation with maven fails, even if we explicitly
specify `rt.jar` as a system dependency (maven) or add it to the classpath for
`javac` (any pointers on how to build this with maven are welcome)

Strangely enough, Eclipse has no problem compiling this class if the proper 
java runtime is set, so to get the distribution jar, do the following: 

* import the project as a maven project in eclipse
* compile with eclipse (should happen automagically on import)
* package it on the console with maven: `mvn package`


## Using it ##

Add it to the bootclasspath of your java application like so: 

    java -Xbootclasspath/p:/your/path/to/the/jar/neo2-awt-hack-[VERSION].jar -cp foo.jar com.example.Foo

Most java applications are started using some script, in this case you either 
have to modify the script or some config file that contains parameters for the JVM.

For IntelliJ, you have to modify your `idea.vmoptions` file and add the following line: 

    -Xbootclasspath/p:/your/path/to/the/jar/neo2-awt-hack-[VERSION].jar

## Download ##

You can download a binary from http://maven.henkelmann.eu/eu/henkelmann/neo2-awt-hack/0.3/neo2-awt-hack-0.3.jar

## Common Pitfalls ##

### Make sure you use XToolkit ###
In order for this hack to work you have to make sure that AWT uses the XToolkit. 
(There are two underlying toolkits AWT uses on Linux and Solaris to map from X11 
to AWT, the XToolkit and the MToolkit, more info here: 
http://download.oracle.com/javase/1.5.0/docs/guide/awt/1.5/xawt.html). 
So if the hack does not work for you, try to set the following environment variable for the app you apply the hack to:

    export AWT_TOOLKIT=XToolkit



