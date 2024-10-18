cmake_minimum_required(VERSION 3.12)
project(pypostal)

find_package (Python3 REQUIRED COMPONENTS Interpreter Development)

execute_process (COMMAND "${Python3_EXECUTABLE}" -c "from distutils import sysconfig;print(sysconfig.get_config_var('EXT_SUFFIX'))"
        RESULT_VARIABLE _result
        OUTPUT_VARIABLE PYTHON_MODULE_EXTENSION
        ERROR_QUIET
        OUTPUT_STRIP_TRAILING_WHITESPACE)

set(EXTENSIONS "dedupe" "expand" "neardupe" "normalize" "parser" "tokenize" "tokentypes")

foreach (EXTENSION ${EXTENSIONS})

    add_library(_${EXTENSION} SHARED postal/py${EXTENSION}.c postal/pyutils.c)
    target_include_directories (_${EXTENSION} PRIVATE ${Python3_INCLUDE_DIRS})
    target_include_directories (_${EXTENSION} PUBLIC ${Python3_INCLUDE_DIRS})
    target_link_libraries (_${EXTENSION} PUBLIC python)

    set_target_properties(
            _${EXTENSION}
            PROPERTIES
            PREFIX ""
            OUTPUT_NAME "postal/_${EXTENSION}.cpython-312-x86_64-linux-gnu.so"
            LINKER_LANGUAGE C
    )

endforeach ()