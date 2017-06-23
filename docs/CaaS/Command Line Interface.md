# Command Line Interface

Interacting with the CaaS deployment is done through the use of a command line interface.  This tool allows access to the features of the various microservices that make up the platform.  For example, the CLI is used for registering and unregistering modules, re-training the intent router, testing the intent router with specific utterances, and manually invoking intent endpoints.  By wrapping the functionality in a simple to use command line interface the power and features of the platform are exposed and can be leveraged without having to manually write Thrift service clients or expose REST APIs for internal services.  Additionally the tool is useful as a means of testing functionality interactively.

## Design

The CLI tool is located in `cli` and is built using the standard build process (currently Gradle).  During the build process the tool generates Python bindings for the various thrift servies that it needs to interact with.  Generally there is a 1:1 correspondence between Python modules and Thrift services, though in some cases modules will cross Thrift service boundaries where it makes sense.

Efforts have been made to ensure that the the tool can be run from source directory so as to limit the need for running a build prior to running.  This design helps with the overall development of the tool and reduces the amount of time needed to implement features as it increases developer productivity.  This is enabled by the main entry point `bin/cedp` adding some directories from the `build` directory to the `PYTHON_PATH`.

As mentioned previously, the main entry point for the tool is `bin/cedp` with all of the module code being in the `cli` package at the root of the project.  This is consistent with generally accepted best practices for Python projects.  Further, the list of 3rd party dependencies that the tool relies on are in `requirements.txt`, again a standard best practice.  During the build all of the packages listed in `requirements.txt`, and their transitive dependencies, are downloaded and extracted into `build/dependencies` in an effort to _vendor_ the dependencies into the solution.  Vendoring was chosen for this tool as a design goal is that the tool should be able to be self contained.  That is, it should be able to be extracted from a .zip file and run so long as there is a suitable Python interpreter available.

## Building

Before running the CLI it must be built.  This is because the tool relies on external dependencies and generated bindings for some of the Thrift services and data structures.  Note that if there are no changes to either of these then it should be considered safe to simply run the tool without a build.  The build is run as any other build from the source directory as below.

    $ ../gradlew assemble

Once the tool has been built it can be run from the command line.  See the following section for information.

## Running

The CLI can be invoked in 2 different ways.  As mentioned previously, the tool can be run in-situ from its location in the source directory, or from the build directory.  The result should be the same either way.  Regardless of the approach taken, one simply needs to run the `cedp` entry point.  The commands below assume that the current working directory is the root of the project, however the program can be invoked from anywhere so long as the path is specified correctly (or the program is on the `PATH`)

    # Run the command from the source directory
    $ bin/cedp

    # Run the command from the build directory
    # build/dist/bin/cedp

## Commands

To get a complete listing of the commands that the CLI supports, run `cedp --help`.  The commands are documented there-in.

## Installation

The CLI can be installed anywhere on your system by running the build and copying the `build/dist` directory to a location outside of the source tree.  This is generally not needed if you are doing development, but can be useful if you want to work with the tool outside of the source tree.

## Custom Endpoints

Given that the CaaS platform is meant to run in a variety of locations, the CLI allows the user to pick a host to interact with.  When doing local development, this will generally be either `localhost` or the hostname of the development VM that the CaaS platform is running on.  To set the host run the following command:

    $ bin/cedp --host hostname configure

 The information is stored in `$HOME/.cedp/cliConfig.yml` which is read on each invocation of the CLI.  The next time the CLI is used, any interaction done with the Thrift services will be directed to the previously configured host.
