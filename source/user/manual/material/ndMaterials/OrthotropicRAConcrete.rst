.. _OrthotropicRAConcrete:

OrthotropicRotatingAngleConcrete Material
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command is used to construct an OrthotropicRotatingAngleConcrete material object. It is the abstract representation of an Orthotropic Concrete Layer (plane stress) 2D material with the Rotating Angle and Tangent formulation for cycling or reversed loading with damage that is used in Finite Element Method or Structural Analysis.

.. admonition:: Command
   
   nDMaterial OrthotropicRotatingAngleConcreteT2DMaterial01 $matTag $conc $ecr $ec $rho <-damageCte1 $DamageCte1> <-damageCte1 $DamageCte1>

.. csv-table:: 
   :header: "Parameter", "Type", "Description"
   :widths: 10, 10, 40

   $matTag, integer, unique tag identifyieng this material
   $conc, integer, tag of unixial simulating concrete
   $ecr, float, strain at tension cracking of the concrete
   $ec, float, strain at the compression strength of the concrete
   $rho, float, density
   $DamageCte1, float, damage constant (optional: default = 0.14)
   $DamageCte1, float, damage constant (optional: default = 0.6)

.. admonition:: Notes
   
   | 1. The implementation of this material includes the effect of damage due to cyclic or reversal loading. Only damage in compression is considered. 
   | 2. If this effect is not to be considered, damage constants can be set equal to **0.0**.
   | 3. The valid queries to the OrthotropicRotatingAngleConcrete material when creating an ElementRecorder are **strain**, **stress** and **tangent** (as with all nDmaterial).

.. admonition:: Examples

   The following example constructs an OrthotropicRotatingAngleConcrete material with tag **1**, composed of an uniaxial concrete material of tag **1**, a strain at tension cracking of **0.00008** and a strain at the compression strength of **-0.002** for a density of **0.0**.

   1. **Tcl Code**

   .. code-block:: tcl
	  
	  nDMaterial OrthotropicRotatingAngleConcreteT2DMaterial01 1 1 0.00008 -0.002 0.0
		
   2. **Python Code**

   .. code-block:: python

      nDMaterial('OrthotropicRotatingAngleConcreteT2DMaterial01', 1, 1, 0.00008, -0.002, 0.0)	  
   

   
**REFERENCES:**

#. Rojas, F., Anderson, J. C., Massones, L. M. (2016). A nonlinear quadrilateral layered membrane with drilling degrees of freedom for the modeling of reinforced concrete walls. Engineering Structures, 124, 521-538.


**Code Developed by:** 

