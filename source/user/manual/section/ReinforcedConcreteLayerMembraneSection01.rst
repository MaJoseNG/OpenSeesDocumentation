.. _ReinforcedConcreteLayerMembraneSection01:

ReinforcedConcreteLayerMembraneSection01
^^^^^^^^^^^^^^^^

This command is used to construct a ReinforcedConcreteLayerMembraneSection01 object. It is the abstract representation for the stress-strain behavior for a reinforced concrete layer membrane element (based on the work of Rojas et al., 2016).

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
   $Thicknesses, list float, a list of *nConcLayer* concrete layers thicknesses 
   $ecr, float, strain at tension cracking of the concrete (optional: default = 0.00008)
   $ec, float, strain at the compression strength of the concrete (optional: default = -0.002)
   
   
   
The following recorders are available with the ReinforcedConcreteLayerMembraneSection01.

.. csv-table:: 
   :header: "Recorder", "Description"
   :widths: 20, 40

   panel_strain, "strains :math:`\varepsilon_{xx}`, :math:`\varepsilon_{yy}`, :math:`\gamma_{xy}`"
   panel_stress, "resulting panel stresses :math:`\sigma_{xx}`, :math:`\sigma_{yy}`, :math:`\tau_{xy}`"

.. admonition:: Notes

   | 1. The **ReinforcedConcreteLayerMembraneSection01** should be used in conjunction with ``OrthotropicRAConcrete`` and ``SmearedSteelDoubleLayer`` NDMaterials. It can also be used in a ``MEFI`` element. 
   | 2. In Tcl, the section can also be referred to as **RCLayerMembraneSection01** or **RCLMS01**.
   
