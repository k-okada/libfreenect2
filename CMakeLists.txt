cmake_minimum_required(VERSION 2.8.3)
project(libfreenect2)

find_package(catkin)
catkin_package() # CATKIN_DEVEL_PREFIX

#libglfw3_3.0.4-1_amd64.deb libglfw3-dev_3.0.4-1_amd64.deb 
add_custom_command(OUTPUT ${PROJECT_SOURCE_DIR}/depends/libglfw3_3.0.4-1_amd64.deb ${PROJECT_SOURCE_DIR}/depends/libglfw3-dev_3.0.4-1_amd64.deb
  COMMAND sh ${PROJECT_SOURCE_DIR}/depends/install_ubuntu.sh
  COMMAND touch ${PROJECT_SOURCE_DIR}/depends/libglfw3_3.0.4-1_amd64.deb ${PROJECT_SOURCE_DIR}/depends/libglfw3-dev_3.0.4-1_amd64.deb
  DEPENDS ${PROJECT_SOURCE_DIR}/depends/install_ubuntu.sh
  WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/depends)

add_custom_target(install_glfw
  DEPENDS ${PROJECT_SOURCE_DIR}/depends/libglfw3_3.0.4-1_amd64.deb ${PROJECT_SOURCE_DIR}/depends/libglfw3-dev_3.0.4-1_amd64.deb
  COMMAND dpkg -s libglfw3 || gksudo dpkg -i ${PROJECT_SOURCE_DIR}/depends/libglfw3_3.0.4-1_amd64.deb ${PROJECT_SOURCE_DIR}/depends/libglfw3-dev_3.0.4-1_amd64.deb
  )

# libfreenect
add_custom_command(OUTPUT ${PROJECT_SOURCE_DIR}/examples/protonect/build/bin/Protonect
  COMMAND cmake -E make_directory ${PROJECT_SOURCE_DIR}/examples/protonect/build
  COMMAND cd ${PROJECT_SOURCE_DIR}/examples/protonect/build && cmake .. -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX}
  COMMAND cd ${PROJECT_SOURCE_DIR}/examples/protonect/build && make
  DEPENDS ${PROJECT_SOURCE_DIR}/examples/protonect/CMakeLists.txt
  WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/examples/protonect/)

add_custom_command(OUTPUT ${CATKIN_DEVEL_PREFIX}/lib/cmake/freenect2/freenect2Config.cmake
  DEPENDS ${PROJECT_SOURCE_DIR}/examples/protonect/build/bin/Protonect
  COMMAND make install
  WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/examples/protonect/build
  )

add_custom_target(compile_libfreenect2 ALL
  DEPENDS ${CATKIN_DEVEL_PREFIX}/lib/cmake/freenect2/freenect2Config.cmake
  )
add_dependencies(compile_libfreenect2 install_glfw)


