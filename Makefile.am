# dumb-alloc: basic OO memory allocator with malloc/free wrappers
# Copyright (C) 2012, 2017, 2020 Eric Herman <eric@freesa.org>
#
# This library is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public
# License as published by the Free Software Foundation; either
# version 2.1 of the License, or (at your option) any later version.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
# Lesser General Public License for more details.

lib_LTLIBRARIES=libdumb-alloc.la

ACLOCAL_AMFLAGS=-I m4 --install

include_HEADERS=src/dumb-alloc.h

libdumb_alloc_la_SOURCES=$(include_HEADERS) src/dumb-alloc.c

TESTS=$(check_PROGRAMS)
check_PROGRAMS=\
 test_simple \
 test_two_alloc \
 test_pool \
 test_free \
 test_checkered_alloc \
 test_checkered_realloc \
 test_to_string \
 test_big_allocs \
 test_os \
 test_out_of_memory

common_test_sources=$(include_HEADERS) \
		    tests/dumb-alloc-test.h \
		    tests/dumb-alloc-test.c

test_simple_SOURCES=$(common_test_sources) tests/test_simple.c
test_simple_LDADD=libdumb-alloc.la

test_os_SOURCES=$(common_test_sources) tests/test_os.c
test_os_LDADD=libdumb-alloc.la

test_two_alloc_SOURCES=$(common_test_sources) tests/test_two_alloc.c
test_two_alloc_LDADD=libdumb-alloc.la

test_big_allocs_SOURCES=$(common_test_sources) tests/test_big_allocs.c
test_big_allocs_LDADD=libdumb-alloc.la

test_free_SOURCES=$(common_test_sources) tests/test_free.c
test_free_LDADD=libdumb-alloc.la

test_out_of_memory_SOURCES=$(common_test_sources) tests/test_out_of_memory.c
test_out_of_memory_LDADD=libdumb-alloc.la

test_checkered_alloc_SOURCES=$(common_test_sources) \
 tests/test_checkered_alloc.c
test_checkered_alloc_LDADD=libdumb-alloc.la

test_checkered_realloc_SOURCES=$(common_test_sources) \
 tests/test_checkered_realloc.c
test_checkered_realloc_LDADD=libdumb-alloc.la

test_to_string_SOURCES=$(common_test_sources) tests/test_to_string.c
test_to_string_LDADD=libdumb-alloc.la
test_to_string_CFLAGS=$(AM_CFLAGS) -D_GNU_SOURCE

test_pool_SOURCES=$(common_test_sources) tests/test_pool.c
test_pool_LDADD=libdumb-alloc.la
test_pool_CFLAGS=$(AM_CFLAGS) -D_GNU_SOURCE

# would rather do "ifeq $(DEBUG)" but that doesn't work with automake :-(
# instead use "./configure --enable-debug"
if DEBUG
BUILD_CFLAGS=-O0 -ggdb -DDUMB_ALLOC_DEBUG=1 \
	-fsanitize=address,leak,undefined \
	-fno-inline-small-functions \
	-fkeep-inline-functions \
	-fkeep-static-functions \
	--coverage
else
BUILD_CFLAGS=-O2 -ggdb -DNDEBUG -fomit-frame-pointer
endif

# would rather do "ifeq $(FAUXFREESTANDING)"
# instead use "./configure --enable-fauxfreestanding"
if FAUXFREESTANDING
BUILD_ENV_CFLAGS=-DFAUXFREESTANDING=1 -DDUMB_ALLOC_HOSTED=0
else
BUILD_ENV_CFLAGS=
endif

# I wonder if we need -fno-strict-aliasing ....
CSTD_CFLAGS=-std=c89 -pedantic
NOISY_CFLAGS=-Wall -Wextra -Wpedantic -Werror
BASE_CFLAGS=-pipe -fdata-sections -I./src

AM_CFLAGS=\
 $(CSTD_CFLAGS) \
 $(NOISY_CFLAGS) \
 $(BUILD_CFLAGS) \
 $(BUILD_ENV_CFLAGS) \
 $(BASE_CFLAGS)

# extracted from https://github.com/torvalds/linux/blob/master/scripts/Lindent
LINDENT=indent -npro -kr -i8 -ts8 -sob -l80 -ss -ncs -cp1 -il0

tidy:
	$(LINDENT) \
		-T FILE \
		-T size_t \
		-T dumb_alloc \
		-T dumb_alloc_block \
		-T dumb_alloc_char_buf \
		-T dumb_alloc_chunk \
		-T dumb_alloc_data \
		-T dumb_alloc_log \
		-T tracking_mem_context_s \
		`find . -type f -name '*.h' -o -name '*.c' -o -name '*.ino'`

spotless:
	rm -rf `cat .gitignore | sed -e 's/#.*//'`
	pushd src && rm -rf `cat ../.gitignore | sed -e 's/#.*//'` && popd
	pushd tests && rm -rf `cat ../.gitignore | sed -e 's/#.*//'` && popd

valgrind: $(check_PROGRAMS)
	./libtool --mode=execute valgrind -q ./test_simple
	./libtool --mode=execute valgrind -q ./test_two_alloc
	./libtool --mode=execute valgrind -q ./test_pool
	./libtool --mode=execute valgrind -q ./test_free
	./libtool --mode=execute valgrind -q ./test_out_of_memory
	./libtool --mode=execute valgrind -q ./test_checkered_alloc
	./libtool --mode=execute valgrind -q ./test_checkered_realloc
	./libtool --mode=execute valgrind -q ./test_to_string
	./libtool --mode=execute valgrind -q ./test_os
	./libtool --mode=execute valgrind -q ./test_big_allocs
