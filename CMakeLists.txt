CMAKE_MINIMUM_REQUIRED(VERSION 3.10.2)

# 打印一下当前的路径
MESSAGE(STATUS "CMAKE_CURRENT_SOURCE_DIR: ${CMAKE_CURRENT_SOURCE_DIR}")

# 编译器路径
SET(TOOLCHAIN_DIR ${CMAKE_CURRENT_SOURCE_DIR}/gcc-arm-none-eabi-10.3-2021.10)

# 查找根路径
SET(CMAKE_FIND_ROOT_PATH "${TOOLCHAIN_DIR}")

# 设置GCC编译器并禁用检查
set(CMAKE_C_COMPILER_WORKS 1)
set(CMAKE_CXX_COMPILER_WORKS 1)
SET(CMAKE_C_COMPILER    ${TOOLCHAIN_DIR}/bin/arm-none-eabi-gcc)
SET(CMAKE_CXX_COMPILER  ${TOOLCHAIN_DIR}/bin/arm-none-eabi-g++)
SET(CMAKE_ASM_COMPILER  ${TOOLCHAIN_DIR}/bin/arm-none-eabi-gcc)
SET(CMAKE_EXE_LINKER    ${TOOLCHAIN_DIR}/bin/arm-none-eabi-gcc)
SET(OBJCOPY             ${TOOLCHAIN_DIR}/bin/arm-none-eabi-objcopy)
SET(SIZE                ${TOOLCHAIN_DIR}/bin/arm-none-eabi-size)

# 工程名称
PROJECT(ch563q-test)

# 相关输出文件
set(ELF_FILE ${PROJECT_NAME}.elf)
set(HEX_FILE ${PROJECT_NAME}.hex)
set(BIN_FILE ${PROJECT_NAME}.bin)
set(MAP_FILE ${PROJECT_NAME}.map)

# 指定系统名称
set(CMAKE_SYSTEM_NAME Generic)

# 指定系统架构
SET(CMAKE_SYSTEM_PROCESSOR armv5te)

# 启用汇编语言的支持
ENABLE_LANGUAGE(ASM)

# 设置连接文件
SET(CMAKE_LD_FILE ${CMAKE_CURRENT_SOURCE_DIR}/CH56X.ld)

# 头文件查找路径
INCLUDE_DIRECTORIES(
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}/include/sys
	)

# 源文件列表
SET(SRCS
    ${CMAKE_CURRENT_SOURCE_DIR}/src/sys/INT.c
    ${CMAKE_CURRENT_SOURCE_DIR}/src/sys/SYSFREQ.c
    ${CMAKE_CURRENT_SOURCE_DIR}/CH56XSTARTUP.S
    )
    
# 设置 c flags
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -mcpu=arm966e-s -mthumb -mthumb-interwork -Os -fmessage-length=0 -fsigned-char -ffunction-sections -fdata-sections -fno-common -g -std=gnu11 -mfloat-abi=soft -march=armv5te -DDEBUG")

# 设置 cxx flags
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${CMAKE_C_FLAGS}")

# 设置 as flags
SET(CMAKE_ASM_FLAGS "${CMAKE_AR_FLAGS} -march=armv5te -mthumb -mthumb-interwork -mfloat-abi=soft")

# 设置 linker flags
SET(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -T ${CMAKE_LD_FILE} -nostartfiles -Xlinker --gc-sections -Wl,-Map,${PROJECT_NAME}.map --specs=nano.specs --specs=nosys.specs")

# 编译对象 elf
ADD_EXECUTABLE(${ELF_FILE} ${SRCS})

# 转换 elf 为 bin 和 hex 文件，打印一下大小
ADD_CUSTOM_COMMAND(TARGET ${ELF_FILE} POST_BUILD
	COMMAND ${OBJCOPY} -Obinary ${ELF_FILE} ${BIN_FILE}
	COMMAND ${OBJCOPY} -Oihex   ${ELF_FILE} ${HEX_FILE}
	COMMAND ${SIZE} --format=berkeley ${ELF_FILE} ${HEX_FILE}
	COMMENT "Invoking: Cross ARM GNU Print Size"
)
