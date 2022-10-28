cmake_minimum_required(VERSION 3.11 FATAL_ERROR)

project(custom_datasets VERSION 0.1 DESCRIPTION "custom torch datasets" LANGUAGES CXX)



include(FetchContent)

add_library(custom_datasets INTERFACE include/custom_datasets.hpp)

if(NOT CUSTOM_DATASETS)
       set(CUSTOM_DATASETS "IRIS" CACHE STRING "IRIS CMNIST FP2")
       message(STATUS "CUSTOM_DATASETS not set using default ${CUSTOM_DATASETS}")
endif(NOT CUSTOM_DATASETS)

foreach(DATASET ${CUSTOM_DATASETS})
	message(STATUS "Loading  dataset ${DATASET}")
	FetchContent_Declare(
		${DATASET}
		GIT_REPOSITORY git@github.com:EddyTheCo/${DATASET}.git
		GIT_TAG main
		)
	FetchContent_MakeAvailable(${DATASET})
	target_compile_definitions(custom_datasets INTERFACE USE_${DATASET})
	target_link_libraries(custom_datasets INTERFACE  ${DATASET})
endforeach()
target_include_directories(custom_datasets INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	"$<INSTALL_INTERFACE:include>")

install(TARGETS custom_datasets EXPORT custom_datasetsTargets DESTINATION lib)
install(DIRECTORY include/ DESTINATION include/)

install(EXPORT custom_datasetsTargets
	FILE custom_datasetsTargets.cmake
	DESTINATION lib/cmake/custom_datasets
	)
include(CMakePackageConfigHelpers)
# generate the config file that is includes the exports
configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
	"${CMAKE_CURRENT_BINARY_DIR}/custom_datasetsConfig.cmake"
	INSTALL_DESTINATION "lib/cmake/custom_datasets"
	NO_SET_AND_CHECK_MACRO
	NO_CHECK_REQUIRED_COMPONENTS_MACRO
	)
write_basic_package_version_file(
	"${CMAKE_CURRENT_BINARY_DIR}/custom_datasetsConfigVersion.cmake"
	VERSION "0.1.1.0"
	COMPATIBILITY AnyNewerVersion
	)
install(FILES
	${CMAKE_CURRENT_BINARY_DIR}/custom_datasetsConfig.cmake
	${CMAKE_CURRENT_BINARY_DIR}/custom_datasetsConfigVersion.cmake
	DESTINATION lib/cmake/custom_datasets
	)
export(EXPORT custom_datasetsTargets
	FILE "${CMAKE_CURRENT_BINARY_DIR}/custom_datasetsTargets.cmake"
	)
