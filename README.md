*Important Notice*

In Java 7 and higher, the NEO2 Keyboard layout works fine out of the box with most programs, so you prabably need not bother with this hack. 

The only exception is Android Studio / IntelliJ, where the main editor component is so customized that it handles the AWT events differently from normal text edit widgets. (See https://youtrack.jetbrains.com/issue/IDEA-113997 and http://wiki.neo-layout.org/ticket/364)

If you use Java 6, the hack in the "Java 6" branch should work for you. (forgot to backup a binary somewhere - sorry)

*Even More Important Notice*

THIS IS A VERY NASTY HACK/WORKAROUND. I TAKE *NO* RESPONSIBLE WHATSOEVER TO ANY DAMAGE IT MIGHT CAUSE!

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

This can not be added to the `vmoptions` since it is a cli parameter. So to add it,
you must find the startup script for your IntelliJ-based IDE (Idea, PyCharm, Android Studio, CLion etc.) you want to hack and add the above option to the actual java cli call (which is usually the last thing in the script).

E.g. the last lines in a `clion.sh` or `idea.sh` now look (almost) like this:

    "$JAVA_BIN" \
      -classpath "$CLASSPATH" \
      ${VM_OPTIONS} \
      "-XX:ErrorFile=$HOME/java_error_in_CLION_%p.log" \
      "-XX:HeapDumpPath=$HOME/java_error_in_CLION.hprof" \
      -Didea.paths.selector=CLion2019.3 \
      "-Djb.vmOptionsFile=$VM_OPTIONS_FILE" \
      ${IDE_PROPERTIES_PROPERTY} \
      -Didea.platform.prefix=CLion \
      --patch-module java.desktop=/your/path/to/the/jar/neo2-awt-hack-0.5-java8oracle.jar \
      com.intellij.idea.Main \
      "$@"

This also fixes problems with the bundled JDK, which works for most Neo 2 features but still has problems when the `shift` key is pressed when using Level 4 (e.g. cursor) keys.

## Download ##

You can download a binary from https://github.com/chenkelmann/neo2-awt-hack/blob/master/releases/neo2-awt-hack-0.5-java8oracle.jar?raw=true

Do not be confused by the suffix `java8oracle`, it seems to work for OpenJDK and versions > 8 as well. 
