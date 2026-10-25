# po/Makefile.am — compile and install .mo translation catalogs.
#
# Deliberately not the full AM_GNU_GETTEXT/gettextize machinery: byobu's
# translations are a small, closed set of hand-maintained .po files, and
# pulling in autopoint plus a generated po/Makefile.in.in is a lot of new
# build-time machinery for what a plain install-data-hook already covers.
# Two downstream packagers (rpm/byobu.spec and Nix's package.nix) had
# already independently hand-rolled this exact msgfmt loop -- installing
# to two different paths, neither of which matches what byobu itself
# looks up at runtime (see TEXTDOMAINDIR in usr/lib/byobu/include/common).
# GH #142

localedir = $(datadir)/locale

POFILES = $(wildcard $(srcdir)/*.po)
LINGUAS = $(patsubst $(srcdir)/%.po,%,$(POFILES))

EXTRA_DIST = $(POFILES) POTFILES.Python POTFILES.Shell byobu.pot

install-data-hook:
	@if [ -z "$(MSGFMT)" ] || ! $(MSGFMT) --version >/dev/null 2>&1; then \
		echo "byobu: msgfmt not found -- skipping translation catalogs" 1>&2; \
		exit 0; \
	fi; \
	for lang in $(LINGUAS); do \
		dir="$(DESTDIR)$(localedir)/$$lang/LC_MESSAGES"; \
		$(MKDIR_P) "$$dir" && \
		$(MSGFMT) -o "$$dir/$(PACKAGE).mo" "$(srcdir)/$$lang.po" || exit 1; \
	done

uninstall-hook:
	@for lang in $(LINGUAS); do \
		rm -f "$(DESTDIR)$(localedir)/$$lang/LC_MESSAGES/$(PACKAGE).mo"; \
	done

# vi: syntax=make
