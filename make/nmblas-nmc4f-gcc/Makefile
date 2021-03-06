#
# This is a universal Makefile for NeuroMatrix projects (application or library) [EDITION 20]
# with automatic recursive search of source files , compiling and linking.
# Makefile is processed by GNU make and may be used both in Linux and Windows OS
#
# To compile it you need: 
# * Latest NMSDK installed with utilities in your $PATH
# * NEURO environment variable pointing to NMSDK directory
# * Host GCC (Since nmcpp doesn't support generating header dependences, we use gcc for that) 
#
# Under OS Windows you need:
# * GNU Make           http://gnuwin32.sourceforge.net/packages/make.htm
# * GNU UNIX utilities http://sourceforge.net/projects/unxutils/
# * PATH enviroment variable pointing to GnuWin32 gcc
# * PATH enviroment variable pointing to GNU make
# * PATH enviroment variable pointing to GNU Utilities  (rm,find..)
#
# NOTE: You can use all-in-one package for Windows (Mingw32 + GNU make + GNU UNIX Utilities) 
#   available at \\dragon\distrib\ext_soft\gcc_make\mingw32_plus_gnumake.zip 
#   set PATH enviroment variable pointing to \mingw32\bin only
#
# USAGE:
#   For release build                    run 'make'
#   For debug   build                    run 'make DEBUG=y'
#   For debug & release  build           run 'make all'
#   For full  rebuild                    run 'make -B'
#   Multithread build (speed up)         run 'make -j4'
#   To clean                             run 'make clean'
#   To clean DEBUG & RELEASE             run 'make cleanall'
#   To create VisualStudio 2005 project  run 'make vs8'
#   For verbose build (no colorizing)    run 'make VERBOSE=y'
#   Execute target                       run 'make run'
#   Execute debug target                 run 'make rund'
#
# WARNING:
#   Makefile is sensitive to leading or trailing spaces and backslashes in the pathes
#
# DEFENITIONS:
#   PROJECT         - project name     
#   TARGET          - output target (.lib or .abs) file with path
#   INC_DIRS        - set of search pathes for the macro libraries and includes (with -I flag separated)
#   SRC_DIRS        - set of explicit  search pathes of all (.c .cpp .asm) source files (with space separated)
#   #SRC_DIR_RECURSE - set of recursive search pathes of all (.c .cpp .asm) source files (with space separated)
#   LIB_DIRS        - set of search library pathes (with -l flag separated)
#   TMP_DIR         - temporary build-folder (Debug/Release)
#   LIBS            - set of (.lib) libraries to be linked (with space separated)
#   
#   CROSS_COMPILE   - prefix before gcc in crosscompiler full name (for example: i686-w64-mingw32-) needed for generation of header dependences
#   CC              - C/C++ compiler(nmcpp/nmcc)
#   CC_FLAGS        - C/C++ compiler(nmcpp/nmcc) flags   
#   AS				- assembler(asm/nmcc)
#   AS_FLAGS        - assembler(asm/nmcc) flags for compiling of pure .asm files 
#   AS_FLAGS_C2ASM  - assembler(asm/nmcc) flags for compiling of .asm files compiled from .c/.cpp sources
#   BUILDER         - builder  (libr/linker) 			
#   BUILDER_FLAGS   - build flags of (libr/linker)
#   RUN_COMMAND     - command line to run target    
#
#   HEADER_DIRS     - set of pathes for .h   files to be included into Visual Studio project (with space separated)
#   RESOURCES       - set of any additionals files to be included into Visual Studio project
#
#============== PROJECT & BUILD CONFIGURATION ==========
#
#include .toolchecked.mk

#ifdef NEURO
#  .DEFAULT_GOAL := default
#else 
#  $(error *** ERROR: 'NEURO' environment variable is not defined! NMSDK might not have installed correctly. )
#  .DEFAULT_GOAL := skip
#endif

ARCH            ?=nmc4
#--------------  target & input dirs -------------------
ROOT             = ../..
include          $(ROOT)/global.mk
PROJECT          = nmblas-$(ARCH)f-gcc
OUT_DIR          = $(ROOT)/lib
TARGET           = $(OUT_DIR)/$(PROJECT).lib
INC_DIRS         = -I"$(NEURO)/include"  -I$(ROOT)/include

LIB_DIRS         = 
TMP_DIR          =Release
LIBS             = 
#--------------  RELEASE/ALL config -------------------
CROSS_COMPILE   ?=
AS               =asm 				 
AS_FLAGS         =-$(ARCH) -nm2ms  $(INC_DIRS) -split_sir -W-111 -W-109
AS_FLAGS_C2ASM   =-$(ARCH) -nm2ms  $(INC_DIRS) -split_sir -W-111 -W-109
LIBS             =
CC               =nmc-gcc

