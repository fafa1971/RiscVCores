
# RISC-V Cores

There are many free (as in free speech) good quality RISC-V CPU cores available. Here we focus on those written directly in Verilog, or those written in other languages but can be "generated" into plain Verilog (or synthesizable SystemVerilog) code.

Examples of such languages are Chisel (based on Scala, and used for the Rocket chip and for Berkeley BOOM) and SpinalHDL (also based on Scala, and used for the Vex and Nax processors by Charles Papon - aka dolu1990).

For those already available in Verilog, we can simply provide a link; for those to be generated, we provide the generated files. There is a category in between which are cores written in another language but for which another third party website already stores the generated Verilog code, for example Rocket and Vex.

All cores can be found in the [verilog](/verilog/) directory.

## LiteX Cores

These cores are simple copies of the original LiteX sub-repositories:
- [CVA5](verilog/CVA5/) ([upstream](https://github.com/openhwgroup/cva5)) is a copy of `LiteX/pythondata-cpu-cva5/pythondata_cpu_cva5/system_verilog/core/`
- [CVA6](verilog/CVA6/) ([upstream](https://github.com/openhwgroup/cva6)) is a copy of `LiteX/pythondata-cpu-cva6/pythondata_cpu_cva6/system_verilog/core/`
- [Ibex](verilog/Ibex/) ([upstream](https://github.com/lowRISC/ibex)) is a copy of `LiteX/pythondata-cpu-ibex/pythondata_cpu_ibex/system_verilog/rtl/`
- [Rocket](verilog/Rocket/) ([upstream](https://github.com/ucb-bar/rocket-chip)) is a copy of `LiteX/pythondata-cpu-rocket/pythondata_cpu_rocket/verilog/generated-src/`
- [OpenC906](verilog/OpenC906/) ([upstream](https://github.com/XUANTIE-RV/openc906)) is a copy of `LiteX/pythondata-cpu-openc906/pythondata_cpu_openc906/verilog/C906_RTL_FACTORY/gen_rtl/`
- [Vex](verilog/Vex/) ([upstream](https://github.com/SpinalHDL/VexRiscv)) is a copy of `LiteX/pythondata-cpu-vexriscv/pythondata_cpu_vexriscv/verilog/`

The exception are Nax and VexII which were generated using LiteX:
- [Nax](verilog/Nax/) ([upstream](https://github.com/SpinalHDL/NaxRiscv)) is a copy of `LiteX/pythondata-cpu-naxriscv/pythondata_cpu_naxriscv/verilog/`
with the `git status` reporting these generated files inside the submodule:
```
Untracked files:
	NaxRiscvLitex_8627c7bc2ad1ab7b8f472a89b62e78d0.v
	ext/
```
- [VexII](verilog/VexII/) ([upstream](https://github.com/SpinalHDL/VexiiRiscv)) is a copy of ../LiteX/pythondata-cpu-vexiiriscv/pythondata_cpu_vexiiriscv/verilog
with the `git status` reporting these generated files inside the submodule:
```
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	6e67a86d10b7a5232e967e5cc469db35.py
	VexiiRiscvLitex_079e876955aaba9eca1f0830e46c699a.v
	ext/
```

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