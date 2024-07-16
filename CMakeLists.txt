cmake_minimum_required(VERSION 2.8)
project(PandarGeneralSDK)

find_package( Boost REQUIRED  COMPONENTS thread)
# find_package( PCL REQUIRED COMPONENTS common )

# set (CMAKE_CXX_FLAGS "-fPIC --std=c++11")
ADD_COMPILE_OPTIONS(-std=c++11 )
ADD_COMPILE_OPTIONS(-std=c++14 )


add_subdirectory(src/PandarGeneralRaw)

include_directories(
    .
    include
    src/PandarGeneralRaw/include
    ${Boost_INCLUDE_DIRS}
    # ${PCL_INCLUDE_DIRS}
)

add_library( ${PROJECT_NAME} SHARED
    src/pandarGeneral_sdk.cc
    src/tcp_command_client.c
    src/util.c
    src/wrapper.cc
)

target_link_libraries(${PROJECT_NAME} 
    PandarGeneral
    ${Boost_LIBRARIES}
    # ${PCL_IO_LIBRARIES}
    Boost::thread
    pcap
)

set(HESAI_DIR HesaiLidar_General_SDK)
set(HESAI_DEST /opt/nvidia/deepstream/deepstream)
set(HESAI_DEST_INCLUDE ${HESAI_DEST}/sources/includes/${HESAI_DIR})
set(HESAI_DEST_LIBS ${HESAI_DEST}/lib)

set(HESAI_HEADERS
  input.h
  pandarGeneral.h
  pandarGeneral_internal.h
  pandarQT.h
  pandarXT.h
  pcap_reader.h
  point_types.h
)

foreach(file ${HESAI_HEADERS})
    install(FILES src/PandarGeneralRaw/include/pandarGeneral/${file} DESTINATION ${HESAI_DEST_INCLUDE}/pandarGeneral)
endforeach()

install(FILES include/pandarGeneral_sdk/pandarGeneral_sdk.h DESTINATION ${HESAI_DEST_INCLUDE}/pandarGeneral_sdk)
install(FILES build/libPandarGeneralSDK.so DESTINATION ${HESAI_DEST_LIBS})
install(FILES build/src/PandarGeneralRaw/libPandarGeneral.a DESTINATION ${HESAI_DEST_LIBS})

# if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
#     find_package(PCL REQUIRED)
add_executable(PandarGeneralTest
    test/test.cc
)

target_link_libraries(PandarGeneralTest
    ${PROJECT_NAME}
    # ${Boost_LIBRARIES}
    # ${PCL_IO_LIBRARIES}
    # Boost::thread
)
# endif(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})

