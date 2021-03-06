DIST_SUBDIRS = lws-term
EXTRA_DIST = \
  hlib/terminal.js $(MISC_COPY_TO_JAR) $(JS_LIBS) $(CLIENT_DATA_FILES) \
  $(domterm_jar_sources) domterm-jar-manifest bin/jdomterm domterm.spec \
  domterm.desktop qtdomterm.desktop domterm.appdata.xml qtdomterm.appdata.xml \
  electron/main.js electron/preload.js  \
  doc/DomTerm.texi doc/webdoc.patch \
  doc/style/domterm.xsl doc/style/docbook-to-html.xsl \
  doc/domterm2.ico \
  doc/help/domterm.adoc \
  doc/help/domterm-attach.adoc \
  doc/help/domterm-browse.adoc \
  doc/help/domterm-hcat.adoc \
  doc/help/domterm-imgcat.adoc \
  doc/help/domterm-is-domterm.adoc \
  doc/help/domterm-list.adoc \
  doc/help/domterm-new.adoc \
  doc/help/domterm-window-specifier.adoc \
  doc/help/qtdomterm.adoc \
  $(DOC_IMAGES) $(DOC_TOOLS) \
  qtdomterm/LICENSE qtdomterm/qtdomterm.pro qtdomterm/processoptions.h \
  qtdomterm/backend.cpp qtdomterm/backend.h \
  qtdomterm/browserapplication.cpp qtdomterm/browserapplication.h \
  qtdomterm/browsermainwindow.cpp qtdomterm/browsermainwindow.h \
  qtdomterm/modelmenu.cpp qtdomterm/modelmenu.h \
  qtdomterm/savepagedialog.cpp qtdomterm/savepagedialog.h \
  qtdomterm/webview.cpp qtdomterm/webview.h \
  qtdomterm/main.cpp qtdomterm/savepagedialog.ui \
  native/preloads.cc \
  native/pty/apue.h native/pty/error.c native/pty/pty.c \
  native/pty/pty_fork.c native/pty/pty_fork.h

SUBDIRS = .
JAVA_HOME = @JAVA_HOME@
SED = @SED@
JAVA = java
JAVAC = javac
LN_S = ln -sf
JAVA_INCLUDE_SUBDIR = @JAVA_INCLUDE_SUBDIR@
JAVAC_WITH_PATH = PATH=$(JAVA_HOME)/bin:$(PATH) $(JAVAC) $(JAVAFX_MODULE_FLAGS)
JAVA_WITH_PATH = PATH=$(JAVA_HOME)/bin:$(PATH) $(JAVA)
CLIENT_DATA_DIR = .
#CLIENT_DATA_DIR = @datadir_relative@/domterm
HTML_DIR = web

if WITH_JAVA
if ENABLE_JAVA_PTY
LIBPTY = lib/libdomterm-pty@LIBEXT@
endif
endif
if WITH_QTWEBENGINE
QTDOMTERM = bin/qtdomterm
endif
domterm_jar_dir = share/domterm

if WITH_JAVA
DOMTERM_JAR = $(domterm_jar_dir)/domterm.jar
if WITH_JAVA_WEBSOCKET
WSDOMTERM_JAR = $(domterm_jar_dir)/wsdomterm.jar
endif
else
if ENABLE_COMPILED_IN_RESOURCES
else
DOMTERM_JAR = $(domterm_jar_dir)/domterm.jar
endif
endif
RESOURCE_DEPS = $(DOMTERM_JAR)
if ENABLE_COMPILED_IN_RESOURCES
if COMBINE_RESOURCES
RESOURCE_DEPS += hlib/dt-combined.js hlib/dt-outer.js
endif
endif
DT_CFLAGS =
if ENABLE_LD_PRELOAD
PRELOADS_SO = lib/domterm-preloads$(LIBEXT)
DT_CFLAGS += -DENABLE_LD_PRELOAD
endif
if WITH_LIBWEBSOCKETS
SUBDIRS += lws-term
endif

if WITH_ASCIIDOCTOR
man1_MANS = share/man/man1/domterm.1
if WITH_QTWEBENGINE
man1_MANS += share/man/man1/qtdomterm.1
endif

