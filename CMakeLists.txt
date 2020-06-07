cmake_minimum_required(VERSION 3.15)
project(lua C)

if(LUA_BUILD_PIC)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
endif()

if(NOT DEFINED LUA_STDLIB)
    define(LUA_STDLIB coro db io math os str tab utf8)
endif()

set(CMAKE_C_DIALECT 99)

include_directories(.)

add_library(lua_objects OBJECT lzio.c lctype.c lopcodes.c lmem.c lundump.c ldump.c lstate.c
        lgc.c llex.c lcode.c lparser.c ldebug.c lfunc.c lobject.c ltm.c lstring.c ltable.c
        ldo.c lvm.c lapi.c  lzio.h lctype.h lopcodes.h lmem.h lundump.h lstate.h
        lgc.h llex.h lcode.h lparser.h ldebug.h lfunc.h lobject.h ltm.h lstring.h
        ltable.h ldo.h lvm.h lapi.h)

if(LUA_STDLIB)
    foreach(lib ${LUA_STDLIB})
        set(libs ${libs} l${lib}lib.c)
    endforeach()
    add_library(luastd OBJECT loadlib.c linit.c lbaselib.c ${libs})
endif()


add_library(lua-static STATIC $<TARGET_OBJECTS:lua_objects> $<TARGET_OBJECTS:luastd>)

if(LUA_BUILD_LUAC)
    add_executable(luac luac.c $<TARGET_OBJECTS:lua_objects>)
    target_link_libraries(luac lua_objects)
endif()

if(LUA_BUILD_INTERP)
    add_executable(lua lua.c)
    target_link_libraries(lua lua-static)
endif()