texs=$(wildcard *.tex)
pdfs=$(texs:.tex=.pdf)
svgs=$(pdfs:.pdf=.svg)

all: clean $(pdfs) $(svgs)

%.pdf: %.tex
	pdflatex $<
%.svg: %.pdf
	inkscape --without-gui --file $< --export-plain-svg $@
clean:
	rm -f *~ *.log *.aux *.bbl *.blg *.synctex.gz *.out *.toc *.lof *.idx *.ilg *.ind *.dvi *.pdf *.svg
