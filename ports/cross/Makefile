#
# Copyright (c) 2001, 2002 Swedish Institute of Computer Science.
# Copyright (c) 2011 Gerald Van Baren
# All rights reserved. 
# 
# Redistribution and use in source and binary forms, with or without modification, 
# are permitted provided that the following conditions are met:
#
# 1. Redistributions of source code must retain the above copyright notice,
#    this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright notice,
#    this list of conditions and the following disclaimer in the documentation
#    and/or other materials provided with the distribution.
# 3. The name of the author may not be used to endorse or promote products
#    derived from this software without specific prior written permission. 
#
# THIS SOFTWARE IS PROVIDED BY THE AUTHOR ``AS IS'' AND ANY EXPRESS OR IMPLIED 
# WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF 
# MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT 
# SHALL THE AUTHOR BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, 
# EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT 
# OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS 
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN 
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING 
# IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY 
# OF SUCH DAMAGE.
#
# This file is part of the lwIP TCP/IP stack.
# 
# Author: Adam Dunkels <adam@sics.se>
# Secondary author: Gerald Van Baren <gvb@unssw.com>
#

CONTRIBDIR=../..
LWIPARCH=$(CONTRIBDIR)/ports/cross/src

TOP_LEVEL_PROGRAM_INCLUDE=$(LWIP_TOP_LEVEL_PROGRAM_INCLUDE)

ifndef CROSS_COMPILE
CROSS_COMPILE = arm-none-eabi-
endif

#Set this to where you have the lwip core module checked out from CVS
#default assumes it's a dir named lwip at the same level as the contrib module
LWIPDIR=$(CONTRIBDIR)/../lwip/src

#Set this to where you have the arch dir for your system for arch/sys_arch.h and arch/cc.h
RTOS_SOURCE_DIR=$(LWIP_RTOS_SOURCE_DIR)


# Convention for where the build detritus ends up
BUILD_DIR = obj/

# Quiet vs. verbose: to make things verbose, put a # in front of the @
Q	?= @

# Make variables (CC, etc...)

AS	= $(CROSS_COMPILE)as
LD	= $(CROSS_COMPILE)ld
CC	= $(CROSS_COMPILE)gcc
CPP	= $(CC) -E
AR	= $(CROSS_COMPILE)ar
NM	= $(CROSS_COMPILE)nm
STRIP	= $(CROSS_COMPILE)strip
OBJCOPY	= $(CROSS_COMPILE)objcopy
OBJDUMP	= $(CROSS_COMPILE)objdump

#
# The flags passed to the compiler.
#
CFLAGS-$(CONFIG_TOOLCHAIN_USE_MTHUMB) = "-mthumb"
CFLAGS-y                              += $(TOOLCHAIN_MCPU_EQ)
CFLAGS-y                              += $(TOOLCHAIN_MARCH_EQ)
CFLAGS-y                              += $(TOOLCHAIN_MFLOAT_ABI_EQ)
CFLAGS-y                              += $(TOOLCHAIN_MFPU_EQ)
CFLAGS-y                              += $(TOOLCHAIN_OPTIMISATION)
CFLAGS-y                              += -ffunction-sections
CFLAGS-y                              += -fdata-sections
CFLAGS-y                              += -std=c99
CFLAGS-y                              += -c
CFLAGS-$(CONFIG_TOOLCHAIN_PEDANTIC)   += -pedantic
######CFLAGS-$(CONFIG_TOOLCHAIN_WALL)       += -Wall
CFLAGS-$(CONFIG_TOOLCHAIN_VERBOSE)    += --verbose
CFLAGS-$(CONFIG_TOOLCHAIN_DEBUG)      += -g
CFLAGS-y                              += -DPART_$(PART)

CFLAGS-y                              += -DIPv4

CFLAGS-y                              += -I$(TOP_LEVEL_PROGRAM_INCLUDE)
CFLAGS-y                              += -I$(LWIPDIR)/include
CFLAGS-y                              += -I$(LWIPARCH)/include
CFLAGS-y                              += -I$(LWIPDIR)/include/ipv4
CFLAGS-y                              += -I./src/include
CFLAGS-y                              += -I./src/include/LM3S
###TBD Do we need this? CFLAGS-y                              += -I$(TOP_LEVEL_PROGRAM)/src
CFLAGS-y                              += -I$(RTOS_SOURCE_DIR)/include

