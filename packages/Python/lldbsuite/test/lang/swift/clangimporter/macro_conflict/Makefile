LEVEL = ../../../../make
SRCDIR := $(shell dirname $(realpath $(lastword $(MAKEFILE_LIST))))
SWIFT_OBJC_INTEROP := 1

# This test builds an Objective-C main program that imports two Swift
# .dylibs with conflicting -D macro definitions.

include $(LEVEL)/Makefile.rules

a.out: main.m libFoo.dylib libBar.dylib
	$(CC) $(CFLAGS) $(MANDATORY_MODULE_BUILD_CFLAGS) -I$(shell pwd) -lFoo -lBar -L$(shell pwd) -o $@ $<
ifneq "$(CODESIGN)" ""
	$(CODESIGN) -s - "$@"
endif


lib%.dylib: %.swift
	$(SWIFTC) -g -Onone $^ -emit-library -module-name $(shell basename $< .swift) -emit-module -Xlinker -install_name -Xlinker @executable_path/$@ -Xcc -I$(SRCDIR) -Xcc -I$(SRCDIR)/$(shell basename $< .swift) $(SWIFTFLAGS) -emit-objc-header-path $(shell basename $< .swift).h -Xcc -DSTRUCTNAME=C$(shell basename $< .swift)
ifneq "$(CODESIGN)" ""
	$(CODESIGN) -s - "$@"
endif

clean::
	rm -rf *.swiftmodule *.swiftdoc *.dSYM *~ lib*.dylib a.out *.o
