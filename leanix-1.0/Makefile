DOCKER_REGISTRY         ?= localhost:5000
PUBLISH_DOCKER_REGISTRY ?= localhost:5000
LOCAL_SOURCES_SERVER    ?= localhost:8000
IMAGE_ARCH              ?= sandybridge

default: all

all: tools \
	 opt_base \
	 opt_bcftools-1.9 \
	 opt_bcftools-1.10.2 \
	 opt_bedtools-2.29.2 \
	 opt_boost-1.74.0 \
	 opt_bwa-0.7.17 \
	 opt_bzip2-1.0.8 \
	 opt_cairo-1.16.0 \
	 opt_curl-7.70.0 \
	 opt_dbus-1.12.16 \
	 opt_delly-0.7.9 \
	 opt_eigen-20200604 \
	 opt_expat-2.2.9 \
	 opt_flex-2.6.4 \
	 opt_fontconfig-2.13.1 \
	 opt_freebayes-1.3.2 \
	 opt_freetype-2.10.2 \
	 opt_fribidi-1.0.9 \
	 opt_gatk-4.1.4.1 \
	 opt_gawk-5.0.1 \
	 opt_glib-2.64.3 \
	 opt_gmp-6.2.0 \
	 opt_gperf-3.1 \
	 opt_grep-3.4 \
	 opt_gzip-1.10 \
	 opt_hdf5-1.10.5 \
	 opt_hmmcopy_utils-20170718 \
	 opt_htslib-1.10.2 \
	 opt_icu-67.1 \
	 opt_lancet-1.1.0 \
	 opt_liblbfgs-1.10 \
	 opt_libffi-3.3 \
	 opt_libpng-1.6.37 \
	 opt_libthai-0.1.28 \
	 opt_libxml2-2.9.10 \
	 opt_manta-1.5.0 \
	 opt_miniconda2-4.7.12.1 \
	 opt_miniconda3-4.8.2 \
	 opt_ncursesw-6.2 \
	 opt_octopus-0.7.0 \
	 opt_openssl-1.1.1g \
	 opt_pairoscope-0.4.2 \
	 opt_pango-1.46.1 \
	 opt_pcre-8.44 \
	 opt_pcre2-10.35 \
	 opt_perl-5.32.0 \
	 opt_pixman-0.34.0 \
	 opt_python-2.7.18.headless \
	 opt_python-3.8.6.headless \
	 opt_python-3.8.6.headless.pybind11 \
	 opt_r-4.0.2 \
	 opt_r-4.0.2.headless \
	 opt_r-4.0.2.headless.genomic \
	 opt_readline-8.0 \
	 opt_rsync-3.2.3 \
	 opt_samtools-1.10 \
	 opt_sed-4.8 \
	 opt_sqlite-3.31.1 \
	 opt_sundials-4.1.0 \
	 opt_swig-4.0.2 \
	 opt_util-linux-2.35.1 \
	 opt_vardict-1.8.1 \
	 opt_vt-0.57721 \
	 opt_x11-20200524 \
	 opt_xz-5.2.5 \
	 opt_zlib-1.2.11
 
tools:
	$(MAKE) -C leanix-1.0_tools

opt_base: tools
	$(MAKE) -C leanix-1.0_opt_base

opt_bcftools-1.9: tools opt_bzip2-1.0.8 opt_xz-5.2.5 opt_zlib-1.2.11 opt_curl-7.70.0
	$(MAKE) -C leanix-1.0_opt_bcftools-1.9
	
opt_bcftools-1.10.2: tools opt_bzip2-1.0.8 opt_xz-5.2.5 opt_zlib-1.2.11 opt_curl-7.70.0
	$(MAKE) -C leanix-1.0_opt_bcftools-1.10.2
	
opt_bedtools-2.29.2: tools
	$(MAKE) -C leanix-1.0_opt_bedtools-2.29.2
	
opt_boost-1.74.0: tools
	$(MAKE) -C leanix-1.0_opt_boost-1.74.0
	
opt_bwa-0.7.17: tools
	$(MAKE) -C leanix-1.0_opt_bwa-0.7.17
	
opt_bzip2-1.0.8: tools
	$(MAKE) -C leanix-1.0_opt_bzip2-1.0.8
	
