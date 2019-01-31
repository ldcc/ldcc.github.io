texs=$(wildcard *.tex)
pdfs=$(texs:.tex=.pdf)

all: clean $(pdfs)

%.pdf: %.tex
	pdflatex $<
clean:
	rm -f *.log *.aux *.bbl *.blg *.synctex.gz *.out *.toc *.lof *.idx *.ilg *.ind *.dvi *.pdf