.. admonition:: Examples
   
   For the development of this example, the RW-A20-P10-S38 wall specimen was used (Tran, 2012). Uniaxial concrete and steel materials are defined, along with orthotropic layers for confined/unconfined concrete and distributed steel for the core and boundaries. Sections of types **a** and **b** are defined, composed of the layers created earlier.

   .. figure:: ReinforcedConcreteLayerMembraneSection01_figure.jpg
	   :align: center
	   :figclass: align-center
	   :width: 90%
	   :name: RCLMS01_FIG
	
	   RW-A20-P10-S38 wall specimen: (a) Cross-sectional view ; (b) Layered view of the model.
   
   
   1. **Tcl Code**

   .. code-block:: tcl

      # ========================================================================================
      # RW-A20-P10-S38 (Tran, 2012) - Definition of properties and creation of materials
      # ========================================================================================

      source LibUnits.tcl;               # define basic units

      # ----------------------------------------------------------------------------------------
      # Create uniaxial steel materials
      # ----------------------------------------------------------------------------------------

      # steel X
      set fyX [expr 469.93*$MPa];        # fy
      set bx 0.02;                       # strain hardening

      # steel Y web
      set fyYw [expr 409.71*$MPa];       # fy
      set byw 0.02;                      # strain hardening

      # steel Y boundary
      set fyYb [expr 429.78*$MPa];       # fy
      set byb 0.01;                      # strain hardening

      # steel misc
      set Esy [expr 200000.0*$MPa];      # Young's modulus
      set Esx $Esy;                      # Young's modulus
      set R0 20.0;                       # initial value of curvature parameter
      set A1 0.925;                      # curvature degradation parameter
      set A2 0.15;                       # curvature degradation parameter
  
      # build steel materials
      uniaxialMaterial  Steel02  1 $fyX  $Esx $bx  $R0 $A1 $A2; # steel X
      uniaxialMaterial  Steel02  2 $fyYw $Esy $byw $R0 $A1 $A2; # steel Y web
      uniaxialMaterial  Steel02  3 $fyYb $Esy $byb $R0 $A1 $A2; # steel Y boundary

      # ----------------------------------------------------------------------------------------
      # Create uniaxial concrete materials
      # ----------------------------------------------------------------------------------------

      # unconfined
      set fpc [expr -47.09*$MPa];                          # peak compressive stress
      set ec0 [expr -0.00232];                             # strain at peak compressive stress
      set ft [expr 2.13*$MPa];                             # peak tensile stress
      set strainAtFt [expr 0.00008];                       # concrete strain at tension cracking
      set Ec [expr 34766.59*$MPa];                         # Young's modulus     

      # confined
      set fpcc [expr -53.78*$MPa];                         # peak compressive stress
      set ec0c [expr -0.00397];                            # strain at peak compressive stress
      set Ecc [expr 36542.37*$MPa];                        # Young's modulus

      # build concrete materials
      uniaxialMaterial Concrete02 4 $fpc $ec0 [expr 0.0*$fpc] -0.037 0.1 $ft [expr 0.05*$Ec];    	# unconfined concrete
      uniaxialMaterial Concrete02 5 $fpcc $ec0c [expr 0.2*$fpc] -0.047 0.1 $ft [expr 0.05*$Ecc]; 	# confined concrete

      # define reinforcing ratios  
      set rouXw 0.0027;   # X web 
      set rouXb 0.0082;   # X boundary 
      set rouYw 0.0027;   # Y web
      set rouYb 0.0323;   # Y boundary

      # damage constants
      set damageConstant_1 0.175;
      set damageConstant_2 0.5;

      # ----------------------------------------------------------------------------------------
      # Create orthotropic concrete layers to represent unconfined and confined concrete
      # ----------------------------------------------------------------------------------------

      nDMaterial OrthotropicRAConcrete 6 4 $strainAtFt $ec0  0.0 -damageCte1 $damageConstant_1 -damageCte2 $damageConstant_2;   # unconfined concrete
      nDMaterial OrthotropicRAConcrete 7 5 $strainAtFt $ec0c 0.0 -damageCte1 $damageConstant_1 -damageCte2 $damageConstant_2;   # confined concrete

      # ----------------------------------------------------------------------------------------
      # Create smeared steel layers to represent boundary and web reinforment
      # ----------------------------------------------------------------------------------------

      nDMaterial SmearedSteelDoubleLayer 8 1 2 $rouXw $rouYw 0.0;    			# steel web
      nDMaterial SmearedSteelDoubleLayer 9 1 3 $rouXb $rouYb 0.0;    			# steel boundary

      # ----------------------------------------------------------------------------------------
      # Create ReinforcedConcreteLayerMembraneSection01 sections composed of concrete and steel layers
      # ----------------------------------------------------------------------------------------

      set tw   [expr 152.4*$mm];    # Wall thickness
      set tnc  [expr 81.0*$mm];     # unconfined concrete wall layer thickness
      set tc   [expr 71.4*$mm];     # confined concrete wall layer thickness   

      section RCLMS01 10 1 1 -reinfSteel 8   -conc 6   -concThick $tw        -epscr $strainAtFt -epsc $ec0;         # Section type b (wall web)
      section RCLMS01 11 1 2 -reinfSteel 9   -conc 6 7 -concThick $tnc $tc   -epscr $strainAtFt -epsc $ec0c;        # Section type a (wall boundary)

		
   2. **Python Code**

   .. code-block:: python

      # ========================================================================================
      # RW-A20-P10-S38 (Tran, 2012) - Definition of properties and creation of materials
      # ========================================================================================

      # Import OpenSeesPy
      import openseespy.opensees as ops

      # define basic units
      mm = 1.
      N = 1.
      sec = 1.

      mm2 = mm*mm
      MPa = N/mm2
      kN = 1000*N

      # ----------------------------------------------------------------------------------------
      # Create uniaxial steel materials
      # ----------------------------------------------------------------------------------------
      # steel x
      fyX = 469.93 * MPa       # fy
      bx = 0.02                # strain hardening

      # steel Y web
      fyYw = 409.71 * MPa      # fy
      byw = 0.02               # strain hardening

      # steel Y boundary
      fyYb = 429.78 * MPa      # fy
      byb = 0.01               # strain hardening

      # steel misc
      Esy = 200000.0 * MPa     # Young's modulus
      Esx = Esy                # Young's modulus
      R0 = 20.0                # initial value of curvature parameter
      A1 = 0.925               # curvature degradation parameter
      A2 = 0.15                # curvature degradation parameter

      # build steel materials
      ops.uniaxialMaterial('Steel02', 1, fyX,  Esx, bx,  R0, A1, A2)  # steel X
      ops.uniaxialMaterial('Steel02', 2, fyYw, Esy, byw, R0, A1, A2)  # steel Y web
      ops.uniaxialMaterial('Steel02', 3, fyYb, Esy, byb, R0, A1, A2)  # steel Y boundary

      # ----------------------------------------------------------------------------------------
      # Create uniaxial concrete materials
      # ----------------------------------------------------------------------------------------
      # unconfined
      fpc = -47.09 * MPa       # peak compressive stress
      ec0 = -0.00232           # strain at peak compressive stress
      ft = 2.13 * MPa          # peak tensile stress
      et = 0.00008             # strain at peak tensile stress
      Ec = 34766.59 * MPa      # Young's modulus

      # confined
      fpcc = -53.78 * MPa      # peak compressive stress
      ec0c = -0.00397          # strain at peak compressive stress
      Ecc = 36542.37 * MPa     # Young's modulus

      # build concrete materials
      ops.uniaxialMaterial('Concrete02', 4, fpc,  ec0,  0.0 * fpc, -0.037, 0.1, ft, 0.05 * Ec)   # unconfined concrete
      ops.uniaxialMaterial('Concrete02', 5, fpcc, ec0c, 0.2 * fpc, -0.047, 0.1, ft, 0.05 * Ecc)  # confined concrete

      # define reinforcing ratios   
      rouXw = 0.0027         # X web 
      rouXb = 0.0082         # X boundary 
      rouYw = 0.0027         # Y web
      rouYb = 0.0323         # Y boundary

      # damage constants
      damageConstant_1 = 0.175
      damageConstant_2 = 0.5

      # ----------------------------------------------------------------------------------------
      # Create orthotropic concrete layers to represent unconfined and confined concrete
      # ----------------------------------------------------------------------------------------

      ops.nDMaterial('OrthotropicRAConcrete', 6, 4, et, ec0,  0.0, '-damageCte1', damageConstant_1, '-damageCte2', damageConstant_2)   # unconfined concrete
      ops.nDMaterial('OrthotropicRAConcrete', 7, 5, et, ec0c, 0.0, '-damageCte1', damageConstant_1, '-damageCte2', damageConstant_2)   # confined concrete

      # ----------------------------------------------------------------------------------------
      # Create smeared steel layers to represent boundary and web reinforment
      # ----------------------------------------------------------------------------------------

      ops.nDMaterial('SmearedSteelDoubleLayer', 8, 1, 2, rouXw, rouYw, 0.0)       # steel web
      ops.nDMaterial('SmearedSteelDoubleLayer', 9, 1, 3, rouXb, rouYb, 0.0)       # steel boundary

      # ----------------------------------------------------------------------------------------  
      # Create ReinforcedConcreteLayerMembraneSection01 sections composed of concrete and steel layers
      # ----------------------------------------------------------------------------------------
      tw  = 152.4*mm     # wall thickness
      tnc = 81.0*mm      # unconfined concrete wall layer thickness
      tc  = 71.4*mm      # confined concrete wall layer thickness   

      ops.section('ReinforcedConcreteLayerMembraneSection01', 10, 1, 1, '-reinfSteel', 8, '-conc', 6,    '-concThick', tw,        '-epscr', 0.00008, '-epsc', -0.002)      # Section type b (wall web)
      ops.section('ReinforcedConcreteLayerMembraneSection01', 11, 1, 2, '-reinfSteel', 9, '-conc', 6, 7, '-concThick', tnc, tc,   '-epscr', 0.00008, '-epsc', -0.002)      # Section type a (wall boundary)   

   
**REFERENCES:**

#. Rojas, F., Anderson, J. C., Massones, L. M. (2016). A nonlinear quadrilateral layered membrane with drilling degrees of freedom for the modeling of reinforced concrete walls. Engineering Structures, 124, 521-538. (`link <https://www.sciencedirect.com/science/article/pii/S0141029616302954>`_).
#. Tran, T. A. (2012). Experimental and Analytical Studies of Moderate Aspect Ratio Reinforced Concrete Structural Walls. Ph.D. Dissertation, Department of Civil and Environmental Engineering, University of California, Los Angeles.

**Code Developed by:** F. Rojas (University of Chile), M.J. Núñez (University of Chile).
