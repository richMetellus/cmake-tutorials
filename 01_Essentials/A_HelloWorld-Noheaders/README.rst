####################################################################
CMake Tutorials Basic Concepts - Hello World | No Header File
####################################################################



********************
Pre-requisites
********************

1. have a machine that already have cmake installed:

   * mine was Ubuntu 22.04.2 LTS (Jammy Jellyfish)
   
      * cmake --version
   
        .. code-block:: console
           
           rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials$ cmake --version
           cmake version 3.26.3
           
           CMake suite maintained and supported by Kitware (kitware.com/cmake).

*************
Concepts
*************

CMake 

* is an open-source, general meta-build system and build management tool that 
  allows developers to specify build configurations for a wide variety of 
  platforms and build environments.

    * Originally designed as a generator for various dialect of ``Makefile``
      CMake runs on most platforms, and can generate project files 
      in many formats, including ninja (use ``-GNinja``).

           

*******************************
Building - Journal Entry
*******************************

Calling Cmake when there is no source File
===============================================

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
         
Points Illustration:

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
     
              * this is referred to as in-tree build. 
     
              .. note::
                 It is not recommended to build where the source files are. This way
                 you can separate where cmake generated stuff go. 


Now the points are illustrated, I can create the main.cpp file.

Steps:

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

#. Build with  cmake <path to CMakeLists.txt> , ``cmake ..``
    
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
           ├── CMakeLists.txt
           ├── main.cpp
           └── README.rst
           
           10 directories, 31 files
           
           rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ git status
           On branch main
           Changes not staged for commit:
             (use "git add/rm <file>..." to update what will be committed)
             (use "git restore <file>..." to discard changes in working directory)
                   deleted:    CMakeCache.txt
                   modified:   README.rst
           
           Untracked files:
             (use "git add <file>..." to include in what will be committed)
                   build/
                   main.cpp
           
           no changes added to commit (use "git add" and/or "git commit -a")
           rmetellus@UbuntuZephyrVM:~/Workspace/Repos/InternalRepos/cmake-tutorials/01_Essentials/A_HelloWorld-Noheaders(main)$ 
           