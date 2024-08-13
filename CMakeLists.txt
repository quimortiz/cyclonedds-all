
cmake_minimum_required(VERSION 3.14)
project(cyclone_dds_wrapper)

# Set the C++ standard
# set(CMAKE_CXX_STANDARD 14)

# Add external projects
include(ExternalProject)

# Find Python
find_package(Python3 REQUIRED COMPONENTS Interpreter Development)

# Get the Python executable and site-packages directory
execute_process(
    COMMAND ${Python3_EXECUTABLE} -c "import site; print(site.getsitepackages()[0])"
    OUTPUT_VARIABLE Python_SitePackages
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

message(STATUS "Using Python: ${Python3_EXECUTABLE}")
message(STATUS "Python site-packages path: ${Python_SitePackages}")


# Build and install Cyclone DDS
# ExternalProject_Add(cyclonedds
#     SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/cyclonedds
#     CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
# )


ExternalProject_Add(cyclonedds
    GIT_REPOSITORY https://github.com/eclipse-cyclonedds/cyclonedds
    # SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/cyclonedds
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
)


# Build and install Cyclone DDS C++

# ExternalProject_Add(cyclonedds-cxx
#     DEPENDS cyclonedds
#     SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/cyclonedds-cxx
#     CMAKE_ARGS -DCYCLONEDDS_DIR=${CMAKE_INSTALL_PREFIX}/lib/cmake/CycloneDDS
#                -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
# )

ExternalProject_Add(cyclonedds-cxx
    DEPENDS cyclonedds
    GIT_REPOSITORY https://github.com/eclipse-cyclonedds/cyclonedds-cxx
    # SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/cyclonedds-cxx
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX}
)



# Build and install Cyclone DDS Python using setup.py
# ExternalProject_Add(cyclonedds-python
#     DEPENDS cyclonedds 
#     GIT_REPOSITORY https://github.com/eclipse-cyclonedds/cyclonedds-python
#     # SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/cyclonedds-python
#     CONFIGURE_COMMAND ""
#     BUILD_COMMAND 
# 	${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_SOURCE_DIR}/cyclonedds-python 
# 	${CMAKE_COMMAND} -E env CYCLONEDDS_HOME=${CMAKE_INSTALL_PREFIX} PYTHONPATH=${Python_SitePackages}/lib/python3.10/site-packages python3 setup.py build
#     INSTALL_COMMAND 
# 	${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_SOURCE_DIR}/cyclonedds-python 
# 	${CMAKE_COMMAND} -E env CYCLONEDDS_HOME=${CMAKE_INSTALL_PREFIX} PYTHONPATH=${Python_SitePackages} python3 setup.py install --prefix=${CMAKE_INSTALL_PREFIX} 
# )

ExternalProject_Add(cyclonedds_python
    DEPENDS cyclonedds 
    GIT_REPOSITORY https://github.com/eclipse-cyclonedds/cyclonedds-python.git
    CONFIGURE_COMMAND ""
    BUILD_COMMAND 
	${CMAKE_COMMAND} -E chdir ${CMAKE_BINARY_DIR}/cyclonedds-python-prefix/src/cyclonedds-python
        ${CMAKE_COMMAND} -E env CYCLONEDDS_HOME=${CMAKE_INSTALL_PREFIX} PYTHONPATH=${Python_SitePackages} ${Python3_EXECUTABLE} setup.py build
    INSTALL_COMMAND 
	${CMAKE_COMMAND} -E chdir ${CMAKE_BINARY_DIR}/cyclonedds-python-prefix/src/cyclonedds-python
        ${CMAKE_COMMAND} -E env CYCLONEDDS_HOME=${CMAKE_INSTALL_PREFIX} PYTHONPATH=${Python_SitePackages} ${Python3_EXECUTABLE} setup.py install --prefix=${CMAKE_INSTALL_PREFIX} 
)


