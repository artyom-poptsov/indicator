bin_SCRIPTS =				\
	indicator

RPC = 					\
	rpc-types+constants.scm		\
	rpc-client.scm			\
	rpc-server.scm

indicatordir = $(datadir)/indicator
indicator_DATA = 			\
	$(RPC)

EXTRA_DIST = indicator.in

GRPCC = `which grpc-compile`

substitute = sed -e 's,[@]PACKAGE_VERSION[@],$(PACKAGE_VERSION),g'

SUFFIXES = .in

# Producing of executable scripts

.in:
	$(substitute) "$^" > "$@.tmp" && 	\
	chmod +x "$@.tmp" &&			\
	mv "$@.tmp" "$@"

indicator: indicator.in
	sed -e 's|__DATA_DIR__|$(datadir)|g' $< > $@
	chmod +x $@

## Generating of RPC files.

rpc-types+constants.scm: rpc.x
	echo ';;; -*- buffer-read-only: t; -*-' > $@
	echo ';;; Automatically generated -- do not modify.' >> $@
	echo '(define-module (indicator rpc-types+constants)' >> $@
	echo '  #:use-module (rpc xdr types)' >> $@
	echo '  #:use-module (rpc xdr))' >> $@
	$(GRPCC) --xdr --constants < $< |\
		sed 's|^(define |(define-public |g' >> $@

rpc-client.scm: rpc.x
	echo ';;; -*- buffer-read-only: t; -*-' > $@
	echo ';;; Automatically generated -- do not modify.' >> $@
	echo '(define-module (indicator rpc-client)' >> $@
	echo '  #:use-module (indicator rpc-types+constants)' >> $@
	echo '  #:use-module (rpc rpc)' >> $@
	echo '  #:use-module (rpc xdr types))' >> $@
	$(GRPCC) --client < $< |\
		sed 's|^(define |(define-public |g' >> $@

rpc-server.scm: rpc.x
	echo ';;; -*- buffer-read-only: t; -*-' > $@
	echo ';;; Automatically generated -- do not modify.' >> $@
	echo '(define-module (indicator rpc-server)' >> $@
	echo '  #:use-module (indicator rpc-types+constants)' >> $@
	echo '  #:use-module (srfi srfi-1)' >> $@
	echo '  #:use-module (rpc rpc server)' >> $@
	echo '  #:use-module (rpc xdr types))' >> $@
	$(GRPCC) --server < $< |\
		sed 's|^(define |(define-public |g' >> $@

# Cleanup targets

clean-rpc: $(RPC)
	-$(RM) $(RPC)
.PHONY: clean-rpc

CLEANFILES = \
	$(bin_SCRIPTS) $(RPC)

# Makefile.am ends here
