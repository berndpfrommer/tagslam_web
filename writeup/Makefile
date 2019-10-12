
FNAME=root

LATEX=pdflatex -interaction=nonstopmode "\input"
BIBTEX=bibtex
all:	$(FNAME).pdf

zip: $(FNAME).zip

$(FNAME).zip: sections/*.tex *.tex *.bib *.cls *.bst *.png *.sty *.bbl
	zip -r $(FNAME).zip sections *.tex *.bib *.cls *.bst *.png *.sty *.bbl

# localization_graph.tex calibration_graph.tex
$(FNAME).pdf: $(FNAME).tex sections/*.tex $(FNAME).bib *.bib
	$(LATEX)  $(FNAME).tex
	$(BIBTEX) $(FNAME)
	$(LATEX)  $(FNAME).tex
	$(LATEX)  $(FNAME).tex

clean:
	@rm -rf $(FNAME).pdf _region_* *.log *.bbl *.aux *.dvi *.blg *.out
