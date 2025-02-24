cmake_minimum_required(VERSION 3.8)
project(qcustomplot CXX)

include(conanbuildinfo.cmake)
conan_basic_setup(TARGETS KEEP_RPATHS)

add_library(${PROJECT_NAME} source_subfolder/qcustomplot.cpp)
set_target_properties(${PROJECT_NAME}
    PROPERTIES
        AUTOMOC ON
        DEBUG_POSTFIX "d"
)
if(BUILD_SHARED_LIBS)
    target_compile_definitions(${PROJECT_NAME}
        PRIVATE QCUSTOMPLOT_COMPILE_LIBRARY
        INTERFACE QCUSTOMPLOT_USE_LIBRARY
    )
    set_target_properties(${PROJECT_NAME}
        PROPERTIES
            CXX_VISIBILITY_PRESET hidden
            VISIBILITY_INLINES_HIDDEN ON
    )
endif()

if(QT_VERSION VERSION_GREATER_EQUAL "6.0.0")
    find_package(Qt6 COMPONENTS Core Gui Widgets PrintSupport REQUIRED CONFIG)
    target_link_libraries(${PROJECT_NAME} PUBLIC Qt6::Core Qt6::Gui Qt6::Widgets Qt6::PrintSupport)
    target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)
elseif(QT_VERSION VERSION_GREATER_EQUAL "5.0.0")
    find_package(Qt5 COMPONENTS Core Gui Widgets PrintSupport REQUIRED CONFIG)
    target_link_libraries(${PROJECT_NAME} PUBLIC Qt5::Core Qt5::Gui Qt5::Widgets Qt5::PrintSupport)
    target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_11)
else()
    message(FATAL_ERROR "Qt < 5 not yet supported in this recipe")
endif()

install(TARGETS ${PROJECT_NAME}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(FILES source_subfolder/qcustomplot.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
