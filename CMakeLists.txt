cmake_minimum_required(VERSION 3.0)
project( PerceptionNeuronROS )

#MESSAGE("Project source dir: ${PROJECT_SOURCE_DIR}")
#SET(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake_modules/)

# Executable output directory
SET(RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin CACHE PATH "Executable output directory")
mark_as_advanced(RUNTIME_OUTPUT_DIRECTORY)


SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${RUNTIME_OUTPUT_DIRECTORY})

foreach(CONFIGURATION_TYPE ${CMAKE_CONFIGURATION_TYPES})
	string(TOUPPER ${CONFIGURATION_TYPE} CONFIGURATION_TYPE)
	SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY_${CONFIGURATION_TYPE} ${RUNTIME_OUTPUT_DIRECTORY})
endforeach(CONFIGURATION_TYPE CMAKE_CONFIGURATION_TYPES)

option(POST_BUILD_COPY_FILES "Copy resource files after build" YES)
mark_as_advanced(POST_BUILD_COPY_FILES)


SET(PERCEPTION_NEURON_FILES ${PROJECT_SOURCE_DIR}/config.txt 
						    ${PROJECT_SOURCE_DIR}/lib/x86/NeuronDataReader.dll)



SET(INCLUDE_DIRS include 
				 ros_lib 
				 ms_includes)

SET(ROS_SRC ros_lib/WindowsSocket.cpp 
			ros_lib/time.cpp 
			ros_lib/duration.cpp)

SET(PERCEPTION_NEURON_ROS_SRC src/PerceptionNeuronROSserial.cpp 
							  src/stdafx.cpp)

FIND_LIBRARY(NEURON_DATA_READER_LIBRARY NeuronDataReader HINTS ${CMAKE_CURRENT_SOURCE_DIR}/lib/x86)

MESSAGE("Found NEURON_DATA_READER_LIBRARY: ${NEURON_DATA_READER_LIBRARY}")

INCLUDE_DIRECTORIES( ${INCLUDE_DIRS} )

ADD_EXECUTABLE( perception_neuron_ros ${PERCEPTION_NEURON_ROS_SRC} 
									  ${ROS_SRC})

TARGET_LINK_LIBRARIES( perception_neuron_ros ${NEURON_DATA_READER_LIBRARY} )

if(POST_BUILD_COPY_FILES)
	file(COPY ${PERCEPTION_NEURON_FILES} DESTINATION ${RUNTIME_OUTPUT_DIRECTORY} NO_SOURCE_PERMISSIONS)
endif(POST_BUILD_COPY_FILES)