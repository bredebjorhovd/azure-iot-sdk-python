#Copyright (c) Microsoft. All rights reserved.
#Licensed under the MIT license. See LICENSE file in the project root for full license information.

set(iothub_service_client_python_c_files
    ./iothub_service_client_python.cpp
)

if(WIN32)
    set(iothub_service_client_python_c_files ${iothub_service_client_python_c_files} ./dllmain.c ./iothub_service_client_python.def)
endif()

include_directories(.)

add_library( iothub_service_client_python SHARED
    ${iothub_service_client_python_c_files} 
)

# output name of python module is iothub_client
SET_TARGET_PROPERTIES(iothub_service_client_python PROPERTIES OUTPUT_NAME "iothub_service_client")
SET_TARGET_PROPERTIES(iothub_service_client_python PROPERTIES PREFIX "")
IF(WIN32)
    SET_TARGET_PROPERTIES(iothub_service_client_python PROPERTIES SUFFIX ".pyd")
ENDIF(WIN32)

target_link_libraries(
    iothub_service_client_python
    iothub_client_http_transport
    iothub_client_amqp_transport
    iothub_client_mqtt_transport
    iothub_service_client
    uamqp
    ${Boost_LIBRARIES}
    ${PYTHON_LIBRARIES}
)

if(${use_wsio} AND WIN32)
    file(COPY $ENV{OpenSSLDir}/bin/libeay32.dll DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/../tests)
    file(COPY $ENV{OpenSSLDir}/bin/ssleay32.dll DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/../tests)
endif()

linkSharedUtil(iothub_service_client_python)
linkUAMQP(iothub_service_client_python)

set(install_libs
	iothub_service_client_python
)

install(TARGETS ${install_libs} 
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

