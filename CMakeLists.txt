cmake_minimum_required(VERSION 3.17)
project(learn_3dzavr)

set(CMAKE_CXX_STANDARD 20)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}")

add_executable(${CMAKE_PROJECT_NAME}
        # custom files:
        test_scene.cpp
        # 3d engine:
        engine/Consts.h
        engine/math/Vec4D.h
        engine/math/Vec4D.cpp
        engine/math/Vec3D.cpp
        engine/math/Vec3D.h
        engine/math/Vec2D.cpp
        engine/math/Vec2D.h
        engine/math/Matrix4x4.h
        engine/math/Matrix4x4.cpp
        engine/Triangle.h
        engine/Triangle.cpp
        engine/math/Plane.h
        engine/math/Plane.cpp
        engine/Mesh.h
        engine/Mesh.cpp
        engine/utils/Log.h
        engine/utils/Log.cpp
        engine/utils/Time.h
        engine/utils/Time.cpp
        engine/utils/Timer.cpp
        engine/utils/Timer.h
        engine/utils/ResourceManager.h
        engine/utils/ResourceManager.cpp
        engine/World.h
        engine/World.cpp
        engine/Camera.h
        engine/Camera.cpp
        engine/io/Screen.h
        engine/io/Screen.cpp
        engine/Engine.h
        engine/Engine.cpp
        engine/io/Keyboard.cpp
        engine/io/Keyboard.h
        engine/io/Mouse.cpp
        engine/io/Mouse.h
        engine/io/SoundController.cpp
        engine/io/SoundController.h
        engine/utils/ObjectController.cpp
        engine/utils/ObjectController.h
        engine/animation/Animation.h
        engine/animation/Timeline.cpp
        engine/animation/Timeline.h
        engine/animation/Interpolation.h
        engine/animation/Animation.cpp
        engine/animation/ATranslate.h
        engine/animation/AScale.h
        engine/animation/ARotate.h
        engine/animation/AWait.h
        engine/animation/AFunction.h
        engine/animation/AAttractToPoint.h
        engine/animation/ARotateRelativePoint.h
        engine/animation/ARotateLeft.h
        engine/animation/Interpolation.cpp
        engine/animation/Animations.h
        engine/animation/AShowCreation.h
        engine/animation/AShowUncreation.h
        engine/animation/ADecompose.h
        engine/physics/RigidBody.cpp
        engine/physics/RigidBody.h
        engine/physics/Simplex.h
        engine/physics/HitBox.cpp
        engine/physics/HitBox.h
        engine/Object.cpp
        engine/Object.h
        engine/gui/Button.cpp
        engine/gui/Button.h
        engine/gui/Window.cpp
        engine/gui/Window.h
        engine/network/ClientUDP.cpp
        engine/network/ClientUDP.h
        engine/network/MsgType.cpp
        engine/network/MsgType.h
        engine/network/ReliableMsg.cpp
        engine/network/ReliableMsg.h
        engine/network/ServerUDP.cpp
        engine/network/ServerUDP.h
        engine/network/UDPConnection.cpp
        engine/network/UDPConnection.h
        engine/network/UDPSocket.cpp
        engine/network/UDPSocket.h
)

if(APPLE OR UNIX)
    include_directories(/usr/local/include)
else()
    set(SFML_DIR "D:/delete_me/SFML/lib/cmake/SFML")
    set(SFML_STATIC_LIBRARIES TRUE)
endif()


# --- OpenAL ---
if(WIN32)
    set(OPENAL_INCLUDE_DIR "d:/delete_me/OpenAL/include")
    set(OPENAL_LIBRARY     "d:/delete_me/OpenAL/libs/Win64/OpenAL32.lib")
endif()

find_package(OpenAL REQUIRED)

# SFML ожидает TARGET OpenAL (НЕ ALIAS!)
# Создаем реальный IMPORTED target OpenAL, на который set_property можно делать.
if(NOT TARGET OpenAL)
    add_library(OpenAL UNKNOWN IMPORTED)
endif()

# Подставим location/include:
# 1) если FindOpenAL выставил переменные — используем их
if(DEFINED OPENAL_LIBRARY)
    set_target_properties(OpenAL PROPERTIES
            IMPORTED_LOCATION "${OPENAL_LIBRARY}"
    )
endif()

if(DEFINED OPENAL_INCLUDE_DIR)
    set_target_properties(OpenAL PROPERTIES
            INTERFACE_INCLUDE_DIRECTORIES "${OPENAL_INCLUDE_DIR}"
    )
endif()

# 2) если FindOpenAL создал OpenAL::OpenAL — "приклеим" его к нашему OpenAL
# (через INTERFACE_LINK_LIBRARIES), но НЕ через ALIAS.
if(TARGET OpenAL::OpenAL)
    set_property(TARGET OpenAL APPEND PROPERTY
            INTERFACE_LINK_LIBRARIES OpenAL::OpenAL
    )
endif()



find_package(SFML 2.6.2 COMPONENTS graphics audio REQUIRED)

if (SFML_FOUND)
    include_directories(${SFML_INCLUDE_DIR})
endif()

target_link_libraries(${CMAKE_PROJECT_NAME} sfml-audio sfml-network sfml-graphics sfml-window sfml-system)

# OpenGL part
if (APPLE)
    set(GLEW_H /usr/local/Cellar/glew/2.1.0/include/GL)
    set(GLFW_H /usr/local/Cellar/glfw/3.2.1/include/GLFW)
    include_directories(${GLEW_H} ${GLFW_H})

    set(GLEW_LINK /usr/local/Cellar/glew/2.1.0/lib/libGLEW.2.1.dylib)
    set(GLFW_LINK /usr/local/Cellar/glfw/3.2.1/lib/libglfw.3.dylib)
    link_libraries(${OPENGL} ${GLEW_LINK} ${GLFW_LINK})

    target_link_libraries(${CMAKE_PROJECT_NAME} "-framework OpenGL")
    target_link_libraries(${CMAKE_PROJECT_NAME} "-framework GLUT")
elseif(UNIX)
    find_package(OpenGL REQUIRED)
    find_package(GLUT REQUIRED)

    target_link_libraries(${CMAKE_PROJECT_NAME} ${OPENGL_LIBRARIES})
    target_link_libraries(${CMAKE_PROJECT_NAME} ${GLUT_LIBRARY})
endif()
