# Credits and licence

RemeshPortal is a free software, distributed under the **GNU General Public License v3** (see `LICENSE`).

## Built on:

* **VCGLib** (Visual Computing Lab, ISTI-CNR) - GPL-3.0. Included in `third_party/vcglib`
  (release  2025.07). Provides the mesh data structures and the algorithms (OBJ import/export,
  topology, cleaning, and in later phases remeshing, simplification, subdivision, quad pairing).
* **MeshLab** (ISTI-CNR) - GPL-3.0. RemeshPortal reuses MeshLab's algorithm choices and
  default settings; the four tools are modelled on its filters.
* **Qt 5.15** (The  Qt Company) - used under the LGPL v3 through the Qt DLLs shipped next to the program.
* **Exo 2** (Natanael Gama), SIL Open Font License 1.1 - used for the "RM" logo lettering.
  Licence text: `resources/brand/OFL-Exo2.txt`.

* **QuadWild-BiMDF** release v0.0.2 (Pietroni et al.; Computer Graphics Group, University of Bern) - GPL-3.0.
  The unmodified Windows programs `quadwild.exe` and `quad_from_patches.exe` are shipped in `tools/quadwild`
  (see `tools/quadwild/NOTICE.txt` and the licence file there) and run as separate processes by
  *Free-Flow Quad Remesh*. Source: https://github.com/cgg-bern/quadwild-bimdf
