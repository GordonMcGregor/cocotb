include $(SIM_ROOT)/makefiles/Makefile.pylib
include $(SIM_ROOT)/makefiles/Makefile.inc

PYTHON_LIBDIR ?= /usr/lib64
SWIG ?= swig
CC=gcc
ifeq ($(ARCH),i686)
	CC += -m32
endif

.PHONY: all
all: io_module.so _hal.so

io_module.o: io.c io_module.h io.h
	$(CC) $(GCC_FLAGS) -pthread -fno-strict-aliasing -O2 -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 \
	-fexceptions --param=ssp-buffer-size=4 -D_GNU_SOURCE \
	-fwrapv -DNDEBUG -I$(PYTHON_INCLUDEDIR) -c $< -o $@

io_module.so: io_module.o
	$(CC) -pthread -shared  $< -L$(PYTHON_LIBDIR) $(PYTHON_LD_FLAGS) -o $@

_hal.so: ../hal/endian_swapper_hal.c endian_swapper_hal_wrap.c io_module.so
	$(CC) -g -ldl -shared -fPIC -I$(shell pwd) -I$(PYTHON_INCLUDEDIR) -I../hal ../hal/endian_swapper_hal.c endian_swapper_hal_wrap.c io_module.so -o $@

endian_swapper_hal_wrap.c: ../hal/endian_swapper_hal.h
	$(SWIG) -python -outcurrentdir ../hal/endian_swapper_hal.h

.PHONY: clean
clean:
	-rm -rf hal.py*
	-rm -rf *.so
	-rm -rf *.o
	-rm -rf endian_swapper_hal_wrap.c
