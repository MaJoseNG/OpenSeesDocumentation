.. _ReinforcedConcreteLayerMembraneSection02:

ReinforcedConcreteLayerMembraneSection02 
^^^^^^^^^^^^^^^^

This command is used to construct a ReinforcedConcreteLayerMembraneSection02 object. It is the abstract representation for the stress-strain behavior for a reinforced concrete layer membrane element.

.. admonition:: Command
   
   section ReinforcedConcreteLayerMembraneSection02 $secTag $matTag $Thickness

.. csv-table:: 
   :header: "Parameter", "Type", "Description"
   :widths: 10, 10, 40

   $secTag, integer, unique section tag
   $matTag, integer, nDMaterial (`FSAM <https://opensees.berkeley.edu/wiki/index.php/FSAM_-_2D_RC_Panel_Constitutive_Behavior>`_) tag
   $Thickness, float, section thickness




The following recorders are available with the ReinforcedConcreteLayerMembraneSection02.
   
.. csv-table:: 
   :header: "Recorder", "Description"
   :widths: 20, 40

   panel_strain, "strains :math:`\epsilon_{xx}`, :math:`\epsilon_{yy}`, :math:`\gamma_{xy}`"
   panel_stress, "resulting panel stresses :math:`\sigma_{xx}`, :math:`\sigma_{yy}`, :math:`\tau_{xy}`"

.. admonition:: Notes

   | 1. The **ReinforcedConcreteLayerMembraneSection02** should be used in conjunction with ``FSAM`` material. It can also be used in a ``MEFI`` element. 
   | 2. The section can also be referred to as **RCLayerMembraneSection02** or **RCLMS02**.

.. admonition:: Examples
   
   For the development of this example, the RW-A20-P10-S38 wall specimen was employed (Tran, 2012). Uniaxial concrete and steel materials are defined, along with FSAM nDMaterials used within RLMS02 sections to represent sections of types **a** and **b**, as illustrated in Fig. 3.1.7.1(a) for the ReinforcedConcreteLayerMembraneSection01 example.

   1. **Tcl Code**

   .. code-block:: tcl

      # ========================================================================================
      # RW-A20-P10-S38 (Tran, 2012) - Definition of properties and creation of materials
      # Created by: Carlos Lopez O.
      # Adapted by: Maria Jose Nunez G.
      #             07/2023
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


      # shear resisting mechanism parameters

      set nu 0.35;                # friction coefficient
      set alfadow [expr 0.005];   # dowel action stiffness parameter
	  
	  # ----------------------------------------------------------------------------------------
      # Create FSAM nDMaterial
      # ----------------------------------------------------------------------------------------
	  
	  nDMaterial FSAM 6  0.0  1   2   4  $rouXw $rouYw  $nu  $alfadow;   # Web (unconfined concrete)
      nDMaterial FSAM 7  0.0  1   3   5  $rouXb $rouYb  $nu  $alfadow;   # Boundary (confined concrete)

      # ----------------------------------------------------------------------------------------
      # Create ReinforcedConcreteLayerMembraneSection02 section
      # ----------------------------------------------------------------------------------------
      
	  set tw   [expr 152.4*$mm];    # Wall thickness

      section RCLMS02 10 6 $tw;     # Wall web
      section RCLMS02 11 7 $tw;     # Wall boundary
		
   2. **Python Code**

   .. code-block:: python

      # ========================================================================================
      # RW-A20-P10-S38 (Tran, 2012) - Definition of properties and creation of materials
      # Created by: Carlos Lopez O.
      # Adapted by: Maria Jose Nunez G.
      #             07/2023
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

      # shear resisting mechanism parameters 
      nu = 0.35                           # friction coefficient
      alfadow = 0.005                     # dowel action stiffness parameter
      
      # ----------------------------------------------------------------------------------------
      # Create FSAM nDMaterial
      # ----------------------------------------------------------------------------------------
      
      ops.nDMaterial('FSAM', 6, 0.0, 1, 2, 4, rouXw, rouYw, nu, alfadow)           # Web (unconfined concrete)
      ops.nDMaterial('FSAM', 7, 0.0, 1, 3, 5, rouXb, rouYb, nu, alfadow)           # Boundary (confined concrete)

      # ----------------------------------------------------------------------------------------
      # Create ReinforcedConcreteLayerMembraneSection02 section
      # ----------------------------------------------------------------------------------------

      tw = 152.4 * mm  # Wall thickness

      ops.section('RCLMS02', 10, 6, tw)    # Wall Web
      ops.section('RCLMS02', 11, 7, tw)    # Wall Boundary



**REFERENCES:**

#. Rojas, F., Anderson, J. C., Massones, L. M. (2016). A nonlinear quadrilateral layered membrane with drilling degrees of freedom for the modeling of reinforced concrete walls. Engineering Structures, 124, 521-538. (`link <https://www.sciencedirect.com/science/article/pii/S0141029616302954>`_).
#. Tran, T. A. (2012). Experimental and Analytical Studies of Moderate Aspect Ratio Reinforced Concrete Structural Walls. Ph.D. Dissertation, Department of Civil and Environmental Engineering, University of California, Los Angeles.

**Code Developed by:** F. Rojas (University of Chile), M.J. Núñez (University of Chile).
