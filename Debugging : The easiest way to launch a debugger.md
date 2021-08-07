
## The easiest method to launch a debugger in .Net(C#) and Win32 (C++).

#### Introduction
Debugging is always a difficult process when we are debugging a complex system involving many applications . It makes
more difficult if we are not sure at what time these applicatiions are started and ended.
In such situations , Normally we attach the debugger to the process directly using the Process ID . Each debugger has different methods for attaching to the debugger. The more easiest method is by using Visual studio you can attach the process directly and start debugging . 

These method are works fine in most of the cases but the method has following problems and does not cover all the scenarios.

#### Scenario 1 - To debug the startup code of the application.
It is difficult to debug the startup code an application like 'WinMain() or main()' becouse these codes are already 
executed when try to attach the debugger. Another example is to debug the startup code of a Win32 service - 'ServiceMain()'.
We always need our debugger should be attached to the process right from startup code (WinMain() | main() | ServiceMain()).

#### Scenario 2 - To debug the entry point function of an assembly or DLL.
It is also difficult to debug an assembly or DLL entry point function when we are not sure at what time our entry point function called or which process load entry point function. 

#### Problem  1 - The debugger attach process will kill most of your time.
There is cost of time to attach the debugger to the running process and it may affect your productivity.

Please use following solution to solve these issues and handle the scenarios while debugging.

####Solution 

#### Method 1 - Launch Debugger in .Net(C#)

#### Method 2 - Lauch a Debugger in Win32(C++)

```C++
require 'redcarpet'
markdown = Redcarpet.new("Hello World!")
puts markdown.to_html
```
