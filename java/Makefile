srcdir := $(dir $(firstword $(MAKEFILE_LIST)))
VERSION = $(shell awk '/Version/ { print $$2; }' $(srcdir)/Manifest.txt)
TARGET_DIR ?= target
TARGET_ARCHIVE = $(TARGET_DIR)/numbertext-$(VERSION)
ARCHIVE_ALL = $(TARGET_ARCHIVE)-all

dist: $(TARGET_ARCHIVE).jar docjar srcjar

$(TARGET_DIR):
	mkdir -p $@/META-INF/
	cp $(srcdir)/../COPYING $@/META-INF/LICENSE-BSD-dual.txt
	cp $(srcdir)/../README.md $@/META-INF/

doc : | $(TARGET_DIR)
	mkdir -p target/javadoc
	javadoc -locale de -classpath . -sourcepath $(srcdir) -encoding utf8 -d target/javadoc org.numbertext

preparedata : | $(TARGET_DIR)
	mkdir -p target/data/org/numbertext/data
	cp $(srcdir)/../data/*.sor target/data/org/numbertext/data

build : preparedata
	mkdir -p target/classes
	CLASSPATH=. javac -classpath $(srcdir) -sourcepath $(srcdir) -encoding utf8 -d target/classes $(srcdir)/org/numbertext/Numbertext.java

srcjar : preparedata
	jar cfm $(TARGET_ARCHIVE)-sources.jar $(srcdir)/Manifest.txt -C target/data org
	jar uf $(TARGET_ARCHIVE)-sources.jar -C $(srcdir) org
	jar uf $(TARGET_ARCHIVE)-sources.jar -C target/ META-INF

$(TARGET_ARCHIVE).jar : build
	jar cfm $@ $(srcdir)/Manifest.txt -C target/data org
	jar uf $@ -C target/classes org
	jar uf $@ -C target/ META-INF

docjar : doc
	jar cfm $(TARGET_ARCHIVE)-javadoc.jar $(srcdir)/Manifest.txt -C target/javadoc .
	jar uf $(TARGET_ARCHIVE)-javadoc.jar -C target/ META-INF

check : $(TARGET_ARCHIVE).jar
	java -jar $(TARGET_ARCHIVE).jar -l en 99-101
	java -jar $(TARGET_ARCHIVE).jar -l en-GB 99-101
	java -jar $(TARGET_ARCHIVE).jar -p ordinal -l en 1-10

alljar : | $(TARGET_DIR)
	mv $(TARGET_ARCHIVE).jar $(ARCHIVE_ALL).jar
	$(eval TARGET_ARCHIVE = $(ARCHIVE_ALL))

alldist : $(TARGET_ARCHIVE).jar alljar
	jar uf $(TARGET_ARCHIVE).jar -C $(srcdir) org

clean:
	rm -rf target
