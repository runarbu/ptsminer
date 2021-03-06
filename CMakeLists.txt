CMAKE_MINIMUM_REQUIRED(VERSION 2.8.0)

###
### FIND ADDITIONAL CMAKE MODULES
###
#SET(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

###
### PROJECT
###
PROJECT(ptsminer)

###
### BOOST
###
SET(Boost_USE_MULTITHREADED ON)
SET(BOOST_MIN_VERSION "1.43.0")
FIND_PACKAGE(Boost ${BOOST_MIN_VERSION} COMPONENTS system thread REQUIRED)

MESSAGE("Boost information: found ${Boost_MAJOR_VERSION}.${Boost_MINOR_VERSION}.${Boost_SUBMINOR_VERSION}") 
MESSAGE("  Boost_INCLUDE_DIRS: ${Boost_INCLUDE_DIRS}") 
MESSAGE("  Boost_LIBRARIES: ${Boost_LIBRARIES}") 
MESSAGE("  Boost_LIBRARY_DIRS: ${Boost_LIBRARY_DIRS}") 

INCLUDE_DIRECTORIES(${Boost_INCLUDE_DIRS})
LINK_DIRECTORIES(${Boost_LIBRARY_DIRS})

if (APPLE)
    set(CMAKE_ASM_FLAGS "-f macho64 --prefix=_")
else (UNIX)
    SET(CMAKE_ASM_FLAGS "-f elf64")
else ()
    SET(CMAKE_ASM_FLAGS "-f win64")
endif()

SET(CMAKE_ASM_COMPILER "yasm")
enable_language(ASM)

#SET(Boost_USE_STATIC_LIBS ON)
#SET(Boost_USE_MULTITHREAD ON)

###
### GENERAL SOURCE STUFF
###
INCLUDE_DIRECTORIES("src")

SET(SRC_FILES
	src/cpuid.c
	src/sha512.c
	src/sph_sha2.c
	src/sph_sha2big.c
	src/main_poolminer.cpp
	src/intel/sha512_avx.asm
	src/intel/sha512_sse4.asm
)

###
### EXECUTABLE
###
ADD_EXECUTABLE(ptsminer
  ${SRC_FILES}
)

###
### LIBRARIES
###
TARGET_LINK_LIBRARIES(ptsminer ${Boost_SYSTEM_LIBRARY})
TARGET_LINK_LIBRARIES(ptsminer ${Boost_THREAD_LIBRARY})
TARGET_LINK_LIBRARIES(ptsminer z dl pthread)

###
### COMMON FLAGS
###
SET(CMAKE_CC_FLAGS "${CMAKE_CC_FLAGS} -O2 -pthread") # let's keep it simple
SET(CMAKE_C_FLAGS "${CMAKE_CC_FLAGS} -O2 -pthread") # ^ <-- THIS is actually bad for SPHLIB
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O2 -pthread") # ^
#SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O2")
#SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -DGLIBC_XX_DEBUG -Wall -ggdb -g3 -fno-rounding-math -msse -O0")
