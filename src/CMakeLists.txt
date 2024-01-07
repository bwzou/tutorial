set(TARGET_NAME Main)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(DEPENDENT_LIBRARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lib")

# 微软（MS）的VC运行库
if(MSVC) 
    add_compile_options("/MV")
endif()    

set(vulkan_include ${DEPENDENT_LIBRARY_DIR}/VulkanSDK/include)

if(WIN32)
    set(vulkan_lib ${DEPENDENT_LIBRARY_DIR}/VulkanSDK/lib/WIN32/vulkan-1.lib)
    set(glslangValidator_executable ${DEPENDENT_LIBRARY_DIR}/VulkanSDK/bin/WIN32/glslangValidator.exe)
    add_compile_definitions("TUTORIAL_VK_LAYER_PATH=${DEPENDENT_LIBRARY_DIR}/VulkanSDK/bin/Win32")    
elseif(UNIX)    
    if(APPLE)
        set(vulkan_lib ${DEPENDENT_LIBRARY_DIR}/VulkanSDK/lib/MacOS/libvulkan.1.dylib)
        set(glslangValidator_executable ${DEPENDENT_LIBRARY_DIR}/VulkanSDK/bin/MacOS/glslangValidator)
        add_compile_definitions("TUTORIAL_VK_LAYER_PATH=${DEPENDENT_LIBRARY_DIR}/VulkanSDK/bin/MacOS")    
        add_compile_definitions("TUTORIAL_VK_ICD_FILENAMES=${DEPENDENT_LIBRARY_DIR}/VulkanSDK/bin/MacOS/MoltenVK_icd.json")    
    else()
        set(vulkan_lib ${DEPENDENT_LIBRARY_DIR}/VulkanSDK/lib/Linux/libvulkan.so.1)
        set(glslangValidator_executable ${DEPENDENT_LIBRARY_DIR}/VulkanSDK/bin/Linux/glslangValidator) 
        add_compile_definitions("TUTORIAL_VK_LAYER_PATH=${DEPENDENT_LIBRARY_DIR}/VulkanSDK/bin/Linux")      
    endif()
else()
    message(FATAL_ERROR "Unknown Platform")        
endif()    

set(SHADER_COMPILE_TARGET TutorialShaderCompile)
add_subdirectory(shader)

add_subdirectory(lib)

file(GLOB TUTORIAL_HEADERS CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/*.h)
file(GLOB TUTORIAL_SOURCES CONFIGURE_DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}" FILES ${TUTORIAL_HEADERS} ${TUTORIAL_SOURCES})

add_executable(${TARGET_NAME} ${TUTORIAL_HEADERS} ${TUTORIAL_SOURCES})

# Link dependencies    
target_link_libraries(${TARGET_NAME} PUBLIC glm)
target_link_libraries(${TARGET_NAME} PUBLIC glfw)
target_link_libraries(${TARGET_NAME} PUBLIC stb)
target_link_libraries(${TARGET_NAME} PUBLIC ${vulkan_lib})

add_dependencies(${TARGET_NAME} ${SHADER_COMPILE_TARGET})

target_include_directories(
  ${TARGET_NAME} 
  PUBLIC $<BUILD_INTERFACE:${DEPENDENT_LIBRARY_DIR}/vulkanmemoryallocator/include>
)

target_include_directories(
  ${TARGET_NAME}
  PUBLIC $<BUILD_INTERFACE:${vulkan_include}>)

add_compile_definitions("TUTORIAL_ROOT_DIR=${BINARY_ROOT_DIR}")

target_compile_options(${TARGET_NAME} PUBLIC "$<$<COMPILE_LANG_AND_ID:CXX,MSVC>:/WX->")

add_custom_command(TARGET ${TARGET_NAME} POST_BUILD
COMMAND ${CMAKE_COMMAND} -E make_directory "${BINARY_ROOT_DIR}"
COMMAND ${CMAKE_COMMAND} -E copy_directory "$<TARGET_FILE_DIR:${TARGET_NAME}>/" "${BINARY_ROOT_DIR}"
COMMAND ${CMAKE_COMMAND} -E remove_directory "${BINARY_ROOT_DIR}/${ENGINE_ASSET_DIR}"
)




