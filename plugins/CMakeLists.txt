include_directories(${OPENRAVE_INCLUDE_LOCAL_DIRS}) # needed since plugins include openrave/openrave.h
set(PLUGIN_LINK_FLAGS "")

if(APPLE)
  set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -undefined dynamic_lookup")
endif(APPLE)

if(CMAKE_COMPILER_IS_GNUCC OR CMAKE_COMPILER_IS_GNUCXXj OR COMPILER_IS_CLANG)
  set(PLUGIN_COMPILE_FLAGS "-DOPENRAVE_DLL ${Boost_CFLAGS} -fPIC")
else()
  set(PLUGIN_COMPILE_FLAGS "-DOPENRAVE_DLL ${Boost_CFLAGS}")
endif()

set(PLUGINS_BASE ${COMPONENT_PREFIX}plugins-base)
string(TOUPPER ${PLUGINS_BASE} PLUGINS_BASE_UPPER)
set(CPACK_COMPONENT_${PLUGINS_BASE_UPPER}_GROUP "Plugins" PARENT_SCOPE)
set(CPACK_COMPONENT_${PLUGINS_BASE_UPPER}_DISPLAY_NAME "Essential plugins for motion and manipulation planning" PARENT_SCOPE)
set(CPACK_COMPONENT_${PLUGINS_BASE_UPPER}_DEPENDS ${COMPONENT_PREFIX}base PARENT_SCOPE)
set(PLUGIN_COMPONENTS ${PLUGINS_BASE})

set(PLUGINS basecontrollers baserobots basesamplers basesensors bulletrave dualmanipulation grasper ikfastsolvers logging oderave pqprave qtcoinrave qtosgrave rmanipulation rplanners textserver)
foreach(PLUGIN ${PLUGINS})
  set(PLUGIN_COMPONENT)
  add_subdirectory(${PLUGIN})
  if( PLUGIN_COMPONENT )
    set(PLUGIN_COMPONENTS ${PLUGIN_COMPONENTS} ${PLUGIN_COMPONENT})
    string(TOUPPER ${PLUGIN_COMPONENT} PLUGIN_COMPONENT_UPPER)
    set(CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_DISPLAY_NAME ${CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_DISPLAY_NAME} PARENT_SCOPE)
    set(CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_DEPENDS ${CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_DEPENDS} ${COMPONENT_PREFIX}base PARENT_SCOPE)
    set(CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_SUGGESTS ${CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_SUGGESTS} ${COMPONENT_PREFIX}base PARENT_SCOPE)
    set(CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_RECOMMENDS ${CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_RECOMMENDS} ${COMPONENT_PREFIX}base PARENT_SCOPE)
    set(CPACK_COMPONENT_${PLUGIN_COMPONENT_UPPER}_GROUP "Plugins" PARENT_SCOPE)
  endif()
endforeach()

set(CPACK_COMPONENT_${COMPONENT_PREFIX_UPPER}PLUGINS-ALL_RECOMMENDS ${PLUGIN_COMPONENTS} PARENT_SCOPE)
set(CPACK_COMPONENT_${COMPONENT_PREFIX_UPPER}PLUGINS-ALL_DISPLAY_NAME "All plugins" PARENT_SCOPE)

set(CPACK_COMPONENTS_ALL ${CPACK_COMPONENTS_ALL} ${PLUGIN_COMPONENTS} ${COMPONENT_PREFIX}plugins-all PARENT_SCOPE)
