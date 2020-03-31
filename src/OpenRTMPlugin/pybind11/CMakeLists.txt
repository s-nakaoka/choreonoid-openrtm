set(target PyOpenRTMPlugin)

add_cnoid_python_module(${target}
  PyOpenRTMPlugin.cpp
  PyItems.cpp
)

target_link_libraries(${target} CnoidOpenRTMPlugin CnoidPyBase C:/Python37/Libs/python37.lib)
