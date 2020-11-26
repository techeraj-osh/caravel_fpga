# Using OpenLANE to Harden Your Design:

You can utilize the Makefile existing here in this directory to do that.

But, first you need to specify 3 things:
```bash
export IMAGE_NAME=openlane:<the openlane tag/version you are using>
export PDK_ROOT=<The location where the pdk is installed>
export OPENLANE_ROOT=<the absolute path to the cloned openlane directory>
```

Then, you have two options:
1. Create a macro for your design and harden it, then insert it into user_project_wrapper.

2. Flatten your design with the user_project_wrapper and harden them as one.


**NOTE:** The OpenLANE documentation should cover everything you might need to create your design. You can find that [here](https://github.com/efabless/openlane/blob/master/README.md).

## Option 1:

This could be done by creating a directory for your design here in this directory, and adding a configuration file for it under the same directory. You can follow the instructions given [here](https://github.com/efabless/openlane#adding-a-design) to generate an initial configuration file for your design, or you can start with the following:

```tcl
set script_dir [file dirname [file normalize [info script]]]

set ::env(DESIGN_NAME) <Your Design Name>

set ::env(VERILOG_FILES) "$script_dir/../../verilog/rtl/<Your RTL.v>"

set ::env(CLOCK_PORT) <Clock port name if it exists>
set ::env(CLOCK_PERIOD) <Desired clock period>
```

Then you can add them as you see fit to get the desired DRC/LVS clean outcome.

After that, run the following command:
```bash
make <your design directory name>
```

Then, follow the instructions given in Option 2.

## Option 2:

1. Add your design to the RTL of the [user_project_wrapper](../verilog/rtl/user_project_wrapper.v).

2. Modify the configuration file [here](./user_project_wrapper/config.tcl) to include any extra files you may need. Make sure to change these accordingly:
```tcl
set ::env(CLOCK_NET) "mprj.clk"


set ::env(VERILOG_FILES) "\
	$script_dir/../../verilog/rtl/defines.v \
	$script_dir/../../verilog/rtl/user_project_wrapper.v"

set ::env(VERILOG_FILES_BLACKBOX) "\
	$script_dir/../../verilog/rtl/defines.v \
	$script_dir/../../verilog/rtl/user_proj_example.v"

set ::env(EXTRA_LEFS) "\
	$script_dir/../../lef/user_proj_example.lef"

set ::env(EXTRA_GDS_FILES) "\
	$script_dir/../../gds/user_proj_example.gds"
```
**NOTE:** Don't change the size or the pin order!

3. Remove this line `add_macro_placement mprj 1150 1700 N` from the interactive script [here](./user_project_wrapper/config.tcl) and replace it with the placement for your macro instances. Or, remove it entirely if you have no macros, along with this line `manual_macro_placement f`.

4. Run your design through the flow: `make user_project_wrapper`

5. Re-iterate until you have what you want.

6. Go back to the main [README.md](../README.md) and continue the process of boarding the chip.


## Extra Pointers:


- The OpenLANE documentation should cover everything you might need to create your design. You can find that [here](https://github.com/efabless/openlane/blob/master/README.md).
- The OpenLANE [FAQs](https://github.com/efabless/openlane/wiki) can guide through your troubles.
- [Here](https://github.com/efabless/openlane/blob/master/configuration/README.md) you can find all the configurations and how to use them.
- [Here](https://github.com/efabless/openlane/blob/master/doc/advanced_readme.md) you can learn how to write an interactive script.
- [Here](https://github.com/efabless/openlane/blob/master/doc/OpenLANE_commands.md) you can find a full documentation for all OpenLANE commands.
- [This documentation](https://github.com/efabless/openlane/blob/master/regression_results/README.md) describes how to use the exploration script to achieve an LVS/DRC clean design.