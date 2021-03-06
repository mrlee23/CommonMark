# To get verbose output: cmake --build build --target "test" -- ARGS='-V'

find_package(PythonInterp 3 REQUIRED)
set(PYTHON ${PYTHON_EXECUTABLE})

add_test(html_normalization
  ${PYTHON} "-m" "doctest"
  "${CMAKE_CURRENT_SOURCE_DIR}/normalize.py"
)

add_test(spectest_library
   ${PYTHON} "${CMAKE_CURRENT_SOURCE_DIR}/spec_tests.py" "--no-normalize" "--spec"
   "${CMAKE_SOURCE_DIR}/spec.txt" "--library-dir" "${CMAKE_BINARY_DIR}/src"
)

add_test(pathological_tests_library
   ${PYTHON} "${CMAKE_CURRENT_SOURCE_DIR}/pathological_tests.py"
   "--library-dir" "${CMAKE_BINARY_DIR}/src"
)

add_test(NAME api_test COMMAND api_test)

if (WIN32)
  file(TO_NATIVE_PATH ${CMAKE_BINARY_DIR}/src WIN_DLL_DIR)
  set_tests_properties(api_test PROPERTIES
    ENVIRONMENT "PATH=${WIN_DLL_DIR};$ENV{PATH}"
  )
endif(WIN32)

add_test(spectest_executable
   ${PYTHON} "${CMAKE_CURRENT_SOURCE_DIR}/spec_tests.py" "--no-normalize" "--spec" "${CMAKE_SOURCE_DIR}/spec.txt" "--program" "${CMAKE_BINARY_DIR}/src/cmark"
)
