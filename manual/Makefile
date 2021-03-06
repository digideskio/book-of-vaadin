all: images icons

################################################################################
################################################################################

DOCROOT = ..
DOCBOOK = docbook-xsl-1.76.1
MANUALPATH = manual

JAVAPARS = -Xms256M -Xmx512M


# Document language; this is the default, translations can override this.
DBLANGUAGE = en
DBEXTRAPARS = l10n.gentext.language=$(DBLANGUAGE)

PROFILE = profile.condition=print

POCKETBOOK_ROOT = book-pocket.xml

# Command-line parameters for Docbook
DBPARS =	section.autolabel=1 \
			section.label.includes.component.label=1 \
			section.autolabel.max.depth=2 \
			draft.mode='no' \
			double.sided=1 \
			manual.pubdate=${DATE} \
			manual.version=$(VERSION) \
			manual.fonts.custom=1 \
			xep.extensions=1 \
			$(DBEXTRAPARS)

# List of images to be converted from SVG to PNG
IMAGES =	intro/architecture-vaadin7 \
			intro/intro-themes-faded \
			getting-started/toolchain \
			arch/architecture-detailed arch/clientside-arch arch/events-classdiagram arch/java-servlet \
			components/component-diagram components/component-abstractions \
			components/tooltip-plain-withpointer components/tooltip-richtext-withpointer \
			components/field-diagram components/field-interface \
			components/slider-example1 \
			components/textfield-diagram \
			application/application-architecture application/resource_classdiagram \
			application/view-navigation \
			advanced/mvp-pattern \
			advanced/threadlocal-sequentiality advanced/threadlocal-concurrency \
			themes/theme-contents themes/eclipse-theme-created-annotated \
			clientsideapp/clientsideapp-architecture \
			advanced/debug-window-annotated advanced/debug-log \
			designer/designer-view-annotated \
			designer/designer-editor-annotated \
			gwt/gwt-widgets gwt/gwt-widget-files \
			gwt/widget-integration \
			datamodel/datamodel-interfaces \
			mobile/mobile-overview mobile/parking-tabs mobile/navigationview mobile/navigationpanel \
			testbench/webdriver-use \
			testbench/debugwindow-select-annotated \
			testbench/tt-workflow testbench/tt-architecture-simple \
			jpacontainer/detailed-architecture jpacontainer/three-layer-architecture jpacontainer/jpa-mapping-graphic \
			jpacontainer/domain-model \
			portal/liferay-display \
			portal/liferay-ipc-architecture portal/liferay-ipc-demo-annotated \
			portal/wsrp-architecture \
			charts/charts-elements charts/charts-axes \
			addons/widgetset-compiling-toolbar

# Currently unused diagrams:
# intro/architecture book-example datamodel/beanitem-doublebinding
# gwt/gwt-custom-architecture gwt/gwt-hostedmode-project-annotated gwt/simplified-integration
# testbench/tt-junit-workflow vaadin-logo

# List of source XML files
XMLFILES = $(POCKETBOOK_ROOT) book-info.xml book-preface.xml book-vol1.xml book-vol2.xml \
	chapter-addons.xml	chapter-datamodel.xml	chapter-layout.xml	chapter-themes.xml \
	chapter-advanced.xml \
	chapter-application.xml	chapter-getting-started.xml	\
	chapter-architecture.xml	\
	chapter-introduction.xml	chapter-sqlcontainer.xml \
	chapter-components.xml	chapter-jpacontainer.xml \
	chapter-portal.xml \
	chapter-clientside.xml chapter-clientsideapp.xml chapter-clientsidewidgets.xml chapter-gwt.xml \
	chapter-addons.xml chapter-charts.xml chapter-mobile.xml chapter-testbench.xml \
	chapter-vaadin.xml 


# List if icons in img/icons
ICONS =		warning note

# Date format
DATE = `date +'%Y-%m-%d'`

# Determine Vaadin version number
VERSION = `cat $(DOCROOT)/build/VERSION.properties|sed -e 's/^version=\([0-9]\+\.[0-9]\+\).\+/\1/'`

JAVACLASSPATH = $(DOCROOT)/build/lib/saxon.jar:$(DOCROOT)/build/lib/xercesImpl.jar:$(DOCROOT)/build/lib/xml-apis.jar:$(DOCROOT)/build/lib/xslthl.jar

################################################################################
# Image conversions
################################################################################

# SVG source images
SRCIMAGES := $(foreach file, $(IMAGES), original-drawings/$(file).svg)

