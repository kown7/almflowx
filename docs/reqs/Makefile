.PHONY: all
all: artifacts/specification.pdf artifacts/req-graph1.png \
	artifacts/req-graph2.png


# Adding new files (especially requirements) can not automatically
# handled.  The 'force' target tries to handle this.
.PHONY: force
force: 
	rm .rmtoo_dependencies
	${MAKE} all

#
# This is the way the rmtoo must be called.
# (The RMTOO_CALL variable is set in the setenv.sh script)
# You can override the default Config.py file by setting
# the RMTOO_CONFIG variable from the command line.
# ie:
#      make RMTOO_CONFIG=YourConfig.json
#
RMTOO_CALL ?= rmtoo
RMTOO_CONFIG=Config.json
RMTOO_CONTRIB_DIR ?= $(shell rmtoo-contrib-dir)

CALL_RMTOO=${RMTOO_CALL} -j file://${RMTOO_CONFIG}

# Needed for local sty files
export TEXINPUTS := ".:../latex//:${TEXINPUTS}:"

#
# Dependency handling
#  The file .rmtoo_dependencies is created by rmtoo itself.
#
include .rmtoo_dependencies

# And how to make the dependencies
.rmtoo_dependencies:
	${CALL_RMTOO} \
		--create-makefile-dependencies=.rmtoo_dependencies

artifacts/req-graph1.png: artifacts/req-graph1.dot
	unflatten -l 23 artifacts/req-graph1.dot | \
		dot -Tpng -o artifacts/req-graph1.png

artifacts/req-graph2.png: artifacts/req-graph2.dot
	dot -Tpng -o artifacts/req-graph2.png artifacts/req-graph2.dot

# Two calls are needed: one for the requirments converting and one for
# backlog creation.
artifacts/specification.pdf: ${REQS_LATEX2} latex/specification.tex
	(cd artifacts && \
	   gnuplot ${RMTOO_CONTRIB_DIR}/gnuplot_stats_reqs_cnt.inc && \
	   epstopdf stats_reqs_cnt.eps)
	(cd artifacts && \
	   gnuplot ${RMTOO_CONTRIB_DIR}/gnuplot_stats_burndown.inc && \
	   epstopdf stats_burndown.eps)
	(cd artifacts && \
	   gnuplot ${RMTOO_CONTRIB_DIR}/gnuplot_stats_sprint_burndown.inc && \
	   epstopdf stats_sprint_burndown.eps)
	(cd artifacts && pdflatex ../latex/specification.tex; \
		pdflatex ../latex/specification.tex; \
		pdflatex ../latex/specification.tex)

.PHONY: clean
clean:
	rm -fr artifacts/html
	rm -f artifacts/* \
		add_data.py*
	rm -fr debian/rmtoo build