ADOC_GENERATED = \
  share/domterm/help/domterm.html \
  share/domterm/help/qtdomterm.html \
  share/domterm/help/domterm-window-specifier.html \
  share/domterm/help/domterm-new.html \
  share/domterm/help/domterm-attach.html \
  share/domterm/help/domterm-browse.html \
  share/domterm/help/domterm-hcat.html \
  share/domterm/help/domterm-imgcat.html \
  share/domterm/help/domterm-is-domterm.html \
  share/domterm/help/domterm-list.html \
  $(man1_MANS)
endif

all-am: client-data-links.stamp $(RESOURCE_DEPS) $(WSDOMTERM_JAR) $(LIBPTY) $(PRELOADS_SO) $(QTDOMTERM) bin/domterm$(EXEEXT) share/domterm/electron/main.js share/domterm/electron/preload.js share/domterm/domterm2.ico $(ADOC_GENERATED) \
  share/domterm/help/domterm.txt \
  share/domterm/help/qtdomterm.txt \
  share/domterm/help/domterm-window-specifier.txt  \
  share/domterm/help/domterm-new.txt  \
  share/domterm/help/domterm-attach.txt  \
  share/domterm/help/domterm-browse.txt  \
  share/domterm/help/domterm-hcat.txt \
  share/domterm/help/domterm-imgcat.txt \
  share/domterm/help/domterm-is-domterm.txt \
  share/domterm/help/domterm-list.txt

share/domterm/domterm2.ico: doc/domterm2.ico
	cp $< $@

# On WSL (Windows Subsystem for Linux) 'electron' is a Windows executable,
# so main.js must be copied, not linked:
share/domterm/electron/main.js: electron/main.js
	cp $< $@
share/domterm/electron/preload.js: electron/preload.js
	cp $< $@

bin/domterm$(EXEEXT):
	cd lws-term && make ../bin/domterm$(EXEEXT)

native/pty/org_domterm_pty_PTY.h: $(DOMTERM_JAR)
	touch native/pty/org_domterm_pty_PTY.h

lib/domterm-preloads$(LIBEXT): native/preloads.cc
	$(MKDIR_P) lib
	$(CXX) $(DT_CFLAGS) -Wall -fPIC -shared -o $@ native/preloads.cc

PTY_COMMON_PARAMS = -fno-strict-aliasing -fPIC -W -Wall  -Wno-unused -Wno-parentheses -fno-omit-frame-pointer

native/pty/pty.o native/pty/pty_fork.o: native/pty/org_domterm_pty_PTY.h

.c.o:
	$(CC) -O2 -ffast-math $(PTY_COMMON_PARAMS) -I$(srcdir)/native/pty -I$(JAVA_HOME)/include -I$(JAVA_HOME)/include/$(JAVA_INCLUDE_SUBDIR) $(DT_CFLAGS) -c $< -o $@

$(LIBPTY): native/pty/pty.o native/pty/pty_fork.o native/pty/error.o
	$(MKDIR_P) lib
	$(CC) $(PTY_COMMON_PARAMS) -shared -o $@ $^

d/domterm: d/domterm.ti
	tic -o. $<

#JS_LIBS = hlib/FileSaver.js hlib/ResizeSensor.js hlib/wcwidth.js hlib/browserkeymap.js

CLIENT_DATA_FILES = repl-client.html $(HLIB_FIXED_FILES)
if COMBINE_RESOURCES
HLIB_DATA_FILES =  hlib/dt-combined.js hlib/dt-outer.js $(HLIB_FIXED_FILES_MISC) hlib/domterm-version.js
else
HLIB_DATA_FILES = $(HLIB_FIXED_FILES_INNER) $(HLIB_FIXED_FILES_OUTER) $(HLIB_FIXED_FILES_MISC) hlib/domterm-version.js
endif

jfx-term.html: repl-client.html.in
if COMBINE_RESOURCES
	sed <$< \
	  -e '/#ifnot COMBINE_RESOURCES/,/#endif/d' \
	  -e '/#ifnot FOR_JAVAFX/,/#endif/d' -e '/#if FOR_JAVAFX/d' \
	  -e '/#if COMBINE_RESOURCES/d' -e '/#endif/d' >$@
else
	sed <$< \
	  -e '/#if COMBINE_RESOURCES/,/#endif/d' \
	  -e '/#ifnot FOR_JAVAFX/,/#endif/d' -e '/#if FOR_JAVAFX/d' \
	  -e '/#ifnot COMBINE_RESOURCES/d' -e '/#endif/d' >$@
