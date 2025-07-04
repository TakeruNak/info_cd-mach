---
title: neb.md
excerpt: <excerpt or description on this script>
author: Takeru Nakashima
collection: portfolio
date: 2025-06-10
# Copyright (c) 2025-06-10 <Takeru Nakashima>. All rights reserved.
---

[🏠 Home](calc_method.md)

# Nudged Elastic Band (NEB) method
NEB計算と構造最適化計算の間には，共通したパラメタが存在する．
NEB計算特有のパラメタで重要となるものは，`MD.NEB.Number.Images`と
`NEB.Atoms.SpeciesAndCoordinates`ブロックである．
`MD.NEB.Number.Images` はNEB計算の際の初状態と終状態の間の中間状態の個数を示す．
`NEB.Atoms.SpeciesAndCoordinates`ブロックは，終状態の結晶構造を定義する．定義方法は，
`Atoms.SpeciesAndCoordinates`ブロックでの結晶構造の書式と同様．
`Atoms.SpeciesAndCoordinates`が始状態に対応し，`NEB.Atoms.SpeciesAndCoordinates`が終状態の結晶構造に対応する．

中間状態のimage座標を与えない場合は，始状態と終状態を`MD.NEB.Number.Images`数で線形補間した構造が
初期のimage構造（中間状態）に対応する．


```
# ---------------------------------
# NEB: MD or Geometry Optimization
# ---------------------------------

MD.Type                     NEB        # Nomd|Opt|DIIS|NVE|NVT_VS|NVT_NH
MD.Opt.DIIS.History          4         # default=7
MD.Opt.StartDIIS            10         # default=5
MD.maxIter                  100        # default=1
MD.TimeStep                 1.0        # default=0.5 (fs)
MD.Opt.criterion         1.0e-4        # default=1.0e-4 (Hartree/bohr)

MD.NEB.Number.Images        8         # default=10


#
# The coordinates of product
#
<NEB.Atoms.SpeciesAndCoordinates
   1    C   -0.77755846408657   -0.00000003553856   -0.77730141035137     2.0     2.0
   2    C    0.77681707294741   -0.00000002413166   -0.77729608216595     2.0     2.0
   3    H    1.23451821718817   -0.88763832172374   -1.23464057728123     0.5     0.5
   4    H    1.23451823170776    0.88763828275851   -1.23464059022330     0.5     0.5
   5    H   -1.23506432458023   -0.88767426830774   -1.23470899088096     0.5     0.5
   6    H   -1.23506425800395    0.88767424658723   -1.23470896874564     0.5     0.5
   7    C   -0.77755854665393    0.00000000908006    0.77730136931056     2.0     2.0
   8    C    0.77681705017323   -0.00000000970885    0.77729611199476     2.0     2.0
   9    H    1.23451826851556   -0.88763828740000    1.23464060936812     0.5     0.5
  10    H    1.23451821324627    0.88763830875131    1.23464061208483     0.5     0.5
  11    H   -1.23506431230451   -0.88767430754577    1.23470894717613     0.5     0.5
  12    H   -1.23506433587007    0.88767428525317    1.23470902573029     0.5     0.5
NEB.Atoms.SpeciesAndCoordinates>
```

## 具体例コード
東京大学物性研systemB Ohtaka上での計算を念頭におき，NEB計算用のインプットファイルを`c2h4_neb.dat`とする．

<details><summary> c2h4_neb.dat </summary>

