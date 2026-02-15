
# RISC-V Cores

There are many free (as in free speech) good quality RISC-V CPU cores available. Here we focus on those written directly in Verilog, or those written in other languages but can be "generated" into plain Verilog (or synthesizable SystemVerilog) code.

Examples of such languages are Chisel (based on Scala, and used for the Rocket chip and for Berkeley BOOM) and SpinalHDL (also based on Scala, and used for the Vex and Nax processors by Charles Papon - aka dolu1990).

For those already available in Verilog, we can simply provide a link; for those to be generated, we provide the generated files. There is a category in between which are cores written in another language but for which another third party website already stores the generated Verilog code, for example Rocket and Vex.

All cores can be found in the [verilog](/verilog/) directory.

## LiteX Cores

- [CVA5](https://github.com/openhwgroup/cva5): [generated core in subrepo](/verilog/Cva5)
- [CVA6](https://github.com/openhwgroup/cva6): [generated core in subrepo](/verilog/Cva6)
- [Ibex](https://github.com/lowRISC/ibex): [generated core in subrepo](/verilog/Ibex)
- [Nax](https://github.com/SpinalHDL/NaxRiscv): [generated core in build directory](/verilog/NaxRiscv)
- [OpenC906](https://github.com/XUANTIE-RV/openc906): [generated core in subrepo](/verilog/OpenC906)
- [Rocket Chip](https://github.com/ucb-bar/rocket-chip): [generated core in subrepo](/verilog/Rocket)
- [Vex](https://github.com/SpinalHDL/VexRiscv): [generated core in subrepo](/verilog/VexRiscv)
- [Vex SMP](https://github.com/SpinalHDL/VexRiscv): [generated core in subrepo](/verilog/VexRisc-SMP)
- [Vex II](https://github.com/SpinalHDL/VexiiRiscv): [generated core in build directory](/verilog/VexiiRiscv)

### How the LiteX CPUs were generated

Of all the pre-requisites of the LiteX repo, some are easily satisfied by using a Python virtual env ("venv"); SBT requires the addition of an external `.deb` repo; and the newest version of Java can cause problems (such as `[error] java.lang.NoClassDefFoundError: Could not initialize class sbt.internal.parser.SbtParser$`) so JDK 17 must be selected as the system's default alternative:
```
sudo apt install openjdk-17-jdk
sudo update-alternatives --config java
```
```
echo "deb https://repo.scala-sbt.org/scalasbt/debian all main" | sudo tee /etc/apt/sources.list.d/sbt.list
curl -sL "https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x2EE0EA64E40A89B84B2DF73499E82A75642AC823" | sudo apt-key add
sudo apt upgrade
sudo apt install sbt
```
We can then follow the instructions on the LiteX github repo to do the one-time set-up:
```
mkdir -p ~/work/RiscVCores/LiteX
cd !$
git clone https://github.com/enjoy-digital/litex.git --recurse-submodules 
cd litex/
python3 -m venv ../../LiteX_venv
source ../../LiteX_venv/bin/activate
./litex_setup.py --init --install --config=full
```
The local copy of the LiteX CVA5 core file `LiteX/litex/litex/soc/cores/cpu/cva5/core.py` has needed patching, to avoid confusion between the `--variant` argument used by LiteX and the `--variant` argument used by the CVA5 core:
```
--- a/litex/soc/cores/cpu/cva5/core.py
+++ b/litex/soc/cores/cpu/cva5/core.py
@@ -66,7 +66,7 @@ class CVA5(CPU):
either wishbone or axi")
-        cpu_group.add_argument("--variant",                    default="Linux", help="The CPU type for now it has the linux type")#TODO add other configs
+        cpu_group.add_argument("--cva5-variant",               default="Linux", help="The CPU type for now it has the linux type")#TODO add other configs
```
Once everything is configured, we can then run the LiteX generation command, for simplicity pointing to the existing Digilent Arty board as a target:
```
python3 -m litex_boards.targets.digilent_arty --cpu-type=naxriscv --build --no-compile
python3 -m litex_boards.targets.digilent_arty --cpu-type=vexriscv --build --no-compile
python3 -m litex_boards.targets.digilent_arty --cpu-type=vexriscv_smp --build --no-compile
python3 -m litex_boards.targets.digilent_arty --cpu-type=ibex --build --no-compile 
python3 -m litex_boards.targets.digilent_arty --cpu-type=cva5 --build --no-compile
python3 -m litex_boards.targets.digilent_arty --cpu-type=cva6 --build --no-compile
python3 -m litex_boards.targets.digilent_arty --cpu-type=rocket --cpu-variant=small --build --no-compile
```

## Chipyard Cores

TODO