endif

repl-client.html: repl-client.html.in
if COMBINE_RESOURCES
	sed <$< \
	  -e '/#ifnot COMBINE_RESOURCES/,/#endif/d' \
	  -e '/#if FOR_JAVAFX/,/#endif/d' -e '/#ifnot FOR_JAVAFX/d' \
	  -e '/#if COMBINE_RESOURCES/d' -e '/#endif/d' >$@
else
	sed <$< \
	  -e '/#if COMBINE_RESOURCES/,/#endif/d' \
	  -e '/#if FOR_JAVAFX/,/#endif/d' -e '/#ifnot FOR_JAVAFX/d' \
	  -e '/#ifnot COMBINE_RESOURCES/d' -e '/#endif/d' >$@
endif

client-data-links.stamp:
	if test "$(srcdir)" != "."; then \
	  cd $(CLIENT_DATA_DIR); \
	  for file in $(CLIENT_DATA_FILES); do \
	      $(LN_S) $(abs_srcdir)/$$file $$file; done; \
	fi
	touch client-data-links.stamp

COMBINED_JSFILES = $(HLIB_FIXED_FILES_INNER)
COMBINED_PATCH_FILE = hlib/dt-combined.patch
tmp-dt-combined.js: $(COMBINED_JSFILES) Makefile.am
	cat $(COMBINED_JSFILES) | \
	  sed -e 's|^export  *\([a-zA-Z]\)|/*export*/ \1|' \
	    -e 's|^export {|//\0|' -e 's|^import {|//\0|' \
	  >tmp-dt-combined.js
hlib/dt-combined.js: tmp-dt-combined.js $(COMBINED_PATCH_FILE)
	patch tmp-dt-combined.js $(COMBINED_PATCH_FILE) -o tmp-dt-patched.js
if WITH_CLOSURE_COMPILER
	@CLOSURE_COMPILER@ --language_in=ES6 --language_out=ES5 --js tmp-dt-patched.js >hlib/dt-combined.js
else
	mv tmp-dt-patched.js hlib/dt-combined.js
endif

OUTER_JSFILES = $(HLIB_FIXED_FILES_OUTER)
#OUTER_PATCH_FILE = hlib/dt-outer.patch
tmp-dt-outer.js: $(OUTER_JSFILES) Makefile.am
	cat $(OUTER_JSFILES) | \
	  sed -e 's|^export  *\([a-zA-Z]\)|/*export*/ \1|' \
	    -e 's|^export {|//\0|' -e 's|^import {|//\0|' \
	  >tmp-dt-outer.js
hlib/dt-outer.js: tmp-dt-outer.js $(OUTER_PATCH_FILE)
#	patch tmp-dt-outer.js $(OUTER_PATCH_FILE) -o tmp-outer-patched.js
	cp tmp-dt-outer.js tmp-outer-patched.js
if WITH_CLOSURE_COMPILER
	@CLOSURE_COMPILER@ --language_in=ES6 --language_out=ES5 --js tmp-outer-patched.js >hlib/dt-outer.js
else
	mv tmp-dt-patched.js hlib/dt-outer.js
endif

#$(CLIENT_DATA_DIR)/hlib/domterm-all.js: hlib/terminal.js $(JS_LIBS)
#	$(MKDIR_P) $(CLIENT_DATA_DIR)/hlib
#if WITH_CLOSURE_COMPILER
# ADVANCED_OPTIMIZATION doesn't quite work, even with closure-externs.js.
# There are some problem - and qtdomterm doesn't work at all. FIXME
#	@CLOSURE_COMPILER@ --js hlib/terminal.js --js hlib/closure-externs.js --compilation_level ADVANCED_OPTIMIZATIONS >tmp-domterm-all.js
#	@CLOSURE_COMPILER@ --js $(JS_LIBS) >>tmp-domterm-all.js
#	(cd $(srcdir) && @CLOSURE_COMPILER@ --language_in=ES6 --language_out=ES5 --js hlib/terminal.js --js $(JS_LIBS)) >tmp-domterm-all.js
#else
#	(cd $(srcdir) && cat $(JS_LIBS) hlib/terminal.js) > tmp-domterm-all.js
#endif
#	mv tmp-domterm-all.js $(CLIENT_DATA_DIR)/hlib/domterm-all.js

