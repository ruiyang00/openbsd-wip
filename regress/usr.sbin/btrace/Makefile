# $OpenBSD: Makefile,v 1.22 2021/09/16 10:54:35 anton Exp $

BTRACE?=		 /usr/sbin/btrace
ALLOWDT!=		sysctl -n kern.allowdt

# scripts that don't need /dev/dt
BT_LANG_SCRIPTS=	arithm beginend boolean comments delete exit \
			histempty if \
			map mapclear mapempty mapsyntax mapzero map-unnamed \
			maxoperand min+max+sum multismts nsecs+var \
			precedence print read-map-after-clear staticv-empty \
			syntaxerror

# scripts that use kernel probes
BT_KERN_SCRIPTS=	multiprobe

REGRESS_EXPECTED_FAILURES=	run-maxoperand

.for b in ${BT_LANG_SCRIPTS}
REGRESS_TARGETS+=		run-$b
run-$b:
	cd ${.CURDIR} && ${BTRACE} $b.bt 2>&1 | diff -u $b.ok /dev/stdin
.endfor

b=staticv
REGRESS_TARGETS+=		run-$b
run-$b:
	${BTRACE} ${.CURDIR}/$b.bt 42 1337 -2019 2>&1 | \
		diff -u ${.CURDIR}/$b.ok /dev/stdin

.for b in ${BT_KERN_SCRIPTS}
REGRESS_TARGETS+=		run-$b
REGRESS_ROOT_TARGETS+=		run-$b
run-$b:
.if ${ALLOWDT}
	${SUDO} ${BTRACE} ${.CURDIR}/$b.bt 2>&1 | \
		diff -u ${.CURDIR}/$b.ok /dev/stdin
.else
	@echo SKIPPED
.endif
.endfor

.include <bsd.regress.mk>
