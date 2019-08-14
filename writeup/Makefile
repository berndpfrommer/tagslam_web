
FNAME=tagslam

LATEX=pdflatex -interaction=nonstopmode "\input"
BIBTEX=bibtex
all:	$(FNAME).pdf

# localization_graph.tex calibration_graph.tex
$(FNAME).pdf: $(FNAME).tex sections/*.tex
	$(LATEX)  $(FNAME).tex
	$(BIBTEX) $(FNAME)
	$(LATEX)  $(FNAME).tex
	$(LATEX)  $(FNAME).tex

clean:
	@rm -rf $(FNAME).pdf _region_* *.log *.bbl *.aux *.dvi *.blg *.out
