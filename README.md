
# RISC-V Cores

There are many free (as in free speech) good quality RISC-V CPU cores available. Here we focus on those written directly in Verilog, or those written in other languages but can be "generated" into plain Verilog (or synthesizable SystemVerilog) code.

Examples of such languages are Chisel (based on Scala, and used for the Rocket chip and for Berkeley BOOM) and SpinalHDL (also based on Scala, and used for the Vex and Nax processors by Charles Papon - aka dolu1990).

For those already available in Verilog, we can simply provide a link; for those to be generated, we provide the generated files. There is a category in between which are cores written in another language but for which another third party website already stores the generated Verilog code, for example Rocket and Vex.

## How the LiteX CPUs were generated

Of all the pre-requisites of the LiteX repo, some are easily satisfied by using a Python virtual env ("venv"); SBT requires the addition of an external `.deb` repo; and the newest version of Java can cause problems (such as `[error] java.lang.NoClassDefFoundError: Could not initialize class sbt.internal.parser.SbtParser$`) so JDK 17 must be selected:
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