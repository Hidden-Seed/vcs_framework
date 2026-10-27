REPO_DIR = $(shell pwd)/..

VCS   = vcs
VSRCS = $(REPO_DIR)/rtl/test.v
SIMV  = $(REPO_DIR)/sim/simv
CC    = gcc

# glibc 2.34 起移除了 pthread_yield，VCS 2018.09 的 vcs_save_restore_new.o 仍引用它。
# 这里在链接时补一个同名符号，不修改 VCS 安装目录；csrc/Makefile 每次运行都会被 VCS
# 重新生成，所以改动它无法保持，相关问题在这里处理。
SHIM_SRC = pthread_yield_shim.c
SHIM_OBJ = $(CURDIR)/pthread_yield_shim.o

FLAGS = -full64 -LDFLAGS "-Wl,--no-as-needed $(SHIM_OBJ)"

.PHONY: sim clean

sim: $(SIMV)
	$^

$(SHIM_OBJ): $(SHIM_SRC)
	$(CC) -fPIC -O2 -c $< -o $@

$(SIMV): $(VSRCS) $(SHIM_OBJ)
	$(VCS) $(FLAGS) $(VSRCS) -o $@

clean:
	rm -rf $(SIMV) $(SIMV).daidir csrc $(SHIM_OBJ) ucli.key
