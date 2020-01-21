set(target CnoidOpenRTM)
choreonoid_corba_compile_idl_to_cpp(idl_cpp_files idl_h_files corba PointCloud)
choreonoid_add_library(${target} STATIC ${idl_cpp_files} HEADERS ${idl_h_files})
