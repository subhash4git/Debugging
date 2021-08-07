
## **The Easiest Method To Launch A Debugger In .Net(C#) And Win32 (C++).**

#### **Introduction**
Debugging is always a difficult process when we are debugging a complex system involving many applications . It makes
more difficult if we are not sure at what time these applicatiions are started and ended.
In such situations , Normally we attach the debugger to the process directly using the Process ID . Each debugger has different methods for attaching to the debugger. The more easiest method is by using Visual studio you can attach the process directly and start debugging . 

These method are works fine in most of the cases but the method has following problems and does not cover all the scenarios.

#### **Scenario 1 - To debug the startup code of the application.**
It is difficult to debug the startup code an application like 'WinMain() or main()' becouse these codes are already 
executed when try to attach the debugger. Another example is to debug the startup code of a Win32 service - 'ServiceMain()'.
We always need our debugger should be attached to the process right from startup code (WinMain() | main() | ServiceMain()).

#### **Scenario 2 - To debug the entry point function of an assembly or DLL.**
It is also difficult to debug an assembly or DLL entry point function when we are not sure at what time our entry point function called or which process load entry point function. 

#### **Problem  1 - Attaching the debugger to a running process will kill most of your time.**
There is cost of time to attach the debugger to the running process and it may affect your productivity.

Please use following solution to solve these issues and handle the scenarios while debugging.

#### **SOLUTION**

#### **Method 1 - Launch Debugger in .Net(C#)**
Infact its very easy in Dot Net based application . You can launch and attach the debugger stright away by running
the following code snippet in your 'main()'. You can even use in your assemblies entry point function. 
So whenever the application start or the entry point function is called the debugger is start running stright away.
This will save most of your time and allow you to debug your startup codes in any complex systems with many applications.
Make sure you annotated the call with #if DEBUG to run the code only in debug mode.
```C#
        static void Main(string[] args)
        {
            // The following code will be executed only in debug mode.
            // The logic will attach a debugger to the process for helping your debugging.
            #if DEBUG
              System.Diagnostics.Debugger.Launch();
            #endif

            Console.WriteLine("Hey , You started debugging...! , Go Ahead. Happy Debugging.");
            Console.ReadKey();
        }
```

#### **Method 2 - Lauch a Debugger in Win32(C++)**
In Win32 based C++ , There is no direct Debug API availble to attach the current running process to debugger.
The following code snippet will attach the current process to Visual Studio JIT debugger.
Make sure you annotated the call with #if DEBUG to run the code only in debug mode.
Call the API - 'AttachDebugger()'.
Prerequisite - Visual studio debugger should be installed in the machine. 
```C++
bool RunDebugger(TCHAR* szPath)
{
    bool bStatus = true;

    if (NULL == szPath) return false;

    STARTUPINFOW si = { 0 };
    PROCESS_INFORMATION pi = { 0 };

    ZeroMemory(&si, sizeof(si));
    si.cb = sizeof(si);
    ZeroMemory(&pi, sizeof(pi));

    bStatus = CreateProcessW(NULL, szPath, NULL, NULL, FALSE, 0, NULL, NULL, &si, &pi);
    if (bStatus) {
        // Close debugger process handles to eliminate resource leak
        CloseHandle(pi.hThread);
        CloseHandle(pi.hProcess);

        // Wait for the debugger to attach
        while (!IsDebuggerPresent()) Sleep(100);

        // Stop execution so the debugger can take over
        DebugBreak();
    }
    return bStatus;
}

bool AttachDebugger()
{
    bool bStatus = true;
    TCHAR szSystemDir[MAX_PATH] = { 0 };
    TCHAR szDebuggerPath[MAX_PATH] = { 0 };
    TCHAR szFullPath[MAX_PATH] = { 0 };

    bStatus = (0 != (GetSystemDirectoryW(szSystemDir, ARRAYSIZE(szSystemDir))));
    if (bStatus) {
        bStatus = SUCCEEDED(StringCchPrintf(szDebuggerPath, ARRAYSIZE(szDebuggerPath), TEXT("%s\\vsjitdebugger.exe"), szSystemDir));
        if (bStatus) {
            bStatus = PathFileExists(szDebuggerPath);
            if (bStatus) {
                // Get process ID and create the command line
                DWORD dwPid = GetCurrentProcessId();
                StringCchPrintf(szFullPath, ARRAYSIZE(szFullPath), TEXT("%s %s %d"),
                                                      szDebuggerPath, L"-p", dwPid);
                if (bStatus) {
                    bStatus = RunDebugger(szFullPath);
                }
            }
        }
    }
    return true;
}

// Forward declarations of functions included in this code module:
ATOM                MyRegisterClass(HINSTANCE hInstance);
BOOL                InitInstance(HINSTANCE, int);
LRESULT CALLBACK    WndProc(HWND, UINT, WPARAM, LPARAM);
INT_PTR CALLBACK    About(HWND, UINT, WPARAM, LPARAM);

int APIENTRY wWinMain(_In_ HINSTANCE hInstance,
                     _In_opt_ HINSTANCE hPrevInstance,
                     _In_ LPWSTR    lpCmdLine,
                     _In_ int       nCmdShow)
{
    UNREFERENCED_PARAMETER(hPrevInstance);
    UNREFERENCED_PARAMETER(lpCmdLine);

     #ifdef _DEBUG

      AttachDebugger();
     
     #endif
    
    return 0;
}
```
