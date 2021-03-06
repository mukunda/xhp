ifdef DEBUG
CPPFLAGS = -fPIC -ggdb -Wall -DDEBUG
else
CPPFLAGS = -fPIC -g -Wall -O3 -minline-all-stringops
endif

ifdef PROFILE
CPPFLAGS += -pg
endif

all: libxhp.a libxhp.so

clean:
	-rm libxhp.a xhpize parser.yacc.cpp scanner.lex.cpp scanner.lex.hpp parser.yacc.output parser.yacc.hpp fastpath.cpp *.o 2>/dev/null

parser.yacc.cpp: parser.y
	bison --debug --verbose -d -o $@ $<

parser.yacc.hpp: parser.yacc.cpp

scanner.lex.cpp: scanner.l
	`which flex35 2>/dev/null || which flex 2>/dev/null` \
	  -C --header-file=scanner.lex.hpp -o $@ -d $<

scanner.lex.hpp: scanner.lex.cpp

fastpath.cpp: fastpath.re
	re2c -c -o $@ $<

%.o: %.cpp
	$(CXX) -c $(CPPFLAGS) -o $@ $<

xhp_preprocess.o: xhp_preprocess.cpp scanner.lex.hpp parser.yacc.hpp
parser.yacc.o: scanner.lex.hpp
scanner.lex.o: parser.yacc.hpp

libxhp.a: code_rope.o scanner.lex.o parser.yacc.o fastpath.o xhp_preprocess.o
	$(AR) -crs $@ $^

libxhp.so: code_rope.o scanner.lex.o parser.yacc.o fastpath.o xhp_preprocess.o
	g++ -shared -Wl,-soname,libxhp.so -o libxhp.so $^

xhpize: xhpize.cpp libxhp.a
	$(CXX) $(CPPFLAGS) -o $@ $^

.PHONY: all clean tags