CFLAGS-$(CONFIG_FREERTOS_PORT_ARM_CM3)  += -I$(RTOS_SOURCE_DIR)/portable/GCC/ARM_CM3
CFLAGS-$(CONFIG_FREERTOS_PORT_ARM_CM4F) += -I$(RTOS_SOURCE_DIR)/portable/GCC/ARM_CM4F

CFLAGS = $(CFLAGS-y)


# COREFILES, CORE4FILES: The minimum set of files needed for lwIP.
COREFILES=$(LWIPDIR)/core/mem.c $(LWIPDIR)/core/memp.c $(LWIPDIR)/core/netif.c \
	$(LWIPDIR)/core/stats.c $(LWIPDIR)/core/sys.c \
	$(LWIPDIR)/core/tcp.c $(LWIPDIR)/core/tcp_in.c $(LWIPDIR)/core/raw.c \
	$(LWIPDIR)/core/tcp_out.c $(LWIPDIR)/core/udp.c $(LWIPDIR)/core/init.c \
	$(LWIPDIR)/core/def.c $(LWIPDIR)/core/timers.c $(LWIPDIR)/core/dhcp.c
CORE4FILES=$(LWIPDIR)/core/ipv4/icmp.c $(LWIPDIR)/core/ipv4/ip.c \
	$(LWIPDIR)/core/ipv4/inet.c $(LWIPDIR)/core/ipv4/ip_addr.c \
	$(LWIPDIR)/core/ipv4/inet_chksum.c $(LWIPDIR)/core/ipv4/ip_frag.c \
	$(LWIPDIR)/core/ipv4/autoip.c


# APIFILES: The files which implement the sequential and socket APIs.
APIFILES=$(LWIPDIR)/api/api_lib.c $(LWIPDIR)/api/api_msg.c $(LWIPDIR)/api/tcpip.c \
	$(LWIPDIR)/api/err.c $(LWIPDIR)/api/sockets.c $(LWIPDIR)/api/netbuf.c $(LWIPDIR)/api/netdb.c

# NETIFFILES: Files implementing various generic network interface functions.'
NETIFFILES=$(LWIPDIR)/netif/etharp.c

#RFHTBD: Additional files needed by httpserver-sockets demo
ADDITIONAL_FILES=$(LWIPDIR)/api/netifapi.c $(LWIPDIR)/core/dns.c $(LWIPDIR)/core/pbuf.c \
                 $(LWIPDIR)/ipv4/igmp.c

# ARCHFILES: Architecture specific files.
ARCHFILES=$(wildcard $(LWIPARCH)/*.c $(LWIPARCH)/netif/tapif.c $(LWIPARCH)/netif/tunif.c $(LWIPARCH)/netif/unixif.c $(LWIPARCH)/netif/list.c $(LWIPARCH)/netif/tcpdump.c)

# LWIPFILES: All the above.
LWIPFILES=$(COREFILES) $(CORE4FILES) $(APIFILES) $(NETIFFILES) $(ARCHFILES) $(ADDITIONAL_FILES)
LWIPFILESW=$(wildcard $(LWIPFILES))
LWIPOBJS=$(addprefix $(BUILD_DIR), $(notdir $(LWIPFILESW:.c=.o)))

LWIPLIB=liblwip.a

VPATH	= $(sort $(dir $(LWIPFILESW)))

$(BUILD_DIR)%.o: %.c
	@echo "+--$(CC) $<"
	$(Q)$(CC) $(CFLAGS) -o $@ $<

.PHONY: all clean distclean depend

all: $(LWIPLIB) 

clean:
	rm -f $(BUILD_DIR)*.o $(BUILD_DIR)*.d $(LWIPLIB) *.s  $(BUILD_DIR)depend

distclean: clean

depend: $(BUILD_DIR)depend

-include $(BUILD_DIR)depend

$(LWIPLIB): $(LWIPOBJS)
	@echo "+--$(AR) $@"
	$(Q)$(AR) -rc $@ $^

$(BUILD_DIR)depend: $(LWIPFILES)
	@echo "+--Building dependancies."
	$(Q)$(CC) $(CFLAGS) -MM $(addprefix -MT, $(LWIPOBJS)) -E $^ > $@ || rm -f $(BUILD_DIR)depend
