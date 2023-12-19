.. _RCLayerMembrane02:

RCLayerMembrane02 Section
^^^^^^^^^^^^^^^^

This command is used to construct a RCLayerMembrane02 section object. It is the abstract representation for the stress-strain behavior for a reinforced concrete layer membrane element in the Finite Element Method or Structural Analysis.

.. admonition:: Command
   
   section ReinforcedConcreteLayerMembraneSection02 $secTag $matTag $Thickness

.. csv-table:: 
   :header: "Parameter", "Type", "Description"
   :widths: 10, 10, 40

   $secTag, integer, unique section tag
   $matTag, integer, nDMaterial reinforced concrete tag
   $Thickness, float, macro-fiber thickness

.. admonition:: Notes

   The valid queries to the Drucker-Prager material when creating an ElementRecorder are ‘strain’ and ‘stress’ (as with all nDmaterial).
   
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
