.. _RCLayerMembrane01:

RCLayerMembrane01 Section
^^^^^^^^^^^^^^^^

This command is used to construct a RCLayerMembrane01 section object. It is the abstract representation for the stress-strain behavior for a reinforced concrete layer membrane element in the Finite Element Method or Structural Analysis.

.. admonition:: Command
   
   section ReinforcedConcreteLayerMembraneSection01 $secTag $nSteelLayer $nConcLayer -reinfSteel{$RSteelAtEachLayer} –conc{$concAtEachLayer} -concThick{$Thicknesses} <-epscr $ecr> <-epsc $ec>

.. csv-table:: 
   :header: "Parameter", "Type", "Description"
   :widths: 10, 10, 40

   $secTag, integer, unique section tag
   $nSteelLayer, integer, number of reinforced steel layers
   $nConcLayer, integer, number of concrete layers
   $RSteelAtEachLayer, list int, a list of *nSteelLayer* nDMaterial reinforced steel tags to be assigned to each layer
   $concAtEachLayer, list int, a list of *nConcLayer* nDMaterial concrete tag to be assigned to each layer
   $Thicknesses, list float, a list of *nConcLayer* concrete layer thicknesses 
   $ecr, float, strain at tension cracking of the concrete (optional: default = 0.00008)
   $ec, float, strain at the compression strength of the concrete (optional: default = -0.002)
   
   
   
The following recorders are available with the RCLayerMembrane01 section.

.. csv-table:: 
   :header: "Recorder", "Description"
   :widths: 20, 40

   panel_strain, "strains :math:`\varepsilon_{xx}`, :math:`\varepsilon_{yy}`, :math:`\gamma_{xy}`"
   panel_stress, "resulting panel stresses :math:`\sigma_{xx}`, :math:`\sigma_{yy}`, :math:`\tau_{xy}`"

.. admonition:: Notes

   | 1. The RCLayerMembrane01 should be used in conjunction with ``OrthotropicRotatingAngleConcrete`` and ``SmearedSteelDoubleLayer`` materials. It can also be used in a ``MEFI`` element. 
   
