set(INSTALL_LIB_DIR "${CMAKE_INSTALL_PREFIX}/lib" CACHE PATH "Installation directory for libraries")
set(INSTALL_INC_DIR_CLIENT "${CMAKE_INSTALL_PREFIX}/include/client" CACHE PATH "Installation directory for headers")

set(CRASHPAD_CLIENT_LIBRARY_FILES
    annotation_list.cc
    annotation.cc
    crash_report_database.cc
    crashpad_info.cc
    prune_crash_reports.cc
    settings.cc
)
set(CRASHPAD_CLIENT_LIBRARY_FILES_HEADERS
    annotation_list.h
    annotation.h
    crash_report_database.h
    crashpad_client.h
    crashpad_info.h
    prune_crash_reports.h
    settings.h
    simple_address_range_bag.h
    simple_string_dictionary.h
    simulate_crash.h
)
if (APPLE)
    list(APPEND CRASHPAD_CLIENT_LIBRARY_FILES
        crash_report_database_mac.mm
        crashpad_client_mac.cc
        simulate_crash_mac.cc
        simulate_crash_mac.h
    )
endif (APPLE)

if (LINUX)
    list(APPEND CRASHPAD_CLIENT_LIBRARY_FILES
        client_argv_handling.cc
        crash_report_database_generic.cc
        crashpad_client_linux.cc
        pthread_create_linux.cc
    )
    list(APPEND CRASHPAD_CLIENT_LIBRARY_FILES_HEADERS
        client_argv_handling.h
    )
endif (LINUX)

if (WIN32)
    list(APPEND CRASHPAD_CLIENT_LIBRARY_FILES
        crash_report_database_win.cc
        crashpad_client_win.cc
    )
endif (WIN32)

add_library(client STATIC ${CRASHPAD_CLIENT_LIBRARY_FILES} ${CRASHPAD_CLIENT_LIBRARY_FILES_HEADERS})

target_compile_features(client PUBLIC cxx_std_14)

target_link_libraries(client PUBLIC mini_chromium util compat)
target_include_directories(client PUBLIC ..)

if (LINUX)
    target_link_libraries(client PUBLIC Threads::Threads)
endif (LINUX)
install(TARGETS client
        ARCHIVE DESTINATION "${INSTALL_LIB_DIR}" )
install(FILES ${CRASHPAD_CLIENT_LIBRARY_FILES_HEADERS} DESTINATION "${INSTALL_INC_DIR_CLIENT}")