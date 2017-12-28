cmake_minimum_required(VERSION 2.6)

if(POLICY CMP0048)
    cmake_policy(SET CMP0048 NEW)
endif()

project(res2h)

#-------------------------------------------------------------------------------
#conan.io
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

set(BOOST_ROOT ${CONAN_BOOST_ROOT})
find_package(Boost 1.65 REQUIRED COMPONENTS filesystem system)

#-------------------------------------------------------------------------------
#define basic sources and headers

# Enable C++11
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang" OR CMAKE_COMPILER_IS_GNUCXX)
    add_definitions(-D_GLIBCXX_USE_CXX11_ABI=1)
endif()

set(R2H_HEADERS
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2h.h
	${CMAKE_CURRENT_SOURCE_DIR}/src/checksum.h
)

set(R2H_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2h.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/src/checksum.cpp
)

#define headers and sources for dump executable
set(R2H_DUMP_HEADERS
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2hinterface.h
	${CMAKE_CURRENT_SOURCE_DIR}/src/checksum.h
)

set(R2H_DUMP_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2hinterface.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2hdump.cpp
)

set(R2H_DUMP_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2hinterface.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2hdump.cpp
	${CMAKE_CURRENT_SOURCE_DIR}/src/checksum.cpp
)

#define headers and sources for unit test executable
set(R2H_UNITTEST_HEADERS
	${CMAKE_CURRENT_SOURCE_DIR}/src/res2h.h
)

set(R2H_UNITTEST_SOURCES
	${CMAKE_CURRENT_SOURCE_DIR}/src/test.cpp
)

#-------------------------------------------------------------------------------
#define OS specific sources and headers

if(MSVC)
	LIST(APPEND R2H_SOURCES
		${CMAKE_CURRENT_SOURCE_DIR}/src/res2h.rc
	)
endif()


#-------------------------------------------------------------------------------
#define target
include_directories(${R2H_INCLUDE_DIRS})
add_executable(res2h ${R2H_SOURCES} ${R2H_HEADERS})
target_link_libraries(res2h ${R2H_LIBRARIES})
target_link_libraries(res2h Boost::filesystem Boost::system)
set_target_properties(res2h PROPERTIES FOLDER 3rdParty/res2h)

#add dump executable to project
add_executable(res2hdump ${R2H_DUMP_SOURCES} ${R2H_DUMP_HEADERS})
target_link_libraries(res2hdump ${R2H_LIBRARIES})
target_link_libraries(res2hdump Boost::filesystem Boost::system)
set_target_properties(res2hdump PROPERTIES FOLDER 3rdParty/res2h)

#add unit test executable to project
#add_executable(unittest ${R2H_UNITTEST_SOURCES} ${R2H_UNITTEST_HEADERS})
#target_link_libraries(unittest ${R2H_LIBRARIES})

install(TARGETS res2h RUNTIME DESTINATION bin)
install(TARGETS res2hdump RUNTIME DESTINATION bin)
