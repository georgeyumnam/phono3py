Interfaces to calculators
==========================

Currently the built-in interfaces for VASP and Pwscf are
prepared. VASP is the default interface and no special option is
necessary to invoke it, but for the other interfaces, each special
option has to be specified, e.g. ``--pwscf``.

.. _calculator_interfaces:

Calculator specific behaviors
------------------------------

Physical unit
^^^^^^^^^^^^^^

The interfaces for VASP and Pwscf are built in to the phono3py command.

For each calculator, each physical unit system is used. The physical
unit systems used for the calculators are summarized below.

::

           | unit-cell  FORCES_FC3   disp_fc3.yaml
   -----------------------------------------------
   VASP    | Angstrom   eV/Angstrom  Angstrom
   Pwscf   | au (bohr)  Ry/au        au

``FORCES_FC2`` and ``disp_fc2.yaml`` have the same physical units as
``FORCES_FC3`` and ``disp_fc3.yaml``, respectively.

.. _default_unit_cell_file_name_for_calculator:

Default unit cell file name
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Default unit cell file names are also changed according to the calculators::
    
   VASP    | POSCAR     
   Pwscf   | unitcell.in


.. _default_displacement_distance_for_calculator:

Default displacement distance created
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Default displacement distances created by ``-d`` option without
``--amplitude`` option are respectively as follows::

   VASP    | 0.03 Angstrom
   Pwscf   | 0.06 au (bohr)

Usage of VASP interface
-------------------------

The VASP interface is the default one. Therefore how to use is found
in :ref:`workflow`.

Usage of Pwscf interface
-------------------------

To invoke the Pwscf interface, ``--pwscf`` option has to be always
specified::

   % phono3py --pwscf [options] [arguments]

The usage of the Pwscf interface is almost the same as that of the
default case shown in :ref:`workflow`. 

When the file name of the unit cell is different from the default one
(see :ref:`default_unit_cell_file_name_for_calculator`), ``-c`` option
is used to specify the file name. Pwscf unit cell file parser used in
phono3py is the same as that in phonopy. It can read
only limited number of keywords that are shown in the phonopy web site
(http://atztogo.github.io/phonopy/pwscf.html#pwscf-interface).

1. Create supercells with displacements

   ::

      % phono3py --pwscf -d --dim="2 2 2" -c Si.in

   In this example, probably 111 different supercells with
   displacements are created. Supercell files (``supercell-xxx.in``)
   are created but they contain only the crystal
   structures. Calculation setting has to be added before running the
   calculation.

2. Run Pwscf for supercell force calculations

   Let's assume that the calculations have been made in ``disp-xxx``
   directories with the file names of ``Si-supercell.in``. Then after
   finishing those calculations, ``Si-supercell.out`` may be created
   in each directory.

3. Collect forces

   ``FORCES_FC3`` is obtained with ``--cf3`` options collecting the
   forces on atoms in Pwscf calculation results::

      % phono3py --pwscf --cf3 disp-00001/Si-supercell.out disp-00002/Si-supercell.out ...

   or in recent bash or zsh::

      % phono3py --pwscf --cf3 disp-{00001..00111}/Si-supercell.out

   ``disp_fc3.yaml`` is used to create ``FORCES_FC3``, therefore it
   must exist in current directory.

4) Calculate 3rd and 2nd order force constants

   ``fc3.hdf5`` and ``fc2.hdf5`` files are created by::

      % phono3py --pwscf --dim="2 2 2" -c Si.in --sym_fc3r --sym_fc2 --tsym

5) Calculate lattice thermal conductivity, e.g., by::

      % phono3py --pwscf --dim="2 2 2" -c Si.in --pa="0 1/2 1/2 1/2 0 1/2 1/2 1/2 0" \
         --mesh="11 11 11" --fc3 --fc2 --br
