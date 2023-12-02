set(fastbit-srcs array_t.cpp
        bitvector.cpp
        bitvector64.cpp
        blob.cpp
        bord.cpp
        bordm.cpp
        bundle.cpp
        capi.cpp
        category.cpp
        colValues.cpp
        column.cpp
        countQuery.cpp
        dictionary.cpp
        fileManager.cpp
        filter.cpp
        fromClause.cpp
        iapi.cpp
        ibin.cpp
        icegale.cpp
        icentre.cpp
        icmoins.cpp
        idbak.cpp
        idbak2.cpp
        idirekte.cpp
        ifade.cpp
        ikeywords.cpp
        imesa.cpp
        index.cpp
        irange.cpp
        irelic.cpp
        iroster.cpp
        isapid.cpp
        isbiad.cpp
        iskive.cpp
        islice.cpp
        ixambit.cpp
        ixbylt.cpp
        ixfuge.cpp
        ixfuzz.cpp
        ixpack.cpp
        ixpale.cpp
        ixzona.cpp
        ixzone.cpp
        jnatural.cpp
        jrange.cpp
        mensa.cpp
        meshQuery.cpp
        part.cpp
        parth.cpp
        parth2d.cpp
        parth3d.cpp
        parth3da.cpp
        parth3db.cpp
        parth3dw.cpp
        parti.cpp
        party.cpp
        qExpr.cpp
        quaere.cpp
        query.cpp
        resource.cpp
        rids.cpp
        selectClause.cpp
        tafel.cpp
        util.cpp
        utilidor.cpp
        whereClause.cpp
        fromLexer.cc
        fromParser.cc
        selectLexer.cc
        selectParser.cc
        whereLexer.cc
        whereParser.cc)

set(CMAKE_THREAD_PREFER_PTHREAD TRUE)
set(THREADS_PREFER_PTHREAD_FLAG TRUE)
find_package(Threads REQUIRED)

# library version set here (e.g. for shared libs).  we allow our container
# to choose the version, or it can just take the default.
if (NOT DEFINED FASTBIT_VERSION_MAJOR)
    set(FASTBIT_VERSION_MAJOR 2)
    set(FASTBIT_VERSION_MINOR 0)
    set(FASTBIT_VERSION_PATCH 3)
endif ()
set(fastbit-vers "${FASTBIT_VERSION_MAJOR}.${FASTBIT_VERSION_MINOR}")
set(FASTBIT_VERSION "${fastbit-vers}.${FASTBIT_VERSION_PATCH}")

# create the library target (user can specify shared vs.
# static using BUILD_SHARED_LIBS).
add_library(fastbit ${fastbit-srcs})
target_compile_definitions(fastbit PUBLIC "WITHOUT_FASTBIT_CONFIG_H")
if (FASTBIT_USE_CXX_ATOMIC)
    target_compile_definitions(fastbit PUBLIC "HAVE_ATOMIC_TEMPLATE")
endif ()
target_include_directories(fastbit PUBLIC $<INSTALL_INTERFACE:include>)
target_include_directories(fastbit BEFORE PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>)
target_link_libraries(fastbit PUBLIC Threads::Threads)
set_target_properties(fastbit PROPERTIES VERSION ${FASTBIT_VERSION}
        SOVERSION ${FASTBIT_VERSION_MAJOR})

# where to install files for "find_package"
set(fastbit-pkg-loc "share/cmake/fastbit")
include(CMakePackageConfigHelpers)
# installation stuff (packaging and install commands)
write_basic_package_version_file(
        ${CMAKE_CURRENT_BINARY_DIR}/fastbit-config-version.cmake
        VERSION ${FASTBIT_VERSION}
        COMPATIBILITY AnyNewerVersion)

# generate our config file for find_package()
configure_package_config_file(fastbit-config.cmake.in
        ${CMAKE_CURRENT_BINARY_DIR}/fastbit-config.cmake
        INSTALL_DESTINATION ${fastbit-pkg-loc})

# "make install" rules
install(TARGETS fastbit EXPORT fastbit-targets
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib)
install(EXPORT fastbit-targets DESTINATION ${fastbit-pkg-loc}
        FILE "fastbit-targets.cmake")
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/fastbit-config.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/fastbit-config-version.cmake"
        DESTINATION ${fastbit-pkg-loc})
install(DIRECTORY . DESTINATION include/fastbit
        FILES_MATCHING PATTERN "*.h")
