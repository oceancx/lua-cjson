cmake_minimum_required(VERSION 3.5)
project(lua-cjson VERSION 1.0.3 LANGUAGES C)

if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX ${CMAKE_SOURCE_DIR}/dist CACHE PATH "..." FORCE)
endif()

list(INSERT CMAKE_PREFIX_PATH 0  ../lua-5.3.5/dist)
find_package(Lua REQUIRED NO_CMAKE_PACKAGE_REGISTRY)

add_library(cjson src/lua_cjson.c src/strbuf.c)
add_library(Lua::CJson ALIAS cjson)
target_compile_definitions(cjson PUBLIC -DVERSION="${PROJECT_VERSION}")

target_link_libraries(cjson PUBLIC Lua::Lua)

set(PROJ_CONFIG_DIR lib/cmake/lua-cjson)
set(PROJ_EXPORT_NAME LuaCJson)

install(TARGETS cjson
    EXPORT  ${PROJ_EXPORT_NAME}Targets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    INCLUDES DESTINATION include
)

#This is required so that the exported target has the name Lua and not lua
set_target_properties(cjson PROPERTIES EXPORT_NAME ${PROJ_EXPORT_NAME})

install(EXPORT ${PROJ_EXPORT_NAME}Targets
    FILE
        ${PROJ_EXPORT_NAME}Targets.cmake 
    NAMESPACE
        ${PROJ_EXPORT_NAME}::
    DESTINATION
        ${PROJ_CONFIG_DIR}
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}ConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(${CMAKE_CURRENT_LIST_DIR}/cmake/${PROJ_EXPORT_NAME}Config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}Config.cmake
    INSTALL_DESTINATION ${PROJ_CONFIG_DIR}
)

#Install the config, configversion and custom find modules
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}Config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}ConfigVersion.cmake
    DESTINATION ${PROJ_CONFIG_DIR}
)

export(EXPORT ${PROJ_EXPORT_NAME}Targets FILE ${CMAKE_CURRENT_BINARY_DIR}/${PROJ_EXPORT_NAME}Targets.cmake  NAMESPACE ${PROJ_EXPORT_NAME}::)


#install_test ( tests/ )
