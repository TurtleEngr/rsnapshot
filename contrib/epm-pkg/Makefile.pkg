# -*- mode: makefile; -*-
# contrib/epm-pkg/Makefile.pkg
# Build packages with epm program.
# All the config files are in: contrib/epm-pkg/

# To use always reference the contrib/epm-pkg/Makefile.pkg. See alias.
# make -f contrib/epm-pkg/Makefile.pkg TARGET


# --------------------
# Macros
SHELL=/bin/bash
mRsyncOpt=-azP
mPkg=contrib/epm-pkg
mPrefix=dist/usr/local
mShare=$(mPrefix)/share/doc/rsnapshot

include $(mPkg)/ver.mak

# --------------------
# Manual Targets

# 1
help : check-top check-deps
	@echo -e "\nalias pkg='make -f contrib/epm-pkg/Makefile.pkg'"
	@echo -e "\nInitialize"
	@echo -e '\tpkg config\n'
	@echo -e "Build the pseudo root dist/"
	@echo -e "\tpkg build\n"
	@echo -e "Create the package. Put it in pkg/"
	@echo -e "\tpkg package"

# 2
config : check-top check-deps configure $(mPkg)/ver.mak

# 3
build : check-top configure dist $(mPkg)/ver.env
	make
	make install
	make html
	-mkdir -p $(mShare)
	cp -r docs/* $(mShare)
	cp COPYING $(mShare)/LICENSE
	cp README.md $(mShare)
	cp rsnapshot.html $(mShare)
	grep PRETTY_NAME /etc/lsb-release >$(mShare)/build-os
	uname -a >>$(mShare)/build-os
	mkepmlist -u root -g root --prefix / dist | patch-epm-list -f ./$(mPkg)/epm.patch >$(mPkg)/epm.list

# 4
test :
	make test

# 5
package : build pkg $(mPkg)/ver.epm

# 6
release : save $(mPkg)/ver.env

# 7 save :
	git pull origin develop
	git commit -am Updated
	git push origin develop

# 8
dist-clean : check-top clean
	-make clean
	-rm -rf dist pkg configure $(mPkg)/epm.list \
		$(mPkg)/ver.mak $(mPkg)/ver.env $(mPkg)/ver.epm

clean :
	-find . -type f -name '*~' -exec rm {} \;

# --------------------
# Rules

check-top :
	@if [[ ! -d contrib ]]; then \
		echo "You are not in the top dir."; \
		exit 1;  \
	fi

check-deps :
	command -v aclocal
	command -v autoreconf
	command -v epm
	command -v git
	command -v mkepmlist
	command -v mkver.pl
	command -v patch-epm-list
	command -v perl
	command -v rsync

configure : autogen.sh
	./autogen.sh
	./configure --prefix=$$PWD/$(mPrefix)

dist pkg :
	mkdir $@

$(mPkg)/ver.mak $(mPkg)/ver.env $(mPkg)/ver.epm : $(mPkg)/ver.sh
	export RELEASE=1; mkver.pl -e 'mak env epm' -d $?
	mv ver.mak ver.env ver.epm $(mPkg)