CCPP_FLAGS       =-nmc3 -DNEURO -OPT2 -inline 
CC_FLAGS         =$(CCPP_FLAGS) -Tc99
BUILDER          =libr
BUILDER_FLAGS    =-c $(TARGET)
#--------------  DEBUG config -------------------------
ifdef DEBUG      
TARGET           =$(OUT_DIR)/$(PROJECT)d.lib
CCPP_FLAGS       =-nmc3 -DNEURO -OPT0 -inline -debug 
CC_FLAGS         =$(CCPP_FLAGS) -Tc99
LIBS            :=
AS_FLAGS        +=-ga
BUILDER_FLAGS   +=-d0
TMP_DIR          =Debug
endif 
#--------------  Visual Studio config (for vs8.mk)--
HEADER_DIRS      =$(ROOT)/include 
RESOURCES        =Makefile



#--------------  Header dependence parsing -------------------------
ifdef HAVE_gcc
PREPROCESS_ASM_DEP = $(CROSS_COMPILE)gcc -E -MM -xassembler-with-cpp -MT$(@) $(<) -o$(@).dep $(INC_DIRS) 
PREPROCESS_CPP_DEP = $(CROSS_COMPILE)gcc -E -MM -MT$(@) $(<) -o$(@).dep $(INC_DIRS)
endif

#=================== SOURCE & OBJECTS COLLECTION ===========================
SRC_DIRS         = 	 \
					$(ROOT)/src/level1/common \
					$(ROOT)/src/level2/common \
					$(ROOT)/src/level1/nm \
					$(ROOT)/src/level2/nm \
					$(ROOT)/src/level1/nmcore \
					$(ROOT)/src/level2/nmcore \

#-include colorizer.mk
-include $(TMP_DIR)/*.dep
.SUFFIXES:




# regular search of *.c *.cpp,*.asm sources in all $(SRC_DIRS)
ALL_CPP =$(wildcard $(addsuffix /*.cpp,$(SRC_DIRS)))
ALL_ASM =$(wildcard $(addsuffix /*.asm,$(SRC_DIRS)))
ALL_S   =$(wildcard $(addsuffix /*.s,$(SRC_DIRS)))
ALL_C   =$(wildcard $(addsuffix /*.c,$(SRC_DIRS)))
ALL_H   =$(wildcard $(addsuffix /*.h,$(HEADER_DIRS)))

VPATH    = $(SRC_DIRS)

# collecting of objects
OBJECTS_CPP = $(notdir $(patsubst %.cpp,%.o,$(ALL_CPP)))
OBJECTS_ASM = $(notdir $(patsubst %.asm,%.o,$(ALL_ASM)))
OBJECTS_S   = $(notdir $(patsubst %.s,%.o,$(ALL_S)))
OBJECTS_C   = $(notdir $(patsubst %.c,%.o,$(ALL_C)))
OBJECTS     = $(addprefix $(TMP_DIR)/,$(OBJECTS_C) $(OBJECTS_CPP)  $(OBJECTS_ASM) $(OBJECTS_S))


#======================== BUILD RULES ====================================
.PHONY:all  clean cleanall vs8  skip $(PROJECT).vcproj

#.DEFAULT_GOAL=default
default: $(TMP_DIR) $(OUT_DIR)  $(TARGET)

all: 
	$(MAKE) -j4 DEBUG=y
	$(MAKE) -j4

	
$(TMP_DIR):
	-mkdir "$(@)"

$(TARGET): $(TMP_DIR) $(OBJECTS) 
	$(BUILDER) $(BUILDER_FLAGS) $(OBJECTS) 

$(OUT_DIR): 
	-mkdir "$(@)"
	
$(TMP_DIR)/%.o: %.asm 
	$(PREPROCESS_ASM_DEP)
	$(AS) $(AS_FLAGS) $(<) -o$(@)

$(TMP_DIR)/%.o: %.s
	$(PREPROCESS_ASM_DEP)
	$(AS) $(AS_FLAGS) $(<) -o$(@)

$(TMP_DIR)/%.o: $(TMP_DIR)/%.asmx 
	$(AS) $(AS_FLAGS_C2ASM) $(<) -o$(@)

$(TMP_DIR)/%.asmx: %.cpp 
	$(PREPROCESS_CPP_DEP)
	$(CC) $(CCPP_FLAGS) $(<) -O$(@) $(INC_DIRS) 

$(TMP_DIR)/%.asmx: %.c
	$(PREPROCESS_CPP_DEP)
	$(CC) $(CC_FLAGS) $(<) -O$(@) $(INC_DIRS) 

clean:
	-$(OS_RM) *.abs *.o *.ncb *.ilk  *.dep *.ac *.map *.suo *.user *.ncb .toolchecked.mk null
	-$(OS_RD) Debug Release
 
#========================== Visual Studio 8 support ===================

vs8: $(PROJECT).vcproj

$(PROJECT).vcproj: Makefile
	make --file=$(ROOT)/deps/projector/vs8.mk nmc-app MAK=$(realpath .)/Makefile --silent >$(@)

vs13: $(PROJECT).vcxproj

$(PROJECT).vcxproj: Makefile
	make --file=$(ROOT)/deps/projector/vs13.mk nmc-app MAK=$(realpath .)/Makefile --silent >$(@)

skip:
	@echo SDK missing. Compilation is skipped!
