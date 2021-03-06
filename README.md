# PerfView OverView
PerfView is a free performance-analysis tool that helps isolate CPU and memory-related performance issues.  It is a Windows tool, but it also has some support for analyzing data collected on Linux machines.  It works for a wide variety of scenarios, but has a number of special features for investigating performance issues in code written for the .NET runtime.  

If you are unfamiliar with PerfView, there are [PerfView video tutorials](http://channel9.msdn.com/Series/PerfView-Tutorial). 
Also, [Vance Morrison's blog](http://blogs.msdn.com/b/vancem/archive/tags/perfview) gives overview and getting 
started information. 

### Getting PerfView 
Please see the [PerfView Download Page](documentation/Downloading.md) for the link and instructions for downloading the 
current version of PerfView.  

### Learning about PerfView 

The PerfView User's Guide is part of the application itself. In addition, you can click the
[Users Guide link](http://htmlpreview.github.io/?https://github.com/Microsoft/perfview/blob/master/src/PerfView/SupportFiles/UsersGuide.htm) 
to see the [GitHub HTML Source File](src/PerfView/SupportFiles/UsersGuide.htm) rendered in your browser.  You can also simply
download PerfView using the instructions above and select the Help -> User's Guide menu item. 

### Asking Questions / Reporting Bugs 

When you have question about PerfView, your first reaction should be to search the Users Guide (Help -> User's Guide) and 
see if you can find the answer already.   If that does not work you can ask a question by creating a [new PerfView Issue](https://github.com/Microsoft/perfview/issues/new).
State your question succinctly in the title, and if necessary give details in the body of the issue, there is a issue tag
called 'question' that you should use as well that marks your issue as a question rather than some bug report.
If the question is is specific to a particular trace (*.ETL.ZIP file) you can drag that file onto the issue and it will be downloaded.
This allows those watching for issues to reproduce your environment and give much more detailed and useful answer.

Note that once you have your question answered, if the issue is likley to be common, you should strongly consider updating the
documentation to include the information.  The documentation is pretty much just
one file https://github.com/Microsoft/perfview/blob/master/src/PerfView/SupportFiles/UsersGuide.htm.
You will need to clone the repository and create a pull request (see [OpenSourceGitWorkflow](https://github.com/Microsoft/perfview/blob/master/documentation/OpenSourceGitWorkflow.md)
for instructions for setting up and creating a pull request.  

Reporting bugs works pretty much the same way as asking a question.  It is very likely that you will want to include the *.ETL.ZIP
file needed to reproduce the problem as well as any steps and the resulting undesirable behavior.

### Developer information

This repository uses [AppVeyor](https://www.appveyor.com/) to automatically build and test pull requests, which allows
the community to easily view build results. Code coverage is provided by [codecov.io](https://codecov.io). The build and
coverage status reflected here is the AppVeyor build status of the **master** branch.

[![Build status](https://ci.appveyor.com/api/projects/status/fxtu3xa874whk2w0?svg=true)](https://ci.appveyor.com/project/sharwell/perfview)

[![codecov](https://codecov.io/gh/Microsoft/perfview/branch/master/graph/badge.svg)](https://codecov.io/gh/Microsoft/perfview)

> :warning: Builds produced by AppVeyor are not considered official builds of PerfView, and are not signed or otherwise
> validated for safety or security in any way. This build integration is provided as a convenience for community
> participants, but is not endorsed by Microsoft nor is it considered an official release channel in any way. For
> information about official builds, see the [PerfView Download Page](documentation/Downloading.md) page.

# Building PerfView Yourself

If you just want to do a performance investigation, you don't need to build PerfView yourself.
Just use the one from the [PerfView Download Page](documentation/Downloading.md).
However if you want new features or just want to contribute to PerfView to make it better
(see [issues](https://github.com/Microsoft/perfview/issues) for things people want)
you can do that by following the rest of these instructions.

### Tools Needed to Build PerfView

The only tool you need to build PerfView is Visual Studio 2017.   The [Visual Studio 2017 Community Edition](https://www.visualstudio.com/vs/community/)
can be downloaded *for free* and has everything you need to fetch PerfView from GitHub, build and test it. We expect you
to download Visual Studio 2017 Community Edition if you don't already have Visual Studio 2017.

PerfView is mostly C# code, however there is a small amount of C++ code to implement some advanced features of PerfView 
(The ETWCLrProfiler dlls that allow PerfView to intercept the .NET Method calls; see .NET Call in the Collect dialog).  If you downloaded the Visual Studio 2017 Community Edition, it does not install the C++ compilation tools by default,
but VS should detect that the solution needs C++ and ask you to install those tools when you open the solution. Allow it
to do this and everything should 'just work'.    

### Cloning the PerfView GitHub Repository. 

The first step in getting started with the PerfView source code is to clone the PerfView GitHub respository.
If you are already familiar with how GIT, GitHub, and Visual Studio 2017 GIT support works, than you can skip this section.
However, if not, the [Setting up a Local GitHub repository with Visual Studio 2017](documentation/SettingUpRepoInVS.md) document
will lead you through the basics of doing this.   All it assumes is that you have Visual Studio 2017 installed.

### How to Build and Debug PerfView 

PerfView is developed in Visual Studio 2017 using features through C# 6.

  * The solution file is PerfView.sln.  Opening this file in Visual Studio (or double clicking on it in 
  the Windows Explorer) and selecting Build -> Build Solution, will build it.    You can also build the 
  non-debug version from the command line using msbuild or the build.cmd file at the base of the repository.
  The build follows standard Visual Studio conventions, and the resulting PerfView.exe file ends up in
  src/PerfView/bin/*BuildType*/PerfView.exe.   You need only deploy this one EXE to use it.  

  * The solution consists of 11 projects, representing support DLLs and the main EXE.   To run PerfView in the 
  debugger **you need to make sure that the 'Startup Project' is set to the 'PerfView' project** so that it launches 
  the main EXE.   If the PerfView project in the Solution Explorer (on the right) is not bold, right click on the PerfView project 
  and select 'Set as Startup Project'.    After doing this 'Start Debugging' (F5) should work.
  (It is annoying that this is not part of the .sln file...).  

### Deploying your new version of Perfview

You will want to deploy the 'Release' rather than the 'Debug' version of PerfView.  Thus, first set your build configuration
to 'Release' (Text window in the top toolbar, or right click on the .SLN file -> Configuration Manager -> Active Solution Configuration).
Next build (Build -> Build Solution (Ctrl-Shift-B)).   The result will be that in the src\perfView\bin\net45\Release directory there will be
among other things a PerfView.exe.   This one file is all you need to deploy.   Simply copy it to where you wish to deploy the app.  

### Information for build troubleshooting.  

  * One of the unusual things about PerfView is that it incorporates its support DLL into the EXE itself, and these get 
  unpacked on first launch.  This means that there are tricky dependencies in the build that are not typical.    You will 
  see errors that certain DLLs can't be found if there were build problems earlier in the build.   Typically you can fix 
  this simply by doing a normal (non-clean) build, since the missing file will be present from the last compilation.
  If this does not fix things, see if the DLL being looked for actually exists (if it does, then rebuilding should fix it).
  It can make sense to go down the project one by one and build them individually to see which one fails 'first'.
  
  * Another unusual thing about PerfView is that it includes an extension mechanism complete with samples.
  This extensions mechanism is the 'Global' project (called that because it is the Global Extension whose commands don't have an
  explicit 'scope') and needs to refer to PerfView to resolve some of its references.   Thus you will get many 'not found'
  issues in the 'Global' project.  These can be ignored until you get every other part of the build working.

  * One of the invariants of the repo is that if you are running Visual Studio 2017 and you simply sync and build the
  PerfView.sln file, it is supposed to 'just work'.   If that does not happen, and the advice above does not help, then
  we need to either fix the repo or update the advice above.   Thus it is reasonable to open an GitHub issue.   If you
  do this, the goal is to fix the problem, which means you have to put enough information into the issue to do that.
  This includes exactly what you tried, and what the error messages were.
  
  * If you get an error "MSB8036: The Windows SDK version 8.1 was not found", go to your Control panel -> Programs and Features, 
  and right click on your VS2017 and select 'Modify'.   Then look under the C++ Desktop Development and check that hte Windows SDK 8.1     option is selected.  If not select it and have the setup install this.  
  
### Running Tests

PerfView has a number of *.Test projects that have automated tests.  They can be run in Visual Studio by selecting the
Test -> Run -> All Tests menu item.    For the most thorough results (and certainly if you intend to submit changes) you 
need to run these tests with a Debug build of the product (see the text window in the top toolbar, it says 'Debug' or 'Release').
If tests fail you can right click on the failed test and select the 'Debug' context menu item to run the test under 
the debugger to figure out what went wrong.  

### Contributing to PerfView 

You can get a lot of value out of the source code base simply by being able to build the code yourself, debug
through it or make a local, specialized feature.    But the real power of open source software happens when
you contribute back to the shared code base and thus help the community as a whole.   **While we encourage this it 
requires significantly more effort on your part**.   If you are interested in stepping up, see the 
[PerfView Contribution Guide](CONTRIBUTING.md) and [PerfView Coding Standards](documentation/CodingStandards.md) before you start.

### Code Organization 

The code is broken into several main sections:

  * PerfView - GUI part of the application
    * StackViewer - GUI code for any view with the 'stacks' suffix
    * EventViewer - GUI code for the 'events' view window
    * Dialogs - GUI code for a variety of small dialog boxes (although the CollectingDialog is reasonably complex)
    * Memory - Contains code for memory investigations, in particular it defines 'Graph' and 'MemoryGraph' which are used 
      to display node-arc graphs (e.g. GC heaps)
  * TraceEvent - Library that understands how to decode Event Tracing for Windows (ETW) which is used to actually 
  collect the data for many investigations
  * MainWindow - GUI code for the window that is initially launched (lets you select files or collect new data)
  * ETWClrProfiler* - There are two projects that build the same source either 32 or 64 bit.   This is (the only) native code
  project in PerfView, and implements the CLR Profiler API and emits ETW events.   It is used to trace object allocation
  stacks and .NET method calls.  
  * HeapDump* There are 32 and 64 bit versions of this project.  These make standalone executables that can dump the GC
  heap using Microsoft.Diagnostics.Runtime APIs.  This allows getting heap dumps from debugger process dumps.  
  * Global - An example of using PerfView's extensibility mechanism
  * CSVReader - old code that lets PerfView read .ETL.CSV files generated by XPERF (probably will delete)
  * Zip - a clone of System.IO.Compression.dll so that PerfView can run on pre V4.5 runtimes (probably will delete)
  * [HtmlJs](src/HtmlJs/Readme.md) - contains a version of the GUI based on HTML and JavaScript (for Linux support). (experimental)

# The TraceEvent Library (Nuget Package)

PerfView is built on top of a stand alone Nuget package called Microsoft.Diagnostics.Tracing (or TraceEvent for short).
You can download it from [Nuget's TraceEvent page](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent/) or
by using the Nuget tool or visual studio to reference the package.  

This package does most of the hard work of creating and parsing the event data generated by the operating system
and organizing it in interesting ways.   PerfView can be thought of as a GUI veneer over this library.   Thus
if you had need to do automated monitoring or want to use the data you see in PerfView in ways that don't make
sense as an extension to PerfView, you can use the TraceEvent library to do this. 

See the [TraceEvent Library Documentation](documentation/TraceEvent/TraceEventLibrary.md) for more.

### Other Documentation

These docs are for specialized scenarios 

  * [Updating SupportFiles](documentation/MakingSupportFilesNugetPackages.md) PerfView uses some binary files that it
does not build itself.   We created two nuget packages to hold these.  This document tells you how to update this
nuget package when these files need to be updated.  Very few people should care about these instructions.  

  * [PerfView Mirror](https://devdiv.visualstudio.com/DefaultCollection/DevDiv/_git/perfview) this is a private version of
the perfView repository that is used for Microsoft-internal purposes.   The link is likely to not work for most people.

