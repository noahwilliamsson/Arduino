# Helper file for running the clang static analyzer

# Via http://clang-analyzer.llvm.org/installation
CHECKER_URL = http://clang-analyzer.llvm.org/downloads/checker-276.tar.bz2
CHECKER_DIR = ~/Downloads/checker-276

CC = clang
CXX = clang++

# Fix unknown location of standard headers
CFLAGS += -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.10.sdk
CXXFLAGS += -std=c++11
CXXFLAGS += -stdlib=libstdc++
CFLAGS += -Wall
# Only target the 32-bit ESP8266
CFLAGS += -arch i386
CFLAGS += -Iesp8266/cores/esp8266
CFLAGS += -Iesp8266/tools/sdk/include 
# For pins_arduino.h
CFLAGS += -Iesp8266/variants/nodemcu/
CFLAGS += $(shell find . -type d -name src |sed 's,^,-I,')

# Attempt to compile all .cpp files
SOURCES += $(shell find . -name "*.c" | grep -v wifio)
SOURCES += $(shell find . -name "*.cpp" | grep -v wifio)
OBJS = $(patsubst %.cpp,%.o,$(patsubst %.c,%.o,$(SOURCES)))

%.o: %.cpp
	$(CXX) $(CXXFLAGS) $(CFLAGS) -c -o $@ $<
%.o: %.c
	$(CC) $(CFLAGS) -c -o $@ $<

all: $(CHECKER_DIR) clean run-scan-build

# Make sure the scan-* tools are installed somewhere
$(CHECKER_DIR):
	$(info Installing LLVM clang-analyzer into $(CHECKER_DIR))
	cd $(shell dirname $(CHECKER_DIR)) && curl $(CHECKER_URL) | tar jx

# Run scan-build against the 'build' target
run-scan-build:
	rm -rf /tmp/scan-build-output
	$(CHECKER_DIR)/scan-build -o /tmp/scan-build-output make -i build
	find /tmp/scan-build-output -type d -not -empty -exec $(CHECKER_DIR)/scan-view {} \;

# Build objects (invoked by run-scan-build target)
build: $(OBJS)

# Clean up object files
clean:
	rm -f $(OBJS)
