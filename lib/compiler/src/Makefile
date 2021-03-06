#
# %CopyrightBegin%
# 
# Copyright Ericsson AB 1996-2009. All Rights Reserved.
# 
# The contents of this file are subject to the Erlang Public License,
# Version 1.1, (the "License"); you may not use this file except in
# compliance with the License. You should have received a copy of the
# Erlang Public License along with this software. If not, it can be
# retrieved online at http://www.erlang.org/.
# 
# Software distributed under the License is distributed on an "AS IS"
# basis, WITHOUT WARRANTY OF ANY KIND, either express or implied. See
# the License for the specific language governing rights and limitations
# under the License.
# 
# %CopyrightEnd%
#

ifdef BOOTSTRAP_COMPILER
EGEN=$(BOOTSTRAP_COMPILER)/egen
EBIN=$(BOOTSTRAP_COMPILER)/ebin
else
ifdef BOOTSTRAP
EGEN=$(BOOTSTRAP_TOP)/lib/compiler/egen
EBIN=$(BOOTSTRAP_TOP)/lib/compiler/ebin
endif
endif

include $(ERL_TOP)/make/target.mk
include $(ERL_TOP)/make/$(TARGET)/otp.mk

# ----------------------------------------------------
# Application version
# ----------------------------------------------------
include ../vsn.mk
VSN=$(COMPILER_VSN)

# ----------------------------------------------------
# Release directory specification
# ----------------------------------------------------
RELSYSDIR = $(RELEASE_PATH)/lib/compiler-$(VSN)

# ----------------------------------------------------
# Target Specs
# ----------------------------------------------------
MODULES =  \
	compile \
	sys_pre_attributes \
	sys_pre_expand \
	sys_expand_pmod \
	v3_core \
	sys_core_fold \
	sys_core_inline \
	sys_core_dsetel \
	core_lib \
	core_scan \
	core_parse \
	core_lint \
	core_pp \
	v3_kernel \
	v3_kernel_pp \
	v3_life \
	v3_codegen \
	beam_block \
	beam_bool \
	beam_dead \
	beam_jump \
	beam_type \
	beam_clean \
	beam_peep \
	beam_bsm \
	beam_trim \
	beam_flatten \
	beam_listing \
	beam_asm \
	beam_dict \
	beam_opcodes \
	beam_disasm \
	beam_utils \
	beam_validator \
        erl_bifs \
	cerl \
	cerl_clauses \
	cerl_inline \
	cerl_trees \
	rec_env

BEAM_H = $(wildcard ../priv/beam_h/*.h)

HRL_FILES= \
	beam_disasm.hrl \
	core_parse.hrl \
	v3_kernel.hrl \
	v3_life.hrl

YRL_FILE = core_parse.yrl

EXTRA_FILES= $(EGEN)/beam_opcodes.hrl

ERL_FILES= $(MODULES:%=%.erl)
INSTALL_FILES= $(MODULES:%=$(EBIN)/%.$(EMULATOR)) $(APP_TARGET) $(APPUP_TARGET)
TARGET_FILES= $(INSTALL_FILES)
 
APP_FILE= compiler.app
APP_SRC= $(APP_FILE).src
APP_TARGET= $(EBIN)/$(APP_FILE)

APPUP_FILE= compiler.appup
APPUP_SRC= $(APPUP_FILE).src
APPUP_TARGET= $(EBIN)/$(APPUP_FILE)

# ----------------------------------------------------
# FLAGS
# ----------------------------------------------------

ERL_COMPILE_FLAGS += +inline +warn_unused_import -I../../stdlib/include -I$(EGEN) -W

# ----------------------------------------------------
# Targets
# ----------------------------------------------------

debug opt: $(TARGET_FILES)

docs:

clean:
	rm -f $(TARGET_FILES)
	rm -f $(EGEN)/beam_opcodes.erl $(EGEN)/beam_opcodes.hrl
	rm -f $(EGEN)/core_parse.erl
	rm -f core

# ----------------------------------------------------
# Special Build Targets
# ----------------------------------------------------

$(APP_TARGET): $(APP_SRC) ../vsn.mk
	sed -e 's;%VSN%;$(VSN);' $< > $@

$(APPUP_TARGET): $(APPUP_SRC) ../vsn.mk
	sed -e 's;%VSN%;$(VSN);' $< > $@

$(EGEN)/beam_opcodes.erl $(EGEN)/beam_opcodes.hrl: genop.tab
	$(PERL) $(ERL_TOP)/erts/emulator/utils/beam_makeops -compiler -outdir $(EGEN) $<

$(EBIN)/beam_asm.beam: $(ESRC)/beam_asm.erl $(EGEN)/beam_opcodes.hrl
	$(ERLC) $(ERL_COMPILE_FLAGS) -DCOMPILER_VSN='"$(VSN)"' -o$(EBIN) $<

$(EBIN)/cerl_inline.beam: $(ESRC)/cerl_inline.erl
	$(ERLC) $(ERL_COMPILE_FLAGS) +nowarn_shadow_vars -o$(EBIN) $<

# ----------------------------------------------------
# Release Target
# ---------------------------------------------------- 
include $(ERL_TOP)/make/otp_release_targets.mk

release_spec: opt
	$(INSTALL_DIR) $(RELSYSDIR)/src
	$(INSTALL_DATA) $(ERL_FILES) $(HRL_FILES) $(EXTRA_FILES) \
		$(YRL_FILE) $(RELSYSDIR)/src
	$(INSTALL_DIR) $(RELSYSDIR)/ebin
	$(INSTALL_DATA) $(INSTALL_FILES) $(RELSYSDIR)/ebin

release_docs_spec:

# ----------------------------------------------------
# Dependencies -- alphabetically, please
# ---------------------------------------------------- 

$(EBIN)/beam_disasm.beam: $(EGEN)/beam_opcodes.hrl beam_disasm.hrl
$(EBIN)/beam_listing.beam: v3_life.hrl
$(EBIN)/beam_validator.beam: beam_disasm.hrl
$(EBIN)/cerl.beam: core_parse.hrl
$(EBIN)/compile.beam: core_parse.hrl ../../stdlib/include/erl_compile.hrl
$(EBIN)/core_lib.beam: core_parse.hrl
$(EBIN)/core_lint.beam: core_parse.hrl
$(EBIN)/core_parse.beam: core_parse.hrl $(EGEN)/core_parse.erl
$(EBIN)/core_pp.beam: core_parse.hrl
$(EBIN)/sys_core_dsetel.beam: core_parse.hrl
$(EBIN)/sys_core_fold.beam: core_parse.hrl
$(EBIN)/sys_core_inline.beam: core_parse.hrl
$(EBIN)/sys_pre_expand.beam: ../../stdlib/include/erl_bits.hrl
$(EBIN)/v3_codegen.beam: v3_life.hrl
$(EBIN)/v3_core.beam: core_parse.hrl
$(EBIN)/v3_kernel.beam: core_parse.hrl v3_kernel.hrl
$(EBIN)/v3_kernel_pp.beam: v3_kernel.hrl
$(EBIN)/v3_life.beam: v3_kernel.hrl v3_life.hrl
