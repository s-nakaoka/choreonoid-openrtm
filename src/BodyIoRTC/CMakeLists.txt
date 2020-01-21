set(target CnoidBodyIoRTC)
set(sources BodyIoRTC.cpp)
set(headers BodyIoRTC.h exportdecl.h)

choreonoid_make_headers_public(BodyIoRTC.h)
choreonoid_add_library(${target} SHARED ${sources} HEADERS ${headers})
target_link_libraries(${target} CnoidOpenRTM 
  ${CHOREONOID_BODY_LIBRARIES} ${CHOREONOID_CORBA_LIBRARIES} ${OPENRTM_LIBRARIES})

function(choreonoid_openrtm_add_body_io_rtc)
  set(target ${ARGV0})
  list(REMOVE_AT ARGV 0)

  add_library(${target} SHARED ${ARGV})
  target_link_libraries(${target} CnoidBodyIoRTC ${OPENRTM_CAMERA_LIBRARY})
  
  if(MSVC)
    add_compile_options(${OPENRTM_CFLAGS})
  endif()

  set_target_properties(${target} PROPERTIES
    LIBRARY_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}/${CHOREONOID_PLUGIN_SUBDIR}/rtc
    PREFIX "")

  if(ENABLE_INSTALL_RPATH)
    set_target_properties(${target} PROPERTIES INSTALL_RPATH "$ORIGIN/../..")
  endif()

  install(TARGETS ${target}
    RUNTIME DESTINATION ${CHOREONOID_PLUGIN_SUBDIR}/rtc
    LIBRARY DESTINATION ${CHOREONOID_PLUGIN_SUBDIR}/rtc)
endfunction()

# Deprecated
function(add_cnoid_body_io_rtc)
  choreonoid_openrtm_add_body_io_rtc(${ARGV})
endfunction()

file(MAKE_DIRECTORY ${PROJECT_BINARY_DIR}/${CHOREONOID_PLUGIN_SUBDIR}/rtc)
