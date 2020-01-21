option(BUILD_CHOREONOID_OPENRTM_SAMPLES "Building Choreonoid-OpenRTM samples" ON)
if(NOT BUILD_CHOREONOID_OPENRTM_SAMPLES)
  return()
elseif(NOT (IS_MASTER_PROJECT OR BUILD_OPENRTM_PLUGIN))
  message(FATAL_ERROR "OpenRTM samples need to build OpenRTMPlugin.")
endif()

set(project_files "")

# General BodyIoRTCs
choreonoid_openrtm_add_body_io_rtc(RobotIoRTC RobotIoRTC.cpp)

# SR1 walk
choreonoid_openrtm_add_body_io_rtc(SR1WalkControllerRTC SR1WalkControllerRTC.cpp)
list(APPEND project_files OpenRTM-SR1Walk.cnoid)
  
# SR1 lifting up
choreonoid_openrtm_add_body_io_rtc(SR1LiftupControllerRTC SR1LiftupControllerRTC.cpp)
list(APPEND project_files OpenRTM-SR1Liftup.cnoid)

# PA10 Pickup
choreonoid_openrtm_add_body_io_rtc(PA10PickupControllerRTC PA10PickupControllerRTC.cpp)
list(APPEND project_files OpenRTM-PA10Pickup.cnoid)

# Tank IO
choreonoid_openrtm_add_body_io_rtc(TankIoRTC TankIoRTC.cpp)

# Joystick
choreonoid_openrtm_add_body_io_rtc(JoystickRTC JoystickRTC.cpp)
choreonoid_openrtm_add_body_io_rtc(RemoteJoystickRTC RemoteJoystickRTC.cpp)

choreonoid_add_simple_controller(RemoteJoystickInputController RemoteJoystickInputController.cpp)
target_link_libraries(RemoteJoystickInputController CnoidOpenRTMPlugin)

# Tank Joystick Controller
set(target TankJoystickControllerRTC)
choreonoid_openrtm_add_body_io_rtc(${target} TankJoystickControllerRTC.cpp)
list(APPEND project_files
  OpenRTM-Tank.cnoid
  TankSystem.xml)

if(NOT USE_BUILTIN_CAMERA_IMAGE_IDL)
  choreonoid_openrtm_add_body_io_rtc(VisionSensorIoRTC VisionSensorIoRTC.cpp)
  list(APPEND project_files
    OpenRTM-TankVisionSensors.cnoid
    TankVisionSystem.xml
    OpenRTM-TankVisionSensors-NewIDL.cnoid
    TankVisionSystem-NewIDL.xml
    )
endif()

# Teleoperation samples
list(APPEND project_files
  OpenRTM-AizuSpiderSS-Remote.cnoid
  OpenRTM-AizuSpider-Terminal.cnoid  
  OpenRTM-DoubleArmV7-Terminal.cnoid)

# Quadcopter sample

#get_directory_property(header_path INCLUDE_DIRECTORIES)
#find_file(RotorDeviceHeader cnoid/RotorDevice PATHS ${header_path} NO_DEFAULT_PATH)
#find_library(MulticopterPluginLibrary CnoidMulticopterPlugin PATHS ${PROJECT_BINARY_DIR}/${CHOREONOID_PLUGIN_SUBDIR} NO_DEFAULT_PATH)
#if(RotorDeviceHeader AND MulticopterPluginLibrary)


#if(BUILD_MULTICOPTER_PLUGIN)
#  choreonoid_openrtm_add_body_io_rtc(QuadcopterControllerRTC QuadcopterControllerRTC.cpp)
#  add_cnoid_body_io_rtc(QuadcopterIoRTC QuadcopterIoRTC.cpp)
#  target_link_libraries(QuadcopterIoRTC CnoidMulticopterPlugin)
#  list(APPEND project_files
#    OpenRTM-QuadcopterJoystick.cnoid
#    OpenRTM-Quadcopter-Terminal.cnoid
#    QuadcopterSystem.xml
#    Quadcopter-RTSystem.xml)
#endif()

install(FILES ${project_files} DESTINATION ${CHOREONOID_SHARE_SUBDIR}/project)