``` 
#
# File Name
#

DATA.PATH {PATH OF DFT_DATA19}

System.CurrrentDirectory         ./    # default=./
System.Name                      c2h4
level.of.stdout                   1    # default=1 (1-3)
level.of.fileout                  1    # default=1 (0-2)

#
# Definition of Atomic Species
#

Species.Number       2
<Definition.of.Atomic.Species
 C   C7.0-s2p2d1    C_PBE19
 H   H6.0-s2p1      H_PBE19
Definition.of.Atomic.Species>

#
# Atoms
#

Atoms.Number         12
Atoms.SpeciesAndCoordinates.Unit   Ang # Ang|AU
<Atoms.SpeciesAndCoordinates
   1    C   -0.66829065594143    0.00000000101783   -2.19961193219289     2.0     2.0
   2    C    0.66817412917689   -0.00000000316062   -2.19961215251205     2.0     2.0
   3    H    1.24159214112072   -0.92942544650857   -2.19953308980064     0.5     0.5
   4    H    1.24159212192367    0.92942544733979   -2.19953308820323     0.5     0.5
   5    H   -1.24165800644131   -0.92944748269232   -2.19953309891389     0.5     0.5
   6    H   -1.24165801380425    0.92944749402510   -2.19953309747076     0.5     0.5
   7    C   -0.66829065113509    0.00000000341499    2.19961191775648     2.0     2.0
   8    C    0.66817411530651   -0.00000000006073    2.19961215383949     2.0     2.0
   9    H    1.24159211310925   -0.92942539308841    2.19953308889301     0.5     0.5
  10    H    1.24159212332935    0.92942539212392    2.19953308816332     0.5     0.5
  11    H   -1.24165799549343   -0.92944744948986    2.19953310195071     0.5     0.5
  12    H   -1.24165801426648    0.92944744880542    2.19953310162389     0.5     0.5
Atoms.SpeciesAndCoordinates>
Atoms.UnitVectors.Unit             Ang # Ang|AU
<Atoms.UnitVectors
 11.0  0.0  0.0
  0.0 10.0  0.0
  0.0  0.0 14.0
Atoms.UnitVectors>

#
# SCF or Electronic System
#

scf.XcType                 GGA-PBE     # LDA|LSDA-CA|LSDA-PW|GGA-PBE
scf.SpinPolarization       off         # On|Off|NC
scf.ElectronicTemperature  600.0       # default=300 (K)
scf.energycutoff           170.0       # default=150 (Ry)
scf.maxIter                100         # default=40
scf.EigenvalueSolver       cluster     # DC|GDC|Cluster|Band
scf.Kgrid                  1 1 1       # means n1 x n2 x n3
scf.Mixing.Type           rmm-diisk    # Simple|Rmm-Diis|Gr-Pulay|Kerker|Rmm-Diisk
scf.Init.Mixing.Weight     0.05        # default=0.30
scf.Min.Mixing.Weight      0.001       # default=0.001
scf.Max.Mixing.Weight      0.400       # default=0.40
scf.Mixing.History          20         # default=5
scf.Mixing.StartPulay       6          # default=6
scf.Mixing.EveryPulay       1          # default=6
scf.Kerker.factor          8.0         # default=1.0
scf.criterion             1.0e-8       # default=1.0e-6 (Hartree)

##

MD.Type                     NEB        # Nomd|Opt|DIIS|NVE|NVT_VS|NVT_NH
MD.Opt.DIIS.History          4         # default=7
MD.Opt.StartDIIS            10         # default=5
MD.maxIter                  100        # default=1
MD.TimeStep                 1.0        # default=0.5 (fs)
MD.Opt.criterion         1.0e-4        # default=1.0e-4 (Hartree/bohr)

MD.NEB.Number.Images        8         # default=10

<NEB.Atoms.SpeciesAndCoordinates
   1    C   -0.77755846408657   -0.00000003553856   -0.77730141035137     2.0     2.0
   2    C    0.77681707294741   -0.00000002413166   -0.77729608216595     2.0     2.0
   3    H    1.23451821718817   -0.88763832172374   -1.23464057728123     0.5     0.5
   4    H    1.23451823170776    0.88763828275851   -1.23464059022330     0.5     0.5
   5    H   -1.23506432458023   -0.88767426830774   -1.23470899088096     0.5     0.5
   6    H   -1.23506425800395    0.88767424658723   -1.23470896874564     0.5     0.5
   7    C   -0.77755854665393    0.00000000908006    0.77730136931056     2.0     2.0
   8    C    0.77681705017323   -0.00000000970885    0.77729611199476     2.0     2.0
   9    H    1.23451826851556   -0.88763828740000    1.23464060936812     0.5     0.5
  10    H    1.23451821324627    0.88763830875131    1.23464061208483     0.5     0.5
  11    H   -1.23506431230451   -0.88767430754577    1.23470894717613     0.5     0.5
  12    H   -1.23506433587007    0.88767428525317    1.23470902573029     0.5     0.5
NEB.Atoms.SpeciesAndCoordinates>
```
</details>

計算環境によっては，basisと擬ポテンシャルのデータセットを特定の場所に置いていると思うので，以下のような`DATA.PATH`変数にDFT_DATA19などのPAO, VPSのパスを定義する必要性がある．
systemBでは下記のように，`c2h4_neb.dat` に追記すれば良い:
```
DATA.PATH /home/issp/materiapps/oneapi_compiler_classic-2023.0.0--openmpi-4.1.5/openmx/openmx-3.9.9-1/DFT_DATA19
```

<details><summary> job-openmx.sh </summary>

```
#SBATCH -p i8cpu
#SBATCH -N 4
#SBATCH -n 16
#SBATCH -c 32
#SBATCH -t 00:30:00
 
set -e
source /home/issp/materiapps/oneapi_compiler_classic-2023.0.0--openmpi-4.1.5/openmx/openmxvars.sh
module list

# DATA.PATH /home/issp/materiapps/oneapi_compiler_classic-2023.0.0--openmpi-4.1.5/openmx/openmx-3.9.9-1/DFT_DATA19
 
# srun openmx input/###.dat
srun -n 16 --cpus-per-task=32 openmx c2h4.dat -nt 32
```

</details>

を用いて，以下のコマンドを入力する．
```sh
sbatch job-opemx.sh
```

[🏠 Home](calc_method.md)
