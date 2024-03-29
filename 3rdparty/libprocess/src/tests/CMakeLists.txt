# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# PROCESS TESTS.
################
set(PROCESS_TESTS_SRC
  ${PROCESS_TESTS_SRC}
  main.cpp
  after_tests.cpp
  collect_tests.cpp
  decoder_tests.cpp
  encoder_tests.cpp
  future_tests.cpp
  http_tests.cpp
  limiter_tests.cpp
  loop_tests.cpp
  metrics_tests.cpp
  mutex_tests.cpp
  owned_tests.cpp
  process_tests.cpp
  queue_tests.cpp
  sequence_tests.cpp
  shared_tests.cpp
  socket_tests.cpp
  statistics_tests.cpp
  subprocess_tests.cpp
  system_tests.cpp
  time_tests.cpp
  timeseries_tests.cpp
  )

if (NOT WIN32)
  set(PROCESS_TESTS_SRC
    ${PROCESS_TESTS_SRC}
    io_tests.cpp
    reap_tests.cpp
    )
endif (NOT WIN32)

if (ENABLE_SSL)
  set(PROCESS_TESTS_SRC
    ${PROCESS_TESTS_SRC}
    jwt_tests.cpp
    ssl_tests.cpp
    )
endif (ENABLE_SSL)


# ADDITIONAL CPP FLAGS FOR PROCESS TEST BINARY (e.g., -DBUILD_DIR=...).
#######################################################################
add_definitions("-DBUILD_DIR=\"${CMAKE_CURRENT_BINARY_DIR}\"")

# INCLUDE DIRECTIVES FOR PROCESS TEST BINARY (generates, e.g., -I/path/to/thing
# on Linux).
###############################################################################
include_directories(${PROCESS_TEST_INCLUDE_DIRS})

# LINKING LIBRARIES BY DIRECTORY (might generate, e.g., -L/path/to/thing on
# Linux).
###########################################################################
link_directories(${PROCESS_TEST_LIB_DIRS})

# THE PROCESS TEST EXECUTABLE (generates, e.g., libprocess-tests, etc., on Linux).
###############################################################################
add_executable(${PROCESS_TESTS_TARGET} EXCLUDE_FROM_ALL ${PROCESS_TESTS_SRC})

GROUP_STOUT_HEADERS()
GROUP_PROCESS_HEADERS()

GROUP_SOURCE(
  "tests"
  "${CMAKE_CURRENT_SOURCE_DIR}"
  "${CMAKE_CURRENT_SOURCE_DIR}"
  "*.[ch]*")

## ADD LINKER FLAGS (generates, e.g., -lglog on Linux).
#######################################################
#target_link_libraries(${PROCESS_TESTS_TARGET} ${PROCESS_TEST_LIBS})
#
## ADD BINARY DEPENDENCIES (tells CMake what to compile/build first).
#####################################################################
#add_dependencies(${PROCESS_TESTS_TARGET} ${PROCESS_TEST_DEPENDENCIES})

# ADDITIONAL EXECUTABLE(s).
###########################
add_executable(test-linkee EXCLUDE_FROM_ALL test_linkee.cpp)
target_link_libraries(test-linkee ${PROCESS_TEST_LIBS})
add_dependencies(${PROCESS_TESTS_TARGET} test-linkee)

if (ENABLE_SSL)
  add_executable(ssl-client EXCLUDE_FROM_ALL ssl_client.cpp)
  target_link_libraries(ssl-client ${PROCESS_TEST_LIBS})
  add_dependencies(${PROCESS_TESTS_TARGET} ssl-client)
endif (ENABLE_SSL)

# ADD TEST TARGET (runs when you do, e.g., `make check`).
#########################################################
add_test(NAME ProcessTests COMMAND ${PROCESS_TESTS_TARGET})
