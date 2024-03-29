set(dependent_library, 'DependentLibrary')

set(BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)

if(NOT TARGET glm)
    option(BUILD_STATIC_LIBS "" ON)
    option(BUILD_TESTING "" OFF)
    option(GLM_TEST_ENABLE "" OFF)
    add_subdirectory(glm)
    set_target_properties(glm_static PROPERTIES FOLDER ${dependent_library}/glm)
endif()    

if(NOT TARGET glfw)
    option(GLFW_BUILD_EXAMPLES "" OFF)
    option(GLFW_BUILD_TESTS "" OFF)
    option(GLFW_BUILD_DOCS "" OFF)
    option(GLFW_INSTANLL "" OFF)
    add_subdirectory(glfw)
    set_target_properties(glfw PROPERTIES FOLDER ${dependent_library}/glfw)
    set_target_properties(update_mappings PROPERTIES FOLDER ${dependent_library}/glfw)
endif()    

if(NOT TARGET stb)
    include(stb.cmake)
    set_target_properties(stb PROPERTIES FOLDER ${dependent_library}/stb)
endif()

if(NOT TARGET tinyobjloader)
    add_subdirectory(tinyobjloader)
    set_target_properties(tinyobjloader PROPERTIES FOLDER ${dependent_library}/tinyobjloader)
    set_target_properties(uninstall PROPERTIES FOLDER ${dependent_library}/tinyobjloader)
endif()