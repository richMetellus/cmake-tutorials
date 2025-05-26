.. _CMakeLesson1:

.. include:: /markupExtension.rst
   
####################################################################
CMake Tutorials Basic Concepts - Hello World | No Header File
####################################################################


****************
Lecture/Notes
****************

**CMake** 

.. include:: ../../../../CMK-0000_CMakeIntro.rst
   :start-after: .. _cmakeIntroSectionStart:
   :end-before: skipSectionStart

.. include:: ../../../../CMK-0000_CMakeIntro.rst
   :start-after: .. skipSectionEnd
   :end-before: .. _cmakeIntroSectionEnd:

if you head over to the `CMake Basic Starting Point Document`_, there is some good
resources.

Main take-away:

To start a basic cmake, you need:


* A ``CMakeLists.txt`` file.

  .. literalinclude:: ./CMakeLists.txt


  * the naming of the file is important.
  * CMake has its own scripting language and every cmake statement will go there.
  * You can have different CMakeLists.txt in separate directory.
  * you can include many cmake scripts.
 
  * the CMakeLists.txt file is a central piece of the CMake build system 
    that defines the build process, configuration options, and dependencies 
    for a particular project. [1]_
  
  * Only one per directory, where CMake is supposed to look.

* In the CMakeList.txt you need to include at minimum these lines:
    
  #. ``cmake_minimum_required(VERSION 3.0)``

     * ``VERSION`` is case sensitive. Spelling Matter
     * the ``cmake_minimum_required`` command should be used at the beginning 
       of the CMakeLists.txt file, before any other commands or macros, 
       to ensure that the correct version of CMake is used to build the project. [1]_

     * cmake commands such as ``cmake_minimum_required`` can be upper case, lower case
       or mixed.

       * This ``cmake_minimum_required`` is a **cmake scripting command** and not a 
         cmake function
       
         .. note:: Do not confuse cmake command with cmake function.
            
            cmake function are user defined and start with the keyword 
            ``function``
         
         * CMake commands can be grouped in 3 categories as:
             * **Scripting Commands**
                * ``if``, ``else``, ``find_package``, ``cmake_minimum_required``
             * Project commands
               
               * ``add_library``, ``add_executable``, ``export``

           * CTest Commands.
               
               * ``ctest_build``, ``ctest_test``
             Visit https://cmake.org/cmake/help/latest/manual/cmake-commands.7.html
           to see more.
             * **I find it easier to think of cmake commands as cmake keywords used in cmake specific
             syntax**
             
              :strike:`even though CMake documentation refers them as command.`

  #. ``project`` command/keyword 
      
      * is used to specify the project name and/or optionally the project version.
      * How useful is that command?
          
        * This is useful to group related project-level variables such as 
          PROJECT_VERSION, PROJECT_NAME
          and configurations together. this can help with the output name of the binary,
          setting package name.
        
        * make referencing certain variables easier when using multiple projects.

        .. seealso:: 
           
           * ChatGPT response on the use of cmake project command/
           * `stackoverflow response <https://stackoverflow.com/questions/26878379/in-cmake-what-is-a-project>`_
               
               .. compound:: 
                  
                  A project logically groups a number of targets 
                  (that is, libraries, executables and custom build steps) i
                  nto a self-contained collection that can be built on its own.

                  you may nest multiple projects. A top-level project may 
                  include a subdirectory which is in turn another 
                  self-contained project. 
                  the project command introduces additional scoping for 
                  certain values. For example, the ``PROJECT_BINARY_DIR``
                  variable will always point to the root binary directory 
                  of the current project. Compare this with ``CMAKE_BINARY_DIR``, 
                  which always points to the binary directory of the 
                  top-level project.

                  .. admonition:: Advice
                     
                     Use sub-projects if your codebase is very complex 
                     and you need users to be able to build certain 
                     components in isolation.
      
      .. note::
         
         There is a shorthand I see people use when using project and add_executable
         when they want the executable to have the same name as the project.

         .. code-block:: c

            cmake_minimum_required(VERSION 3.13)
            project(HelloWorld VERSION 1.0.0)
            
            add_executable(${PROJECT_NAME} main.cpp)
         
         * ``PROJECT_NAME`` variable was set by the ``project`` command with a Value HelloWorld
         * The executable as a result will be named HelloWorld as well.
            

      * Synopsis
          
        .. code-block:: console
           
           project(<PROJECT-NAME> [<language-name>...])
           project(<PROJECT-NAME>
                   [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
                   [DESCRIPTION <project-description-string>]
                   [HOMEPAGE_URL <url-string>]
                   [LANGUAGES <language-name>...])

       * Sets the name of the project, and stores it in the variable 
         ``PROJECT_NAME``. 
       * When called from the top-level CMakeLists.txt also stores 
         the project name in the variable ``CMAKE_PROJECT_NAME``.
       * more information available on `cmake doc <https://cmake.org/cmake/help/latest/command/project.html#command:project>`_
         This gives you a very powerful mechanism for structuring the build system.

  #. ``add_executable`` command/keyword 
      
     * Add an executable to the project using the specified source files.
     * based on `the command documentation <https://cmake.org/cmake/help/latest/command/add_executable.html>`_
       there are different type of executables.
         
         * Normal executables
         * Imported executable
         * Alias executable
     
     * The source needs to be included in the add_executable otherwise you get the following error:
         
       * point proven using commit: ``65d7018784ecf61267b79857d42813e1a83f0590``
       
       .. code-block:: console

           CMake Error at CMakeLists.txt:4 (add_executable):
           No SOURCES given to target: HelloWorldExecutable
        


###############
CMAKE LAB 1
###############

*******
Scope
*******

Goals:

* cmake basic syntax
* cmake basic project

* Explore the different ways of building a cmake-based project.

  1. In-place build
  2. Out-of-Source build

**********************
LAB: Pre-requisites
**********************

1. have a machine that already have cmake installed:

   * mine was Ubuntu 22.04.2 LTS (Jammy Jellyfish)
   
     * cmake --version
   
       .. code-block:: console
          
          rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ cmake --version
          cmake version 3.26.3
          
          CMake suite maintained and supported by Kitware (kitware.com/cmake).
        


*************************************
LAB: Building - Journal Entry - Demo
*************************************

Configuration Phase - Generate Build file (Makefile)
=========================================================

* I started with a bare bone project like this:
   
* the folder structure:

  .. code-block:: console
 
     rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ tree .
     .
     └── 01_Essentials
         └── A_HelloWorld-Noheaders
             ├── CMakeLists.txt
             └── README.rst
     
     2 directories, 2 files

* The CMakeLists.txt script:

   .. code-block:: console

      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ 
      cat 01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt 
      cmake_minimum_required(Version 3.13)
      project(HelloWorlProj Version 1.0.0)
      
      add_executable(HelloWorldExecutable main.cpp)
      
**Points Illustration:**

1. To Illustrate the point that cmake expect a filename called ``CMakeLists.txt``,
   invoke ``cmake`` command from a directory that doesn't any cmake script file

   .. code-block:: console

      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ cmake .
      CMake Warning:
        Ignoring extra path from command line:
      
         "."
      
      
      CMake Error: The source directory "/home/rmetellus/Workspace/Repos/InternalRepos/cmake-tutorials" does not appear to contain CMakeLists.txt.
      Specify --help for usage, or press the help button on the CMake GUI
     
2. To Illustrate the point that cmake ``add_executable`` function will look for the
   source files relatively to where the ``CMakeLists.txt`` script file is located, and what
   happened when there is no main.cpp file:

   i. From that A_HelloWorld... basic bare bone project, invoke the command ``cmake .``
 
      * I got a warning due to the keyword ``VERSION`` is case sensitive.
      * I got a bunch more errors due to cmake unable to find a compiler
      
      .. collapse:: show/hide errors and warning log
 
         .. code-block:: console
            
            rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders$ cmake .
            CMake Error at CMakeLists.txt:1 (cmake_minimum_required):
              cmake_minimum_required called with unknown argument "Version".
            
            
            CMake Warning (dev) at CMakeLists.txt:2 (project):
              cmake_minimum_required() should be called prior to this top-level project()
              call.  Please see the cmake-commands(7) manual for usage documentation of
              both commands.
            This warning is for project developers.  Use -Wno-dev to suppress it.
            
            CMake Error: Could not find cmake module file: CMakeDetermineVersionCompiler.cmake
            CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
            Missing variable is:
            CMAKE_Version_COMPILER_ENV_VAR
            CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
            Missing variable is:
            CMAKE_Version_COMPILER
            CMake Error: Could not find cmake module file: /home/rmetellus/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/CMakeFiles/3.26.3/CMakeVersionCompiler.cmake
            CMake Error: Could not find cmake module file: CMakeDetermine1.0.0Compiler.cmake
            CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
            Missing variable is:
            CMAKE_1.0.0_COMPILER_ENV_VAR
            CMake Error: Error required internal CMake variable not set, cmake may not be built correctly.
            Missing variable is:
            CMAKE_1.0.0_COMPILER
            CMake Error: Could not find cmake module file: /home/rmetellus/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/CMakeFiles/3.26.3/CMake1.0.0Compiler.cmake
            CMake Error at CMakeLists.txt:2 (project):
              No CMAKE_Version_COMPILER could be found.
            
              Tell CMake where to find the compiler by setting the CMake cache entry
              CMAKE_Version_COMPILER to the full path to the compiler, or to the compiler
              name if it is in the PATH.
            
            
            CMake Error: Could not find cmake module file: CMakeVersionInformation.cmake
            CMake Error at CMakeLists.txt:2 (project):
              No CMAKE_1.0.0_COMPILER could be found.
            
              Tell CMake where to find the compiler by setting the CMake cache entry
              CMAKE_1.0.0_COMPILER to the full path to the compiler, or to the compiler
              name if it is in the PATH.
            
            
            CMake Error: Could not find cmake module file: CMake1.0.0Information.cmake
            CMake Error: CMAKE_Version_COMPILER not set, after EnableLanguage
            CMake Error: CMAKE_1.0.0_COMPILER not set, after EnableLanguage
            -- Configuring incomplete, errors occurred!

   #. I fixed the CMakeLists.txt Version and re-run
     
      .. code-block:: console
         
         rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ git diff
         diff --git a/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt b/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
         index eed78bf..51f9e4d 100644
         --- a/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
         +++ b/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
         @@ -1,4 +1,4 @@
         -cmake_minimum_required(Version 3.13)
         -project(HelloWorlProj Version 1.0.0)
         +cmake_minimum_required(VERSION 3.13)
         +project(HelloWorlProj VERSION 1.0.0)
          
          add_executable(HelloWorldExecutable main.cpp)
   
   #. Re-run without a main.cpp
     
      .. collapse:: show/hide error message
         
         .. code-block:: console
            
            rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ git status
            On branch main
            
            No commits yet
            
            Changes to be committed:
              (use "git rm --cached <file>..." to unstage)
                    new file:   01_Essentials/A_HelloWorld-Noheaders/CMakeCache.txt
                    new file:   01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
                    new file:   01_Essentials/A_HelloWorld-Noheaders/README.rst
            
            Changes not staged for commit:
              (use "git add <file>..." to update what will be committed)
              (use "git restore <file>..." to discard changes in working directory)
                    modified:   01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
            
            rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ git diff
            diff --git a/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt b/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
            index eed78bf..51f9e4d 100644
            --- a/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
            +++ b/01_Essentials/A_HelloWorld-Noheaders/CMakeLists.txt
            @@ -1,4 +1,4 @@
            -cmake_minimum_required(Version 3.13)
            -project(HelloWorlProj Version 1.0.0)
            +cmake_minimum_required(VERSION 3.13)
            +project(HelloWorlProj VERSION 1.0.0)
             
             add_executable(HelloWorldExecutable main.cpp)
            rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ cd 01_Essentials/A_HelloWorld-Noheaders/
            rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders$ ls -la
            total 28
            drwxrwxr-x 2 rmetellus rmetellus 4096 Apr 25 16:35 .
            drwxrwxr-x 3 rmetellus rmetellus 4096 Apr 25 15:30 ..
            -rw-rw-r-- 1 rmetellus rmetellus 4707 Apr 25 16:28 CMakeCache.txt
            -rw-rw-r-- 1 rmetellus rmetellus  121 Apr 25 16:37 CMakeLists.txt
            -rw-rw-r-- 1 rmetellus rmetellus 7042 Apr 25 16:40 README.rst
            rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders$ cmake .
            -- The C compiler identification is GNU 11.3.0
            -- The CXX compiler identification is GNU 11.3.0
            -- Detecting C compiler ABI info
            -- Detecting C compiler ABI info - done
            -- Check for working C compiler: /usr/bin/cc - skipped
            -- Detecting C compile features
            -- Detecting C compile features - done
            -- Detecting CXX compiler ABI info
            -- Detecting CXX compiler ABI info - done
            -- Check for working CXX compiler: /usr/bin/c++ - skipped
            -- Detecting CXX compile features
            -- Detecting CXX compile features - done
            -- Configuring done (2.0s)
            CMake Error at CMakeLists.txt:4 (add_executable):
              Cannot find source file:
            
                main.cpp
            
              Tried extensions .c .C .c++ .cc .cpp .cxx .cu .mpp .m .M .mm .ixx .cppm .h
              .hh .h++ .hm .hpp .hxx .in .txx .f .F .for .f77 .f90 .f95 .f03 .hip .ispc
            
            
            CMake Error at CMakeLists.txt:4 (add_executable):
              No SOURCES given to target: HelloWorldExecutable
            
            
            CMake Generate step failed.  Build files cannot be regenerated correctly.
            rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders$ 
            * The example folder content after this stage
      
          .. collapse:: show/hide folder content
             :open:
    
             .. code-block:: console
                
                rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders$ tree -a .
                .
                ├── CMakeCache.txt
                ├── CMakeFiles
                │   ├── 3.26.3
                │   │   ├── CMakeCCompiler.cmake
                │   │   ├── CMakeCXXCompiler.cmake
                │   │   ├── CMakeDetermineCompilerABI_C.bin
                │   │   ├── CMakeDetermineCompilerABI_CXX.bin
                │   │   ├── CMakeSystem.cmake
                │   │   ├── CompilerIdC
                │   │   │   ├── a.out
                │   │   │   ├── CMakeCCompilerId.c
                │   │   │   └── tmp
                │   │   └── CompilerIdCXX
                │   │       ├── a.out
                │   │       ├── CMakeCXXCompilerId.cpp
                │   │       └── tmp
                │   ├── cmake.check_cache
                │   ├── CMakeConfigureLog.yaml
                │   ├── CMakeScratch
                │   └── pkgRedirects
                ├── CMakeLists.txt
                └── README.rst
                
                8 directories, 14 files
                rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders$ ls -la
                total 44
                drwxrwxr-x 3 rmetellus rmetellus  4096 Apr 25 16:41 .
                drwxrwxr-x 3 rmetellus rmetellus  4096 Apr 25 15:30 ..
                -rw-rw-r-- 1 rmetellus rmetellus 14729 Apr 25 16:41 CMakeCache.txt
                drwxrwxr-x 5 rmetellus rmetellus  4096 Apr 25 16:41 CMakeFiles
                -rw-rw-r-- 1 rmetellus rmetellus   121 Apr 25 16:37 CMakeLists.txt
                -rw-rw-r-- 1 rmetellus rmetellus 10735 Apr 25 16:46 README.rst
                rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders$ 
            * **Lesson**
    
         * commit number related to the lesson: commit 177024ef86386c49c1d12a7983cbc4cf15514ab5
    
         * this give an error because you specify a file/ a path ``main.cpp``  that 
           is relative to the directory where the target ``HelloWorldExecutable``
           was created. That path of the target will be root,top project where we 
           invoke the ``cmake`` command. In that case ``cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders``
    
            * this is referred to as in-place build. 
    
            .. note::
               It is not recommended to build where the source files are. This way
               you can separate where cmake generated stuff go. 


Now the points are illustrated, I can create the main.cpp file.

Out-of-Source Build
-----------------------

Out-of-Source Build Steps:

#. create the main.cpp file
   
   .. code-block:: console
      
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ cat main.cpp
      #include <iostream>
      
      int main()
      {
          std::cout<<"Hello World!";
          
          return 0;
      }
      
#. create a build dir and navigate to that directory.
  
   .. code-block:: console

      cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ mkdir -p build
      cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ cd build/

#. Build the configuration files with  ``cmake <path to CMakeLists.txt>`` , ``cmake ..``
    
   .. code-block:: console
      
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ cmake ..
      -- The C compiler identification is GNU 11.3.0
      -- The CXX compiler identification is GNU 11.3.0
      -- Detecting C compiler ABI info
      -- Detecting C compiler ABI info - done
      -- Check for working C compiler: /usr/bin/cc - skipped
      -- Detecting C compile features
      -- Detecting C compile features - done
      -- Detecting CXX compiler ABI info
      -- Detecting CXX compiler ABI info - done
      -- Check for working CXX compiler: /usr/bin/c++ - skipped
      -- Detecting CXX compile features
      -- Detecting CXX compile features - done
      -- Configuring done (1.4s)
      -- Generating done (0.0s)
      -- Build files have been written to: /home/rmetellus/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build

   * The example workspace after this step:

     .. code-block:: console

        rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ tree -a .
        .
        ├── build
        │   ├── CMakeCache.txt
        │   ├── CMakeFiles
        │   │   ├── 3.26.3
        │   │   │   ├── CMakeCCompiler.cmake
        │   │   │   ├── CMakeCXXCompiler.cmake
        │   │   │   ├── CMakeDetermineCompilerABI_C.bin
        │   │   │   ├── CMakeDetermineCompilerABI_CXX.bin
        │   │   │   ├── CMakeSystem.cmake
        │   │   │   ├── CompilerIdC
        │   │   │   │   ├── a.out
        │   │   │   │   ├── CMakeCCompilerId.c
        │   │   │   │   └── tmp
        │   │   │   └── CompilerIdCXX
        │   │   │       ├── a.out
        │   │   │       ├── CMakeCXXCompilerId.cpp
        │   │   │       └── tmp
        │   │   ├── cmake.check_cache
        │   │   ├── CMakeConfigureLog.yaml
        │   │   ├── CMakeDirectoryInformation.cmake
        │   │   ├── CMakeScratch
        │   │   ├── HelloWorldExecutable.dir
        │   │   │   ├── build.make
        │   │   │   ├── cmake_clean.cmake
        │   │   │   ├── compiler_depend.make
        │   │   │   ├── compiler_depend.ts
        │   │   │   ├── DependInfo.cmake
        │   │   │   ├── depend.make
        │   │   │   ├── flags.make
        │   │   │   ├── link.txt
        │   │   │   └── progress.make
        │   │   ├── Makefile2
        │   │   ├── Makefile.cmake
        │   │   ├── pkgRedirects
        │   │   ├── progress.marks
        │   │   └── TargetDirectories.txt
        │   ├── cmake_install.cmake
        │   └── Makefile
        ├── CMakeLists.txt    # -> top level 
        ├── main.cpp
        └── README.rst
        
        10 directories, 31 files
        
        rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ git status
        On branch main
        Changes not staged for commit:
          (use "git add/rm <file>..." to update what will be committed)
          (use "git restore <file>..." to discard changes in working directory)
                deleted:    CMakeCache.txt #--> Deleted by me, manually
                modified:   README.rst
        
        Untracked files:
          (use "git add <file>..." to include in what will be committed)
                build/
                main.cpp
        
        no changes added to commit (use "git add" and/or "git commit -a")
        rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ 

#. Navigate to the ``build`` directory and run the build system ``make`` or whatever
   was configured to build the program

   * Since in the build directory, there is a ``Makefile`` created by cmake 
     then GNU Make is the tool which controls the generation of executables.

     .. code-block:: console

        rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ cd build/
        rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ make 
        [ 50%] Building CXX object CMakeFiles/HelloWorldExecutable.dir/main.cpp.o
        [100%] Linking CXX executable HelloWorldExecutable
        [100%] Built target HelloWorldExecutable
        rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ 


   * The build directory after this stage:

     .. code-block:: console

        cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ tree .
        .
        ├── CMakeCache.txt
        ├── CMakeFiles
        │   ├── 3.26.3
        │   │   ├── CMakeCCompiler.cmake
        │   │   ├── CMakeCXXCompiler.cmake
        │   │   ├── CMakeDetermineCompilerABI_C.bin
        │   │   ├── CMakeDetermineCompilerABI_CXX.bin
        │   │   ├── CMakeSystem.cmake
        │   │   ├── CompilerIdC
        │   │   │   ├── a.out
        │   │   │   ├── CMakeCCompilerId.c
        │   │   │   └── tmp
        │   │   └── CompilerIdCXX
        │   │       ├── a.out
        │   │       ├── CMakeCXXCompilerId.cpp
        │   │       └── tmp
        │   ├── cmake.check_cache
        │   ├── CMakeConfigureLog.yaml
        │   ├── CMakeDirectoryInformation.cmake
        │   ├── CMakeScratch
        │   ├── HelloWorldExecutable.dir
        │   │   ├── build.make
        │   │   ├── cmake_clean.cmake
        │   │   ├── compiler_depend.make
        │   │   ├── compiler_depend.ts
        │   │   ├── DependInfo.cmake
        │   │   ├── depend.make
        │   │   ├── flags.make
        │   │   ├── link.txt
        │   │   ├── main.cpp.o
        │   │   ├── main.cpp.o.d
        │   │   └── progress.make
        │   ├── Makefile2
        │   ├── Makefile.cmake
        │   ├── pkgRedirects
        │   ├── progress.marks
        │   └── TargetDirectories.txt
        ├── cmake_install.cmake
        ├── HelloWorldExecutable
        └── Makefile         

        9 directories, 31 file
               
#. Run the executable ``./HelloWorlExecutable``
   
   .. code-block:: Console

      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ ./HelloWorldExecutable 
      Hello World!rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$
   
   .. note::
      
      notice how the ``Hello World`` is missing an end of line character.

#. Made some changes in the main.cpp and re-run make, no configuration phase needed.
   
   .. code-block:: console
 
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ git diff ../main.cpp
      diff --git a/01_Essentials/A_HelloWorld-Noheaders/main.cpp b/01_Essentials/A_HelloWorld-Noheaders/main.cpp
      index 058f51a..4b37ad0 100644
      --- a/01_Essentials/A_HelloWorld-Noheaders/main.cpp
      +++ b/01_Essentials/A_HelloWorld-Noheaders/main.cpp
      @@ -2,7 +2,7 @@
       
       int main()
       {
      -    std::cout<<"Hello World!";
      +    std::cout<<"Hello World!" << std::endl;
           
           return 0;
       }
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ make
      [ 50%] Building CXX object CMakeFiles/HelloWorldExecutable.dir/main.cpp.o
      [100%] Linking CXX executable HelloWorldExecutable
      [100%] Built target HelloWorldExecutable
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ ./HelloWorldExecutable 
      Hello World!
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ 

Running CMake Without Knowing The underlying Build Tool Used
===============================================================

.. important::

   A very generic way you can run build without knowing what king of build system tool cmake use
   in the background is through ``cmake --build`` command. 
   ``cmake-build <directory-where CMakeCache.txt file is>``

   .. note::
      
      The cmake configuration has to be done first. Otherwise you will get these
      errors.

      .. code-block::

         rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ rm -rf *
         rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ cd ..
         rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ tree -a .
         .
         ├── build
         ├── CMakeLists.txt
         ├── .gitignore
         ├── main.cpp
         └── README.rst
         
         1 directory, 4 files
         rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ cd build/

         rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ cmake --build ..
         Error: could not load cache

         rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ cmake --build .
         Error: could not load cache


The illustration of calling the underlying build system cmake was configured for:

1. create a build directory, delete anything in build directory if one already exist
2. from the build directory, run ``cmake <path to where CMakeLists.txt>``
3. Run ``cmake --build .``

.. collapse:: show/hide steps with output

   .. code-block:: console
      
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ rm -rf *
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ cd ..
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ tree -a .
      .
      ├── build
      ├── CMakeLists.txt
      ├── .gitignore
      ├── main.cpp
      └── README.rst
      
      1 directory, 4 files
      
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ cmake ..
      -- The C compiler identification is GNU 11.3.0
      -- The CXX compiler identification is GNU 11.3.0
      -- Detecting C compiler ABI info
      -- Detecting C compiler ABI info - done
      -- Check for working C compiler: /usr/bin/cc - skipped
      -- Detecting C compile features
      -- Detecting C compile features - done
      -- Detecting CXX compiler ABI info
      -- Detecting CXX compiler ABI info - done
      -- Check for working CXX compiler: /usr/bin/c++ - skipped
      -- Detecting CXX compile features
      -- Detecting CXX compile features - done
      -- Configuring done (1.2s)
      -- Generating done (0.0s)
      -- Build files have been written to: /home/rmetellus/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ cmake --build .
      [ 50%] Building CXX object CMakeFiles/HelloWorldExecutable.dir/main.cpp.o
      [100%] Linking CXX executable HelloWorldExecutable
      [100%] Built target HelloWorldExecutable
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ ./HelloWorldExecutable 
      Hello World!
      rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders/build(main)$ 





***************************
References/Inspirations
***************************

.. 
   Links:

.. _CMake: https://cmake.org/
.. _kitware Meta-configuration of C/C++ Projects with CMake by Sandy McKenzie: https://www.kitware.com//meta-configuration-of-cc-projects-with-cmake/
.. _CMake Basic Starting Point Document: https://cmake.org/cmake/help/latest/guide/tutorial/A%20Basic%20Starting%20Point.html#exercise-1-building-a-basic-project

.. 
   References

.. [1] :doc:`/Wiki_CMake/CMK-JNL-0199_chatGPTCMakeQueries.md`

.. .. [2] `kitware Meta-configuration of C/C++ Projects with CMake by Sandy McKenzie`_