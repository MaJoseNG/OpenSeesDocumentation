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

.. admonition:: Notes

   | 1. 
   | 2. 
   
.. admonition:: Examples

   The following example constructs an OrthotropicRotatingAngleConcrete material with tag **1** and an uniaxial conrete material of tag **1**, with a strain at tension cracking of **0.00008** and a strain at the compression strength of **-0.002** for a density of **0.0**    

   1. **Tcl Code**

   .. code-block:: tcl
	  
	  nDMaterial OrthotropicRotatingAngleConcreteT2DMaterial01 1 1 0.00008 -0.002 0.0
		
   2. **Python Code**

   .. code-block:: python

      nDMaterial('OrthotropicRotatingAngleConcreteT2DMaterial01', 1, 1, 0.00008, -0.002, 0.0)	  
   

   
**REFERENCES:**

#. Rojas, F., Anderson, J. C., Massones, L. M. (2016). A nonlinear quadrilateral layered membrane with drilling degrees of freedom for the modeling of reinforced concrete walls. Engineering Structures, 124, 521-538.


**Code Developed by:** 