# Two size of target images
TRGIMAGES_HI := $(foreach file, $(IMAGES), img/$(file)-hi.png)
TRGIMAGES_LO := $(foreach file, $(IMAGES), img/$(file)-lo.png)

images: $(TRGIMAGES_HI) $(TRGIMAGES_LO) FORCE

$(TRGIMAGES_HI): img/%-hi.png: original-drawings/%.svg
	inkscape --export-png $@ --export-dpi=300 --export-area-drawing $<

$(TRGIMAGES_LO): img/%-lo.png: original-drawings/%.svg
	inkscape --export-png $@ --export-dpi=90 --export-area-drawing $<


################################################################################
# Icons
################################################################################

SRCICONS := $(foreach file, $(ICONS), original-drawings/icons/$(file).svg)
TRGICONS := $(foreach file, $(ICONS), img/icons/$(file).png)

icons: $(TRGICONS) FORCE

$(TRGICONS): img/icons/%.png: original-drawings/icons/%.svg
	inkscape --export-png $@ --export-dpi=150 --export-area-drawing $<

################################################################################
# Docbook processing
################################################################################
# Main target: book.pdf
# Parameters:
#     A4: Make A4 page size. 
#         $ make book.pdf A4=1

DBROOT = $(DOCROOT)/build/docbook
DOCBOOK = $(DOCROOT)/build/docbook/conf/custom-fo-docbook.xsl
DOCBOOK_POCKET = $(DOCROOT)/build/docbook/conf/custom-fo-docbook-pocket.xsl
#HIGHLIGHTER_CONFIG = -Dxslthl.config=$(PWD)/../build/docbook/xsl/highlighting/xslthl-config.xml

# The highlighter configuration is included in the DocBook package.
# It must be given as an absolute URL.
HIGHLIGHTER_CONFIG = -Dxslthl.config="file://$(PWD)/../build/docbook/xsl/highlighting/xslthl-config.xml"

# Create profiled print book with a profiling pass
book-print.xml: $(POCKETBOOK_ROOT) $(XMLFILES) images
	java $(JAVAPARS) -classpath $(JAVACLASSPATH) com.icl.saxon.StyleSheet -o book-print.xml \
		 $(POCKETBOOK_ROOT) $(DBROOT)/xsl/profiling/profile.xsl $(PROFILE)

# Process Docbook to FO
book.fo: book.xml $(XMLFILES) images
	@echo $(VERSION)
	@echo "Highlighter configuration: $(HIGHLIGHTER_CONFIG)"
	java $(JAVAPARS) -classpath $(JAVACLASSPATH) $(HIGHLIGHTER_CONFIG) com.icl.saxon.StyleSheet -o book.fo \
		 book.xml $(DOCBOOK) $(DBPARS)
# ../build/lib/xalan.jar ... org.apache.xalan.xslt.Process

# Use book-pocket.xml for two-volume version
book-pocket.fo: book-print.xml images
	@echo $(VERSION)
	@echo "Highlighter configuration: $(HIGHLIGHTER_CONFIG)"
	java $(JAVAPARS) -classpath $(JAVACLASSPATH) $(HIGHLIGHTER_CONFIG) com.icl.saxon.StyleSheet -o book-pocket.fo \
		 book-print.xml $(DOCBOOK_POCKET) $(DBPARS)

XEPPARS = -Dcom.renderx.xep.CONFIG=build/lib/XEP/xep-vaadinfonts.xml -quiet

# Run XEP FO processor to convert FO to PDF.
book.pdf: book.fo
	cd $(DOCROOT) ; /opt/RenderX/XEP/xep $(XEPPARS) <$(MANUALPATH)/book.fo >$(MANUALPATH)/book.pdf
	rm book.fo
#	cp book.pdf /var/www/book.pdf

book-pocket.pdf: book-pocket.fo
	cd $(DOCROOT) ; /opt/RenderX/XEP/xep $(XEPPARS) <manual/book-pocket.fo >manual/book-pocket.pdf
	rm book-pocket.fo
#	cp book-pocket.pdf /var/www/book-pocket.pdf

# View the result
viewbook: book.pdf FORCE
	kpdf book.pdf

# Grayscaled print edition
book-pocket-grayscale.pdf: book-pocket.pdf
	gs -sOutputFile=book-pocket-grayscale.pdf -sDEVICE=pdfwrite   -sColorConversionStrategy=Gray -dProcessColorModel=/DeviceGray -dCompatibilityLevel=1.4 book-pocket.pdf < /dev/null

