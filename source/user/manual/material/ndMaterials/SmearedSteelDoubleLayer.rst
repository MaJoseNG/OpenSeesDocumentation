.. _SmearedSteelDoubleLayer:

SmearedSteelDoubleLayer Material
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command is used to construct a SmearedSteelDoubleLayer material object. It is the abstract representation of a double perpendicular smeared steel layers (plane stress) 2D material with a tangent formulation, and use a uniaxial material in each direction that is used in Finite Element Method or Structural Analysis.

.. admonition:: Command
   
   nDMaterial SmearedSteelDoubleLayerT2DMaterial01 $matTag $s1 $s2 $ratioSteelLayer1 $ratioSteelLayer2 $OrientationEmbeddedSteel

.. csv-table:: 
   :header: "Parameter", "Type", "Description"
   :widths: 10, 10, 40

   $matTag, integer, unique tag identifyieng this material
   $s1, integer, tag of unixial simulating horizontal reinforcement
   $s2, integer, tag of unixial simulating vertical reinforcement
   $ratioSteelLayer1, float, reinforcing ratio in horizontal direction of smeared steel
   $ratioSteelLayer2, float, reinforcing ratio in vertical direction of smeared steel
   $OrientationEmbeddedSteel, float, orientation of the smeared steel layer

.. admonition:: Notes

   | 1. No reductions in yield strength and strain-hardening ratio are accounted for (due to the steel being embedded in concrete).
   | 2.	The valid queries to the SmearedSteelDoubleLayer material when creating an ElementRecorder are **strain**, **stress** and **tangent** (as with all nDmaterial).

.. admonition:: Examples

   The following example constructs a SmearedSteelDoubleLayer material with tag **1** , composed of a horizontal and vertical uniaxial steel material of tag **1**, reinforcing ratio in horizontal and vertical direction of **1%** and an orientation of the smeared steel layer of **0.0** radians.   

   1. **Tcl Code**

   .. code-block:: tcl
	  
	  nDMaterial SmearedSteelDoubleLayerT2DMaterial01 1 1 1 0.01 0.01 0.0;
		
   2. **Python Code**

   .. code-block:: python

      nDMaterial('SmearedSteelDoubleLayerT2DMaterial01', 1, 1, 1, 0.01, 0.01, 0.0)	  
   

   
**REFERENCES:**

#. Rojas, F., Anderson, J. C., Massones, L. M. (2016). A nonlinear quadrilateral layered membrane with drilling degrees of freedom for the modeling of reinforced concrete walls. Engineering Structures, 124, 521-538.


**Code Developed by:** 

