cmake_minimum_required(VERSION 3.0.2)
project(rqt_bag)
find_package(catkin REQUIRED)
catkin_package()
catkin_python_setup()

install(FILES plugin.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

install(DIRECTORY launch resource
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

catkin_install_python(PROGRAMS scripts/rqt_bag
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

if("${CMAKE_INSTALL_PREFIX}" MATCHES "^/opt/")
  # Handle release case
  set(DESKTOP_APP_DIR "/usr/share/applications")
  set(DESKTOP_ICON_DIR "/usr/share/icons")
else()
  set(DESKTOP_APP_DIR "$ENV{HOME}/.local/share/applications")
  set(DESKTOP_ICON_DIR ${DESKTOP_APP_DIR})
  set(DESKTOP_APP_SUFFIX " (local)")
endif()

# Create directory for the desktop file/icon
install(DIRECTORY DESTINATION
  ${DESKTOP_APP_DIR}
)
install(DIRECTORY DESTINATION
  ${DESKTOP_ICON_DIR}
)

# Configure / install bash launch script
configure_file(
  scripts/${PROJECT_NAME}.bash.in
  scripts/${PROJECT_NAME}.bash
  @ONLY
)

# Install bash script
install(
  PROGRAMS
  ${CMAKE_CURRENT_BINARY_DIR}/scripts/${PROJECT_NAME}.bash
  DESTINATION
  ${CATKIN_PACKAGE_BIN_DESTINATION}
)

# Configure / install desktop launchers
if(DEFINED DESKTOP_APP_SUFFIX)
  configure_file(
    desktop/${PROJECT_NAME}.desktop.in
    desktop/${PROJECT_NAME}_local.desktop
  )
else()
  configure_file(
    desktop/${PROJECT_NAME}.desktop.in
    desktop/${PROJECT_NAME}.desktop
  )
endif()
if(DEFINED DESKTOP_APP_SUFFIX)
  install(
    FILES
    ${CMAKE_CURRENT_BINARY_DIR}/desktop/${PROJECT_NAME}_local.desktop
    DESTINATION
    ${DESKTOP_APP_DIR}
  )
else()
  install(
    FILES
    ${CMAKE_CURRENT_BINARY_DIR}/desktop/${PROJECT_NAME}.desktop
    DESTINATION
    ${DESKTOP_APP_DIR}
  )
endif()

# Install icon
install(
  FILES
  desktop/${PROJECT_NAME}.png
  DESTINATION
  ${DESKTOP_ICON_DIR}
)

