# Overview

A simple example to build an mqtt client based on an external mqtt paho c lib.

## References

Lots of examples helped in making this:

- An external multilib downloader for cmake https://gist.github.com/roxlu/0108d45308a0434e27d4320396399153#file-cmakelists-txt-L54
- https://github.com/charlesnicholson/cmake-external-project-test/blob/master/CMakeLists.txt 
- https://github.com/mfreiholz/cmake-example-external-project/blob/master/CMakeLists.txt
- Paho MQTT C https://github.com/eclipse/paho.mqtt.c

## Building

On Posix platforms:

mkdir build && cd build && cmake .. && make

Windows would have some slightly different steps.

## Known Issues

There is a lot of cruft.  Many things need to get cleaned up.  Making it work on windows should get done.  Add static linking as an alternative.

