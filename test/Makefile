# Default paths, can be overridden by setting MARSDEV before calling make
MARSDEV ?= ${HOME}/mars
MARSCPU  = m68k-elf
MARSGCC  = $(MARSDEV)/$(MARSCPU)

MARSBIN  = $(MARSGCC)/bin
TOOLSBIN = $(MARSDEV)/bin

TARGET = test

# GCC and Binutils
CC   = $(MARSBIN)/$(MARSCPU)-gcc
CXX  = $(MARSBIN)/$(MARSCPU)-g++
AS   = $(MARSBIN)/$(MARSCPU)-as
LD   = $(MARSBIN)/$(MARSCPU)-ld
NM   = $(MARSBIN)/$(MARSCPU)-nm
OBJC = $(MARSBIN)/$(MARSCPU)-objcopy

# LTO plugin path
GCC_VER := $(shell $(CC) -dumpversion)
PLUGIN   = $(MARSGCC)/libexec/gcc/$(MARSCPU)/$(GCC_VER)/liblto_plugin.so

# Includes: GCC + Newlib + DOS
INCS     = -I$(MARSGCC)/lib/gcc/$(MARSCPU)/$(GCC_VER)/include
INCS    += -I$(MARSGCC)/$(MARSCPU)/include
INCS    += -I../include

# Libraries: GCC + Newlib + DOS
LIBS     = -L$(MARSGCC)/lib/gcc/$(MARSCPU)/$(GCC_VER) -lgcc
LIBS    += -L.. -ldos -L$(MARSGCC)/$(MARSCPU)/lib -lc -lnosys

CCFLAGS  = -m68000 -Wall -Wextra
CCFLAGS += -O2 -fomit-frame-pointer -flto -fuse-linker-plugin

CXXFLAGS := $(CCFLAGS) -std=gnu++11
CCFLAGS  := $(CCFLAGS) -std=gnu99

ASFLAGS  := -m68000 --register-prefix-optional --bitwise-or

LDFLAGS  := -Wl,-q -T human68kxfile.x

CS    = $(wildcard *.c)
CPPS  = $(wildcard *.cpp)
SS    = $(wildcard *.s)
OBJS  = $(CS:.c=.o)
OBJS += $(CPPS:.cpp=.o)
OBJS += $(SS:.s=.o)

.PHONY: all
all: $(TARGET).X $(TARGET).lst

%.lst: %.elf
	$(NM) --plugin=$(PLUGIN) -n $< > $@

%.X: %.elf
	python3 elf2x.py $< $@

%.elf: $(OBJS)
	$(CC) -o $@ $(LDFLAGS) $(OBJS) $(LIBS)

%.o: %.c
	$(CC) -o $@ $(CCFLAGS) $(INCS) -c $<

%.o: %.cpp
	$(CXX) -o $@ $(CXXFLAGS) $(INCS) -c $<

%.o: %.s
	$(AS) -o $@ $(ASFLAGS) $<

.PHONY: clean
clean:
	rm -f $(OBJS) $(TARGET).X $(TARGET).lst