.. admonition:: Examples
   
   This example demonstrates the modeling of a wall with confined boundary elements. Uniaxial concrete and steel materials are defined, along with orthotropic layers for confined/unconfined concrete and distributed steel for the core and boundaries. Composite sections comprising layers of concrete and steel are created and subsequently assigned to an element.

   .. figure:: RCLayerMembrane01.jpg
	   :align: center
	   :figclass: align-center
	   :width: 1000px
	   :name: RCLMS01_FIG
	
	   Wall model: (a) Cross-sectional view; (b) Layered view.
   
   
   1. **Tcl Code**

   .. code-block:: tcl

      # set up a 2D and 3DOF/node model
      model BasicBuilder -ndm 2 -ndf 3
      node 1 0.0 0.0
      node 2 1.0 0.0
      node 3 1.0 1.0
      node 4 0.0 1.0

      # create an uniaxial unconfined concrete material
      uniaxialMaterial Concrete02 1 -35 -0.002 0.0 -0.006 0.1 1.8 1750.

      # create an uniaxial confined concrete material
      uniaxialMaterial Concrete02 2 -40 -0.004 -8.0 -0.014 0.1 2.0 1000.

      # create an uniaxial steel material 
      uniaxialMaterial  Steel02  3 420.0 200000.0 0.01 20.0 0.925 0.15

      # define reinforcing ratios
      set rouXb 0.008;               # X boundary
      set rouYb 0.025;               # Y boundary
      set rouXw 0.003;               # X web
      set rouYw 0.003;               # Y web

      # create smeared steel layers to represent boundary and web reinforment
      nDMaterial SmearedSteelDoubleLayerT2DMaterial01     4   3   3      $rouXb            $rouYb        0.0;    # boundary reinforcement
      nDMaterial SmearedSteelDoubleLayerT2DMaterial01     5   3   3      $rouXw            $rouYw        0.0;    # web reinforcement

      # create orthotropic concrete layers to represent unconfined and confined concrete
      nDMaterial OrthotropicRotatingAngleConcreteT2DMaterial01      6     1   0.00008  -0.002  0.0 -damageCte1 0.175 -damageCte2 0.5;   # unconfined concrete
      nDMaterial OrthotropicRotatingAngleConcreteT2DMaterial01      7     2   0.00008  -0.004  0.0 -damageCte1 0.175 -damageCte2 0.5;   # confined concrete

      # create ReinforcedConcreteLayerMembraneSection01 sections composed of concrete and steel layers 
      section ReinforcedConcreteLayerMembraneSection01 8 1 2 -reinfSteel 4 -conc 6 7 -concThick 0.75 0.25;     # boundary section
      section ReinforcedConcreteLayerMembraneSection01 9 1 1 -reinfSteel 5 -conc 6   -concThick 0.1;           # web section

      # create a MEFI element with 6 panels/fibers and 2 types of sections
      element MEFI 1 1 2 3 4 6 -width 0.1 0.2 0.2 0.2 0.2 0.1 -section 8 9 9 9 9 8

      # record panel strain and stress of integration point 1  
      recorder Element -file MEFI_panel_1_strain.out -time -ele 1 RCPanel 1 panel_strain
      recorder Element -file MEFI_panel_1_stress.out -time -ele 1 RCPanel 1 panel_stress
		
   2. **Python Code**

   .. code-block:: python

	  # Import OpenSeesPy
	  import openseespy.opensees as ops

	  # set up a 2D and 3DOF/node model
	  ops.model('BasicBuilder', '-ndm', 2, '-ndf', 3)
	  ops.node(1, *[0.0, 0.0])
	  ops.node(2, *[1.0, 0.0])
	  ops.node(3, *[1.0, 1.0])
	  ops.node(4, *[0.0, 1.0])

	  # create an uniaxial unconfined concrete material
	  ops.uniaxialMaterial('Concrete02', 1, -35, -0.002, 0.0, -0.006, 0.1, 1.8, 1750)

	  # create an uniaxial confined concrete material
	  ops.uniaxialMaterial('Concrete02', 2, -40, -0.004, -8.0, -0.014, 0.1, 2.0, 1000)

	  # create an uniaxial steel material
	  ops.uniaxialMaterial('Steel02', 3, 420.0, 200000.0, 0.01, 20.0, 0.925, 0.15)

	  # define reinforcing ratios
	  rouXb = 0.008  # X boundary
	  rouYb = 0.025  # Y boundary
	  rouXw = 0.003  # X web
	  rouYw = 0.003  # Y web

	  # create smeared steel layers to represent boundary and web reinforment
	  ops.nDMaterial('SmearedSteelDoubleLayerT2DMaterial01', 4, 3, 3, rouXb, rouYb, 0.0)  # boundary reinforcement
	  ops.nDMaterial('SmearedSteelDoubleLayerT2DMaterial01', 5, 3, 3, rouXw, rouYw, 0.0)  # web reinforcement

	  # create orthotropic concrete layers to represent unconfined and confined concrete
	  ops.nDMaterial('OrthotropicRotatingAngleConcreteT2DMaterial01', 6, 1, 0.00008, -0.002, 0.0, '-damageCte1', 0.175, '-damageCte2', 0.5)  # unconfined concrete
	  ops.nDMaterial('OrthotropicRotatingAngleConcreteT2DMaterial01', 7, 2, 0.00008, -0.004, 0.0, '-damageCte1', 0.175, '-damageCte2', 0.5)  # confined concrete

	  # create ReinforcedConcreteLayerMembraneSection01 sections composed of concrete and steel layers 
	  ops.section('ReinforcedConcreteLayerMembraneSection01', 8, 1, 2, '-reinfSteel', 4, '-conc', 6, 7, '-concThick', 0.75, 0.25)  # boundary section
	  ops.section('ReinforcedConcreteLayerMembraneSection01', 9, 1, 1, '-reinfSteel', 5, '-conc', 6, '-concThick', 0.1)  		   # web section

	  # create a MEFI element with 6 panels/fibers and 2 types of sections
	  ops.element('MEFI', 1, 1, 2, 3, 4, 6, '-width', 0.1, 0.2, 0.2, 0.2, 0.2, 0.1, '-section', 8, 9, 9, 9, 9, 8)

	  # record panel strain and stress of integration point 1
	  ops.recorder('Element', '-file', 'MEFI_panel_1_strain.out', '-time', '-ele', 1, 'RCPanel', 1, 'panel_strain')
	  ops.recorder('Element', '-file', 'MEFI_panel_1_stress.out', '-time', '-ele', 1, 'RCPanel', 1, 'panel_stress')
   

   
**REFERENCES:**

#. Rojas, F., Anderson, J. C., Massones, L. M. (2016). A nonlinear quadrilateral layered membrane with drilling degrees of freedom for the modeling of reinforced concrete walls. Engineering Structures, 124, 521-538. (`link <https://www.sciencedirect.com/science/article/pii/S0141029616302954>`_).


**Code Developed by:** F. Rojas (University of Chile), M.J. Núñez (University of Chile).