book-of-vaadin-vol1.pdf: book-pocket.pdf
	pdftk A=$< cat A1-336 output $@

book-of-vaadin-vol2.pdf: book-pocket-grayscale.pdf
	pdftk A=$< cat A337-end output $@

volumes: book-of-vaadin-vol1.pdf book-of-vaadin-vol2.pdf

################################################################################
# Internationalization
################################################################################

i18n: pot

POTFILES := $(foreach file, $(XMLFILES), $(patsubst %.xml, %.pot, translations/pot/$(file)))
POTEXPORTS := $(foreach file, $(XMLFILES), $(patsubst %.xml, %.po, translations/pot-exports/$(file)))

pot: $(POTFILES) init-pot-exports $(POTEXPORTS)

# Note that we have to use a modified version of xmlpo (sub-package in kdesdk) to
# make it process <book-example> tags correctly.
# 
# 1. As root, get the build-deps and sources for the kdesdk:
#    # apt-get build-dep poxml
#    # apt-get source poxml
#    # cd kdesdk-x.x.x
#
# 2. Modify the poxml/parser.cpp by adding the following tags in the
#    "cuttingtags" array:
#        "book-example",
#        "info", "titleimage", "websitenotice", "publisher", "productname",
#        "edition", "printer", "biblioid", "copyright", "legalnotice", "pubdate",
#        "holder", "orgname", "address",
#
# 3. Build the custom package:
#    # dpkg-buildpackage -us -uc -nc
#
# 4. Install the custom package with:
#    # dpkg -i ../poxml-x.x.x.deb
translations/pot/%.pot: %.xml FORCE
	xml2pot $< > $@
#	xml2pot $< | msgcat - --no-wrap > $@

init-pot-exports:
	mkdir -p translations/pot-exports

translations/pot-exports/%.po: translations/pot/%.pot FORCE
	cp $< $@
#	curl --form file=@$@ --user $(GLUSER):$(GLPASSWORD) https://api.getlocalization.com/bookofvaadin7/api/update-master/

i18n-clean: FORCE
	rm -f translations/pot/*
	rm -f translations/pot-exports/*
	rmdir translations/pot-exports

################################################################################
# Compile localizations
################################################################################

l10n: fi_FI

FI_TRANSLATED := book.po

################################################################################
# Chapter Extraction
################################################################################

vaadin-charts.pdf: book.pdf
	pdftk A=book.pdf cat A439-474 output vaadin-charts.pdf

publish-charts: vaadin-charts.pdf
	scp vaadin-charts.pdf vaadin@vaadin.com:download/book-of-vaadin/vaadin-charts/pdf/
	scp book.pdf vaadin@vaadin.com:download/book-of-vaadin/vaadin-charts/pdf/book-of-vaadin.pdf

################################################################################
# Conversion to AsciiDoc
################################################################################

ASCIIDOC_REPO = ../../vaadin-doc

# This uses docbookrx, a Ruby converter. It doesn't chunk, and has other issues.
docbookrx:
	ruby ../work/docbookrx/docbookrx.rb book-print.xml > ../work/asciidoc/book-print.ad
	asciidoctor ../work/asciidoc/book-print.ad

# Where the d2a was cloned to
DOCBOOK2ASCIIDOC = ../work/docbook2asciidoc

d2a:
	java -jar saxon9he.jar \
         -s:book.xml -o:$(ASCIIDOC_REPO)/book.asc \
         $(DOCBOOK2ASCIIDOC)/bov-to-asciidoc-d2a.xsl chunk-output=true chunk-sections=true

asciidoc:
	cp -r -p img $(ASCIIDOC_REPO)/
	cd $(ASCIIDOC_REPO) ; make chunked
#cd $(ASCIIDOC_REPO) ; asciidoctor book-of-vaadin.asc

# Build book generated from the AsciiDoc
book-ad.fo: book-of-vaadin.xml $(XMLFILES)
	@echo $(VERSION)
	java $(JAVAPARS) -classpath $(JAVACLASSPATH) $(HIGHLIGHTER_CONFIG) com.icl.saxon.StyleSheet -o book-ad.fo \
		 book-of-vaadin.xml $(DOCBOOK_POCKET) $(DBPARS)

book-ad.pdf: book-ad.fo
	cd $(DOCROOT) ; /opt/RenderX/XEP/xep $(XEPPARS) <manual/book-ad.fo >manual/book-ad.pdf
	rm book-ad.fo

################################################################################
# Misc
################################################################################

FORCE:
