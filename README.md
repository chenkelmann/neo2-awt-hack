*Important Notice*
In Java 7 and higher, the NEO2 Keyboard layout works fine out of the box with most programs, so you prabably need not bother with this hack.

The only exception is Android Studio / IntelliJ, where the main editor component is so customized that it handles the AWT events differently
from normal text edit widgets. (See https://youtrack.jetbrains.com/issue/IDEA-113997 and http://wiki.neo-layout.org/ticket/364)

If you use Java 6, the hack in the "Java 6" branch should work for you. (forgot to backup a binary somewhere - sorry)

*Even More Important Notice*
THIS IS A VERY NASTY HACK/WORKAROUND. I TAKE *NO* RESPONSIBLE WHATSOEVER TO ANY DAMAGE IT MIGHT CAUSE!

# NEO2 AWT Hack #

This is the version for Java 8, Linux, official Oracle JDK.
If you need a version for Java7 and/or OpenJDK, just get the source for the respective version of the `KeyEvent` class and apply the hack. To find the modified parts, just grep for "begin of ugly hack" in the current version.

If you successfully use it for OpenJDK 8, please let me know.

This is a nasty hack to enable the neo 2 keyboard layout in IntelliJ under X11 (e.g. Linux). 

For information on the neo 2 keyboard layout see: http://www.neo-layout.org/
Usage of this hack is a workaround for the following issue: http://wiki.neo-layout.org/ticket/364

## Why? ##
The neo 2 keyboard layout uses additional modifiers that enable additional 
keyboard layers. These do not work in the IntelliJ main editor widget.

## How? ## 
This hack contains a modified version of the java class 
`java.awt.KeyEvent`. This modified version contains an additional lookup into a hardcoded
array that maps to the appropriate key code if one of the known broken keys is pressed.

By adding the patched class to the bootclasspath it gets loaded before the "proper"
class shipped with the JRE, which causes the patch to be used when mapping keycodes.

## Compiling ##
Simply compile with maven: `mvn clean package`


## Using it ##

Add it to the bootclasspath of your java application like so: 

    java -Xbootclasspath/p:/your/path/to/the/jar/neo2-awt-hack-0.4-java8oracle.jar -cp foo.jar com.example.Foo

Most java applications are started using some script, in this case you either 
have to modify the script or some config file that contains parameters for the JVM.

For IntelliJ, you have to modify your `idea.vmoptions` file and add the following line: 

    -Xbootclasspath/p:/your/path/to/the/jar/neo2-awt-hack-0.4-java8oracle.jar
    
For Android Studio the file is `studio.vmoptions` or `studio64.vmoptions`

## Download ##

You can download a binary from https://github.com/chenkelmann/neo2-awt-hack/tree/master/releases/neo2-awt-hack-0.4-java8oracle-intellij.jar
