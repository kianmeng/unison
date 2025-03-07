 # Manual

all:: unison-manual.html

-include ../src/Makefile.ProjectInfo
include ../src/Makefile.OCaml

STRINGS = ../icons/Unison.gif
SOURCES = unison-manual.tex \
          local.tex short.tex postproc$(EXEC_EXT) Makefile

# ifeq ($(shell ls /usr/local/bin/hevea), /usr/local/bin/hevea)
#   HEVEAPATH = /usr/local/bin/hevea
# endif
ifeq ($(shell echo type -t hevea | bash), file)
  HEVEAPATH = $(shell echo type -p hevea | bash)
endif
ifneq ($(HEVEAPATH),)
  HEVEA=true
endif

unison-manual.html: $(SOURCES)
	$(MAKE) real

# If the .aux file is missing, do 'make once' an extra time to make
# sure it's present (with section numbers, etc.) before we build the
# manual for real.
real:
	@(if [ ! -f unison-manual.aux ]; then $(MAKE) once; fi)
	$(MAKE) once

once: postproc$(EXEC_EXT) $(STRINGS) docs$(EXEC_EXT)
	echo '\def\unisonversion{$(VERSION)}' > unisonversion.tex
	@echo HEVEAPATH = $(HEVEAPATH)
	@(if [ ! -f prefs.tmp ]; then $(MAKE) prefs.tmp; fi)
	@(if [ ! -f prefsdocs.tmp ]; then $(MAKE) prefsdocs.tmp; fi)
ifeq ($(HEVEA),true)
	printf '$(TEXDIRECTIVES)\\textversiontrue\\draftfalse' \
           > texdirectives.tex
	latex unison-manual.tex
	hevea unison-manual.tex
	./postproc < unison-manual.html > temp.html
	(TERM=vt100; export TERM; lynx -display_charset=utf8 -dump temp.html > unison-manual.dtxt)
	sed -e "/^----SNIP----/,+2 d" -e "/^Junk/,$$ d" unison-manual.dtxt > unison-manual.txt
	./docs$(EXEC_EXT)
endif
	printf '$(TEXDIRECTIVES)\\textversionfalse\\draftfalse' > texdirectives.tex
	latex unison-manual.tex
	latex unison-manual.tex
	cp unison-manual.dvi temp.dvi
	dvips -t letter -o unison-manual.ps unison-manual.dvi
	dvips -t letter -z -Ppdf -G0 -D600 unison-manual.dvi -o junk.ps
	ps2pdf junk.ps unison-manual.pdf
ifeq ($(HEVEA),true)
	hevea unison-manual.tex
endif

# Listing of preferences
prefs.tmp:
	-(if [ -f ../src/$(NAME)$(EXEC_EXT) ]; then \
           ../src/unison -help > prefs.tmp; \
           fi)
prefsdocs.tmp:
	-(if [ -f ../src/$(NAME)$(EXEC_EXT) ]; then \
           ../src/unison -prefsdocs 2> prefsdocs.tmp; \
           fi)

# Tools
docs$(EXEC_EXT): docs.ml
	$(OCAMLC) -o $@ $^

postproc$(EXEC_EXT) : postproc.ml
	ocamlopt -o $@ $^

%.ml : %.mll
	-$(RM) $@
	ocamllex $<

clean::
	$(RM) -r \
	   *.dtxt *.aux *.haux *.log *.out \
	   texdirectives.tex \
	   junk.* *.htoc *.toc *.bak \
	   docs docs.exe temp.dvi temp.html unison-manual.html \
	   postproc postproc.exe postproc.ml \
	   unison-manual.dvi unison-manual.ps unison-manual.pdf \
	   unison-manual.txt unison-manual.info* unisonversion.tex \
	   contact.html faq.html faq.haux index.html

