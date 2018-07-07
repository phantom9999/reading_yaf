cmake_minimum_required(VERSION 2.8)
project(reading_yaf)


if (DEFINED PHP_CONFIG_DIR)
    set(PHP_CONFIG_DIR "${PHP_CONFIG_DIR}/")
else ()
    set(PHP_CONFIG_DIR "")
endif ()
# 读取include目录
execute_process(COMMAND ${PHP_CONFIG_DIR}php-config --include-dir
        OUTPUT_VARIABLE PHP_INCLUDE_DIR
        OUTPUT_STRIP_TRAILING_WHITESPACE
        )
# 读取链接库
execute_process(COMMAND ${PHP_CONFIG_DIR}php-config --libs
        OUTPUT_VARIABLE PHP_LIBS
        OUTPUT_STRIP_TRAILING_WHITESPACE
        )
# 读取链接参数
execute_process(COMMAND ${PHP_CONFIG_DIR}php-config --ldflags
        OUTPUT_VARIABLE PHP_LDFLAGS
        OUTPUT_STRIP_TRAILING_WHITESPACE
        )
# 获取插件存放目录
execute_process(COMMAND ${PHP_CONFIG_DIR}php-config --extension-dir
        #    RESULT_VARIABLE PHP_EXTDIR
        OUTPUT_VARIABLE PHP_EXTDIR
        OUTPUT_STRIP_TRAILING_WHITESPACE
        )
add_definitions(
        -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1
        -DHAVE_CONFIG_H
        -DPHP_ATOM_INC
)
# 包含目录, 保持与php-config --includes的结果一致
include_directories(
        BEFORE
        ${PHP_INCLUDE_DIR}
        ${PHP_INCLUDE_DIR}/Zend
        ${PHP_INCLUDE_DIR}/main
        ${PHP_INCLUDE_DIR}/TSRM
        ${PHP_INCLUDE_DIR}/ext
        ${PHP_INCLUDE_DIR}/ext/date/lib
)


# 添加链接库
#link_directories(${PHP_LDFLAGS})
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -g -fno-common -fPIC -DPIC -pipe -w -Wall")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fno-common -DPIC")

include_directories(.)
include_directories(configs)
include_directories(requests)
include_directories(responses)
include_directories(routes)
include_directories(views)


aux_source_directory(. SRC)
aux_source_directory(configs SRC)
aux_source_directory(requests SRC)
aux_source_directory(responses SRC)
aux_source_directory(routes SRC)
aux_source_directory(views SRC)
add_library(yaf SHARED ${SRC})
install(
        TARGETS yaf
        LIBRARY DESTINATION ${PHP_EXTDIR}
)

target_link_libraries(
        yaf

)


set_target_properties(
        yaf PROPERTIES
        PREFIX ""
        SUFFIX ".so"
)



