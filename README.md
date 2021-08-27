# CLR.ahk

Key Features:

  - Load the Common Language Runtime into the script's process.
  - Load .NET assemblies (dll files) by full name, partial name, or path.
  - Instantiate objects and call instance methods or properties.
  - Compile C# or VB code on the fly or to file.

The script should work with .NET Framework versions up to v4.x. It **does not support** .NET Core or .NET 5.0, as hosting these runtimes requires the use of different APIs, with significant limitations.

For compiling code, up to C# 5.0 is supported, depending on the installed .NET Framework version. Later versions of the compiler are not included in the .NET Framework.

Examples (mostly for AutoHotkey v1.1) and discussion can be found at [the AutoHotkey forums](https://www.autohotkey.com/boards/), in particular [NET Framework Interop (CLR, C#, VB) - AutoHotkey Community](https://www.autohotkey.com/boards/viewtopic.php?f=6&t=4633).

## Functions

### CLR_Start( [ RuntimeVersion ] )
Loads the Common Language Runtime. *RuntimeVersion* specifies the exact version to load - for example, "v2.0.50727" or "v4.0.30319". If omitted, the latest version is loaded. If this function is not called and another CLR function requires the runtime to be loaded, the latest version will be loaded.

### CLR_StartDomain( ByRef AppDomain [, BaseDirectory ] )
Starts a new AppDomain and stores a pointer or reference to it in *AppDomain*. This can be passed to CLR_LoadLibrary() to load an assembly into the AppDomain. *BaseDirectory* defines the base search path used when loading assemblies into the AppDomain.

### CLR_StopDomain( AppDomain )
Stops the specified AppDomain and attempts to unload any assemblies that were loaded into it.

### CLR_LoadLibrary( AssemblyName [, AppDomain ] )
Loads an assembly, where *AssemblyName* is its full name, partial name or path. Optionally loads the assembly into the given AppDomain instead of the default AppDomain. Returns a pointer or reference to the Assembly, which can be used with CLR_CreateObject.

**Note:** Once an assembly is loaded, it can only be unloaded by stopping the AppDomain which contains it.

### CLR_CreateObject( Assembly, sType [, Arg1, Arg2 ... ] )
Instantiates an object of the specified type from the specified assembly. Optionally accepts a list of arguments to pass to the object's constructor. Use `ComObject(Type, Arg)` to pass a typed value. A list of type codes can be found [here](https://www.autohotkey.com/docs/commands/ComObjType.htm). Alternatively, you can call `Assembly.CreateInstance(sType)` directly if you do not need to pass parameters.

### CLR_CompileC#( Code, References [, AppDomain, FileName, CompilerOptions ] )
### CLR_CompileVB( Code, References [, AppDomain, FileName, CompilerOptions ] )
Compile the specified C# or VB code. If *FileName* is omitted, the assembly is compiled "in-memory" and automatically loaded. DLL and EXE files may be generated. Specify for *References* a pipe (`|`) delimited list of assemblies that the code requires. If *FileName* is omitted and compilation is successful, the function returns a reference to the compiled and loaded Assembly, which can be used with CLR_CreateObject; otherwise it returns *FileName* on success or 0 on failure.

**Note:** Some versions of .NET may require an explicit reference to the appropriate language dll, such as `Microsoft.CSharp.dll`.

Additional command-line arguments can be passed to the compiler via *CompilerOptions*. For instance, if FileName specifies an .exe file, a console app is generated unless *CompilerOptions* includes "/target:winexe".
