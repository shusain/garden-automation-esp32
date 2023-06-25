# This project serves as a demo to enable using esp-mqtt on ESP platform targets as well as on linux
cmake_minimum_required(VERSION 3.16)

# For ESP32 platform target
set(EXTRA_COMPONENT_DIRS $ENV{IDF_PATH}/examples/common_components/protocol_examples_common)


include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(mqtt_garden_controller)
