cmake_minimum_required(VERSION 2.8.3)
project(rviz_animated_view_controller)

find_package(cmake_modules REQUIRED)
find_package(Eigen REQUIRED)
find_package(Boost REQUIRED system filesystem)

# Ogre
pkg_check_modules(OGRE OGRE)

find_package(catkin REQUIRED COMPONENTS
 rviz
 pluginlib
 view_controller_msgs
)

# Qt Stuff
if(rviz_QT_VERSION VERSION_LESS "5")
  find_package(Qt4 COMPONENTS QtCore QtGui REQUIRED)
  include(${QT_USE_FILE})
else()
  find_package(Qt5Widgets REQUIRED)
endif()
add_definitions(-DQT_NO_KEYWORDS -g)

find_package(OpenGL REQUIRED)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS view_controller_msgs
  )

if(rviz_QT_VERSION VERSION_LESS "5")
  qt4_wrap_cpp(MOC_FILES include/${PROJECT_NAME}/rviz_animated_view_controller.h)
else()
  qt5_wrap_cpp(MOC_FILES include/${PROJECT_NAME}/rviz_animated_view_controller.h)
endif()

include_directories(include
                    ${catkin_INCLUDE_DIRS}
                    ${Boost_INCLUDE_DIRS}
                    ${QT_INCLUDE_DIR}
                    ${OGRE_INCLUDE_DIRS}
                    ${OPENGL_INCLUDE_DIR}
                    ${CMAKE_CURRENT_BINARY_DIR})

add_library(${PROJECT_NAME}
  src/rviz_animated_view_controller.cpp
  ${MOC_FILES})

if(rviz_QT_VERSION VERSION_LESS "5")
  target_link_libraries(${PROJECT_NAME} ${QT_LIBRARIES} ${OGRE_LIBRARIES}
    ${catkin_LIBRARIES} ${Boost_LIBRARIES})
else()
  target_link_libraries(${PROJECT_NAME} Qt5::Widgets ${OGRE_LIBRARIES}
    ${catkin_LIBRARIES} ${Boost_LIBRARIES})
endif()

install(DIRECTORY include/${PROJECT_NAME}/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION} PATTERN ".svn" EXCLUDE)

install(FILES plugin_description.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(DIRECTORY launch DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

install(TARGETS ${PROJECT_NAME}
   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
 )
