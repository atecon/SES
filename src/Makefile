PKG = ses
GRETLDIR = $(HOME)/.gretl/functions

# The package lives in its own subdirectory, together with its data file,
# so that "open algeria.gdt --frompkg=ses" works
$(PKG).gfn: $(PKG).inp $(PKG).spec $(PKG)_sample.inp $(PKG)_help.md algeria.gdt
	gretlcli --makepkg $(PKG).inp

$(PKG).zip: $(PKG).gfn algeria.gdt
	rm -rf build && mkdir -p build/$(PKG)
	cp $(PKG).gfn algeria.gdt build/$(PKG)/
	cd build && zip -qr ../$(PKG).zip $(PKG)
	rm -rf build

install: $(PKG).gfn
	# The help text doubles as the repository's README
	cp $(PKG)_help.md ../README.md
	rm -f $(GRETLDIR)/$(PKG).gfn
	mkdir -p $(GRETLDIR)/$(PKG)
	cp $(PKG).gfn algeria.gdt $(GRETLDIR)/$(PKG)/

clean:
	rm -rf $(PKG).gfn $(PKG).zip build
