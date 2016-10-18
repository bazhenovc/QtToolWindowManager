
project(QToolWindowManager)

find_package(Qt5Widgets REQUIRED)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)

include_directories(.)

add_library(QToolWindowManager STATIC
    qabstracttoolwindowmanagerarea.h
    qabstracttoolwindowmanagerarea.cpp
    qtoolwindowmanager.h
    qtoolwindowmanager.cpp
    qtoolwindowmanagerarea.cpp
    qtoolwindowmanagerwrapper.cpp
    private/qtoolwindowmanager_p.h
    private/qtoolwindowmanagerarea_p.h
    private/qtoolwindowmanagerwrapper_p.h
)
target_link_libraries(QToolWindowManager Qt5::Widgets)
