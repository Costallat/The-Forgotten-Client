cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

# apt install build-essential git
# git clone https://github.com/Kitware/CMake/; cd CMake
# ./bootstrap && make && sudo make install

# *****************************************************************************
# Cmake Hunter
# *****************************************************************************
if(UNIX)
  include("cmake/HunterGate.cmake")

  HunterGate(
      URL "https://github.com/cpp-pm/hunter/archive/v0.23.285.tar.gz"
      SHA1 "f8af933fb1b13563f604ee3bb61da008e7ec7a67"
      LOCAL
  )

endif()

# *****************************************************************************
# Project The Forgotten Client
# *****************************************************************************
project(the-forgotten-client)


# *****************************************************************************
# Options
# *****************************************************************************
option(OPTIONS_ENABLE_CCACHE "Enable ccache" ON)
option(OPTIONS_ENABLE_IPO "Check and Enable interprocedural optimization (IPO/LTO)" ON)


# *****************************************************************************
# Append cmake search path
# *****************************************************************************
list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)


# *****************************************************************************
# Include cmake tools
# *****************************************************************************
include(MessageColors)
include(LoggingHelper)


# *****************************************************************************
# Options Code
# *****************************************************************************

# === CCACHE ===
if(OPTIONS_ENABLE_CCACHE)
    find_program(CCACHE ccache)
    if(CCACHE)
        log_option_enabled("ccache")
        set(CMAKE_CXX_COMPILER_LAUNCHER ${CCACHE})
        else()
        log_option_disabled("ccache")
    endif()
endif()


# === IPO ===
option(OPTIONS_ENABLE_IPO "Check and Enable interprocedural optimization (IPO/LTO)" ON)
if(OPTIONS_ENABLE_IPO)
  log_option_enabled("ipo")

  include(CheckIPOSupported)
  check_ipo_supported(RESULT result OUTPUT output)
  if(result)
    set(CMAKE_INTERPROCEDURAL_OPTIMIZATION TRUE)
  else()
    log_war("IPO is not supported: ${output}")
  endif()
else()
  log_option_disabled("ipo")
endif()


# *****************************************************************************
# Add project
# *****************************************************************************
add_subdirectory(src)
