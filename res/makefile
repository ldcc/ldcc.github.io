TEXS = $(shell find . -name *.tex | sort | uniq)
PDFS = $(TEXS:.tex=.pdf)
SVGS = $(TEXS:.tex=.svg)
CLS = $(TEXS:.tex=.cls)

all: $(CLS) $(PDFS) $(SVGS)

%.svg: %.pdf
	inkscape --without-gui --file $< --export-plain-svg $@
%.pdf: %.tex
	pdflatex -output-directory `dirname $@` $<
%.cls:
	rm -f $*~ $*.log $*.aux $*.bbl $*.blg $*.synctex.gz $*.out $*.toc $*.lof $*.idx $*.ilg $*.ind $*.dvi $*.pdf $*.svg

pos:  syn-jpn/pos.cls   syn-jpn/pos.svg
lan:  la-notes/lan.cls  la-notes/lan.svg
unic: la-notes/unic.cls la-notes/unic.svg