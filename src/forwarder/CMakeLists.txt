cmake_minimum_required (VERSION 3.1)
project (lsst_dm_forwarder)

# Build forwarder objects
set(OBJ
    "CURLHandle.cpp"
    "DAQFetcher.cpp"
    "FileSender.cpp"
    "FitsOpener.cpp"
    "FitsFormatter.cpp"
    "Formatter.cpp"
    "HeaderFetcher.cpp"
    "MessageBuilder.cpp"
    "miniforwarder.cpp"
    "PixelArray.cpp"
    "ReadoutPattern.cpp"
    "Scoreboard.cpp"
    "StripeArray.cpp"
)

add_library(lsst_dm_forwarder STATIC ${OBJ})
target_compile_definitions(lsst_dm_forwarder PRIVATE BOOST_LOG_DYN_LINK)
target_include_directories(lsst_dm_forwarder PRIVATE
    "${Boost_INCLUDE_DIRS}"
    "${Daq_INCLUDE_DIRS}"
    "../../include"
)

# Build forwarder executable
add_executable(dm_forwarder main.cpp)
target_compile_definitions(dm_forwarder PRIVATE BOOST_LOG_DYN_LINK)
target_include_directories(dm_forwarder PRIVATE
    "${Boost_INCLUDE_DIRS}"
    "${Daq_INCLUDE_DIRS}"
    "../../include"
)

target_link_libraries(dm_forwarder PRIVATE
    lsst_dm_forwarder
    lsst_iip_core
    ${daq_ims}
    ${daq_daq}
    ${daq_dsi}
    ${boost_log}
    ${boost_thread}
    ${boost_filesystem}
    SimpleAmqpClient
    curl
    yaml-cpp
    pthread
    cfitsio
    hiredis
)

install(
    TARGETS dm_forwarder
    DESTINATION /opt/lsst/dm_forwarder/bin/
    COMPONENT dm_forwarder
)

install(
    FILES ${CMAKE_SOURCE_DIR}/bin/setup.env
    DESTINATION /opt/lsst/dm_forwarder/bin/
    COMPONENT dm_forwarder
)

install(
    FILES ${CMAKE_SOURCE_DIR}/etc/services/dm_forwarder.service
    DESTINATION /etc/systemd/system
    COMPONENT dm_forwarder
)
