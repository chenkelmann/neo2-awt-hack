*Important Notice*

In Java 7 and higher, the NEO2 Keyboard layout works fine out of the box with most programs, so you prabably need not bother with this hack. 

For Android Studio / IntelliJ / PyCharm you also do not need this hack anymore - you can apply the solution described in the following jetbrains ticket instead: 
https://youtrack.jetbrains.com/issue/IDEA-197762

> Enable "Settings | Keymap | Use national keyboards for shortcuts".

---------------------------------------------------------------

*Even More Important Notice*

THIS IS A VERY NASTY HACK/WORKAROUND. I TAKE *NO* RESPONSIBLE WHATSOEVER TO ANY DAMAGE IT MIGHT CAUSE!

----------------------------------------------------------------

The following instructions are kept for archive reasons - you really should not need to use this hack anymore! (see above)

# NEO2 AWT Hack #

This is the version for Java 8 and above, Linux, tested with official Oracle JDK and OpenJDK.

If you need a version for Java7, just get the source for the respective version of the `KeyEvent` class and apply the hack. To find the modified parts, just grep for "begin of ugly hack" in the current version.

So far it has been reported with Java 8, OpenJDK and Oracle JDK and Java 11 OpenJDK (including the version bundled with IntelliJ)

This is a nasty hack to enable the neo 2 keyboard layout in IntelliJ / Android Studio / PyCharm under X11 (e.g. Linux).

For information on the neo 2 keyboard layout see: http://www.neo-layout.org/
Usage of this hack is a workaround for the following issue: http://wiki.neo-layout.org/ticket/364

##Why?
The neo 2 keyboard layout uses additional modifiers that enable additional keyboard layers. These do not work or only work partially in the IntelliJ main editor widget. 

##How?
This hack contains a modified version of the java class
`java.awt.KeyEvent`. This modified version contains an additional lookup into a hardcoded
array that maps to the appropriate key code if one of the known broken keys is pressed.

By adding the patched class to the bootclasspath it gets loaded before the "proper"
class shipped with the JRE, which causes the patch to be used when mapping keycodes.

## Compiling ##
Simply compile with maven: `mvn clean package`

## Using it ##

### JDK 6 

If you use Java 6, the hack in the "Java 6" branch should work for you. (forgot to backup a binary somewhere - sorry)

### JDK 8
For Java 8, add it to the bootclasspath of your java application like so:

    java -Xbootclasspath/p:/your/path/to/the/jar/neo2-awt-hack-0.5-java8oracle.jar -cp foo.jar com.example.Foo

Most java applications are started using some script, in this case you either
have to modify the script or some config file that contains parameters for the JVM.

For IntelliJ, you have to modify your `idea.vmoptions` file and add the following line:

    -Xbootclasspath/p:/your/path/to/the/jar/neo2-awt-hack-0.5-java8oracle.jar

For Android Studio the file is `studio.vmoptions` or `studio64.vmoptions`
For PyCharm it is `pycharm.vmoptions` or `pycharm64.vmoptions`.

The `vmoptions` files of IntelliJ based IDEs can be found in the `bin` folder of the IDE installation.

### JDK 9+

Since JDK 9 the option `-Xbootclasspath/p` is not available anymore. It was replaced
with `--patch-module` which has to be used like so:

    --patch-module java.desktop=/your/path/to/the/jar/neo2-awt-hack-0.5-java8oracle.jar

It can be added to the `vmoptions` file like so: 
    
    --patch-module=java.desktop=/your/path/to/the/jar/neo2-awt-hack-0.5-java8oracle.jar
    
(note the `=` after `patch-module`)       

This also fixes problems with the bundled JDK, which works for most Neo 2 features but still has problems when the `shift` key is pressed when using Level 4 (e.g. cursor) keys.

## Download ##

You can download a binary from https://github.com/chenkelmann/neo2-awt-hack/blob/master/releases/neo2-awt-hack-0.5-java8oracle.jar?raw=true

Do not be confused by the suffix `java8oracle`, it seems to work for OpenJDK and versions > 8 as well. 