EXTRA_CLASSPATH =
SERVER_ARGS =
run-server: libpty@LIBEXT@ d/domterm $(DOMTERM_JAR)
	$(JAVA) -cp $(EXTRA_CLASSPATH)@CLASSPATH_SEPARATOR@domterm.jar@CLASSPATH_SEPARATOR@@conf_classpath@ -Djava.library.path=`pwd` org.domterm.websocket.DomServer $(SERVER_ARGS)

run-shell: $(DOMTERM_JAR)
	CLASSPATH=$(DOMTERM_JAR) $(JAVA_WITH_PATH) org.domterm.javafx.RunProcess

clean-am:
	-rm -rf *.stamp tmp-* org/domterm/*.class org/domterm/*/*.class websocketterm/*.class libpty@LIBEXT@ build doc/DomTerm.xml web/*.html $(DOMTERM_JAR) $(WSDOMTERM_JAR) native/pty/*.o native/pty/org_domterm_pty_PTY.h lib/*$(LIBEXT)
	-rm -rf hlib/dt-combined.js hlib/dt-outer.js repl-client.html jfx-term.html \
	  web/tools web/images \
	  share/domterm/electron/*.js share/domterm/help \
	  share/man/man1/domterm.1 share/man/man1/qtdomterm.1
	if test -x "@QMAKE@"; then cd qtdomterm && $(MAKE) clean; fi
	rm -rf bin/qtdomterm

websocket_sources = org/domterm/websocket/DomServer.java
pty_sources = org/domterm/pty/PtyBackend.java org/domterm/pty/PTY.java
javafx_sources =\
    org/domterm/javafx/WebTerminalApp.java \
    org/domterm/javafx/RunClass.java \
    org/domterm/javafx/RunProcess.java \
    org/domterm/javafx/WebWriter.java \
    org/domterm/javafx/WebTerminal.java \
    org/domterm/javafx/Main.java
javafx_pty_sources = org/domterm/pty/RunPty.java

base_domterm_jar_sources = \
  org/domterm/Backend.java \
  org/domterm/DomHttpServer.java \
  org/domterm/ClassBackend.java \
  org/domterm/ProcessBackend.java \
  org/domterm/util/DomTermErrorStream.java \
  org/domterm/util/DomTermErrorWriter.java \
  org/domterm/util/PrintNested.java \
  org/domterm/util/StringBufferedWriter.java \
  org/domterm/util/StyleSheets.java \
  org/domterm/util/Util.java \
  org/domterm/util/WTDebug.java \
  org/domterm/util/Utf8WriterOutputStream.java
domterm_jar_sources = $(base_domterm_jar_sources)
wsdomterm_jar_sources =
if WITH_JAVA_WEBSOCKET
  wsdomterm_jar_sources += $(websocket_sources)
endif
if ENABLE_JAVA_PTY
  domterm_jar_sources += $(pty_sources)
endif
if WITH_JAVAFX
  domterm_jar_sources += $(javafx_sources)
if ENABLE_JAVA_PTY
  domterm_jar_sources += $(javafx_pty_sources)
endif
endif
dist_java_sources = $(base_domterm_jar_sources) $(pty_sources) \
  $(javafx_sources) $(javafx_pty_sources)

classes.stamp: $(domterm_jar_sources)
if WITH_JAVA
	$(JAVAC_WITH_PATH) -d . -cp .@CLASSPATH_SEPARATOR@@conf_classpath@ -h native/pty $?
endif
	touch classes.stamp

wsclasses.stamp: $(wsdomterm_jar_sources)
	$(JAVAC_WITH_PATH) -d . -cp .@CLASSPATH_SEPARATOR@@conf_classpath@ $?
	touch wsclasses.stamp

MISC_COPY_TO_JAR = repl-client.html jfx-term.html $(HLIB_DATA_FILES)

if WITH_JAVA
PLAIN_COPY_TO_JAR = $(MISC_COPY_TO_JAR) \
  org/domterm/*.class org/domterm/javafx/*.class org/domterm/util/*.class
if ENABLE_JAVA_PTY
PLAIN_COPY_TO_JAR += org/domterm/pty/*.class
endif
else
PLAIN_COPY_TO_JAR = $(MISC_COPY_TO_JAR) hlib/domterm-*.js hlib/*.css
endif

SIXEL_SRC_PATH = $$HOME/Software/node-sixel/src
recompile-sixel:
	tsc -t es2017 -m es6 --removeComments --outDir hlib/sixel $(SIXEL_SRC_PATH)/SixelDecoder.ts  $(SIXEL_SRC_PATH)/Colors.ts
	sed "/import .* from/s|'[.]/Colors'|'./Colors.js'|" <hlib/sixel/SixelDecoder.js >hlib/sixel/SD-tmp
	mv hlib/sixel/SD-tmp hlib/sixel/SixelDecoder.js
	rm hlib/sixel/Types.js

$(DOMTERM_JAR): classes.stamp $(MISC_COPY_TO_JAR)
	$(MKDIR_P) $(domterm_jar_dir) tmp-for-jar
	tar cf - $(PLAIN_COPY_TO_JAR) | (cd tmp-for-jar; tar xf -)
if WITH_XTERMJS
	cp $(XTERMJS_PATH)/xterm.js \
	  $(XTERMJS_PATH)/xterm.css $(XTERMJS_PATH)/addons/fit/fit.js \
	  tmp-for-jar/hlib
endif
if WITH_JAVA
	cd tmp-for-jar && \
	  jar cmf $(abs_top_srcdir)/domterm-jar-manifest ../$(DOMTERM_JAR) .
else
	cd tmp-for-jar && zip -r ../$(DOMTERM_JAR) .
endif
	rm -rf tmp-for-jar

$(WSDOMTERM_JAR): wsclasses.stamp wsclasses.stamp
	$(MKDIR_P) $(wsdomterm_jar_dir) tmp-for-jar
	tar cf - org/domterm/websocket/*.class | (cd tmp-for-jar; tar xf -)
if WITH_JAVA_WEBSOCKET
	cd tmp-for-jar && unzip -o ../java_websocket.jar 'org/java_websocket/*.class'
endif
	cd tmp-for-jar && \
	  jar cmf $(abs_top_srcdir)/wsdomterm-jar-manifest ../$(WSDOMTERM_JAR) .
	rm -rf tmp-for-jar

MAKEINFO = makeinfo
XSLT = xsltproc
DOCBOOK_XSL_DIR = /usr/share/sgml/docbook/xsl-stylesheets-1.79.2
doc/index.html: doc/DomTerm.texi
	$(MAKEINFO) -I$(srcdir) --html --no-node-files $< -o doc

DOC_IMAGES = \
  doc/domterm1.svg \
  doc/images/domterm-1.png \
  doc/images/domterm-2.png \
  doc/images/domterm-panes-1.png \
  doc/images/domterm-sh-svg.png \
  doc/images/domterm-polygon-1.png \
  doc/images/dt-atom1.png \
  doc/images/dt-help1.png \
  doc/images/dt-gnuplot1.png \
  doc/images/dt-fish1.png \
  doc/images/dt-pretty1.png \
  doc/images/dt-sixel.png \
  doc/images/emacs-in-firefox-1.png \
  doc/images/dropdown-gnome.png \
  doc/images/pyplot.png \
  doc/images/python-error.png \
  doc/images/python-repl.png \
  doc/images/python-pandas.png \
  doc/images/python-pretty1.png
DOC_TOOLS = \
  tools/bash-preexec.sh \
  tools/shell-integration.bash \
  tools/shell-integration.fish \
  tools/shell-integration.zsh \
  tools/python/domterm/utils.py

doc/DomTerm.xml: doc/DomTerm.texi
	$(MKDIR_P) doc
	$(MAKEINFO) -I=doc --docbook $< -o - | \
	$(SED) \
	-e 's|_002d|-|g' \
	-e 's|<emphasis></emphasis>||' \
	-e 's|<inlinemediaobject><imageobject><imagedata fileref="\(.*\)" format="\(.*\)"></imagedata></imageobject></inlinemediaobject>|<ulink url="\1"><inlinemediaobject><imageobject><imagedata fileref="\1" format="\2"></imagedata></imageobject></inlinemediaobject></ulink>|' \
	-e 's|<chapter label="" id="Top">|<chapter label="Top" id="Top"><?dbhtml filename="index.html"?>|' \
	-e '/Suspends additional output/s|><listitem>| continuation="continues"><listitem>|' \
	> doc/DomTerm.xml

$(HTML_DIR)/index.html: doc/DomTerm.xml Makefile
	$(MKDIR_P) $(HTML_DIR)/images $(HTML_DIR)/tools
	$(XSLT) --path $(DOCBOOK_XSL_DIR)/html \
	  --output $(HTML_DIR)/  \
	  --stringparam root.filename toc \
	  --stringparam generate.section.toc.level 0 \
	  --stringparam chunker.output.encoding UTF-8 \
	  --stringparam chunker.output.doctype-public "-//W3C//DTD HTML 4.01 Transitional//EN" \
	  --stringparam generate.index 1 \
	  --stringparam use.id.as.filename 1 \
	  --stringparam chunker.output.indent yes \
	  --stringparam chunk.first.sections 1 \
	  --stringparam chunk.section.depth 1 \
	  --stringparam chapter.autolabel 0 \
	  --stringparam chunk.fast 1 \
	  --stringparam toc.max.depth 4 \
	  --stringparam toc.list.type ul \
	  --stringparam toc.section.depth 3 \
	  --stringparam chunk.separate.lots 1 \
	  --stringparam chunk.tocs.and.lots 1 \
	  $(srcdir)/doc/style/domterm.xsl doc/DomTerm.xml
	$(SED) -s 's|<a name=".*"></a>Why|Why|' <$(HTML_DIR)/index.html >$(HTML_DIR)/tmp-index.html
	mv $(HTML_DIR)/tmp-index.html $(HTML_DIR)/index.html
	cd $(srcdir) && cp $(DOC_IMAGES) $(abs_builddir)/web/images
	(cd $(srcdir) && tar cf - $(DOC_TOOLS))|(cd web && tar xf -)

web/saved/hlib:
	$(MKDIR_P) web/saved/hlib
	cd web/saved/hlib && \
	  for f in `sed -n -e "/<script /s|.*\(hlib/.*js\)'.*|\1|p" -e "/<link /s|.*\(hlib/.*css\)'.*|\1|p" <../../../tools/wrap-saved`; \
            do ln ../../../$$f .; \
	    done

web/saved/dt-prettynested.html:
	tools/wrap-saved doc/misc/dt-prettynested-saved.html $@

WEB_SERVER_ROOT=bothner@bothner.com:domterm.org
upload-web: web/index.html
	cd web && \
	  rsync -v -r -u -l -p -t --relative . $(WEB_SERVER_ROOT)

bin/qtdomterm:
	cd qtdomterm && @QMAKE@ -makefile PREFIX=$(prefix) && $(MAKE)
if MACOS

	cd bin && ln -s ../qtdomterm/QtDomTerm.app/Contents/MacOS/QtDomTerm qtdomterm
endif

ELECTRON_FILES = electron/package.json electron/main.js electron/preload.js

install-data-local:
	$(MKDIR_P) $(DESTDIR)$(pkgdatadir)
if WITH_JAVA
	$(INSTALL_DATA) -t $(DESTDIR)$(pkgdatadir) share/domterm/domterm.jar
else
if ENABLE_COMPILED_IN_RESOURCES
else
	$(INSTALL_DATA) -t $(DESTDIR)$(pkgdatadir) share/domterm/domterm.jar
endif
endif
	$(INSTALL_DATA) -t $(DESTDIR)$(pkgdatadir) share/domterm/domterm2.ico
	$(MKDIR_P) $(DESTDIR)$(datadir)/applications \
	    $(DESTDIR)$(datadir)/appdata
	$(INSTALL_DATA) $(srcdir)/domterm.desktop $(DESTDIR)$(datadir)/applications/
	$(INSTALL_DATA) $(srcdir)/domterm.appdata.xml $(DESTDIR)$(datadir)/appdata/
if WITH_QTWEBENGINE
	$(INSTALL_DATA) $(srcdir)/qtdomterm.desktop $(DESTDIR)$(datadir)/applications/
	$(INSTALL_DATA) $(srcdir)/qtdomterm.appdata.xml $(DESTDIR)$(datadir)/appdata/
endif
	$(MKDIR_P) $(DESTDIR)$(datadir)/domterm/electron
	$(INSTALL_DATA) $(srcdir)/electron/package.json $(srcdir)/electron/main.js $(srcdir)/electron/preload.js\
	    $(DESTDIR)$(datadir)/domterm/electron/
	$(MKDIR_P) $(DESTDIR)$(datadir)/domterm/help
	$(INSTALL_DATA) share/domterm/help/*.txt share/domterm/help/*.html \
	  $(DESTDIR)$(datadir)/domterm/help/

uninstall-local:
	rm -f $(DESTDIR)$(datadir)/applications/domterm.desktop \
	  $(DESTDIR)$(datadir)/applications/qtdomterm.desktop \
	  $(DESTDIR)$(datadir)/appdata/domterm.appdata.xml \
	  $(DESTDIR)$(datadir)/appdata/qtdomterm.appdata.xml
	rm -f $(DESTDIR)$(bindir)/domterm$(EXEEXT)
if WITH_QTWEBENGINE
	rm -f $(DESTDIR)$(bindir)/qtdomterm$(EXEEXT)
endif
	rm -rf $(DESTDIR)$(datadir)/domterm

install-ldomterm:
	cd lws-term && $(MAKE) install

install-exec-hook:
	$(MKDIR_P) $(DESTDIR)$(bindir)
if WITH_QTWEBENGINE
	$(INSTALL_PROGRAM) bin/qtdomterm$(EXEEXT) $(DESTDIR)$(bindir)/qtdomterm$(EXEEXT)
endif

$(pkgdatadir)/help:
	$(MKDIR_P) $(pkgdatadir)/help

share/domterm/help:
	$(MKDIR_P) share/domterm/help

ASCIIDOCTOR = asciidoctor
#ADOC2HTML_OPTS = --no-doc-title --initial-header-level=2 \
#  --link-stylesheet --stylesheet=docutils.css
ADOC2HTML_OPTS = -s
ADOC2MAN_OPTS = -b manpage
#MAN_PIPE = man -l --nj --nh -
MAN_PIPE = nroff -man

if WITH_ASCIIDOCTOR
share/domterm/help/%.txt: doc/help/%.adoc
	$(ASCIIDOCTOR) $(ADOC2MAN_OPTS) $< -o - \
	  | $(MAN_PIPE) \
	  | $(SED) -e '1,3d' -e '4s/^ *//' -e '$$d' > $@
