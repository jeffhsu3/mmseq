# for static compilation: STATIC=1 make
# for debug: make DEBUG=1

SUFFIX = # set on command line for static compilation
MACROS = -DVERSION=$(shell echo `cat VERSION`)
ifdef DEBUG
  MACROS += -DDEBUG
endif

CC = gcc
CXX = g++
LIBS = -lgsl -lgslcblas -lboost_regex -lbam -lboost_iostreams -lz -llapack -lblas -larmadillo -lgfortran
OMP = -fopenmp
OPT = -O3
OPTSAFE = -O2

ifeq ($(shell echo `uname -s`),Darwin)
#  CLEANSUFFIX = $(shell echo `cat VERSION`-`uname -s`-`ar -x /opt/local/lib/libgsl.a rng.o && file rng.o | sed -E 's/.*\ (.*)/\1/'`)
  CLEANSUFFIX = $(shell echo -`cat VERSION`-`uname -s`-`uname -m`)
endif
ifeq ($(shell echo `uname -s`),Linux)
  CLEANSUFFIX = $(shell echo -`cat VERSION`-`uname -s`-`uname -m`)
endif
ifdef STATIC
  CC += -static-libgcc 
  CXX += -static-libgcc
  SUFFIX = ${CLEANSUFFIX}
  ifeq ($(shell echo `uname -s`),Darwin)
    $(warning "Static compilation for Mac OS X")
# comment out #define ARMA_USE_WRAPPER in /usr/local/include/armadillo_bits/config.hpp
    CXXFLAGS += -framework Accelerate -mmacosx-version-min=10.6
    LIBS = /usr/local/lib/libgsl.a /usr/local/lib/libgslcblas.a /usr/local/lib/libboost_iostreams.a -lz /usr/local/lib/libboost_regex-mt.a /usr/local/lib/libbam.a 
  endif
  ifeq ($(shell echo `uname -s`),Linux)
    $(warning "Static compilation for Linux")
    CXXFLAGS += -static -Wl,--whole-archive -lpthread -Wl,--no-whole-archive
  endif
endif
  

.PHONY : all
all : mmseq bam2hits extract_transcripts hitstools t2g_hits mmcollapse postdiff mmdiff

mmseq : sokal.o uh.o hitsio.o mmseq.cpp
	$(CXX) ${MACROS} $(CXXFLAGS) ${OMP} ${OPT} $^ -o $@${SUFFIX} ${LIBS}

bam2hits : fasta.o hitsio.o bam2hits.cpp
	${CXX} ${MACROS} $(CXXFLAGS) ${OMP} ${OPT} $^ -o $@${SUFFIX} ${LIBS}

extract_transcripts : fasta.o extract_transcripts.cpp
	${CXX} ${MACROS} $(CXXFLAGS) ${OPT} $^ -o $@${SUFFIX}

hitstools: hitsio.o hitstools.cpp
	$(CXX) ${MACROS} $(CXXFLAGS) ${OMP} ${OPT} $^ -o $@${SUFFIX} ${LIBS}

t2g_hits : t2g_hits.cpp
	${CXX} ${MACROS} $(CXXFLAGS) ${OPT} $^ -o $@${SUFFIX}

postdiff : postdiff.cpp
	${CXX} ${MACROS} ${CXXFLAGS} ${OPT} $^ -o $@${SUFFIX}

mmcollapse: uh.o sokal.o mmcollapse.cpp
	${CXX} ${MACROS} ${CXXFLAGS} ${OMP} ${OPT} $^ -o $@${SUFFIX} ${LIBS}

mmdiff: bms.o mmdiff.cpp
	${CXX} ${MACROS} ${CXXFLAGS} ${OMP} ${OPTSAFE} $^ -o $@${SUFFIX} ${LIBS}

sokal.o : sokal.cc sokal.hh
	$(CXX) ${OPT} -c sokal.cc

fasta.o : fasta.c fasta.h

uh.o : uh.cpp uh.hh
	$(CXX) ${OMP} ${OPT} -c uh.cpp 

hitsio.o: hitsio.cpp hitsio.hpp
	$(CXX) ${MACROS} $(CXXFLAGS) ${OMP} ${OPT} $^ -c hitsio.cpp

bms.o: bms.cpp bms.hpp
	$(CXX) ${MACROS} $(CXXFLAGS) ${OMP} ${OPTSAFE} $^ -c bms.cpp

.PHONY : clean

clean :
	rm -f mmseq bam2hits extract_transcripts hitstools t2g_hits mmcollapse postdiff mmseq${CLEANSUFFIX} bam2hits${CLEANSUFFIX} extract_transcripts${CLEANSUFFIX} hitstools${CLEANSUFFIX} t2g_hits${CLEANSUFFIX} mmcollapse${CLEANSUFFIX} postdiff${CLEANSUFFIX} mmdiff${CLEANSUFFIX} *.o