opt_cairo-1.16.0: tools opt_pixman-0.34.0 opt_libpng-1.6.37 opt_pixman-0.34.0 opt_fontconfig-2.13.1 opt_glib-2.64.3
	$(MAKE) -C leanix-1.0_opt_cairo-1.16.0
	
opt_curl-7.70.0: tools opt_openssl-1.1.1g
	$(MAKE) -C leanix-1.0_opt_curl-7.70.0
	
opt_dbus-1.12.16: tools opt_expat-2.2.9
	$(MAKE) -C leanix-1.0_opt_dbus-1.12.16
	
opt_delly-0.7.9: tools opt_htslib-1.10.2
	$(MAKE) -C leanix-1.0_opt_delly-0.7.9
	
opt_eigen-20200604: tools
	$(MAKE) -C leanix-1.0_opt_eigen-20200604
	
opt_expat-2.2.9: tools
	$(MAKE) -C leanix-1.0_opt_expat-2.2.9
	
opt_flex-2.6.4: tools
	$(MAKE) -C leanix-1.0_opt_flex-2.6.4
	
opt_freetype-2.10.2: tools
	$(MAKE) -C leanix-1.0_opt_freetype-2.10.2
	
opt_freebayes-1.3.2: tools
	$(MAKE) -C leanix-1.0_opt_freebayes-1.3.2
	
opt_fontconfig-2.13.1: tools opt_util-linux-2.35.1 opt_gperf-3.1 opt_freetype-2.10.2 opt_expat-2.2.9
	$(MAKE) -C leanix-1.0_opt_fontconfig-2.13.1
	
opt_fribidi-1.0.9: tools
	$(MAKE) -C leanix-1.0_opt_fribidi-1.0.9
	
opt_gatk-4.1.4.1: tools opt_openjdk-8u opt_r-4.0.2.headless.genomic opt_miniconda3-4.8.2
	$(MAKE) -C leanix-1.0_opt_gatk-4.1.4.1
	
opt_gawk-5.0.1: tools
	$(MAKE) -C leanix-1.0_opt_gawk-5.0.1
	
opt_glib-2.64.3: tools opt_pcre2-10.35 opt_libffi-3.3
	$(MAKE) -C leanix-1.0_opt_glib-2.64.3
	
opt_gmp-6.2.0: tools
	$(MAKE) -C leanix-1.0_opt_gmp-6.2.0
	
opt_gperf-3.1: tools
	$(MAKE) -C leanix-1.0_opt_gperf-3.1
	
opt_grep-3.4: tools opt_pcre-8.44
	$(MAKE) -C leanix-1.0_opt_grep-3.4
	
opt_gzip-1.10: tools
	$(MAKE) -C leanix-1.0_opt_gzip-1.10
	
opt_hdf5-1.10.5: tools
	$(MAKE) -C leanix-1.0_opt_hdf5-1.10.5
	
opt_hmmcopy_utils-20170718: tools
	$(MAKE) -C leanix-1.0_opt_hmmcopy_utils-20170718
	
opt_htslib-1.10.2: tools
	$(MAKE) -C leanix-1.0_opt_htslib-1.10.2
	
opt_icu-67.1: tools
	$(MAKE) -C leanix-1.0_opt_icu-67.1
	
opt_lancet-1.1.0: tools
	$(MAKE) -C leanix-1.0_opt_lancet-1.1.0
	
opt_libffi-3.3: tools
	$(MAKE) -C leanix-1.0_opt_libffi-3.3
	
opt_liblbfgs-1.10: tools
	$(MAKE) -C leanix-1.0_opt_liblbfgs-1.10
	
opt_libpng-1.6.37: tools
	$(MAKE) -C leanix-1.0_opt_libpng-1.6.37
	
opt_libthai-0.1.28: tools
	$(MAKE) -C leanix-1.0_opt_libthai-0.1.28
	
opt_libxml2-2.9.10: tools
	$(MAKE) -C leanix-1.0_opt_libxml2-2.9.10
	
opt_manta-1.5.0: tools opt_miniconda2-4.7.12.1
	$(MAKE) -C leanix-1.0_opt_manta-1.5.0
	
opt_miniconda2-4.7.12.1: tools
	$(MAKE) -C leanix-1.0_opt_miniconda2-4.7.12.1
	
opt_miniconda3-4.8.2: tools
	$(MAKE) -C leanix-1.0_opt_miniconda3-4.8.2
	
opt_ncursesw-6.2: tools
	$(MAKE) -C leanix-1.0_opt_ncursesw-6.2
	