else
share/domterm/help/%.txt: doc/help/%.adoc share/domterm/help
	$(SED) -e '/^ifdef::basebackend-manpage/,/^endif::/d' \
	  -e '/^ifndef::basebackend-manpage/d' -e '/^endif::/d' \
	  -e 's/^[+]$$//' \
	< $< > $@
endif

share/domterm/help/%.html: doc/help/%.adoc share/domterm/help
	$(ASCIIDOCTOR) $(ADOC2HTML_OPTS) $< -o $@

#share/domterm/help/docutils.css: share/domterm/help
#	echo | $(RADOC2HTML) - - | sed -e '1,/<style /d' -e '/<\/style>/,$$d' >$@

share/man/man1/%.1: doc/help/%.adoc
	$(MKDIR_P) doc/man/man1
	$(ASCIIDOCTOR) $(ADOC2MAN_OPTS) $< -o $@

# helper rule for updating hlib/wcwidth.js
# (Does not handle combining adjacent ranges - do that manually.)
EAST_ASIAN_WIDTH_TXT = /tmp/EastAsianWidth.txt
wcwidth-list.txt: $(EAST_ASIAN_WIDTH_TXT)
	sed -n \
	  -e 's/^\([0-9A-F][0-9A-F]*\);[FW].*$$/    0x\1, 0x\1,/p' \
	  -e 's/^\([0-9A-F][0-9A-F]*\)..\([0-9A-F][0-9A-F]*\);[FW].*$$/    0x\1, 0x\2,/p' \
	   $(EAST_ASIAN_WIDTH_TXT) | tr A-Z a-z > wcwidth-list.txt

IMAGEMAGICK_CONVERT = convert
doc/domterm2.ico: # doc/domterm2.svg
	$(IMAGEMAGICK_CONVERT) -background none -density 384 doc/domterm2.svg -define icon:auto-resize=64,32,16 doc/domterm2.ico
