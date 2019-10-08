
BUILD_DIR 			= build
TARGET   			= APPGS3MDM32A01

GCC_PATH 			= tools/gcc/win/gcc-arm-none-eabi
ENV_PATH			= $(GCC_PATH)/bin
ENV_INC				= $(GCC_PATH)/arm-none-eabi/include
ENV_LIB_EABI		= $(GCC_PATH)/arm-none-eabi/lib/armv7e-m/fpu
ENV_LIB_GCC			= $(GCC_PATH)/lib/gcc/arm-none-eabi/4.8.3/armv7e-m/fpu
ENV_LIB				= -L$(ENV_LIB_EABI) -L$(ENV_LIB_GCC)
CC 					= $(ENV_PATH)/arm-none-eabi-gcc
OC 					= $(ENV_PATH)/arm-none-eabi-objcopy
HG					= make/GFH_Generator

CORTEX  = -mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16 -mthumb -mthumb-interwork

CFLAGS += -I$(ENV_INC)
CFLAGS += -Iinclude
CFLAGS += -Iril/inc
CFLAGS += -Icustom/config
# ADD YOUR INC FILES
#CFLAGS +=

CFLAGS += -Os -fno-strict-aliasing -ffunction-sections -fdata-sections -fno-builtin -mlong-calls -std=c99
CFLAGS += -D_REENT_SMALL
# ADD YOUR DEFINES FILES
CFLAGS += -D__CUSTOMER_CODE__

CF = $(CORTEX) $(CFLAGS) -Wall -Wp,-w -c

all: start clean $(BUILD_DIR)/$(TARGET).elf $(BUILD_DIR)/$(TARGET).bin

$(BUILD_DIR)/$(TARGET).elf: 
	@echo " * COMPILE SDK FILES"
	@$(CC) $(CF) custom/config/custom_sys_cfg.c -o $(BUILD_DIR)/custom_sys_cfg.o
	@$(CC) $(CF) custom/config/sys_config.c -o $(BUILD_DIR)/sys_config.o
	@$(CC) $(CF) ril/src/ril_atResponse.c -o $(BUILD_DIR)/ril_atResponse.o
	@$(CC) $(CF) ril/src/ril_dfota.c -o $(BUILD_DIR)/ril_dfota.o
	@$(CC) $(CF) ril/src/ril_init.c -o $(BUILD_DIR)/ril_init.o
	@$(CC) $(CF) ril/src/ril_lwm2m.c -o $(BUILD_DIR)/ril_lwm2m.o
	@$(CC) $(CF) ril/src/ril_mqtt.c -o $(BUILD_DIR)/ril_mqtt.o
	@$(CC) $(CF) ril/src/ril_network.c -o $(BUILD_DIR)/ril_network.o
	@$(CC) $(CF) ril/src/ril_socket.c -o $(BUILD_DIR)/ril_socket.o
	@$(CC) $(CF) ril/src/ril_system.c -o $(BUILD_DIR)/ril_system.o
	@$(CC) $(CF) ril/src/ril_urc.c -o $(BUILD_DIR)/ril_urc.o
	@$(CC) $(CF) ril/src/ril_util.c -o $(BUILD_DIR)/ril_util.o
	@echo " * COMPILE USER FILES"
# ADD YOUR SRC FILES
	@$(CC) $(CF) -c custom/main.c -o $(BUILD_DIR)/main.o

	@echo " * LINKING"
	@$(CC) $(CORTEX) -o $@ -T libs/gcc/linkscript.ld 	\
		-nostartfiles 									\
		-Xlinker --gc-sections -Wl,--gc-sections		\
		$(BUILD_DIR)/*.o $(ENV_LIB) 					\
		-Wl,--start-group -lc -lgcc -lm libs/gcc/app_start.lib -Wl,--end-group 

$(BUILD_DIR)/$(TARGET).bin: $(BUILD_DIR)/$(TARGET).elf
	@echo " * CREATE BIN"
	@$(OC) -O binary $< $@
	@echo " * CREATE APP:" $@
	@$(HG) $(BUILD_DIR)/$(TARGET).bin
	@cmd /C copy /y libs/app_image_bin.cfg $(BUILD_DIR)

clean:
	@echo " * CLEAN"
	@rm -rf $(BUILD_DIR)
	@mkdir -p $(BUILD_DIR)
	
start:
	@echo "-= VSCode Quectel BC66 OpenCPU 2016 Georgi Angelov =-"	