opt_octopus-0.7.0: tools
	$(MAKE) -C leanix-1.0_opt_octopus-0.7.0
	
opt_openjdk-8u: tools opt_x11-20200524
	$(MAKE) -C leanix-1.0_opt_openjdk-8u
	
opt_openssl-1.1.1g: tools
	$(MAKE) -C leanix-1.0_opt_openssl-1.1.1g
	
opt_pairoscope-0.4.2: tools
	$(MAKE) -C leanix-1.0_opt_pairoscope-0.4.2
	
opt_pango-1.46.1: tools opt_fontconfig-2.13.1 opt_fribidi-1.0.9 opt_icu-67.1 opt_libthai-0.1.28 opt_flex-2.6.4
	$(MAKE) -C leanix-1.0_opt_pango-1.46.1
	
opt_pcre-8.44: tools
	$(MAKE) -C leanix-1.0_opt_pcre-8.44
	
opt_pcre2-10.35: tools opt_readline-8.0 opt_zlib-1.2.11
	$(MAKE) -C leanix-1.0_opt_pcre2-10.35
	
opt_perl-5.32.0: tools
	$(MAKE) -C leanix-1.0_opt_perl-5.32.0
	
opt_pixman-0.34.0: tools
	$(MAKE) -C leanix-1.0_opt_pixman-0.34.0

opt_python-2.7.18.headless: tools opt_bzip2-1.0.8 opt_libffi-3.3 opt_ncursesw-6.2 opt_readline-8.0 opt_sqlite-3.31.1 opt_xz-5.2.5 opt_zlib-1.2.11
	$(MAKE) -C leanix-1.0_opt_python-2.7.18.headless
	
opt_python-3.8.6.headless: tools opt_bzip2-1.0.8 opt_libffi-3.3 opt_ncursesw-6.2 opt_readline-8.0 opt_sqlite-3.31.1 opt_bzip2-1.0.8 opt_xz-5.2.5 opt_zlib-1.2.11
	$(MAKE) -C leanix-1.0_opt_python-3.8.6.headless
	
opt_python-3.8.6.headless.pybind11: tools leanix-1.0_opt_python-3.8.6.headless opt_boost-1.74.0
	$(MAKE) -C leanix-1.0_opt_python-3.8.6.headless.pybind11
	
opt_r-4.0.2: tools opt_sed-4.8
	$(MAKE) -C leanix-1.0_opt_r-4.0.2
	
opt_r-4.0.2.headless: tools
	$(MAKE) -C leanix-1.0_opt_r-4.0.2.headless
	
opt_r-4.0.2.headless.genomic: tools opt_r-4.0.2.headless
	$(MAKE) -C leanix-1.0_opt_r-4.0.2.headless.genomic
	
opt_readline-8.0: tools opt_ncursesw-6.2
	$(MAKE) -C leanix-1.0_opt_readline-8.0

opt_rsync-3.2.3: tools
	$(MAKE) -C leanix-1.0_opt_rsync-3.2.3
	
opt_samtools-1.10: tools
	$(MAKE) -C leanix-1.0_opt_samtools-1.10
	
opt_sed-4.8: tools
	$(MAKE) -C leanix-1.0_opt_sed-4.8
	
opt_sqlite-3.31.1: tools
	$(MAKE) -C leanix-1.0_opt_sqlite-3.31.1

opt_sundials-4.1.0: tools
	$(MAKE) -C leanix-1.0_opt_sundials-4.1.0
		
opt_swig-4.0.2: tools
	$(MAKE) -C leanix-1.0_opt_swig-4.0.2
	
opt_util-linux-2.35.1: tools
	$(MAKE) -C leanix-1.0_opt_util-linux-2.35.1
		
opt_vardict-1.8.1: tools
	$(MAKE) -C leanix-1.0_opt_vardict-1.8.1
	
opt_vt-0.57721: tools
	$(MAKE) -C leanix-1.0_opt_vt-0.57721
	
opt_x11-20200524: tools opt_libxml2-2.9.10
	$(MAKE) -C leanix-1.0_opt_x11-20200524
	
opt_xz-5.2.5: tools
	$(MAKE) -C leanix-1.0_opt_xz-5.2.5
	
opt_zlib-1.2.11: tools
	$(MAKE) -C leanix-1.0_opt_zlib-1.2.11


