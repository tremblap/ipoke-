cmake_minimum_required(VERSION 3.19)
project(ipoke~)

if(NOT SDK)
  message(FATAL_ERROR "Pass -DSDK=/path/to/max-sdk-base")
endif()

string(REPLACE "~" "_tilde" EXT_SAFE_NAME "${PROJECT_NAME}")

set(EXT_VERSION   "4.2.0" CACHE STRING "External version (major.minor.patch)")
set(EXT_COPYRIGHT "copyright P.A.Tremblay 2026")

set(C74_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/out")

include(${SDK}/script/max-pretarget.cmake)
include_directories(${C74_INCLUDES})

add_library(${PROJECT_NAME} MODULE ipoke~.c)

include(${SDK}/script/max-posttarget.cmake)

# ---------------------------------------------------------------- macOS
if(APPLE)
  set(PLIST "$<TARGET_BUNDLE_CONTENT_DIR:${PROJECT_NAME}>/Info.plist")
  set(PB /usr/libexec/PlistBuddy)

  # Set a string key, adding it if it doesn't exist yet
  function(plist_set key value)
    add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
      COMMAND sh -c "${PB} -c 'Set :${key} ${value}' '${PLIST}' 2>/dev/null || ${PB} -c 'Add :${key} string ${value}' '${PLIST}'"
      VERBATIM)
  endfunction()

  plist_set(CFBundleInfoDictionaryVersion "${EXT_VERSION}")
  plist_set(CFBundleShortVersionString    "${EXT_VERSION}")
  plist_set(CFBundleLongVersionString     "$<TARGET_FILE_NAME:${PROJECT_NAME}>, ${EXT_COPYRIGHT}")

  # Must stay the LAST post-build step, since any plist edit invalidates the signature
  add_custom_command(TARGET ${PROJECT_NAME} POST_BUILD
    COMMAND codesign --force --sign - "$<TARGET_BUNDLE_DIR:${PROJECT_NAME}>"
    VERBATIM)
endif()

# -------------------------------------------------------------- Windows
if(WIN32)
  enable_language(RC)

  # "2.3.1" -> "2,3,1,0" (the numeric form VERSIONINFO requires)
  string(REPLACE "." "," EXT_VERSION_RC "${EXT_VERSION}")
  set(EXT_VERSION_RC "${EXT_VERSION_RC},0")

  set(EXT_FILENAME "${PROJECT_NAME}.mxe64")
  set(EXT_RC_FILE  "${CMAKE_BINARY_DIR}/${EXT_SAFE_NAME}_version.rc")

  file(WRITE "${EXT_RC_FILE}" "\
#include <winver.h>

VS_VERSION_INFO VERSIONINFO
FILEVERSION    ${EXT_VERSION_RC}
PRODUCTVERSION ${EXT_VERSION_RC}
FILEFLAGSMASK  0x3fL
FILEFLAGS      0x0L
FILEOS         VOS_NT_WINDOWS32
FILETYPE       VFT_DLL
FILESUBTYPE    VFT2_UNKNOWN
BEGIN
  BLOCK \"StringFileInfo\"
  BEGIN
    BLOCK \"040904b0\"
    BEGIN
      VALUE \"FileDescription\",  \"${PROJECT_NAME} Max external\"
      VALUE \"FileVersion\",      \"${EXT_VERSION}\"
      VALUE \"ProductName\",      \"${PROJECT_NAME}\"
      VALUE \"ProductVersion\",   \"${EXT_VERSION}\"
      VALUE \"InternalName\",     \"${EXT_SAFE_NAME}\"
      VALUE \"OriginalFilename\", \"${EXT_FILENAME}\"
      VALUE \"LegalCopyright\",   \"${EXT_COPYRIGHT}\"
    END
  END
  BLOCK \"VarFileInfo\"
  BEGIN
    VALUE \"Translation\", 0x409, 1200
  END
END
")

  target_sources(${PROJECT_NAME} PRIVATE "${EXT_RC_FILE}")
endif()