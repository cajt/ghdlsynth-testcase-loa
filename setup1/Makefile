
DOCKER=docker
PWD = $(shell pwd)
DOCKERARGS = run --rm -v $(PWD):/src -w /src
GHDL      = $(DOCKER) $(DOCKERARGS) ghdl/synth:beta ghdl
GHDLSYNTH = $(DOCKER) $(DOCKERARGS) ghdl/synth:beta ghdl
YOSYS     = $(DOCKER) $(DOCKERARGS) ghdl/synth:beta yosys
NEXTPNR   = $(DOCKER) $(DOCKERARGS) ghdl/synth:nextpnr-ice40 nextpnr-ice40
ICEPACK   = $(DOCKER) $(DOCKERARGS) ghdl/synth:icestorm icepack

GHDL_GENERICS=
PCF=constraints/toplevel.pcf
PACKAGE=tq144
NEXTPNR_FLAGS=--hx1k --freq 12

SRC= 	\
hdl/bus_pkg.vhd \
hdl/reset_pkg.vhd \
hdl/utils_pkg.vhd \
hdl/hdlc_pkg.vhd \
hdl/hdlc_crc_pkg.vhd \
hdl/xilinx_block_ram_pkg.vhd \
hdl/xilinx_block_ram.vhd \
hdl/reg_file_pkg.vhd \
hdl/peripheral_register.vhd \
hdl/edge_detect.vhd \
hdl/double_buffering.vhd \
hdl/reg_file.vhd \
hdl/reg_file_bram.vhd \
hdl/reg_file_bram_double_buffered.vhd \
hdl/text_pkg.vhd \
hdl/clock_divider.vhd \
hdl/fractional_clock_divider_variable.vhd\
hdl/dff.vhd \
hdl/fractional_clock_divider.vhd \
hdl/uart_pkg.vhd \
hdl/uart_rx.vhd \
hdl/uart_tx.vhd \
hdl/uart.vhd \
hdl/fifo_sync_pkg.vhd \
hdl/fifo_sync.vhd \
hdl/hdlc_dec.vhd \
hdl/hdlc_enc.vhd \
hdl/hdlc_busmaster.vhd \
hdl/hdlc_busmaster_with_support.vhd \
hdl/event_hold_stage.vhd \
src/toplevel.vhd

all: toplevel.bit

toplevel.json: $(SRC)
	$(GHDL) -a --std=93 --ieee=synopsys $^
	$(YOSYS) -m ghdl -p "ghdl --std=93 --ieee=synopsys $(GHDL_GENERICS) toplevel; synth_ice40 top toplevel json $@"

toplevel.config: toplevel.json $(LPF)
	$(NEXTPNR) --json $< --asc $@ --pcf $(PCF) $(NEXTPNR_FLAGS) --package $(PACKAGE)

toplevel.bit: toplevel.config
	$(ICEPACK) $< $@

toplevel.svf: toplevel.bit

clean:
	@rm -f work-obj08.cf *.bit *.json *.svf *.config

