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
   $matTag, integer, nDMaterial (`FSAM <https://opensees.berkeley.edu/wiki/index.php/FSAM_-_2D_RC_Panel_Constitutive_Behavior>`_) tag
   $Thickness, float, macro-fiber thickness




The following recorders are available with the RCLayerMembrane02 section.
   
.. csv-table:: 
   :header: "Recorder", "Description"
   :widths: 20, 40

   panel_strain, "strains :math:`\epsilon_{xx}`, :math:`\epsilon_{yy}`, :math:`\gamma_{xy}`"
   panel_stress, "resulting panel stresses :math:`\sigma_{xx}`, :math:`\sigma_{yy}`, :math:`\tau_{xy}`"

.. admonition:: Notes

   | 1. The RCLayerMembrane02 should be used in conjunction with ``FSAM`` material. It can also be used in a ``MEFI`` element. 
   
.. admonition:: Examples

   The following example constructs an OrthotropicRotatingAngleConcrete material with tag **1**, composed of an uniaxial concrete material of tag **1**, a strain at tension cracking of **0.00008** and a strain at the compression strength of **-0.002** for a density of **0.0**.    

   1. **Tcl Code**

   .. code-block:: tcl
	  
	  nDMaterial OrthotropicRotatingAngleConcreteT2DMaterial01 1 1 0.00008 -0.002 0.0
		
   2. **Python Code**

   .. code-block:: python

      nDMaterial('OrthotropicRotatingAngleConcreteT2DMaterial01', 1, 1, 0.00008, -0.002, 0.0)	  
   

   
**REFERENCES:**

#. Rojas, F., Anderson, J. C., Massones, L. M. (2016). A nonlinear quadrilateral layered membrane with drilling degrees of freedom for the modeling of reinforced concrete walls. Engineering Structures, 124, 521-538. (`link <https://www.sciencedirect.com/science/article/pii/S0141029616302954>`_).


**Code Developed by:** F. Rojas (University of Chile), M.J. Núñez (University of Chile).
