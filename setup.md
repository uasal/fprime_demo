# f' Flight Software Setup (Cross-Compilation)
## RASPBERRY-PI SETUP








## LINUX SETUP
### Setting up files and required bash scripts prior to installing
1. Clone the fprime repo from nasa/fprime to your local repository.\
   `git clone https://github.com/nasa/fprime.git`
2. All essentials for the software are based in this directory and you don't need to go back to GitHub if you miss some files.

### Installing System Requirements
1. The distribution requires that a certain set of tools be installed on the host where it is being built.
2. We will install tools for Ubuntu since we are using Linux and therefore it is going to be a Python based system.
3. The code generators for fprime require Python > 2.4x to run as well as additional packages. To make
installing these packages easier, the python package “pip” can be used with the pip requirements files
`sudo ./mk/python/pip_required_build.txt` and `sudo ./mk/python/python/pip_required_gui.txt` (see pip documentation).
4. For some installations, pip does not sort out the dependencies well, so you may have to install the contents manually.
5. Alternatively, if you are installing for Ubuntu you can run the script `sudo ./mk/os-pkg/ubuntu-packages.sh`, which will install the bulk of the python packages. After running the script, the requirements files can be used more successfully.

### How does the demo work?
The demonstration application consists of the following components:
1. A rate group driver passive component, which cycles at 1 Hz. This component takes the 1 Hz
base clock and distributes messages to the rate group components to run at their prescribed rate.
2. Three rate group active component instances running at 1Hz, 0.5Hz, and 0.25Hz that are cycled
by the rate group driver.
A driver active component that accept buffers. Normally a driver would interact with hardware,
but for this demo it simply loops data coming to the input port back to an output port. The driver
is connected to the 1Hz rate group to show how a driver can be periodically run to check for
hardware events. The driver also has an “interrupt” port connected to the rate group driver to
drive it at 1Hz. The “1Hz” interrupt in the demo is achieved by executing a loop with 1 second
delays.
3. A “producer” queued component (SendBuff) that is connected to the 0.5Hz rate group and
produces data that gets sent to the driver component.
4. A “consumer” active component (RecvBuff) that accepts buffers from the driver component.
Since the driver component loops back buffers, every time the producer component sends a
buffer, the consumer component will receive it.
5. A passive uplink/downlink component that receives telemetry from other components for
downlinking and receives uplinked commands. It uses TCP/IP sockets to communicate with
ground software. For this demo, the ground software is not present.
6. An active telemetry component that accepts channelized telemetry from the other components. It
is connected to the uplink/downlink interface component, and sends data based on the 4Hz rate
group.
7. An active logger component that receives logging output from components and sends it to the
uplink/downlink component.
8. A passive text logger component that receives the text version of the logging events and prints it
to standard output.
9. An active command dispatcher component that receives uplinked commands from the
uplink/downlink component, decodes the command identifier (opcode), and forwards it to the
component that executes the command.
10. An active parameter database component that stores parameter values for the consumer and
producer components.
11. A time source passive component that reads Linux time and returns it to the caller. The time is
used as time tags for events and telemetry sent by the components.
12. A command sequencer to execute a set of commands.
13. A health component to verify the active components are still running.
14. Some signal generator components to generate test data.\

Details for each component can be seen in the component SDD (Software Design Documents) in the
`docs` subdirectory for each component as well as the Ref application (TODO).


### Building the Demo
The demo is going to be run on the Linux Computer Host through the fprime repository.
1. Change directory to the `Ref` (AKA “Reference Application) directory inside `fprime`.
2. Type `make gen_make` from the prompt. Output similar to the following should appear:\
    ![make gen_make Output](/home/uasal/makegen_make.png)
   The source directories contain files names **mod.mk** which are scanned by the **gen_make**
   script to generate a make file to perform the build.
3. Type `make` from the prompt. The code should build to completion.
4. To install the command/telemetry GUI dictionary files, type: `make dict_install`

### Running the Demo
The demo requires a system running an X server to display the GUI. To run the demo, execute the
following script: `sudo ./Ref/script/run_ref.sh`. This will bring up the command and telemetry GUI seen below.\
![FGS GUI](/home/uasal/makegen_make.png)\
Another window will show the standard output of the reference application.\
The user can enter commands and view telemetry by selecting the various tabs. Once the GUI is exited,
the script will shut down the window containing the application.
